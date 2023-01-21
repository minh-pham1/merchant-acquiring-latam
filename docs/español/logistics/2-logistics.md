---
tags: [Logistics, API, LATAM]
---

# Logistics

## Latam Merchant Logistics APIs

En el negocio de medios de pagos, proveedores de servicios de campo tienen un role importante para habilitar nuevos comercios, a través de la instalación de terminales, entrega de rollos de papel térmicos, por brindar mantenimiento a terminales rotos, cambiando SimCards y retirando equipos de comercios siempre que requerido. Para levantar un “Service order” a un proveedor de servicios de campo certificado, Fiserv entrega un conjunto de APIs, detalladas en este documento, que están tipificadas en dos secciones, la primera dedicada a las instituciones financieras para brindar soporte a sus comercios y la segunda parte dedicada a ayudar un proveedor de servicios de campo que quiera integrarse con Fiserv.

---

<!--
type: tab
titles: Institución Financiera, Proveedor Servicios en campo
-->

En diferentes momentos un comercio puede requerir servicios que necesitan intervención de uno de nuestros proveedores de servicios de campo y aquí detallamos algunos conceptos que piden cuidado antes de aprender cómo utilizar las APIs.
(i)	Debe haber un comercio activo al nivel miembro para permitir la requisición de instalación de un terminal y la identificación de este comercio debe ser previamente conocida,
(ii)	Debe haber un terminal activo para el comercio seleccionado al nivel miembro para levantar un pedido de rollos de papel y la identificación de los dos debe ser previamente conocida,
(iii)	Debe haber por lo menos un terminal activo para el comercio seleccionado al nivel miembro para pedir su retirada. Eso implica que no se puede pedir, para eventos con periodicidad definida, un terminal para instalación en una fecha futura y, al mismo tiempo, pedir su retiro un par de semanas o meses después. El “Service Order” de retiro solo puede ser generada cuando el terminal está instalado en el comercio,
(iv)	Es mandatorio para el éxito de una ”Service Order” que la dirección del comercio esté confirmada con el comercio antes de generar el pedido, una vez que eses datos no se pueden cambiar enseguida.
(v)	Debido a los medios de integración entre Fiserv y sus proveedores de servicio en campo, las actualizaciones de cada Service Order ocurren casi real time, lo que ayuda a proveer información siempre actualizada.
(i)	Para utilizar las APIs detalladas en ese documento, un proceso de registro en este portal de desarrollador debe estar concluido. Ese proceso generará las credenciales requeridas para uso como parámetros durante una llamada de API
El siguiente diagrama ilustra los momentos en qué cada API debe ser utilizada

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

Las principales API's disponibles para uso por la Institución Financiera son:

|API|	Dueño de la API	|	Usuario del API|	Descripción|
|---|-------------|---------|-----------|
|New ticket|	Proveedor Servicios en campo|	Fiserv|API para habilitar la creación de Service Orders en la plataforma del proveedor de servicios en campo.|
|Ticket update|	Proveedor Servicios en campo|	Fiserv|	API para habilitar la actualización de Service Orders en la plataforma del proveedor de servicios en campo.|
|Uninstall|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para pedir retiro de terminales|
|Supply request|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para requerimiento de rollos de papel|
|Re-schedule|	Fiserv|	Institución Financiera y Proveedor Servicios en campo|	AAPI para habilitar la actualización de Service Orders (exclusivamente para retrasar el due date)|
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

<!--
type: tab
-->

Fiserv no repasa a sus proveedores de servicios en campo acceso a su herramienta de gestión de “Service Orders” (TMP), una vez que la herramienta no se dibujó para elaborar rutas, mantener documentación requerida para el cierre o cancelación de una Service Order. Fiserv espera que sus proveedores de servicio en campo utilicen sus plataformas internas y integren un conjunto específico de controles para asegurar visibilidad de las Service Orders asignadas al proveedor y para asegurar trazabilidad de los equipos de Fiserv o sus asociadas Instituciones Financieras guardadas con el proveedor de servicios en campo.
Existen dos formas por las que la integración se realizará. La primera es a través de APIs armadas para (i) incorporar nuevos Service Orders de Fiserv para la herramienta del proveedor, (ii) para intercambio de status entre Fiserv y el proveedor, (iii) para registrar el éxito o cancelación de un Service Order basado en un conjunto de opciones predeterminadas, (iv) para comunicar el ingreso de nuevos terminales, accesorios, etc.
Principales APIs entregues para utilización del proveedor de servicios en campo son:

|API|	Dueño de la API	|	Usuario del API|	Descripción|
|---|-------------|---------|-----------|
|New ticket|	Proveedor Servicios en campo|	Fiserv|API para habilitar la creación de Service Orders en la plataforma del proveedor de servicios en campo.|
|Ticket update|	Proveedor Servicios en campo|	Fiserv|	API para habilitar la actualización de Service Orders en la plataforma del proveedor de servicios en campo.|
|Uninstall|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para pedir retiro de terminales|
|Supply request|	Fiserv|	Institución Financiera|	API para habilitar la creación de Service Orders para requerimiento de rollos de papel|
|Re-schedule|	Fiserv|	Institución Financiera y Proveedor Servicios en campo|	AAPI para habilitar la actualización de Service Orders (exclusivamente para retrasar el due date)|
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


Prerrequisito para la utilización de las APIs es la adquisición de un certificado digital privado, que será detallado en ese material.

El según método será a través de cambios de archivos XML, una vez al día, en que Fiserv espera de sus proveedores de servicios en campo que comuniquen todo cambio de ubicación de un equipo del día anterior, incorporando (i) terminales o SimCards transferidos de un estoque central a un puesto avanzado o viceversa, (ii) terminales o SimCards transferidos de un puesto avanzado para comercios o viceversa, (iii) terminales o SimCards transferidos de “triage” para laboratorio o viceversa.

<!-- type: tab-end -->

---
