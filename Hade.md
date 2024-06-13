# Tarea_12
## Ejercicios Propuestos
#### 1. ¿Cuál es el total de ventas realizadas en el año 2009?
DECLARE @fecha_inicio datetime = '2009-01-01';
DECLARE @fecha_fin datetime = '2009-12-31';

SELECT SUM(total) AS Total_Ventas
FROM ve.documento
WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin;

#####Ejemplo
DECLARE @fecha_inicio_2005 datetime = '2005-01-01';
DECLARE @fecha_fin_2005 datetime = '2005-12-31';

SELECT SUM(total) AS Total_Ventas
FROM ve.documento
WHERE fechaMovimiento BETWEEN @fecha_inicio_2005 AND @fecha_fin_2005;

#### 2. ¿Cuáles son las personas que no tienen una entrada registrada en la tabla personaDestino?
SELECT p.*
FROM ma.persona p
LEFT JOIN ma.personaDestino pd ON p.persona = pd.persona
WHERE pd.persona IS NULL;

SELECT * FROM ma.persona

#### 3. ¿Cuál es el promedio del monto total de todas las transacciones de ventas registradas en la base de datos, expresado en moneda local (soles peruanos)?
SELECT FORMAT(AVG(total), 'C', 'es-PE') AS [Promedio del Monto Total en Soles]
FROM ve.documento;

#### 4. Obtén una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos.
SELECT *
FROM ve.documento
WHERE total > (SELECT AVG(total) FROM ve.documento);

#### 5. Listar los documentos de ventas que han sido pagados utilizando una forma de pago específica desde [ve].[documentoPago]
SELECT d.*
FROM ve.documentoPago dp
INNER JOIN ve.documento d ON dp.documento = d.documento
INNER JOIN pa.pago p ON dp.pago = p.pago
WHERE p.formaPago = 10; -- 1 es pago al Contado

SELECT * FROM pa.pago

SELECT * FROM ve.documento

SELECT * FROM ve.documentoPago

#### 6. ¿Cuál es la consulta que devuelve los detalles de los documentos de ventas que han sido canjeados, considerando que un documento puede ser canjeado por uno o más documentos, y estos detalles deben incluir información de los documentos originales y los documentos canjeados?
SELECT d.*, dc.*
FROM ve.documento d
INNER JOIN ve.documentosCanjeados dc ON d.documento = dc.documento01 AND d.documento = dc.documento02;

#### 7. "¿Cómo se distribuye el saldo total entre los diferentes almacenes, considerando la información de los saldos iniciales de  inventario en la base de datos?"
SELECT almacen, SUM(costoSoles) AS Saldo_Total
FROM ma.saldosIniciales
GROUP BY almacen;

SELECT * FROM ma.saldosIniciales

#### 8. ¿Cuáles son los detalles de los documentos de ventas asociados al vendedor con identificación número 3 en la base de datos, considerando la información detallada de cada documento en relación con sus elementos de venta?
SELECT d.*
FROM ve.documento d
INNER JOIN ve.documentoDetalle dd ON d.documento = dd.documento
WHERE d.vendedor = 3;

#### 9. ¿Cuál es el total de ventas por año y vendedor en la base de datos de ventas, considerando solo aquellos vendedores cuya suma total de ventas en un año específico sea superior a 100,000 unidades monetarias?
SELECT vendedor, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento
GROUP BY vendedor, YEAR(fechaMovimiento)
HAVING SUM(total) > 100000
ORDER BY SUM(total) ASC;

#### 10. ¿Cuál es el desglose mensual de las ventas por vendedor en cada año, considerando la suma total de ventas para cada mes y año específico?
##### Ascendente
SELECT vendedor, MONTH(fechaMovimiento) AS Mes, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento
GROUP BY vendedor, MONTH(fechaMovimiento), YEAR(fechaMovimiento)
HAVING SUM(total) > 100000
ORDER BY SUM(total) ASC;

##### Descendente
SELECT vendedor, MONTH(fechaMovimiento) AS Mes, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento
GROUP BY vendedor, MONTH(fechaMovimiento), YEAR(fechaMovimiento)
HAVING SUM(total) > 100000
ORDER BY SUM(total) DESC;

