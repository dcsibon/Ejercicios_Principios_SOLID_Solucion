## Ejercicio 08: Sistema de Logros en un Juego

### Contexto y Código que Incumple el DIP

Imagina que estás desarrollando un sistema de logros para un videojuego. Inicialmente, los logros se registran directamente en una base de datos y se notifican al jugador a través de la interfaz del juego. 
Sin embargo, esta implementación tiene un acoplamiento fuerte entre la lógica del juego y el sistema de base de datos específico, así como con el sistema de notificaciones en la interfaz del usuario.

```kotlin
class SistemaBaseDatos {
    fun guardarLogro(logro: String, jugadorId: String) {
        println("Guardando logro '$logro' para el jugador $jugadorId en la base de datos.")
    }
}

class SistemaNotificacionesUI {
    fun notificarUsuario(logro: String, jugadorId: String) {
        println("Notificando al jugador $jugadorId sobre el nuevo logro: $logro.")
    }
}

class SistemaLogros {
    private val db = SistemaBaseDatos()
    private val ui = SistemaNotificacionesUI()

    fun otorgarLogro(logro: String, jugadorId: String) {
        db.guardarLogro(logro, jugadorId)
        ui.notificarUsuario(logro, jugadorId)
    }
}
```

### Tarea de Refactorización

- Refactoriza el sistema de logros para cumplir con el Principio de Inversión de Dependencias. Desacopla la lógica del juego de las implementaciones concretas del sistema de base de datos y del sistema de notificaciones. 
- Realiza una demostración de su uso en el programa principal, por ejemplo para el logro "Primer Enemigo Derrotado" y el id de jugador "Jugador123".
- Realiza dos versiones, una con interfaces y otra con clases abstractas, junto con la inyección de dependencias para lograr una arquitectura más flexible y desacoplada.

### Solución refactorizada con Interfaces

Para cumplir con el Principio de Inversión de Dependencias (DIP), introducimos interfaces para las dependencias y aplicamos inyección de dependencias para desacoplar el `SistemaLogros` de las implementaciones concretas del sistema de base de datos y del sistema de notificaciones.

#### Paso 1: Definir Interfaces

```kotlin
interface AlmacenamientoLogros {
    fun guardarLogro(logro: String, jugadorId: String)
}

interface NotificadorUsuario {
    fun notificarUsuario(logro: String, jugadorId: String)
}
```

#### Paso 2: Implementar Interfaces

```kotlin
class SistemaBaseDatos : AlmacenamientoLogros {
    override fun guardarLogro(logro: String, jugadorId: String) {
        println("Guardando logro '$logro' para el jugador $jugadorId en la base de datos.")
    }
}

class SistemaNotificacionesUI : NotificadorUsuario {
    override fun notificarUsuario(logro: String, jugadorId: String) {
        println("Notificando al jugador $jugadorId sobre el nuevo logro: $logro.")
    }
}
```

#### Paso 3: Refactorizar `SistemaLogros` para Utilizar Inyección de Dependencias

```kotlin
class SistemaLogros(private val almacenamiento: AlmacenamientoLogros, private val notificador: NotificadorUsuario) {
    fun otorgarLogro(logro: String, jugadorId: String) {
        almacenamiento.guardarLogro(logro, jugadorId)
        notificador.notificarUsuario(logro, jugadorId)
    }
}
```

#### Demostración en el `main`

```kotlin
fun main() {
    val sistemaLogros = SistemaLogros(SistemaBaseDatos(), SistemaNotificacionesUI())
    sistemaLogros.otorgarLogro("Primer Enemigo Derrotado", "Jugador123")
}
```

Este diseño cumple con el DIP al depender de abstracciones (`AlmacenamientoLogros` y `NotificadorUsuario`) en lugar de implementaciones concretas. La inyección de dependencias permite flexibilidad para cambiar las implementaciones de almacenamiento y notificación sin alterar `SistemaLogros`.

### Solución refactorizada utilizando Clases Abstractas

#### Paso 1: Definir Clases Abstractas

Vamos a definir clases abstractas para el almacenamiento de logros y la notificación de usuarios, ofreciendo así un punto de extensión para diferentes implementaciones.

```kotlin
abstract class AlmacenamientoLogros {
    abstract fun guardarLogro(logro: String, jugadorId: String)
}

abstract class NotificadorUsuario {
    abstract fun notificarUsuario(logro: String, jugadorId: String)
}
```

#### Paso 2: Implementar Clases Concretas

Las implementaciones concretas de estas clases abstractas proporcionarán la funcionalidad específica para guardar logros y notificar a los usuarios.

```kotlin
class SistemaBaseDatos : AlmacenamientoLogros() {
    override fun guardarLogro(logro: String, jugadorId: String) {
        println("Guardando logro '$logro' para el jugador $jugadorId en la base de datos.")
    }
}

class SistemaNotificacionesUI : NotificadorUsuario() {
    override fun notificarUsuario(logro: String, jugadorId: String) {
        println("Notificando al jugador $jugadorId sobre el nuevo logro: $logro.")
    }
}
```

#### Paso 3: Refactorizar `SistemaLogros` para Utilizar Clases Abstractas

En lugar de depender directamente de las implementaciones concretas, `SistemaLogros` ahora utilizará las clases abstractas. Esto se puede lograr a través de la inyección de dependencias en el constructor de `SistemaLogros`.

```kotlin
class SistemaLogros(private val almacenamiento: AlmacenamientoLogros, private val notificador: NotificadorUsuario) {
    fun otorgarLogro(logro: String, jugadorId: String) {
        almacenamiento.guardarLogro(logro, jugadorId)
        notificador.notificarUsuario(logro, jugadorId)
    }
}
```

#### Demostración en el `main`

```kotlin
fun main() {
    val sistemaLogros = SistemaLogros(SistemaBaseDatos(), SistemaNotificacionesUI())
    sistemaLogros.otorgarLogro("Primer Enemigo Derrotado", "Jugador123")
}
```

Esta refactorización muestra cómo las clases abstractas pueden ser utilizadas efectivamente para desacoplar componentes del sistema y cumplir con el DIP, permitiendo un diseño más flexible y extensible.
