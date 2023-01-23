---
tags: [Financial Institution, Logistics, API, LATAM]
---

# Financial Institution

En diferentes momentos, un comercio puede requerir servicios que necesitan intervención de uno de nuestros proveedores de servicios de campo y aquí detallamos algunos conceptos que requieren atención antes de aprender a utilizar las APIs.

1. Debe haber un comercio activo al nivel miembro para permitir la solicitud de instalación de una terminal y la identificación de este comercio debe ser previamente conocida.
2. Debe haber un terminal activo para el comercio seleccionado al nivel miembro para levantar un pedido de rollos de papel y la identificación de los dos debe ser previamente conocida.
3. Debe haber por lo menos una terminal activo para el comercio seleccionado al nivel miembro para pedir su retirada, eso implica que no se puede pedir, para eventos con periodicidad definida, una terminal para instalación en una fecha futura y, al mismo tiempo, pedir su retiro un par de semanas o meses después. La “Service Order” de retiro solo puede ser generada cuando la terminal está instalada en el comercio,
4. Es mandatorio para el éxito de una ”Service Order” que la dirección del comercio esté confirmada con el comercio antes de generar el pedido, porque esos datos no se pueden cambiar.
5. Debido a los medios de integración entre Fiserv y sus proveedores de servicio en campo, las actualizaciones de cada “Service Order” ocurren casi en tiempo real, lo que ayuda a proveer información siempre actualizada.
6. Para utilizar las APIs detalladas en este documento, el proceso de registro en este portal de desarrollador debe estar concluido. Ese proceso generará las credenciales requeridas para uso como parámetros durante una llamada de API

El siguiente diagrama ilustra los momentos en qué cada API debe ser utilizada

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

Las principales API's disponibles para uso por la Institución Financiera son:

|API|	Dueño de la API	|	Usuario del API|	Descripción|
|---|-------------|---------|-----------|
|New ticket|	Proveedor Servicios en campo|	Fiserv|API para habilitar la creación de Service Orders en la plataforma del proveedor de servicios en campo.|
|Ticket update|	Proveedor Servicios en campo|	Fiserv|	API para habilitar la actualización de Service Orders en la plataforma del proveedor de servicios en campo.|
|Uninstall|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para pedir retiro de terminales|
|Supply request|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para requerimiento de rollos de papel|
|Re-schedule|	Fiserv|	Institución Financiera y Proveedor Servicios en campo|	API para habilitar la actualización de Service Orders (exclusivamente para retrasar el due date)|
|Order Update|	Fiserv|	Field Services provider|	API para habilitar actualización de Service Orders asignadas a un proveedor de servicios en campo|
|Maintenance / Replacement|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders pidiendo un cambio de terminal|
|Maintenance / Change Technology|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders pidiendo el reemplazo de tecnología de terminal|
|Maintenance / Change Carrier|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders pidiendo un cambio de SimCard|
|Install|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders pidiendo la instalación de un terminal|
|Cancel order|	Fiserv|	Institución Financiera y Proveedor Servicios en campo|	API para habilitar la cancelación de una Service Order abierta previamente|
|Ticket List|	Fiserv|	Institución Financiera|	API para habilitar la busqueda de Service Orders asociadas a un comercio particular|
|Terminal List|	Fiserv	|Institución Financiera|	API para habilitar la búsqueda de terminales asociados a un comercio particular|
|Product|	Fiserv|	Institución Financiera|	API para habilitar la búsqueda de un modelo de terminal basado a su número de serie|
|Ticket List details|	Fiserv|	Institución Financiera y Proveedor Servicios en campo|	API para habilitar la búsqueda de detalles de una Service Order de acuerdo a su número de registro|

---

## See Also

- [Field Services Provider](?path=docs/español/logistics/2-2-field-services-provider.md )

---