#### 11. ¿Cuántos clientes compraron más de 10 veces en un año?
##### Ascendente
SELECT persona, YEAR(fechaMovimiento) AS Año, FORMAT(COUNT(*), 'C', 'es-PE') AS Compras
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING COUNT(*) > 10
ORDER BY Año ASC;

##### Descendente
SELECT persona, YEAR(fechaMovimiento) AS Año, FORMAT(COUNT(*), 'C', 'es-PE') AS Compras
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING COUNT(*) > 10
ORDER BY Año DESC;

#### 12. ¿Cuál es el total acumulado de descuentos aplicados por cada vendedor en la base de datos de ventas, considerando la suma de los descuentos descto01, descto02 y descto03, y mostrando solo aquellos vendedores cuyo total de descuentos acumulados supere los 5000?
##### Ascendente
SELECT vendedor, FORMAT(SUM(descto01 + descto02 + descto03), 'C', 'es-PE') AS Descuentos_Acumulados
FROM ve.documento
GROUP BY vendedor
HAVING SUM(descto01 + descto02 + descto03) > 5000
ORDER BY vendedor ASC;

##### Descendente
SELECT vendedor, FORMAT(SUM(descto01 + descto02 + descto03), 'C', 'es-PE') AS Descuentos_Acumulados
FROM ve.documento
GROUP BY vendedor
HAVING SUM(descto01 + descto02 + descto03) > 5000
ORDER BY vendedor DESC;

#### 13. ¿Cuál es el total anual de ventas realizadas por cada persona en la base de datos de ventas, considerando únicamente los movimientos de tipo venta (tipoMovimiento = 1), y mostrando solo aquellas personas cuyas ventas anuales superen los 10000?
##### Ascendente
SELECT persona, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Anual
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING SUM(total) > 10000
ORDER BY Año ASC;

##### Descendente
SELECT persona, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Anual
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING SUM(total) > 10000
ORDER BY Año DESC;

#### 14. ¿Cuál es el recuento total de productos vendidos por cada vendedor en la base de datos de ventas?
##### Ascendente
SELECT d.vendedor, COUNT(dd.documentoDetalle) AS Total_Productos_Vendidos
FROM ve.documentoDetalle dd
JOIN ve.documento d ON dd.documento = d.documento
GROUP BY d.vendedor;

