## Ejercicio: Sistema de Manejo de Documentos

### **Contexto:**

Una aplicación de gestión documental necesita permitir operaciones sobre diferentes tipos de documentos, como editar, imprimir y previsualizar. Se creó una interfaz `ManejadorDocumento` que incluye todos estos métodos. 
Sin embargo, algunos documentos solo se pueden imprimir y previsualizar, pero no editar (por ejemplo, un PDF protegido), lo que lleva a una violación del Principio de Segregación de Interfaces (ISP) al forzar la 
implementación de métodos no utilizados.

Este ejercicio os ayudará a comprender mejor y aplicar el Principio de Segregación de Interfaces (ISP) y permitirá ver cómo un diseño más modular y enfocado puede llevar a un código más limpio, mantenible y extensible.

### **Código original que no cumple el ISP:**

```kotlin
interface ManejadorDocumento {
    fun editar(documento: String)
    fun imprimir(documento: String)
    fun previsualizar(documento: String)
}

class DocumentoPDF : ManejadorDocumento {
    override fun editar(documento: String) {
        throw UnsupportedOperationException("Los PDF protegidos no pueden ser editados.")
    }

    override fun imprimir(documento: String) {
        println("Imprimiendo PDF: $documento")
    }

    override fun previsualizar(documento: String) {
        println("Previsualizando PDF: $documento")
    }
}

class DocumentoTexto : ManejadorDocumento {
    override fun editar(documento: String) {
        println("Editando texto: $documento")
    }

    override fun imprimir(documento: String) {
        println("Imprimiendo texto: $documento")
    }

    override fun previsualizar(documento: String) {
        println("Previsualizando texto: $documento")
    }
}
```

### **Tarea:**

Su tarea es refactorizar este sistema para cumplir con el Principio de Segregación de Interfaces (ISP). Esto significa que deberán descomponer la interfaz `ManejadorDocumento` en interfaces más pequeñas y específicas que representen 
funcionalidades individuales. Esto permitirá que las implementaciones concretas solo necesiten implementar las interfaces que correspondan a las operaciones que realmente soportan.

Demostrar la flexibilidad y seguridad que me proporciona la nueva solución al problema... en el método `main` demuestra cómo las clases ajustadas pueden ser utilizadas para manejar documentos, respetando el ISP, es decir, crea dos 
instancias una de un documento PDF y otra de Texto y utiliza su funcionalidad (comentando el método que no es posible utilizar en una de las instancias gracias a nuestro nuevo diseño).

Contesta a la siguiente pregunta: ¿la refactorización que has realizado para resolver la violación del Principio de Segregación de Interfaces (ISP) también contribuye a la aplicación de otros principios SOLID en el diseño del sistema? 
Identifícalos y explica cómo contribuye a su aplicación.

### **Solución refactorizada:**

Los pasos a realizar serán los siguientes:

   1. **Segregar la Interfaz `ManejadorDocumento`**: Divida la interfaz en `Editable`, `Imprimible`, `Previsualizable`, etc., según las operaciones disponibles.
   
   2. **Implementar las Nuevas Interfaces**: Ajuste las clases `DocumentoPDF` y `DocumentoTexto` para que implementen solo aquellas interfaces que correspondan a las funcionalidades que efectivamente pueden realizar.

   3. **Demostrar la Flexibilidad**: Incluya un ejemplo en el método `main` que demuestre cómo las clases ajustadas pueden ser utilizadas para manejar documentos, respetando el ISP.

#### Paso 1: Segregar la Interfaz `ManejadorDocumento`

Dividimos la interfaz grande en interfaces más pequeñas y específicas.

```kotlin
interface Editable {
    fun editar(documento: String)
}

interface Imprimible {
    fun imprimir(documento: String)
}

interface Previsualizable {
    fun previsualizar(documento: String)
}
```

#### Paso 2: Implementar las Nuevas Interfaces en las Clases Concretas

Adaptamos las clases `DocumentoPDF` y `DocumentoTexto` para que implementen las interfaces adecuadas.

