<h1>Bootcamp_PBI</h1>

- Estado del proyecto: En Construcción

- Paso 1 .- Para realizar este challenge, la tienda de mascotas Gatito ha puesto a tu disposición dos bases de datos que contienen las informaciones de stock.

- Paso 2.- El primer paso para desarrollar este desafío es importar las bases de datos en Power BI. Trabajarás con dos archivos principales: el archivo Productos, que está en formato .csv, y el archivo Datos Stock, que está en formato .xlsx.

Paso.-3 Para todo proyecto de datos, una de las partes más importantes es la que consiste en  la limpieza de los datos. Utiliza el editor de Power Query para limpiar y transformar las informaciones en las bases de datos.

Paso 4.- Crea las relaciones entre las tablas en Power BI para garantizar que los datos estén correctamente conectados y puedan ser analizados de forma integrada.
  Solución: Se generan la relaciones entre las columnas ID producto de las tablas de Productos y Datos Stock

Paso 5.-Necesitamos agregar a la tabla de Datos Stock la información de las columnas Precio de Costo y Precio de Venta que están disponibles en la tabla de Productos.

   Solución: Se agregan columnas con las siguientes operaciones:
       Precio_Costo = RELATED(Productos[Precio de Costo])
       Precio_Venta = RELATED(Productos[Precio de Venta] )

Paso 6.- Calcula el valor de cada producto en stock con base en la información disponible. Crea una nueva columna en la tabla de Datos Stock. Utiliza una función DAX para multiplicar el "Precio de Costo" por la "Cantidad".

    Solución: Se crea una columna con la siguiente función DAX:
      Costo_Total = 'Datos stock'[Cantidad] * 'Datos stock'[Precio_Costo]

Paso 7.- Calcula el valor de venta de cada producto con base en la información disponible.Crea una nueva columna en la tabla de Datos stock.
Utiliza una función DAX para multiplicar el "Precio de Venta" por la "Cantidad".

    Solución 1: Se crea una columna con la función IF, considerando que calcule sólo las salidas como precio de venta, ya que en términos financieros no podemos considerar las entradas con el precio de venta, ya que sería un error, en otras palabras, las entradas se deben de considerar con el precio de costo, ya que determinarán el valor de los inventarios. En este sentido se la función DAX IF tendrá el objetivo  de que sólo calcule el valor de cada producto cuando el tipo de movimiento sea una salida. 

    Ventas_Totales = IF('Datos stock'[Tipo de Movimiento]="Salida",'Datos stock'[Cantidad] * 'Datos stock'[Precio_Venta],0)

    Solución 2.- 

    Inciso A) Se crea una medida que sume los datos de la columna Cantidad con la finalidad de tener todas las entradas y salidas.

Total_Movimiento_EyS = 
    sum('Datos stock'[Cantidad])
    
      Inciso B) Se crea una medida con las funciones CALCULATE Y FILTER, con la finalidad de que se filtren sólo los tipos de movimiento "Salida", tomando como base el paso A
    Salidas = 
 CALCULATE(
        'Medidas'[Total_Movimiento_EyS],
        FILTER(
            'Datos stock',
            'Datos stock'[Tipo de Movimiento] ="Salida"
        ))
      
      Inciso C) Se crea una medida de Ventas totales considerando los productos y el precio de venta, sólo considerando los datos de la salida.
      
      Ventas_Totales2 = 
    SUMX('Productos','Productos'[Precio de Venta]*'Medidas'[Salidas])

Paso 8) Calcula la cantidad total en stock con base en la información disponible. Utiliza el lenguaje DAX para crear una medida que haga la diferencia entre la cantidad total de entradas y salidas. Elige una visualización para observar esta métrica en el Dashboard.

Crea una nueva medida en Power BI para calcular la cantidad total en stock. Utiliza variables DAX para almacenar la cantidad total de entradas y salidas. Calcula la diferencia entre el total de entradas y el total de salidas. Un visual de tarjeta puede ser una buena opción para mostrar un valor.

      Solución 1: Se desarrolla completo 

      Stock_Disponible_Simplificado = 
    VAR SumaEyS = sum('Datos stock'[Cantidad])

    VAR Entradas = CALCULATE(
                    SumaEyS,
                        FILTER(
                        'Datos stock',
                        'Datos stock'[Tipo de Movimiento] ="Entrada"))

    VAR Salidas = CALCULATE(
                    SumaEyS,
                        FILTER(
                        'Datos stock',
                        'Datos stock'[Tipo de Movimiento] ="Salida"))
    RETURN [Entradas] - [Salidas]
      
      
      
      Solución 2: 
      
      Inciso A) Tomando como base la medida desarrollada en la solución del Paso 7, inciso A, se filtran los cantidad de entradas.