#### 15. ¿Cuánto se vendió cada mes del año 2009, desglosado por tipo de pago?
SELECT MONTH(d.fechaMovimiento) AS Mes, p.formaPago, FORMAT(SUM(d.total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento d
JOIN pa.pago p ON d.vendedor = p.vendedor
WHERE YEAR(d.fechaMovimiento) = 2009
GROUP BY MONTH(d.fechaMovimiento), p.formaPago;

#### 16. ¿Cuál es el total de ventas realizadas en el año 2007?
DECLARE @fecha_inicio_2007 datetime = '2007-01-01';
DECLARE @fecha_fin_2007 datetime = '2007-12-31';

SELECT SUM(total) AS Total_Ventas
FROM ve.documento
WHERE fechaMovimiento BETWEEN @fecha_inicio_2007 AND @fecha_fin_2007;

#### 17. ¿Cuáles son las personas que no tienen una entrada registrada en la tabla personaDestino en el año 2008?
SELECT p.*
FROM ma.persona p
LEFT JOIN ma.personaDestino pd ON p.persona = pd.persona
WHERE pd.persona IS NULL

SELECT * FROM ma.persona

SELECT * FROM ma.personaDestino

#### 18. ¿Cuál es el promedio del monto total de todas las transacciones de ventas registradas en la base de datos en el año 2009, expresado en moneda local (soles peruanos)?
SELECT AVG(total) AS Promedio_Ventas
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2009;

#### 19. Obtén una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos en el año 2005.
SELECT *
FROM ve.documento
WHERE total > (SELECT AVG(total) FROM ve.documento WHERE YEAR(fechaMovimiento) = 2005);

#### 20. Listar los documentos de ventas que han sido pagados utilizando una forma de pago específica desde la tabla documentoPago en el año 2006.
SELECT *
FROM ve.documentoPago dp
INNER JOIN ve.documento d ON dp.documento = d.documento
INNER JOIN pa.pago p ON dp.pago = p.pago
WHERE YEAR(fechaMovimiento) = 2006;

#### 21. ¿Cómo se distribuye el saldo total entre los diferentes almacenes, considerando la información de los  saldos iniciales de inventario en la base de datos en el año 2007?
SELECT almacen, FORMAT(SUM(costoSoles), 'C', 'es-PE') AS saldo_total
FROM ma.saldosIniciales
WHERE YEAR(periodo) = 2007
GROUP BY almacen;

#### 22. Obtén una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos en el año 2008.
SELECT *
FROM ve.documento
WHERE total > (SELECT AVG(total) FROM ve.documento WHERE YEAR(fechaMovimiento) = 2008);

#### 23. ¿Cuáles son los detalles de los documentos de ventas asociados al vendedor con identificación número 3 en la base de datos en el año 2009, considerando la información detallada de cada documento en relación con sus elementos de venta?
SELECT *
FROM ve.documentoDetalle dd
JOIN ve.documento d ON dd.documento = d.documento
WHERE d.vendedor = 3 AND YEAR(d.fechaMovimiento) = 2009;

#### 24. ¿Cuál es el total de ventas por año y vendedor en la base de datos de ventas, considerando solo aquellos vendedores cuya suma total de ventas en el año 2008 sea superior a 100,000 unidades monetarias?
SELECT YEAR(fechaMovimiento) AS Año, vendedor, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2008
GROUP BY YEAR(fechaMovimiento), vendedor
HAVING SUM(total) > 100000;

#### 25. ¿Cuál es el desglose mensual de las ventas por vendedor en cada año, considerando la suma total de ventas para  cada mes y año específico en el año 2009?
SELECT YEAR(fechaMovimiento) AS Año, MONTH(fechaMovimiento) AS Mes, vendedor, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2009
GROUP BY YEAR(fechaMovimiento), MONTH(fechaMovimiento), vendedor;

#### 26. ¿Cuántos clientes compraron más de 10 veces en un año en el año 2005?
SELECT persona, COUNT(DISTINCT documento) AS Total_Compras
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2005
GROUP BY persona
HAVING COUNT(DISTINCT documento) > 10;

#### 27. ¿Cuál es el total acumulado de descuentos aplicados por cada vendedor en la base de datos de ventas, considerando la suma de los descuentos descto01, descto02 y descto03, y mostrando solo aquellos vendedores cuyo total de descuentos acumulados supere los 5000 en el año 2005?
SELECT vendedor, FORMAT(SUM(descto01 + descto02 + descto03), 'C', 'es-PE') AS Total_Descuentos
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2005
GROUP BY vendedor
HAVING SUM(descto01 + descto02 + descto03) > 5000;

#### 28. ¿Cuál es el total anual de ventas realizadas por cada persona en la base de datos de ventas, considerando únicamente  los movimientos de tipo venta (tipoMovimiento = 1), y mostrando solo aquellas personas cuyas ventas anuales superen los 10000 en el año 2007?
SELECT persona, YEAR(fechaMovimiento) AS Año, FORMAT(SUM(total), 'C', 'es-PE') AS Total_Anual
FROM ve.documento
WHERE tipoMovimiento = 1 AND YEAR(fechaMovimiento) = 2007
GROUP BY persona, YEAR(fechaMovimiento)
HAVING SUM(total) > 10000;

#### 29. ¿Cuál es el recuento total de productos vendidos por cada vendedor en la base de datos de ventas en el año 2008?
SELECT d.vendedor, COUNT(dd.documentoDetalle) AS Total_Productos_Vendidos
FROM ve.documentoDetalle dd
JOIN ve.documento d ON dd.documento = d.documento
WHERE YEAR(d.fechaMovimiento) = 2008
GROUP BY d.vendedor;

#### 30 ¿Cuánto se vendió cada mes del año 2009, desglosado por tipo de pago?
SELECT YEAR(d.fechaMovimiento) AS Año, MONTH(d.fechaMovimiento) AS Mes, p.formaPago, FORMAT(SUM(d.total), 'C', 'es-PE') AS Total_Ventas
FROM ve.documento d
JOIN pa.pago p ON d.vendedor = p.vendedor
WHERE YEAR(d.fechaMovimiento) = 2009
GROUP BY YEAR(d.fechaMovimiento), MONTH(d.fechaMovimiento), p.formaPago;
