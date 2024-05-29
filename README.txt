Trabajo final de programacion

Integrantes:

Sebastian Barco 
Natalia Gallego
Johan Camilo Zuluaga


Marco teórico:

Para el presente proyecto se ha elegido el fenómeno natural del cambio de estaciones y como afecta a las plantas. 
Durante el período que va desde el otoño hasta el invierno, 
las hojas pasan por varios cambios químicos y fisiológicos que las preparan para la caída y la dormancia. 
Aquí hay una descripción más detallada del proceso durante este período:

Reducción de la fotosíntesis: Con la disminución de la luz solar y las temperaturas más frías, 
las hojas reducen gradualmente su capacidad para realizar la fotosíntesis. 
La clorofila, el pigmento verde responsable de la captura de la luz solar, comienza a descomponerse, 
lo que da lugar a la revelación de otros pigmentos como los carotenoides y las antocianinas.

Cierre de los vasos conductores: La planta comienza a restringir el flujo de agua y nutrientes hacia las hojas. 
Esto se logra mediante la formación de barreras en los vasos conductores que conectan las hojas con el resto de la planta.
Esta reducción en el suministro de agua contribuye a la sequedad y eventual caída de las hojas.

Producción de una capa de abscisión: La planta forma una capa de células especializadas llamada capa de abscisión en la base de cada hoja. 
Estas células están llenas de enzimas que debilitan la unión entre la hoja y el tallo.

Secado de la hoja: Con el cierre de los vasos conductores y la reducción de la fotosíntesis, 
las hojas comienzan a secarse gradualmente. Esto se debe a la pérdida de agua y la descomposición de los tejidos.

Caída de la hoja: Una vez que la capa de abscisión está completamente formada y las conexiones entre la hoja y el tallo se debilitan lo suficiente, 
la hoja se cae. Este proceso puede ocurrir gradualmente a lo largo del otoño e incluso durante el invierno, 
dependiendo de las condiciones climáticas y del tipo de árbol.

Durante el invierno, las hojas caídas se descomponen gradualmente en el suelo, 
liberando nutrientes que pueden ser reciclados por el ecosistema. Mientras tanto, 
el árbol entra en un estado de dormancia para conservar energía y sobrevivir a las condiciones adversas del invierno, 
preparándose para volver a crecer en la primavera. 

Explicación del desarrollo:

Para empezar con las modificaciones al modelo original, lo primero que hicimos fue crear una nueva variable global, llamada "season",
la cual se encargaría de diferenciar en que punto del modelo nos encontramos, ya sea en la estacion de otoño o de invierno, como corresponde,
en el setup inicial del modelo agregamos una linea de codigo para definir la estacion en otoño, 
además, agregamos dos lineas de codigo extra que se encargan de darle un valor inicial tanto a la temperatura como a la intensidad del sol, 
ya que en estas nos basaremos para hacer el cambio a invierno. Los siguientes cambios realizados fueron directamente en la funcion "go",
en el modelo original, este paraba la simulación en cuanto todas las hojas caian del arbol, modificamos esta sentencia de modo que si, 
la estacion es otoño y ya no hay hojas en el arbol, en lugar de parar la simulacion, haga un llamado a una nueva funcion que creamos llamada "change-to-winter",
esta funcion se encarga, como su nombre lo dice, de hacer la transicion entre las estaciones, 
empezando con una declaracion en la variable global "season" cambiandola a invierno, seguido de colocar la temperatura en 0°C y cambiar el color del suelo a blanco, para simular la acumulacion de nieve. 

A continuacion, hicimos la modificacion en la funcion "go", de modo que si no hay hojas en el arbol se haga el llamado a la funcion "make-snow-fall", 
esta funcion es similar a la funcion original del modelo que se encargaba de hacer caer lluvia, solo que cambiamos el color de las gotas a blanco, para que este simule la nieve, pero mantenga las propiedades de caida que tenia la lluvia. 
Al mismo tiempo, mientras esto empieza a suceder, agregamos una sentencia en la funcion "go", lo que hace esta sentencia es, si la estacion actual es invierno, 
entonces irá reduciendo gradualmente la intensidad del sol en la simulacion, 
hasta llegar a una intensidad de 9%, la cual, es tambien la condicion para parar la simulacion. 


La documentación y el código fuente está en el archivo código.
