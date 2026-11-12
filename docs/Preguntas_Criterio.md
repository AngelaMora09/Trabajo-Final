# Criterios de Decisión: Estructuras de Datos en la Red de Sensores

Este documento es la parte más importante de tu entrega. No basta con que el código funcione; debes justificar **por qué** elegiste una colección de Java sobre otra.

Responde a las siguientes preguntas basándote en la teoría de nodos y el rendimiento del JCF.

### 1. El dilema del Historial
Para el `historialEventos`, podrías usar un `ArrayList` o un `LinkedList`. 
- Si la plataforma registra miles de eventos por segundo y necesitas eliminar los más antiguos frecuentemente para ahorrar memoria, ¿cuál de las dos colecciones es más eficiente y por qué? (Pista: piensa en el costo de eliminar el primer elemento).


  Respuesta: 
  LinkedList.
  Porque un ArrayList es como una fila de casillas pegadas. Si quitas la primera, todos los demás deben moverse un lugar para tapar el hueco, y eso es lento si lo haces miles de veces. En un LinkedList cada elemento apunta al siguiente, así que quitar el primero solo cambia a quién apunta la cabeza. No mueve nada.
  Sin embargo, LinkedList gasta más memoria y es lenta para buscar por posición, pero en un historial casi no se busca por posición.
### 2. Navegación Bidireccional
En el `navegadorAlertas`, utilizamos un `Deque` (Double Ended Queue). 
- ¿Qué ventaja ofrece un `Deque` sobre una `Queue` simple para implementar la funcionalidad de "Atrás" y "Adelante"? 

Respuesta: Deque, porque permite entrar y salir por los dos extremos, porque para “adelante” sacas del frente y lo pones al final. Para “atrás” haces lo contrario: sacas del final y lo pones al frente. Una Queue solo deja trabajar por un lado, así que “atrás” no se podría.
- ¿Cómo se relaciona esto con el concepto de un `NodoDoble`?

Respuesta: un NodoDoble guarda quién va antes y quién va después, así que se puede caminar en los dos sentidos. El Deque hace lo mismo.

### 3. El Ciclo de Monitoreo
Para la `colaMonitoreo`, implementaste una rotación (sacar del frente y meter al final).
- ¿Por qué una `Queue` es la estructura natural para este problema? 

Respuesta: Deque, porque na Queue funciona como la fila del banco: el primero que llega es el primero que se atiende (FIFO). Cada sensor es consultado y pasa al final, así que todos tienen su turno.
- ¿Qué pasaría con la equidad del sistema si usaras una `Stack` (Pila) en su lugar?

Respuesta:funciona como una pila de platos, y el último que pones es el primero que sacas (LIFO). Si sacas un sensor y lo vuelves a poner, queda arriba otra vez, y siempre se consulta el mismo. Los demás nunca (inanición).
### 4. Topología de Red (El Grafo)
Representaste la red de sensores como un `Map<String, List<String>>`.
- ¿Por qué usamos un `Map` y no un arreglo bidimensional (matriz de adyacencia) considerando que la mayoría de los sensores solo están conectados a 2 o 3 vecinos?

Respuesta:Map, porque una matriz de 1.000 sensores tiene 1.000.000 de casillas, pero cada sensor solo está conectado a 2 o 3, así que casi todas quedan vacías. El Map solo guarda lo que existe: cada sensor con su lista de vecinos.
- ¿Cómo se traduce esta estructura de `Map` a la idea de un `NodoGrafo` con una lista de adyacencias?

Respuesta: un NodoGrafo es un nodo con su lista de vecinos. En el Map, la clave es el sensor y el valor es esa lista.
### 5. Buffer de Ingesta y Rendimiento
El `bufferIngesta` procesa datos en orden FIFO.
- Si el volumen de datos aumenta drásticamente y necesitas procesar las lecturas en paralelo usando múltiples hilos, ¿seguiría siendo adecuado un `ArrayDeque` o deberías cambiar a una `ConcurrentLinkedQueue`? Justifica tu respuesta basándote en la seguridad de hilos (Thread-safety).


Respuesta:ConcurrentLinkedQueue. Porque si varios hilos usan el ArrayDeque a la vez, se pueden pisar entre ellos y se pierden lecturas, o se entrega la misma dos veces. Eso pasa porque no es thread-safe.
ConcurrentLinkedQueue está hecha para varios hilos, mantiene el orden FIFO y no los bloquea.