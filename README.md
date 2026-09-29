# Taller-Ingesoft
Solución al taller numero uno (1) de la materia de ingeniera de software 2 con el lenguaje de Java

# Ejercicio S — Single Responsibility Principle
  “Una clase debe tener una, y solo una, razón para cambiar.”

  - Describan qué hace esta clase en una sola frase. ¿Cuántas veces usaron la palabra “y”?
      - La clase Estudiante calcula el promedio del estudiante, guarda sus datos en un archivo, imprime el boletín y envía           un correo al acudiente.
  - Si el colegio cambia el formato del boletín, ¿qué clase tocan? ¿Y si cambian el archivo por una base de datos?
      - Si cambian el formato del boletín → tocan la clase Estudiante (método imprimirBoletin).
      - Si cambian el archivo por una base de datos → también tocan la clase Estudiante (método guardarEnArchivo).

## Problema identificado.
La clase estudiante tiene multiples responsabilidades como gestionar los datos del estudiante y calcular el promedio ademas, tambien guarda el archivo, imprime el boletin y envia el correo al acudiente del estudiante.
## Código original.
  ```java
public class Estudiante {
    private String nombre;
    private double[] notas;

    public Estudiante(String nombre, double[] notas) {
        this.nombre = nombre;
        this.notas = notas;
    }

    public double calcularPromedio() {
        double suma = 0;
        for (double n : notas) suma += n;
        return suma / notas.length;
    }

    public void guardarEnArchivo() {
        System.out.println("Guardando " + nombre + " en estudiantes.txt...");
    }

    public void imprimirBoletin() {
        System.out.println("=== BOLETÍN ===");
        System.out.println("Nombre: " + nombre);
        System.out.println("Promedio: " + calcularPromedio());
    }

    public void enviarCorreoAlAcudiente() {
        System.out.println("Enviando boletín por correo al acudiente de " + nombre);
    }
}
  ```
## Código corregido Su solución.
 ```java
class Estudiante {
    private String nombre;
    private double[] notas;

    public Estudiante(String nombre, double[] notas) {
        this.nombre = nombre;
        this.notas = notas;
    }

    public String getNombre() {
        return nombre;
    }

    public double calcularPromedio() {
        if (notas == null || notas.length == 0) return 0.0;
        double suma = 0;
        for (double n : notas) suma += n;
        return suma / notas.length;
    }
}

class RepositorioEstudiante {
    public void guardar(Estudiante estudiante) {
        System.out.println("Guardando " + estudiante.getNombre() + " en estudiantes.txt...");
    }
}

class ImpresorBoletin {
    public void imprimir(Estudiante estudiante) {
        System.out.println("=== BOLETÍN ===");
        System.out.println("Nombre: " + estudiante.getNombre());
        System.out.println("Promedio: " + estudiante.calcularPromedio());
    }
}

class NotificadorCorreo {
    public void enviar(Estudiante estudiante) {
        System.out.println("Enviando boletín por correo al acudiente de " + estudiante.getNombre());
    }
}

public class Main {
    public static void main(String[] args) {
        Estudiante estudiante = new Estudiante("Ana Pérez", new double[]{4.5, 3.8, 4.2, 5.0});

        // Cada clase tiene una sola responsabilidad
        new RepositorioEstudiante().guardar(estudiante);
        new ImpresorBoletin().imprimir(estudiante);
        new NotificadorCorreo().enviar(estudiante);
    }
}
 ```


## Justificación Por qué es mejor.
En la correción del codigo cada clase tiene una sola responsabilidad, La clase estudiante se encarga de los datos del estudiante y calcular su promedio, la clase impresor boletin se encarga de presentarlo y la clase Modificar correo es la encargada de enviar la notificación
## Evidencia
<img width="489" height="162" alt="image" src="https://github.com/user-attachments/assets/7cbf44a8-039d-4dac-b156-20295da9d0df" />

--------------------------------------

# Ejercicio O — Open/Closed Principle
“Las entidades de software deben estar abiertas para extensión, pero cerradas para modificación.”

- La empresa quiere agregar el envío ***MISMO_DIA.*** ¿Qué tienen que modificar?
    - En el código original hay que modificar la clase CalculadoraEnvio (agregar otro else if).
- ¿Qué pasa con esta clase si en un año hay 15 tipos de envío?
    - El método calcular se vuelve muy largo, difícil de leer, propenso a errores y complicado de mantener. Cada nuevo tipo       obliga a tocar código ya existente.
  
## Problema identificado.
Cada vez que se necesita agregar un nuevo metodo de envio hay que modificar el metodo calcular que se encuentra en la clase de calcular envio (agregar otro ***else if***) ademas la clase no esta abierta para modificacion ni para extención.
## Código original.

```java
public class CalculadoraEnvio {

    public double calcular(String tipoEnvio, double peso) {
        if (tipoEnvio.equals("NORMAL")) {
            return peso * 2000;
        } else if (tipoEnvio.equals("EXPRESS")) {
            return peso * 5000 + 10000;
        } else if (tipoEnvio.equals("INTERNACIONAL")) {
            return peso * 15000 + 50000;
        }
        throw new IllegalArgumentException("Tipo de envío no soportado");
    }
}
```

