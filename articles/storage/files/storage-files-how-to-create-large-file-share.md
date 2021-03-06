---
title: Habilitación y creación de recursos compartidos de archivos grandes - Azure Files
description: En este artículo aprenderá a habilitar y crear recursos compartidos de archivos grandes.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a9b545d71f21138c0374cf199ce10dc2dc246afb
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732151"
---
# <a name="enable-and-create-large-file-shares"></a>Habilitación y creación de recursos compartidos de archivos grandes

Originalmente, los recursos compartidos de archivos estándar solo podían escalarse verticalmente hasta 5 TiB. Ahora, gracias a los recursos compartidos de archivos grandes, se pueden escalar hasta 100 TiB. Esta escalabilidad se puede habilitar para los recursos compartidos de archivos existentes en las cuentas de almacenamiento existentes. Los recursos compartidos de archivos Premium escalan verticalmente de forma predeterminada hasta 100 TiB.

## <a name="prerequisites"></a>Prerequisites

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Si planea usar la CLI de Azure, [instale la versión más reciente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Si planea usar Azure PowerShell, [instale la versión más reciente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restricciones

Por ahora, solo puede usar LRS o ZRS en cuentas habilitadas para recursos compartidos de archivos grandes. No puede usar GZRS, GRS o RA-GRS.
La habilitación de recursos compartidos de archivos grandes en una cuenta es un proceso irreversible. Después de habilitarlos, no podrá convertir la cuenta a GZRS, GRS o RA-GRS.

## <a name="create-a-new-storage-account"></a>Creación de una cuenta de almacenamiento nueva

### <a name="portal"></a>Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En Azure Portal, seleccione **Todos los servicios**. 
1. En la lista de recursos, escriba **Cuentas de almacenamiento**. La lista se filtra en función de lo que escriba. Seleccione **Cuentas de almacenamiento**.
1. En la ventana **Cuentas de almacenamiento** que aparece, seleccione **Agregar**.
1. Seleccione la suscripción que usará para crear la cuenta de almacenamiento.
1. En el campo **Grupo de recursos**, haga clic en **Crear nuevo**. Escriba un nombre para el nuevo grupo de recursos.

    ![Captura de pantalla que muestra cómo crear un grupo de recursos en el portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Después, escriba un nombre para la cuenta de almacenamiento. El nombre debe ser único en Azure. El nombre también debe tener entre 3 y 24 caracteres de longitud y solo puede incluir números y letras minúsculas.
1. Seleccione una ubicación para la cuenta de almacenamiento y asegúrese de que sea [una de las regiones compatibles con recursos compartidos de archivos grandes](storage-files-planning.md#regional-availability).
1. Establezca la replicación en **Almacenamiento con redundancia local** o **Almacenamiento con redundancia de zona**.
1. Deje estos campos con sus valores predeterminados:

   |Campo  |Value  |
   |---------|---------|
   |Modelo de implementación     |Resource Manager         |
   |Rendimiento     |Estándar         |
   |Tipo de cuenta     |StorageV2 (uso general v2)         |
   |Nivel de acceso     |Acceso frecuente         |

1. Seleccione **Avanzado** y, luego, seleccione el botón de opción **Habilitado** a la derecha de **Large file shares** (Recursos compartidos de archivos grandes).
1. Seleccione **Revisar y crear** para revisar la configuración de la cuenta de almacenamiento y crear la cuenta.

    ![Captura de pantalla con la opción "habilitado" en una nueva cuenta de almacenamiento de Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Seleccione **Crear**.

### <a name="cli"></a>CLI

En primer lugar, [instale la versión más reciente de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para que pueda habilitar recursos compartidos de archivos grandes.

Para crear una cuenta de almacenamiento que tenga habilitados recursos compartidos de archivos grandes, use el siguiente comando. Reemplace `<yourStorageAccountName>`, `<yourResourceGroup>` y `<yourDesiredRegion>` por su información.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

En primer lugar, [instale la versión más reciente de PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) para poder habilitar recursos compartidos de archivos grandes.

Para crear una cuenta de almacenamiento que tenga habilitados recursos compartidos de archivos grandes, use el siguiente comando. Reemplace `<yourStorageAccountName>`, `<yourResourceGroup>` y `<yourDesiredRegion>` por su información.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Habilitación de recursos compartidos de archivos grandes en una cuenta existente

También puede habilitar recursos compartidos de archivos grandes en las cuentas existentes. En este caso, no podrá convertirlos a GZRS, GRS o RA-GRS. La habilitación de recursos compartidos de archivos grandes es irreversible en esta cuenta de almacenamiento.

### <a name="portal"></a>Portal

1. Abra [Azure Portal](https://portal.azure.com) y vaya a la cuenta de almacenamiento en la que quiere habilitar recursos compartidos de archivos grandes.
1. Abra la cuenta de almacenamiento y seleccione **Configuración**.
1. Seleccione **Habilitado** en **Large file shares** (Recursos compartidos de archivos grandes) y, a continuación, seleccione **Guardar**.
1. Seleccione **Información general** y elija **Actualizar**.

![Selección del botón de opción Habilitado en una cuenta de almacenamiento existente en Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Ahora ha habilitado los recursos compartidos de archivos grandes en la cuenta de almacenamiento. Luego, debe actualizar la cuota del recurso compartido existente para aprovechar las ventajas de una mayor capacidad y escalabilidad.

Si recibe un mensaje de error que dice que los recursos compartidos de archivos grandes no están disponibles aún para la cuenta, es posible que la región esté en proceso de completar su implementación. Si necesita urgentemente recursos compartidos de archivos grandes, póngase en contacto con el servicio de soporte técnico.

### <a name="cli"></a>CLI

Para habilitar recursos compartidos de archivos grandes en una cuenta existente, use el siguiente comando. Reemplace `<yourStorageAccountName>` y `<yourResourceGroup>` por su propia información.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Para habilitar recursos compartidos de archivos grandes en una cuenta existente, use el siguiente comando. Reemplace `<yourStorageAccountName>` y `<yourResourceGroup>` por su propia información.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Creación de un recurso compartido de archivos grandes

Después de habilitar recursos compartidos de archivos grandes en la cuenta de almacenamiento, puede crear recursos compartidos de archivos en esa cuenta con cuotas más altas. 

### <a name="portal"></a>Portal

La creación de un recurso compartido de archivos grandes es casi idéntica a la creación de un recurso compartido de archivos estándar. La principal diferencia es que puede establecer una cuota de hasta 100 TiB.

1. En la cuenta de almacenamiento, seleccione **Recurso compartido de archivos**.
1. Seleccione **+ Recurso compartido de archivos**.
1. Escriba el nombre del recurso compartido de archivos. También puede establecer el tamaño de cuota que prefiera, hasta 100 TiB. Seleccione **Crear**. 

![La interfaz de usuario de Azure Portal con los cuadros de nombre y cuota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Para crear un recurso compartido de archivos grande, use el siguiente comando. Reemplace `<yourStorageAccountName>`, `<yourStorageAccountKey>` y `<yourFileShareName>` por su información.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Para crear un recurso compartido de archivos grande, use el siguiente comando. Reemplace `<YourStorageAccountName>`, `<YourStorageAccountKey>` y `<YourStorageAccountFileShareName>` por su información.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expansión de recursos compartidos de archivos existentes

Después de habilitar recursos compartidos de archivos grandes en la cuenta de almacenamiento, puede expandir también los recursos compartidos de archivos existentes en esa cuenta a una cuota más alta. 

### <a name="portal"></a>Portal

1. En la cuenta de almacenamiento, seleccione **Recurso compartido de archivos**.
1. Haga clic con el botón derecho en el recurso compartido de archivos y seleccione **Cuota**.
1. Escriba el nuevo tamaño que quiera y, luego, seleccione **Aceptar**.

![La interfaz de usuario de Azure Portal con la cuota de los recursos compartidos de archivos existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Para establecer la cuota en el tamaño máximo, use el siguiente comando. Reemplace `<yourStorageAccountName>`, `<yourStorageAccountKey>` y `<yourFileShareName>` por su información.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Para establecer la cuota en el tamaño máximo, use el siguiente comando. Reemplace `<YourStorageAccountName>`, `<YourStorageAccountKey>` y `<YourStorageAccountFileShareName>` por su información.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Pasos siguientes

* [Conexión y montaje de un recurso compartido de archivos en Windows](storage-how-to-use-files-windows.md)
* [Conexión y montaje de un recurso compartido de archivos en Linux](storage-how-to-use-files-linux.md)
* [Conexión y montaje de un recurso compartido de archivos en macOS](storage-how-to-use-files-mac.md)
