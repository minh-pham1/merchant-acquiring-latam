# Pre-Requisito - Configuración de ofertas

> Antes de comenzar a incorporar a sus comerciantes, querrá asegurarse de estar embarcado en Fiserv usted mismo: comuníquese con el equipo del Portal de desarrolladores para comenzar este viaje.

## Ofertas y configuración

Una parte de su incorporación es la configuración de "ofertas" y la configuración de estas ofertas es esencial para que funcione el proceso de incorporación. La configuración se crea en otra herramienta provista por Fiserv (Sistema de Gestión de Oferta). Este es un lugar donde se mantienen todas las ofertas específicas de Alliance y Partner. La configuración inicial normalmente la realiza Fiserv en base a un acuerdo anterior con la Alianza o el Socio. El acceso a esta herramienta también se puede otorgar a los usuarios de Alliance y Partner para que puedan realizar actualizaciones a la configuración existente de acuerdo con sus necesidades comerciales.

> Toda la información de las secciones siguientes asume que se ha cumplido esta condición previa.
<!-- tema: advertencia -->

Hay dos tipos de ofertas que se pueden configurar:

|OFERTAS DE EQUIPOS | TRAMITACIÓN/ADQUISICIÓN DE OFERTAS |
|---------------|----------------------------------|
|Cuando se establece una combinación de equipos que se puede ofrecer a los comerciantes, el costo de ese equipo y las funciones que admite.|Cuando se establecen tarifas relacionadas con el procesamiento de transacciones y otras tarifas de procesamiento de cuentas, como una tarifa de inscripción.|
|El comercio electrónico también se configura dentro de la oferta de Equipos, no se restringe solo a equipos físicos como terminales o periféricos.|

1. Solo se requiere una oferta de cualquier tipo como configuración mínima, según las necesidades de su negocio.
2. Ambos pueden configurarse y usarse juntos.
3. Las ofertas también se pueden configurar para que solo se apliquen a un rango de criterios configurado.
4. Fuera de la caja, este criterio incluye opciones de MCC, valor promedio de transacción, volumen de negocios anual y dirección.

## Acceso

Debe tener una **clave de API** vinculada con la alianza o el socio para el que planea incorporar a los comerciantes.|

La clave API debe proporcionarse como el valor del campo de encabezado `appKey` de cada llamada API.

Valores específicos de la alianza
Antes de comenzar a construir la carga útil json, asegúrese de tener los valores para los siguientes objetos:


1. `allianceCode`
1. `partnerCode` (solo en caso de que vayas a integrar un Socio)
1. `accountType`
1. `accountTypeSysId`
1. `customFieldCode`

Los valores para esos objetos siempre son específicos de Alliance/Partner y deben acordarse previamente con Fiserv u obtenerse antes de enviar sus primeras solicitudes de embarque. Si necesita tener todos los valores enumerados anteriormente depende del conjunto de servicios que activará para los comerciantes y del tipo de negocio que sea.

> Asegúrese de estar familiarizado con los conceptos básicos del acceso y la configuración de gestión de ofertas antes de enviar sus primeras solicitudes de embarque.
<!-- tema: advertencia -->
