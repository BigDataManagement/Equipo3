# PROYECTO 1 - Equipo 3<h1>
# Gestión de datos grandes <h2>

### Miembros:
__Miembros del equipo:__
* Juan David Loaiza Botero
* Juan Camilo Gomez Ruiz    
* Daniel Morales Londoño    

### Información del proyecto:
* __Equipo:__ #3
* __Motor de base de datos:__ MySQL
* __IDE:__ MySQL workbench
* __Integración de datos:__ Pentaho: Data Integration, Business Analytics and Big Data

### Objetivo:
Activar un workflow de gestión de datos en Pentaho Data Integration cuya fuente de datos son dos DBMSs (MySQL y MongoDB).

### Importación de datos a MySQl y limpieza de los mismos
Usamos dos archivos, ``movie_titles.csv`` y ``combined_data_3.txt``, para nuestra base de datos ``netflix``

### Creación de tablas
Para cada uno de los archivos creamos su tabla correspondiente: ``movie_titles`` y ``scores_3``

```sql
CREATE TABLE movie_titles(
  id_pelicula INT NOT NULL primary key,
  año_lanzamiento INT NOT NULL,
  nombre_pelicula TEXT NOT NULL
);

CREATE TABLE scores_3(
  id_calificacion INT AUTO_INCREMENT primary key,
  id_pelicula INT NOT NULL,
  codigo_cliente INT NOT NULL,
  calificacion INT NOT NULL,
  fecha DATE NOT NULL,
FOREIGN KEY (id_pelicula) REFERENCES netflix.movie_titles(id_pelicula)
);

```

#### Carga de datos
Para el archivo ``movie_titles.csv`` creamos la tabla ``movie_titles`` e hicimos la carga de los datos por medio de la siguiente sentencia:
```sql
LOAD DATA LOCAL INFILE 'C:/Users/CAMILO/Desktop/gestion/movie_titles.csv' INTO TABLE netflix.movie_titles;
```
Para el archivo ``combined_data_3.txt``, primero lo convertimos por medio del siguiente código, obteniendo el archivo ``prueba_convertida.csv``.

__Java__
__Convertidor.java:__ El código abre el archivo a traducir y lo divide en 4 elementos, genera un archivo en donde escribir luego en el escribe un contador, y los siguientes 4 datos tomados del archivo a traducir.

```java
import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.PrintWriter;
import java.io.IOException;
import java.util.ArrayList;

public class Convertidor {

    private static ArrayList<String> datos = new ArrayList<>();

    public static void main(String args[]) {
	String cadena;
	int contador=0;
	int cont = 0;
	try{
	    FileReader f = new FileReader("combined_data_3.csv");
	    BufferedReader b = new BufferedReader(f);
	    //System.out.println("Antes while");
	    cadena = b.readLine();
	    String []id_pelicula = cadena.split(":");
	    //pel++;
	    for (int i=0; i<4157; i++){
		do{
		    if (cont!=0){
		    contador++;
			datos.add(ConvertirLinea(cadena, id_pelicula[0],contador));
			//System.out.println();
		    }else{
			cont++;
		    }

		}while((cadena = b.readLine())!=null && NoEsPelica(cadena));
		id_pelicula = cadena.split(":");
		cont=0;
	    }
	    b.close();
	}catch(Exception e){e.printStackTrace();}
	//System.out.println(pel);
	CrearArchivo(datos);
    }

    public static boolean NoEsPelica(String cadena) {
	cadena = cadena.replace(" ","");
	int index = cadena.indexOf(":");
	if (index == -1){
	    return true;
	}else{
	    return false;
	}

    }
    public static String ConvertirLinea(String cadena, String id_pelicula,int contador) {
	String []parts = cadena.split(",");
	return contador+" "+id_pelicula + " " + parts[0] + " " + parts[1] + " " + parts[2];
    }

    public static void CrearArchivo(ArrayList<String> lineas){
	FileWriter fichero = null;
        PrintWriter pw = null;
        try
        {
            fichero = new FileWriter("prueba_convertida.csv");
            pw = new PrintWriter(fichero);
	    int tm = datos.size();
            for (int i = 0; i < tm; i++)
                pw.println(lineas.get(i));

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
           try {
           // Nuevamente aprovechamos el finally para
           // asegurarnos que se cierra el fichero.
           if (null != fichero)
              fichero.close();
           } catch (Exception e2) {
              e2.printStackTrace();
           }
        }
    }
}

```

Para el archivo resultante ``prueba_convertida.csv`` creamos la tabla ``scores_3`` e hicimos la carga de los datos por medio de la siguiente sentencia:

```sql
LOAD DATA LOCAL INFILE 'C:/Users/CAMILO/Desktop/gestion/prueba_convertida.csv' INTO TABLE netflix.scores_3;
```

### Limpieza de datos:
Borramos los registros de la tabla ``movie_titles`` que no están referenciados desde ``scores_3``
```sql
SET SQL_SAFE_UPDATES = 0;
DELETE FROM netflix.movie_titles
WHERE id_pelicula NOT IN (
  SELECT id_pelicula FROM netflix.scores_3
);
```
Antes de hacer la limpieza teníamos __17.762__ registros en ``movie_titles`` y luego de correr el query de limpieza, quedaron __4.156__ los demás __13.606__ registros no estaban referenciados en ``scores_3``

### Integración de datos en Pentaho
Primero descargamos la plataforma y buscamos los controladores necesarios para hacer la conexión con MySQL. En esta caso se uso ``mysql-connector-java-5.1.46.jar`` el cual se instalo, posteriormente se restauro la aplicación y procedimos a la conexión.

#### Workflow
El Workflow se desarrolló sólo con nuestro motor ya que el otro equipo no había terminado su parte.

__Conexión:__
* ![conection](http://img.fenixzone.net/i/f5pkUo6.jpeg)

__Mensaje:__
* ![message](http://img.fenixzone.net/i/QUPk1rp.jpeg)

__Creación:__
* ![table1](http://img.fenixzone.net/i/kTg7IVo.jpeg)
* ![table2](http://img.fenixzone.net/i/XTNSwUW.jpeg)

### Problemas
* __Transformación de datos:__ Inicialmente usamos un código en Python el cual no convertia efectivamente el archivo al formato en que desabamos, por lo tanto usamos Java para convertir el archivo.
* __Importación de datos a MySQL workbench:__ Estuvimos 2 días intentando subir los datos al framewrok sin exito, debido a problemas de rendimiento en las máquinas por el alto volumen de datos y la mala creación de la tablas.
* __Creación de workflow en Pentaho:__ En Pentaho tuvimos la dificultad de instalar el driver ya que no lo leía pero después nos dimos cuenta que había que restart el programa y funcionó correctamente.
