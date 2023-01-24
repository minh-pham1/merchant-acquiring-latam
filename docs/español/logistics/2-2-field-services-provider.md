---
tags: [Field Services Provider, Logistics, API, LATAM]
---

# Field Services Provider

Fiserv no extiende a sus proveedores de servicios en campo acceso a su herramienta de gestión de “Service Orders” (TMP), una vez que la herramienta no se diseño para elaborar rutas, mantener documentación requerida para el cierre o cancelación de una Service Order. Fiserv espera que sus proveedores de servicio en campo utilicen sus plataformas internas y integren un conjunto específico de controles para asegurar visibilidad de las “Service Orders” asignadas al proveedor y para asegurar trazabilidad de los equipos de Fiserv o sus asociadas Instituciones Financieras guardadas con el proveedor de servicios en campo.

Existen dos formas por las que la integración se realizará. La primera es a través de APIs armadas para 1. incorporar nuevos Service Orders de Fiserv para la herramienta del proveedor, 2. para intercambio de status entre Fiserv y el proveedor, 3. para registrar el éxito o cancelación de un Service Order basado en un conjunto de opciones predeterminadas, 4. para comunicar el ingreso de nuevos terminales, accesorios, etc.

Principales APIs para utilización del proveedor de servicios en campo son:

| API                             | Dueño de la API              | Usuario del API                                       | Descripción                                                                                                 |
|---------------------------------|------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| New ticket                      | Proveedor Servicios en campo | Fiserv                                                | API para habilitar la creación de Service Orders en la plataforma del proveedor de servicios en campo.      |
| Ticket update                   | Proveedor Servicios en campo | Fiserv                                                | API para habilitar la actualización de Service Orders en la plataforma del proveedor de servicios en campo. |
| Uninstall                       | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders para pedir retiro de terminales                            |
| Supply request                  | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders para requerimiento de rollos de papel                      |
| Re-schedule                     | Fiserv                       | Institución Financiera y Proveedor Servicios en campo | API para habilitar la actualización de Service Orders (exclusivamente para retrasar el due date)            |
| Order Update                    | Fiserv                       | Field Services provider                               | API para habilitar actualización de Service Orders asignadas a un proveedor de servicios en campo           |
| Maintenance / Replacement       | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders pidiendo un cambio de terminal                             |
| Maintenance / Change Technology | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders pidiendo el reemplazo de tecnología de terminal            |
| Maintenance / Change Carrier    | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders pidiendo un cambio de SimCard                              |
| Install                         | Fiserv                       | Institución Financiera                                | API para habilitar la creación de Service Orders pidiendo la instalación de un terminal                     |
| Cancel order                    | Fiserv                       | Institución Financiera y Proveedor Servicios en campo | API para habilitar la cancelación de una Service Order abierta previamente                                  |
| Ticket List                     | Fiserv                       | Institución Financiera                                | API para habilitar la busqueda de Service Orders asociadas a un comercio particular                         |
| Terminal List                   | Fiserv                       | Institución Financiera                                | API para habilitar la búsqueda de terminales asociados a un comercio particular                             |
| Product                         | Fiserv                       | Institución Financiera                                | API para habilitar la búsqueda de un modelo de terminal basado a su número de serie                         |
| Ticket List details             | Fiserv                       | Institución Financiera y Proveedor Servicios en campo | API para habilitar la búsqueda de detalles de una Service Order de acuerdo a su número de registro          |


Prerrequisito para la utilización de las APIs es la adquisición de un certificado digital privado, que será detallado en este material.

El según método será a través de cambios de archivos XML, una vez al día, en que Fiserv espera de sus proveedores de servicios en campo que comuniquen todo cambio de ubicación de un equipo del día anterior, incorporando 1. terminales o SimCards transferidos de un estoque central a un puesto avanzado o viceversa, 2. terminales o SimCards transferidos de un puesto avanzado para comercios o viceversa, 3. terminales o SimCards transferidos de “triage” para laboratorio o viceversa.

---

## See Also

- [Financial Institution](?path=docs/español/logistics/2-1-financial-institution.md)

---
