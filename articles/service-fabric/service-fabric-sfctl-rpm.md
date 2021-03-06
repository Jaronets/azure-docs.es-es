---
title: Azure Service Fabric CLI- sfctl rpm
description: Más información sobre sfctl, la interfaz de la línea de comandos de Azure Service Fabric. Incluye una lista de comandos para el servicio del administrador de reparaciones.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639095"
---
# <a name="sfctl-rpm"></a>rpm de sfctl
Consulte y envíe comandos al servicio del administrador de reparaciones.

## <a name="commands"></a>Comandos:

|Get-Help|Descripción|
| --- | --- |
| approve-force | Fuerza la aprobación de la tarea de reparación determinada. |
| delete | Elimina una tarea de reparación completada. |
| list | Obtiene una lista de tareas de reparación que coinciden con los filtros especificados. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
Fuerza la aprobación de la tarea de reparación determinada.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --task-id [Obligatorio] | El identificador de la tarea de reparación. |
| --version | El número de versión actual de la tarea de reparación. Si es distinto de cero, la solicitud se realizará correctamente solo si este valor coincide con la versión actual real de la tarea de reparación. Si es cero, no se realiza ninguna comprobación de la versión. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug | Aumente el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestre este mensaje de ayuda y salga. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Elimina una tarea de reparación completada.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --task-id [Obligatorio] | El identificador de la tarea de reparación completada que se va a eliminar. |
| --version | El número de versión actual de la tarea de reparación. Si es distinto de cero, la solicitud se realizará correctamente solo si este valor coincide con la versión actual real de la tarea de reparación. Si es cero, no se realiza ninguna comprobación de la versión. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug | Aumente el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestre este mensaje de ayuda y salga. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
Obtiene una lista de tareas de reparación que coinciden con los filtros especificados.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --executor-filter | El nombre del ejecutar de reparación cuyas tareas notificadas deben incluirse en la lista. |
| --state-filter | Una operación OR bit a bit de los siguientes valores, especificando qué estados de tareas deben incluirse en la lista de resultados. <br> 1\. Creado <br>2\. Notificado  <br>4\. Preparando  <br>8\. Aprobado  <br>16. Ejecutando  <br>32. Restaurando  <br>64. Completado |
| --task-id-filter | El prefijo del identificador de tarea de reparación que debe coincidir. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug | Aumente el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestre este mensaje de ayuda y salga. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |


## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).