## Código corregido Su solución.
``` Java
interface TipoEnvio {
    double calcular(double peso);
}

class EnvioNormal implements TipoEnvio {
    public double calcular(double peso) {
        return peso * 2000;
    }
}

class EnvioExpress implements TipoEnvio {
    public double calcular(double peso) {
        return peso * 5000 + 10000;
    }
}

class EnvioInternacional implements TipoEnvio {
    public double calcular(double peso) {
        return peso * 15000 + 50000;
    }
}

class EnvioMismoDia implements TipoEnvio {
    public double calcular(double peso) {
        return peso * 8000 + 20000;
    }
}

class CalculadoraEnvio {
    public double calcular(TipoEnvio tipo, double peso) {
        return tipo.calcular(peso);
    }
}

public class Main {
    public static void main(String[] args) {
        CalculadoraEnvio calc = new CalculadoraEnvio();

        System.out.println("Envio normal: " + calc.calcular(new EnvioNormal(), 2.5));
        System.out.println("Envio express: " +calc.calcular(new EnvioExpress(), 2.5));
        System.out.println("Envio internacional: " +calc.calcular(new EnvioInternacional(), 2.5));
        System.out.println("Envio el mismo dia: " +calc.calcular(new EnvioMismoDia(), 2.5));
    }
}
```
## Justificación Por qué es mejor.
Ahora, para agregar un nuevo tipo solo se crea una clase nueva. No hay que modificar CalculadoraEnvio ni las clases que ya existían.

## Evidencia
<img width="337" height="150" alt="image" src="https://github.com/user-attachments/assets/e93e0e8e-f328-40d1-b024-e6ce1f5cb4d1" />

--------------------------------------

# Ejercicio L — Liskov Substitution Principle
“Si S es un subtipo de T, los objetos de tipo T pueden ser reemplazados por objetos de
tipo S sin alterar el correcto funcionamiento del programa.”

- Ejecuten ***agregarFirma*** con una lista que contenga un Archivo y un ArchivoSoloLectura. ¿Qué pasa?
    - Se lanza una UnsupportedOperationException cuando el editor intenta escribir en el ArchivoSoloLectura. El programa se       rompe.
- Alguien propone agregar ***if (!(a instanceof ArchivoSoloLectura))*** dentro del ciclo. ¿Por qué eso es un parche y no una solución?
    - Porque está comprobando el tipo concreto en tiempo de ejecución. Eso indica que la jerarquía de herencia está mal diseñada. Cada vez que aparezca un nuevo tipo de archivo especial habría que agregar más if, lo cual es frágil y viola el espíritu de la orientación a objetos.
- ¿Un archivo de solo lectura **realmente** “es un” archivo que se puede escribir?
    - No. Por eso no debería heredar de Archivo si Archivo promete la operación de escritura.

## Problema identificado.
El ArchivoSoloLectura hereda de Archivo no cumple lo esperado del método escribir: lanza una excepción en lugar de escribir.
Según el principio de Liskov, cualquier subtipo de Archivo debería poder usarse donde se espera un Archivo sin alterar el comportamiento del programa.

## Código original.

```java
public class Archivo {
    protected String contenido = "";

    public String leer() { return contenido; }

    public void escribir(String texto) { contenido += texto; }
}

public class ArchivoSoloLectura extends Archivo {
    @Override
    public void escribir(String texto) {
        throw new UnsupportedOperationException("Este archivo es de solo lectura");
    }
}

public class Editor {
    public void agregarFirma(java.util.List<Archivo> archivos) {
        for (Archivo a : archivos) {
            a.escribir("\n-- Firmado por el sistema");
        }
    }
}
```

## Código corregido Su solución.

``` java
interface ArchivoLegible {
    String leer();
}

class Archivo implements ArchivoLegible {
    protected String contenido = "";

    public String leer() {
        return contenido;
    }

    public void escribir(String texto) {
        contenido += texto;
    }
}

class ArchivoSoloLectura implements ArchivoLegible {
    private String contenido;

    public ArchivoSoloLectura(String contenido) {
        this.contenido = contenido;
    }

    public String leer() {
        return contenido;
    }
}

class Editor {
    public void agregarFirma(java.util.List<Archivo> archivos) {
        for (Archivo a : archivos) {
            a.escribir("\n-- Firmado por el sistema");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Archivo archivoNormal = new Archivo();
        archivoNormal.escribir("Contenido original");

        ArchivoSoloLectura archivoSoloLectura = new ArchivoSoloLectura("Solo lectura");

        Editor editor = new Editor();
        java.util.List<Archivo> lista = new java.util.ArrayList<>();
        lista.add(archivoNormal);
        editor.agregarFirma(lista);

        System.out.println("Archivo normal: " + archivoNormal.leer());
        System.out.println("Archivo solo lectura: " + archivoSoloLectura.leer());
    }
}
```