Entradas = 
    CALCULATE(
        'Medidas'[Total_Movimiento_EyS],
        FILTER(
            'Datos stock',
            'Datos stock'[Tipo de Movimiento] ="Entrada"
        ))
      
    
      Inciso b) Se toman la medida de salidas desarrollada en el paso 7 inciso B para hacer la operación de E menos S para determinar la cantidad de Stock disponible 
      
      Stock_Disponible = 
    CALCULATE(
        'Medidas'[Entradas] -'Medidas'[Salidas])

Paso 9 Calcula la facturación total basada en las salidas registradas. Utiliza el lenguaje DAX para crear una medida que sume el valor total de las salidas. Elige una visualización para observar esta métrica en el Dashboard. 

Consejos: Crea una nueva medida en Power BI para calcular la facturación total de las salidas. Aplica los filtros adecuados para sumar solo las salidas. Utiliza la columna calculada "Facturación" que ya has definido en tu fórmula. Un visual de tarjeta puede ser una buena opción para mostrar un valor

    Solución 1.- Se crea una columna con la función IF pára sólo multiplicar el precio de venta por la cantidad, considerando únicamente el tipo de movimiento salidas.

    Ventas_Totales = IF('Datos stock'[Tipo de Movimiento]="Salida",'Datos stock'[Cantidad] * 'Datos stock'[Precio_Venta],0)

    Solución 2 = Se crea una medida considerando precio de venta por cantidad, únicamente del tipo de movimiento salidas.

    Ventas_Totales2 = 
    SUMX('Productos','Productos'[Precio de Venta]*'Medidas'[Salidas])


Paso 10.- Calcula a través de los datos el valor total en stock y elige una visualización para observar esta métrica en el Dashboard.

Consejos: Crea una nueva medida en Power BI para calcular el valor total en stock. Aplica los filtros adecuados en la medida para sumar el valor total basado en las entradas y salidas. Utiliza la columna calculada que ya has creado, la cual multiplica el precio de venta por la cantidad disponible. Un visual de tarjeta puede ser una buena opción para mostrar un valor.

Solucíón  :

Valor_Stock = 
    SUMX('Productos','Productos'[Precio de Costo]*'Medidas'[Stock_Disponible])

Paso 11.- Elige una visualización que permita visualizar los datos de facturación y cantidad de salidas a lo largo del tiempo.

Consejos: Utiliza las medidas de facturación y salidas que has creado para segmentar y visualizar los datos de manera efectiva.
El gráfico de columnas apiladas y de líneas puede ser una buena opción.

Paso 12.- Elige una visualización que permita observar en el Dashboard de forma detallada y clara los datos de nombre del producto, categoría, cantidad en stock y valor del stock.

Consejo: Utiliza las medidas que has creado para segmentar y visualizar los datos de manera efectiva. Utiliza una visualización de tabla para mostrar estos datos de forma detallada y organizada.

Paso 13.- Elige una visualización que permita visualizar en el Dashboard los datos de Cantidad de Stock por categoría

Consejo:Utiliza la medida de cantidad de stock que has creado y la columna de categoría de la tabla de productos para segmentar y visualizar los datos de manera efectiva. El gráfico Treemap puede ser una buena opción.

Paso 14.- Elige una visualización que permita visualizar en el Dashboard los datos de Facturación por categoría.

Consejo: Utiliza la medida de facturación que has creado para segmentar y visualizar los datos de manera efectiva. El gráfico de barras apiladas puede ser una buena opción.

Paso 15.- Propone decisiones y otras métricas que puedan ayudar a Gatito. 

Solución.- Es importante destacar cuáles son los productos que mayor demanda tienen con la finalidad de tener presente su cantidad económica de pedido.

  Solución 1)
  .
  % Acumulado_Vtas_x_Prod = 
    divide('Medidas'[Ventas_Totales2],'Medidas'[Ventas_Totales2 ALL],0)


  Solución 2 
  
  % Ventas_x_Producto = 
divide(
        [Ventas_Totales2],
        [Ventas_Totales2 ALL],
        0
        )

  Ambas consideran la siguiente operación:

  Ventas_Totales2 ALL = 
    SUMX(
        ALL('Productos'),
        'Productos'[Precio de Venta]*'Medidas'[Salidas])

        
- Estado del proyecto

- Término de la etapa 1 del Chalenge Gatito


  

  


