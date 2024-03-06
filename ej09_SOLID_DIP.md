## Ejercicio 09: Diseño de un Sistema de Inventario para un Juego RPG ("Role-Playing Game" Juego de Rol) Aplicando el DIP

### Contexto y Requisitos

Estás diseñando un sistema de inventario para un juego de rol donde los jugadores pueden recoger, usar o intercambiar diversos objetos dentro del juego. Los objetos en el inventario pueden tener interacciones complejas: algunos objetos pueden ser combinados para crear nuevos objetos, otros son consumibles y algunos tienen efectos permanentes en el jugador.

Para cumplir con el DIP, el sistema de inventario debe ser capaz de manejar la diversidad de objetos sin depender directamente de implementaciones concretas para cada tipo de objeto.

### Requisitos Específicos

1. **Interfaz `ObjetoInventario`**: Crea una interfaz que defina acciones comunes como `usar()`, `combinarCon(ObjetoInventario): ObjetoInventario`, y `describir()`.
   
2. **Implementaciones de `ObjetoInventario`**: Desarrolla varias clases que representen diferentes tipos de objetos (por ejemplo, `Consumible`, `Equipable`, `Material`), cada una implementando la interfaz `ObjetoInventario` y sus métodos de manera adecuada.
   
3. **Sistema de Inventario**: Implementa un sistema de inventario que utilice `ObjetoInventario` para manipular objetos. Este sistema no debe tener un acoplamiento fuerte con las clases concretas de los objetos, sino operar a través de la interfaz `ObjetoInventario`.
   
4. **Gestión de Dependencias**: Asegúrate de que el diseño permita la fácil extensión del sistema de inventario para admitir nuevos tipos de objetos en el futuro, sin necesidad de modificar el sistema de inventario existente.

### Tarea

Desarrolla el sistema de inventario siguiendo los principios de diseño SOLID, en particular el Principio de Inversión de Dependencias. Tu implementación debe demostrar cómo se pueden agregar nuevos tipos de objetos al inventario con mínimas modificaciones en el código existente, enfatizando la flexibilidad y la extensibilidad del diseño.

### Solución refactorizada

#### Paso 1: Interfaz `ObjetoInventario`

```kotlin
interface ObjetoInventario {
    fun usar()
    fun combinarCon(otro: ObjetoInventario): ObjetoInventario
    fun describir(): String
}
```

#### Paso 2: Implementaciones Concretas

```kotlin
class Consumible : ObjetoInventario {
    override fun usar() {
        println("Usando consumible...")
    }
    override fun combinarCon(otro: ObjetoInventario): ObjetoInventario {
        println("Combinando consumibles...")
        return Consumible() // Simplificación para el ejemplo
    }
    override fun describir(): String = "Un objeto consumible"
}

class Equipable : ObjetoInventario {
    override fun usar() {
        println("Equipando...")
    }
    override fun combinarCon(otro: ObjetoInventario): ObjetoInventario {
        println("No se puede combinar equipables.")
        return this
    }
    override fun describir(): String = "Un objeto equipable"
}
```

#### Paso 3: Sistema de Inventario

```kotlin
class SistemaInventario {
    private val objetos: MutableList<ObjetoInventario> = mutableListOf()

    fun agregarObjeto(objeto: ObjetoInventario) {
        objetos.add(objeto)
    }

    fun describirObjetos() {
        objetos.forEach { println(it.describir()) }
    }
}
```

#### Demostración en el `main`

```kotlin
fun main() {
    val sistemaInventario = SistemaInventario()
    sistemaInventario.agregarObjeto(Consumible())
    sistemaInventario.agregarObjeto(Equipable())

    sistemaInventario.describirObjetos()
}
```

Este diseño separa las responsabilidades de manejar diferentes tipos de objetos de inventario a través de la interfaz `ObjetoInventario`, lo que permite que el `SistemaInventario` funcione independientemente de las implementaciones específicas de los objetos. 
La adición de nuevos tipos de objetos al inventario se puede realizar implementando la interfaz `ObjetoInventario`, cumpliendo así con el Principio de Inversión de Dependencias al depender de abstracciones en lugar de concreciones.
