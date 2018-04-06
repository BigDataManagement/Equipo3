# PROYECTO 2 -Equipo 3<h1>
# Gestion de datos grandes <h2>

### Miembros:
__Miembros del equipo:__
* Juan David Loaiza Botero 
* Juan Camilo Gomez Ruiz    
* Daniel Morales Londoño    

### 1. Codigo:

__Java__
* __Convertidor.java:__ el codigo abre el archivo a traducir y lo divide en 4 elementos, genera un archivo en donde escribir
						luego en el escribe un contador, y los siguientes 4 datos tomados del archivo a traducir.
```javascript
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


### 2. Sentencias:
* __MYSQL:__
	* __Comparacion de datos en las 2 tablas:__
	```javascript
	SET SQL_SAFE_UPDATES = 0;
	DELETE FROM netflix.movie_titles
	WHERE id_pelicula NOT IN 
    (
    SELECT id_pelicula FROM netflix.scores_3
    );
	```
	

