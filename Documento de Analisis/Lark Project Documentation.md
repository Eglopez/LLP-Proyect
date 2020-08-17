PAC II 2020 - LL 1100
@author Daniel Arteaga, Luis Martinez, Brando Fernandez
@date 2020/08/16

Documentación Proyecto - Lenguajes de Programación:
===================================
* Acontinuación se muestra la documentación del proyecto de Lenguajes de Programación usando Lark.

Forma de Trabajo
======
* Se decide dividir el proyecto, separándolo en dos partes:
    
    * **Interpretación y Demostración** lo cual se refiere a lo relacionado con el Lenguaje Javascript.
    * **Reconocimiento** que se refiere a lo relacionado con los Lenguajes Ruby y Bash.

    Asignando cada parte a un equipo de trabajo de dos personas. Cada equipo decide de que manera trabajar su parte del proyecto, asi como las tareas específicas que debe realizar cada integrante.

Ruby y Bash:
============
* Se lee la definición del proyecto para poder entender lo que se pide hacer con respecto al apartado de reconocimiento del lenguaje Ruby y Bash.

* Se procede a estudiar la gramática y sintaxis de los lenguajes Ruby y Bash, enfocandose más en las instrucciones que debe comprender el apartado de Reconocimiento, mencionadas en la definición del proyecto.

* <ins>Hallazgos encontrados:</ins>
    
    * **PLANIFICADO:**

        * Dividir el trabajo de realizar la gramática de Ruby y Bash, asignando una única gramática por integrante.

    * **NO PLANIFICADO:**

        * Al encontrarse con un conflicto con la gramática de comentarios de múltiples líneas en Ruby se comienza a investigar la forma en la que trabaja Python Lark para poder solucionar el conflicto.

        * Al tener la gramática del lenguaje Ruby terminada, se encuentra un error al poder llamar a funciones no definidas o código perteneciente a otro Lenguaje, e.g.: `console.log("Hello World")` , por lo que se empieza a buscar la solución a éste error.

Javascript:
====================================================
Se lee la definición del proyecto para inmediantamente tener ideas claras de lo que se tiene que hacer y lo que no se debe de hacer.

Se procede a estudiar la gramatica, sintaxis de el Lenguaje de Programación de Javascript y todo sus niveles de error para luego sea aplicado a Python Lark.

## Lluvia de Ideas:

¿Que debemos hacer para empezar con esta asignacion?
- Definir una gramatica que corresponda a la sintaxis de javascript para cada bloque de funcion junto con todos aquellas expresiones que puedan ir dentro o fuera de ellas
- Definir gramatica de asignaciones mediante palabras reservadas
- Definir gramatica de estructura de funciones de condición "if"
- Separar gramaticas de expresiones de las que se pueden ejecutar fuera de funciones mediante alias de aquellas que no deben ejecutarse al estar dentro de una funcion sin que esta sea llamada.
- Guardar las funciones y sus expresiones sin ejecutarlas antes del llamado de la función
- Guardar las variables instanciadas fuera y dentro de una función
- Generar nombres diferentes para la identificación de variables que esten dentro y fuera de una función
- Generar nombres diferentes para la identificacion de parametros de una funcion
- Identificar cuando se desea realizar una operacion recursiva en una función

