---
title: Creación de una función de Python desencadenada mediante HTTP en Azure
description: Aprenda a crear su primera función de Python en Azure mediante Azure Functions Core Tools y la CLI de Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3de8c42c59455cc326fa909bc520a94daac68706
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769343"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Inicio rápido: Creación de una función de Python desencadenada mediante HTTP en Azure

En este artículo se explica cómo usar las herramientas de línea de comandos para crear un proyecto de Python que se ejecuta en Azure Functions. También puede crear una función que desencadena una solicitud HTTP. Después de la ejecución local, publique el proyecto para que se ejecute como una [función sin servidor](functions-scale.md#consumption-plan) en Azure. 

Este artículo es el primero de dos artículos de inicio rápido de Python para Azure Functions. Después de completar este inicio rápido, puede [agregar el enlace de salida de la cola de Azure Storage](functions-add-output-binding-storage-queue-python.md) a la función.

También hay una [versión basada en Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01) de este artículo.

## <a name="prerequisites"></a>Prerequisites

Antes de comenzar, debe hacer lo siguiente:

+ Instale [Python 3.7.4](https://www.python.org/downloads/). Esta versión de Python se comprueba con Functions. Python 3.8 y versiones posteriores aún no se admiten.

+ Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versión 2.7.1846 u otra posterior.

+ Instale la [CLI de Azure](/cli/azure/install-azure-cli) versión 2.0.76 u otra posterior.

+ Tener una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Creación y activación de un entorno virtual

Debe usar un entorno de Python 3.7 para desarrollar localmente funciones de Python. Ejecute los comandos siguientes para crear y activar un entorno virtual denominado `.venv`.

> [!NOTE]
> Si Python no instaló venv en la distribución de Linux, puede instalarlo con el siguiente comando:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell o un símbolo del sistema de Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Ahora que ha activado el entorno virtual, ejecute los comandos restantes en él. Para salir del entorno virtual, ejecute `deactivate`.

## <a name="create-a-local-functions-project"></a>Creación de un proyecto local de Functions

Un proyecto de Functions Puede tener varias funciones que comparten la misma configuración local y de host.

En el entorno virtual, ejecute los comandos siguientes:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

El comando `func init` crea una carpeta _MyFunctionProj_. El proyecto de Python de esta carpeta todavía no tiene ninguna función. Las agregará a continuación.

## <a name="create-a-function"></a>Creación de una función

Para agregar una función a su proyecto, ejecute el siguiente comando:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Este comando crea una subcarpeta denominada _HttpTrigger_, que contiene los archivos siguientes:

* *function.json*: archivo de configuración que define la función, el desencadenador y otros enlaces. Tenga en cuenta que, en este archivo, el valor de `scriptFile` apunta al archivo que contiene la función, mientras que la matriz `bindings` define el desencadenador de invocación y los enlaces.

    Cada enlace requiere una dirección, un tipo y un nombre único. El desencadenador HTTP tiene un enlace de entrada de tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) y un enlace de salida de tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: archivo de script que es su función de desencadenador HTTP. Tenga en cuenta que este script tiene el valor predeterminado `main()`. Los datos HTTP del desencadenador pasan a la función mediante el `req` denominado `binding parameter`. `req`, que se define en function.json, es una instancia de la [clase azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    El objeto devuelto, definido como `$return` en *function.json*, es una instancia de la [clase azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para más información, vea [Enlaces y desencadenadores HTTP de Azure Functions](functions-bindings-http-webhook.md).

Ahora puede ejecutar la nueva función en el equipo local.

## <a name="run-the-function-locally"></a>Ejecución local de la función

Este comando inicia la aplicación de funciones mediante el tiempo de ejecución de Azure Functions (func.exe):

```console
func host start
```

Debería ver la siguiente información escrita en el resultado:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Copie la dirección URL de la función `HttpTrigger` que aparece en el resultado y péguela en la barra de direcciones del explorador. Agregue la cadena de consulta `?name=<yourname>` a esta dirección URL y ejecute la solicitud. La captura de pantalla siguiente muestra la respuesta a la solicitud GET devuelta que la función local devuelve al explorador:

![Comprobación local en el explorador](./media/functions-create-first-function-python/function-test-local-browser.png)

Use CTRL+C para cerrar la ejecución de la aplicación de funciones.

Ahora que ha ejecutado la función localmente, puede implementar el código de función en Azure.  
Para poder implementar la aplicación, deberá crear algunos recursos de Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Creación de una aplicación de función en Azure

Una aplicación de función proporciona un entorno para la ejecución del código de su función. Le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación, el escalado y el uso compartido de recursos.

Ejecute el siguiente comando: Reemplace `<APP_NAME>` por un nombre único de aplicación de funciones. Reemplace `<STORAGE_NAME>` por el nombre de la cuenta de almacenamiento. `<APP_NAME>` también es el dominio DNS predeterminado de la aplicación de función. Este nombre debe ser único entre todas las aplicaciones de Azure.

> [!NOTE]
> No se pueden hospedar aplicaciones de Windows y Linux en el mismo grupo de recursos. Si tiene un grupo de recursos existente llamado `myResourceGroup` con una aplicación web o aplicación de función de Windows, debe usar un grupo de recursos diferente.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

El comando anterior crea una aplicación de funciones que ejecuta Python 3.7.4. También aprovisiona una instancia asociada de Azure Application Insights en el mismo grupo de recursos. Puede usar esta instancia para supervisar la aplicación de funciones y ver los registros. 

Ahora ya está listo para publicar el proyecto de funciones local en la aplicación de función en Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementación del proyecto de aplicación de función en Azure

Después de crear la aplicación de funciones en Azure, puede usar el comando [func azure functionapp publish](functions-run-local.md#project-file-deployment) de Core Tools para implementar el código del proyecto en Azure. En este ejemplo, reemplace `<APP_NAME>` por el nombre de la aplicación.

```console
func azure functionapp publish <APP_NAME>
```

El proyecto de Python se crea de forma remota en Azure a partir de los archivos del paquete de implementación. 

Verá una salida similar al del mensaje siguiente. Se trunca aquí para que pueda leerlo mejor:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Puede copiar el valor `Invoke url` para `HttpTrigger` y usarlo para comprobar la función en Azure. La dirección URL contiene un valor de cadena de consulta `code` que es la clave de función, lo que dificulta que otros usuarios llamen al punto de conexión del desencadenador HTTP en Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Para ver los registros casi en tiempo real de una aplicación de Python publicada, utilice [Live Metrics Stream de Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Pasos siguientes

Ha creado un proyecto de funciones de Python con una función desencadenada por HTTP, lo ha ejecutado en el equipo local y lo ha implementado en Azure. Ahora, amplíe la función como sigue...

> [!div class="nextstepaction"]
> [Adición de un enlace de salida de la cola de Azure Storage](functions-add-output-binding-storage-queue-python.md)