```kotlin
class DocumentoPDF : Imprimible, Previsualizable {
    override fun imprimir(documento: String) {
        println("Imprimiendo PDF: $documento")
    }

    override fun previsualizar(documento: String) {
        println("Previsualizando PDF: $documento")
    }
}

class DocumentoTexto : Editable, Imprimible, Previsualizable {
    override fun editar(documento: String) {
        println("Editando texto: $documento")
    }

    override fun imprimir(documento: String) {
        println("Imprimiendo texto: $documento")
    }

    override fun previsualizar(documento: String) {
        println("Previsualizando texto: $documento")
    }
}
```

#### Paso 3: Demostración en el Método `main`

Proporcionamos un ejemplo de cómo utilizar las clases ajustadas para demostrar la flexibilidad del nuevo diseño y el cumplimiento del ISP.

```kotlin
fun main() {
    val pdf = DocumentoPDF()
    val texto = DocumentoTexto()

    println("Manejando Documento PDF:")
    pdf.imprimir("archivo.pdf")
    pdf.previsualizar("archivo.pdf")
    // pdf.editar("archivo.pdf") // Esto ahora es inválido y no compilará, reflejando el cumplimiento del ISP.

    println("\nManejando Documento de Texto:")
    texto.editar("documento.txt")
    texto.imprimir("documento.txt")
    texto.previsualizar("documento.txt")
}
```

Este enfoque garantiza que cada tipo de documento solo necesita conocer y implementar las operaciones que son relevantes para su tipo, eliminando la necesidad de implementaciones vacías o lanzamiento de excepciones para métodos no soportados. 
Al hacerlo, no solo cumplimos con el Principio de Segregación de Interfaces (ISP), sino que también facilitamos la extensibilidad del sistema y mejoramos su mantenibilidad al permitir añadir nuevas operaciones o tipos de documentos sin afectar 
a los existentes.

#### Respuesta a la pregunta propuesta:

La refactorización propuesta para resolver la violación del ISP también contribuye a la aplicación de otros principios SOLID:

   1. Principio de Responsabilidad Única (SRP) -> Al segregar la interfaz `ManejadorDocumento` en interfaces más específicas (`Editable`, `Imprimible`, `Previsualizable`), estamos asegurando que cada interfaz tenga una única responsabilidad. 
   Cada una se centra en una operación específica sobre los documentos, lo que simplifica la implementación de las clases que las utilizan y hace que el sistema sea más mantenible.

   2. Principio de Abierto/Cerrado (OCP) -> La segregación de interfaces mejora la extensibilidad del sistema. Con interfaces más pequeñas y específicas, podemos extender el sistema añadiendo nuevas implementaciones para diferentes tipos 
   de documentos o nuevas operaciones sin necesidad de modificar las implementaciones existentes. Esto es posible porque las clases pueden implementar nuevas interfaces o extenderse sin alterar el 
   comportamiento de las operaciones ya soportadas.

   3. Principio de Sustitución de Liskov (LSP) -> Aunque el enfoque principal de la refactorización fue el ISP, el cumplimiento de este principio también asegura que el LSP se mantenga. Al utilizar interfaces específicas para cada tipo de operación, garantizamos que cualquier clase que implemente 
   una de estas interfaces pueda ser sustituida por cualquier otra clase que implemente la misma interfaz, sin afectar la corrección del programa. Esto es porque cada interfaz define claramente un contrato que todas sus implementaciones deben seguir.

   4. Principio de Inversión de Dependencias (DIP) -> Aunque el ejercicio se centró más en la segregación de interfaces, el principio de diseño general que subyace a la segregación de interfaces favorece el uso de abstracciones (interfaces) sobre implementaciones concretas. 
   Esto está alineado con el DIP, que aboga por depender de abstracciones y no de concreciones. Al utilizar interfaces específicas para definir operaciones, las clases dependen menos unas de otras y más de abstracciones, 
   lo que puede contribuir a un acoplamiento más débil.

#### Conclusión:

La refactorización para cumplir con el ISP, por tanto, no solo resuelve el problema inicial de requerir que las implementaciones concretas implementen métodos que no necesitan, sino que también refuerza otros principios SOLID, contribuyendo a un diseño 
de software más limpio, mantenible y extensible.