## Justificación Por qué es mejor.
Se elimina la herencia incorrecta, el ArchivoSoloLectura ya no es un sibtipo de Archio, porque no puede complir lo que se espera de escritura. Ahora solo comparten la capacidad de lectura

## Evidencia
<img width="340" height="118" alt="image" src="https://github.com/user-attachments/assets/edae14c8-48f2-40ab-850a-4b1c5d994cd4" />

--------------------------------------

# Ejercicio I — Interface Segregation Principle
“Los clientes no deben ser forzados a depender de métodos que no usan.”

- Si alguien llama ***impresoraBasica.escanear(çontrato")***, ¿qué pasa? ¿Se entera de que no funcionó?
    - No pasa nada (el método está vacío). El programa no avisa de ningún error, el cliente cree que la operacion se realizó, pero en realidad no hizo nada.
- Si se agrega un método ***enviarPorCorreo*** a la interfaz, ¿cuántas clases hay que modi- ficar?
    - Se tienen que modificar todas las clases que implimenten *Dispositivo*

## Problema identificado.
La interfaz *Dispositivo* es demasiado robusta, obliga a todas las clases que la implementan a definir metodos que quizas no utilicen.

## Código original.

```java
public interface Dispositivo {
    void imprimir(String documento);
    void escanear(String documento);
    void enviarFax(String documento);
    void fotocopiar(String documento);
}

public class ImpresoraMultifuncional implements Dispositivo {
    public void imprimir(String d) { System.out.println("Imprimiendo " + d); }
    public void escanear(String d) { System.out.println("Escaneando " + d); }
    public void enviarFax(String d) { System.out.println("Enviando fax " + d); }
    public void fotocopiar(String d) { System.out.println("Fotocopiando " + d); }
}

public class ImpresoraBasica implements Dispositivo {
    public void imprimir(String d) { System.out.println("Imprimiendo " + d); }
    public void escanear(String d) { } // no puede
    public void enviarFax(String d) { } // no puede
    public void fotocopiar(String d) { } // no puede
}
```

## Código corregido Su solución.

```java
interface Imprimible {
    void imprimir(String documento);
}

interface Escaneable {
    void escanear(String documento);
}

interface Enviable {
    void enviarFax(String documento);
}

interface Fotocopiable {
    void fotocopiar(String documento);
}

// Impresora básica: solo imprime
class ImpresoraBasica implements Imprimible {
    public void imprimir(String d) {
        System.out.println("Imprimiendo " + d);
    }
}

// Impresora multifuncional: implementa varias interfaces
class ImpresoraMultifuncional implements Imprimible, Escaneable, Enviable, Fotocopiable {
    public void imprimir(String d) { System.out.println("Imprimiendo " + d); }
    public void escanear(String d) { System.out.println("Escaneando " + d); }
    public void enviarFax(String d) { System.out.println("Enviando fax " + d); }
    public void fotocopiar(String d) { System.out.println("Fotocopiando " + d); }
}

// Reto extra: un escáner que solo escanea
class Escaner implements Escaneable {
    public void escanear(String d) {
        System.out.println("Escaneando " + d);
    }
}

public class Main {
    public static void main(String[] args) {
        ImpresoraBasica basica = new ImpresoraBasica();
        basica.imprimir("Contrato");

        ImpresoraMultifuncional multi = new ImpresoraMultifuncional();
        multi.imprimir("Informe");
        multi.escanear("Informe");
        multi.enviarFax("Informe");
        multi.fotocopiar("Informe");

        Escaner escaner = new Escaner();
        escaner.escanear("Documento");
    }
}
```

## Justificación Por qué es mejor.
Se divide la interfaz grande en varias pequeñas y especificas, de esta manera cada clase solo implementa los metodos que realmente necesita.

## Evidencia
<img width="344" height="174" alt="image" src="https://github.com/user-attachments/assets/a2531f30-66de-416a-85e0-1445bfbb1a32" />

--------------------------------------

# Ejercicio D — Dependency Inversion Principle
“Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben
depender de abstracciones.”

- La empresa decide migrar a MongoDB. ¿Qué tienen que modificar en ServicioUsuarios?
- ¿Cómo harían una prueba de registrar sin conectarse a una base de datos real?
- ¿Quién decide qué base de datos se usa: ServicioUsuarios o alguien de afuera?

## Problema identificado.

## Código original.

```java
public class MySQLDatabase {
    public void guardar(String dato) {
        System.out.println("[MySQL] Guardando: " + dato);
    }
}

public class ServicioUsuarios {
    private MySQLDatabase db = new MySQLDatabase();

    public void registrar(String nombreUsuario) {
        if (nombreUsuario == null || nombreUsuario.isBlank()) {
            throw new IllegalArgumentException("Nombre inválido");
        }
        db.guardar(nombreUsuario);
    }
}
```

## Código corregido Su solución.

```java

```

## Justificación Por qué es mejor.

## Evidencia