![Estructura de una función en Javascript](https://www.code-morning.com/wp-content/uploads/2016/09/Function1-768x394.png)
> Imagen 1 estructura de una función en Javascript
> Obtenido de www.code-morning.com/understand-functions-in-javascript/

![image](https://drive.google.com/uc?id=1Uov1azXhZXwkJr7EGGHZQSZMBnND_lvM)
    
### Gramaticas para expresiones comunes fuera de una función

Esta proción de gramatica es utilizada para expresiones que se encuentran fuera de una función, de modo que si se detecta la gramatica esta pueda ser ejecutada por la clase semantica mediante el alias.

```
?exp: varkeyword identifier opequals string eos -> assignvar
    | varkeyword identifier opequals string opsum identifier eos -> assignvaralt
    | varkeyword identifier opequals arithmeticoperation eos -> assignvar
    | varkeyword identifier opequals bool eos -> assignvar 

    | consolelog leftpar string rightpar eos -> print_
    | consolelog leftpar string "," identifier leftpar (identifier | int | float) rightpar rightpar eos -> printwithfunc
    | consolelog leftpar string opsum identifier rightpar eos -> print_alt

    | consolelog leftpar identifier rightpar eos -> printvar
    | consolelog leftpar identifier "+" identifier rightpar eos -> printvar_alt

    | consolelog leftpar arithmeticoperation rightpar eos -> printnum
    | consolelog leftpar arithmeticoperation "+" arithmeticoperation rightpar eos -> printnum_alt

    | consoleerror leftpar string rightpar eos -> print_error

    | identifier leftpar (int | float | identifier) "," (int | float | identifier) rightpar eos -> funcexists
    | identifier leftpar (identifier | int | float) rightpar eos -> funcexists
    | length
    | cond
    | while
    | for
```

## Cosas de las que nos hemos dado cuenta

**PLANIFICADO:** 

- Dividir en partes la parte de Javascript donde se trabajara la gramatica y semantica aparte y luego se trabaja la tabla de simbolos.

- Usar la tecnicas aprendidas en clase para poder realizar la gramatica y semantica correcta.

- Hacer uso de la documentación disponible de Python Lark para facilitar el uso del mismo y poder reducir la semantica y gramatica.

- Verificar la gramatica de Javascript a la hora de implementarla para evitar ambiguedades, errores y confusiones.

- Investigar como funciona el Lenguaje de Javascript, si su ejecución es en tiempo real, como funcionan los conficionales, etc.

- Empezar a programar la funcionalidad de todos los componentes que tiene Javascript empezando por impresión de string,cadenas y demas, así mismo como los condicionales y funciones.

**NO PLANIFICADO:**

Al tener lo basico de la gramatica y semantica se pudieron visualizar alguno escenarios en donde fallaba por ejemplo:
            
- if's aninados, varios condicionales dentro de una sola función, ¿que pasa dentro de un condicional hay otro?

- Definición de variables dentro de una función.

- Impresión y ejecución de todo lo que hay dentro de una función y condicional.

- Al tener claro que Lark trabaja con àrboles se procede a buscar la manera en que recursivamente se pueda buscar dentro de los àrboles todo aquello que sea un condicional y se ejecute.

- Funciones recursivas

- Llamado de funciones con valores temporales.

**Otros casos**

1. Cuando se parsea el archivo especifico con Lark, este envia la gramatica a un alias (si se especificó) donde se puede ejecutar. El problema es que detecta gramaticas dentro de funciones y estas se ejecutan sin antes haberse llamado la función. 

2. v_args(inline=true) no resulta util para este proyecto, a veces las funciones especificadas en la semantica reciben más parametros de los que debería, esto ocurre porque depende de la cantidad de expresiones que contenga el arbol de gramatica. 

3. v_args(tree=true) era una alternativa que retornaba un arbol que podia contener n expresiones. Sin embargo, este metodo no se aplicó porque encontramos que si utilizamos ningún v_args() nosotros podiamos definir lo que deseabamos retornar en la semantica si se detectaba una expresión. Por ejemplo, si se detectaba una asignación var = number, nosotros podiamos retornar una lista con los tokens sin necesidad de trabajar con arboles. 

4. La solución del inciso (3) resultó tener una limitante, y es que siempre se devuelven arboles cuando se contienen expresiones dentro de otras expresiones (como estructuras de flujo y condicionales). Decidimos que podiamos trabajar con estos arboles siempre y cuando manteniamos el retorno de una lista con tokens.
5. A veces nuestra lista contenía tokens, arboles y listas. Se iteró en la lista de modo que se verificaba el tipo de objeto para ver si era árbol, lista o Token. Si este árbol contenía una gramatica para estructuras de flujo y condiciones este se enviaba al metodo que la ejecutaría.

## ¿Como podemos guardar las funciones?

Al principio se pensó utilizar clases para guardar funciones, pero esta idea fue descartada porque necesitabamos manetener todas las funciones e identificadores en un solo lugar. De modo que si necesitabamos verificar si una variable existía solo necesitabamos buscar su nombre en una lista. 

Lo que utilizamos fueron diccionaros JSON cuya clave o keys eran los identificadores o nombres de  funciones, variables, etc, y sus values eran las expresiones o asignaciones. 

Rapidamente decidimos utilizar tres diccionarios diferentes:
1. Diccionario para funciones
2. Diccionario para variables
3. Diccionario para parametros

De este modo podiamos buscar en un lugar especifico lo que quisieramos sin necesidad de iterar en otros objetos que no correspondían con la busqueda.

## Problemas con el nombre de variables

Se detectó que si una variable dentro de una función contenía el mismo nombre de una variable fuera de la función, esta se sobreescribiría con el valor de la que está fuera de la función.

Decidimos utilizar una estructura de modo que podriamos nombrar las variables diferenciando en qué función se encontraba mediante:

`
<nombre_funcion>_<nombre_variable>
`

Lo mismo sucedía con los parametros, estos se nombraron con la siguiente estructura:

`
<nombre_funcion>_param_<posición_de_parametro>_<nombre_del_parametro>
`
## Problemas con whiles, y fors

**PREFACIO:**

* Para poder identificar cuando hay mas de un condicional dentro de una función o aninados se tuvo que encontrar la forma de recorrer el árbol que proveé Lark, una vez se encontro la forma mas eficiente de hacerlo se reconoce y se ejecuta lo que hay dentro de cada condicional. Al mismo tiempo se busco todo escenario posible en cuanto a las operaciones que se pueden hacer dentro de estos dos condicionales (<,>,==,>=,<=)

**WHILE'S:**

* Al ser Javascript y Python lenguajes de programación que se parecen es ciertas cosas hacer el uso de while's en la semantica no presento problema. Los problemas encontrados en el condicional de While's fueron poder incrementar el valor de la variable dependiendo de cuantos recorridos sean, tener en cuenta en los escenarios donde puedan haber bucles infinitos etc.

**FOR'S**:

* En Javascript se trabaja el for de una manera muy distinta y no es la misma que Python. Investigando se llego a la manera para poder usar el condicional for de la misma manera que en Javascript. Una vez terminado lo anterior se procedio a encontrar problemas los cuales fueron los mismo que el condicional while, incrementar la variable dependiendo de su recorrido. 