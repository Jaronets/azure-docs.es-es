---
title: Configuración de Azure Private Link para una cuenta de Azure Cosmos
description: Aprenda a configurar Azure Private Link para acceder a una cuenta de Azure Cosmos mediante una dirección IP privada en una red virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: fde8829da3e523ced44143db0dee6b93cf9152bd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147773"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configuración de Azure Private Link para una cuenta de Azure Cosmos (versión preliminar)

Al usar Azure Private Link, puede conectarse a una cuenta de Azure Cosmos a través de un punto de conexión privado. El punto de conexión privado es un conjunto de direcciones IP privadas en una subred dentro de la red virtual. A continuación, puede limitar el acceso a una cuenta de Azure Cosmos a través de direcciones IP privadas. Cuando se combina Private Link con directivas de NSG restringidas, ayuda a reducir el riesgo de la exfiltración de datos. Para más información sobre los puntos de conexión privados, consulte el artículo [Azure Private Link](../private-link/private-link-overview.md).

Private Link permite a los usuarios obtener acceso a una cuenta de Azure Cosmos desde dentro de la red virtual o cualquier red virtual emparejada. También se puede acceder a los recursos asignados a Private Link en el entorno local a través de un emparejamiento privado a través de VPN o Azure ExpressRoute. 

Puede conectarse a una cuenta de Azure Cosmos configurada con Private Link mediante el método de aprobación automático o manual. Para obtener más información, consulte la sección [flujo de trabajo de aprobación](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) de la documentación de Private Link. 

En este artículo se describen los pasos para crear un punto de conexión privado. Se da por supuesto que usa el método de aprobación automático.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Creación de un punto de conexión privado mediante Azure Portal

Siga los pasos que se indican a continuación para crear un punto de conexión privado para una cuenta de Azure Cosmos existente mediante Azure Portal:

1. En el panel **Todos los recursos**, elija una cuenta de Azure Cosmos.

1. Seleccione **Conexiones de punto de conexión privado** en la lista de valores y, a continuación, seleccione **Punto de conexión privado**:

   ![Selecciones para la creación de un punto de conexión privado en Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. En **Crear un punto de conexión privado (vista previa) - Conceptos básicos**, escriba o seleccione los detalles siguientes:

    | Configuración | Valor |
    | ------- | ----- |
    | **Detalles del proyecto** | |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione un grupo de recursos.|
    | **Detalles de instancia** |  |
    | NOMBRE | Escriba cualquier nombre para su punto de conexión privado. Si el nombre ya existe, cree uno único. |
    |Region| Seleccione la región en la que desea implementar Private Link. Cree el punto de conexión privado en la misma ubicación donde existe la red virtual.|
    |||
1. Seleccione **Siguiente: Resource** (Siguiente: Recurso).
1. En **Create a private endpoint - Resource** (Crear un punto de conexión privado: recurso), escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    |Método de conexión  | Seleccione **Conectarse a un recurso de Azure en mi directorio**. <br/><br/> A continuación, puede elegir uno de sus recursos para configurar Private Link. O bien, puede conectarse al recurso de otro usuario mediante un alias o identificador del recurso que haya compartido con usted.|
    | Subscription| Seleccione su suscripción. |
    | Tipo de recurso | Seleccione **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Resource |Seleccione la cuenta de Azure Cosmos. |
    |Recurso secundario de destino |Seleccione el tipo de API de Azure Cosmos DB que desea asignar. Este valor predeterminado es solo una opción para las API de SQL, MongoDB y Cassandra. En el caso de las API de Gremlin y Table, también puede elegir **Sql**, ya que estas API son interoperables con la API de SQL. |
    |||

1. Seleccione **Siguiente: Configuration** (Siguiente: Configuración).
1. En **Create a private endpoint (Preview) - Configuración** (Crear un punto de conexión privado [versión preliminar]: Configuración), escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    |**Redes**| |
    | Virtual network| Seleccione la red virtual. |
    | Subnet | Seleccionar la subred. |
    |**Integración de DNS privado**||
    |Integración con una zona DNS privada |Seleccione **Sí**. <br><br/> Para conectar de forma privada con el punto de conexión privado, necesita un registro DNS. Se recomienda integrar el punto de conexión privado con una zona DNS privada. También se pueden usar los servidores DNS propios o bien crear registros DNS con los archivos de host de las máquinas virtuales. |
    |Zona DNS privada |Seleccione **privatelink.documents.azure.com**. <br><br/> La zona DNS privada se determina automáticamente. No puede cambiarla con Azure Portal.|
    |||

1. Seleccione **Revisar + crear**. En la página **Revisar y crear**, Azure valida la configuración.
1. Cuando reciba el mensaje **Validación superada**, seleccione **Crear**.

Cuando haya aprobado Private Link para una cuenta de Azure Cosmos, la opción **Todas las redes** en el panel **Firewall y redes virtuales** no estará disponible en Azure Portal.

En la tabla siguiente se muestra la asignación entre diferentes tipos de API de la cuenta de Azure Cosmos, los subrecursos admitidos y los nombres de zona privada correspondientes. También se puede acceder a las cuentas de Gremlin y Table API a través de la API de SQL, por lo que hay dos entradas para estas API.

|Tipo de API de la cuenta de Azure Cosmos  |Subrecursos admitidos (o identificaciones de grupo) |Nombre de zona privada  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabla    |    Tabla     |   privatelink.table.cosmos.azure.com    |
|Tabla     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Capturar direcciones IP privadas

Una vez aprovisionado el punto de conexión privado, puede consultar las direcciones IP. Para ver las direcciones IP desde Azure Portal:

1. Seleccione **Todos los recursos**.
1. Busque el punto de conexión privado que creó anteriormente. En este caso, es **cdbPrivateEndpoint3**.
1. Seleccione la pestaña **Información general** para ver la configuración de DNS y las direcciones IP.

![Direcciones IP privadas en Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Se crean varias direcciones IP por punto de conexión privado:

* Una para el punto de conexión global (independiente de la región) de la cuenta de Azure Cosmos
* Una para cada región donde se ha implementado la cuenta de Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Creación de un punto de conexión privado mediante Azure PowerShell

Ejecute el siguiente script de PowerShell para crear un punto de conexión privado denominado "MyPrivateEndpoint" para una cuenta de Azure Cosmos existente. Reemplace los valores variables por los detalles de su entorno.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integración del punto de conexión privado con una zona DNS privada

Después de crear el punto de conexión privado, puede integrarlo con una zona DNS privada mediante el siguiente script de PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Capturar direcciones IP privadas

Después de haber aprovisionado el punto de conexión privado, puede consultar las direcciones IP y la asignación de FQDN mediante el siguiente script de PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Creación de un punto de conexión privado mediante una plantilla de Resource Manager

Para configurar Private Link, cree un punto de conexión privado en una subred de la red virtual. Para ello, use una plantilla de Azure Resource Manager.

Use el siguiente código para crear una plantilla de Resource Manager denominada "PrivateEndpoint_template.json". Esta plantilla crea un punto de conexión privado para una cuenta existente de la API de Azure Cosmos SQL en una red virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definición del archivo de parámetros para la plantilla**

Cree un archivo de parámetros para la plantilla y asígnele el nombre "PrivateEndpoint_parameters.json". Agregue el siguiente código al archivo de parámetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Implementación de la plantilla mediante un script de PowerShell**

Cree un script de PowerShell con el código siguiente. Antes de ejecutar el script, reemplace el identificador de la suscripción, el nombre del grupo de recursos y otros valores de variables con los detalles de su entorno.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

En el script de PowerShell, la variable `GroupId` puede contener solo un valor. Ese valor es el tipo de API de la cuenta. Los valores permitidos son: `Sql`, `MongoDB`, `Cassandra`, `Gremlin` y `Table`. Algunos tipos de cuenta de Azure Cosmos son accesibles a través de varias API. Por ejemplo:

* Se puede acceder a una cuenta de la API de Gremlin desde cuentas de API de Gremlin y de SQL.
* Se puede acceder a una cuenta de la Table API desde cuentas de Table API y de SQL.

Para esas cuentas, debe crear un punto de conexión privado para cada tipo de API. El tipo de API correspondiente se especifica en la matriz `GroupId`.

Una vez que la plantilla se ha implementado correctamente, puede ver un resultado similar a la que se muestra en la siguiente imagen. Si los puntos de conexión privados están configurados correctamente, el valor `provisioningState` es `Succeeded`.

![Resultado de la implementación de la plantilla de Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Una vez implementada la plantilla, las direcciones IP privadas se reservan dentro de la subred. La regla de firewall de la cuenta de Azure Cosmos está configurada para aceptar conexiones solo del punto de conexión privado.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integración del punto de conexión privado con una zona de DNS privada

Use el código siguiente para crear una plantilla de Resource Manager llamada "PrivateZone_template.json". Esta plantilla crea una zona DNS privada para una cuenta de SQL API de Azure Cosmos existente en una red virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }       
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }       
    ]
}
```

Use el código siguiente para crear una plantilla de Resource Manager llamada "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }       
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }   
    ]
}
```

**Definición del archivo de parámetros para la plantilla**

Cree los dos siguientes archivos de parámetros para la plantilla. Cree el archivo "PrivateZone_parameters.json" por el siguiente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Cree el archivo "PrivateZoneRecords_parameters.json" por el siguiente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Implementación de la plantilla mediante un script de PowerShell**

Cree un script de PowerShell con el código siguiente. Antes de ejecutar el script, reemplace el identificador de la suscripción, el nombre del grupo de recursos y otros valores de variables con los detalles de su entorno.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Configuración de DNS personalizado

Debe usar una zona DNS privada dentro de la subred en la que ha creado el punto de conexión privado. Configure los puntos de conexión de modo que cada dirección IP privada se asigne a una entrada DNS (consulte la propiedad `fqdns` en la respuesta mostrada anteriormente).

Al crear el punto de conexión privado, puede integrarlo con una zona DNS privada en Azure. Si, en su lugar, elige usar una zona DNS personalizada, debe configurarla para agregar registros DNS para todas las direcciones IP privadas reservadas para el punto de conexión privado.

## <a name="private-link-combined-with-firewall-rules"></a>Combinación de Private Link con las reglas de firewall

Las situaciones y resultados que se muestran a continuación son posibles cuando se usa Private Link en combinación con las reglas de firewall:

* Si no configura ninguna regla de firewall, todo el tráfico podrá tener acceso a una cuenta de Azure Cosmos de forma predeterminada.

* Si configura el tráfico público o un punto de conexión de servicio y crea puntos de conexión privados, los distintos tipos de tráfico entrante estarán autorizados por el tipo de regla de firewall correspondiente.

* Si no configura ningún tráfico público ni punto de conexión de servicio y crea puntos de conexión privados, la cuenta de Azure Cosmos solo es accesible a través de los puntos de conexión privados. Si no configura ningún tráfico público ni punto de conexión de servicio, después de que se rechacen o eliminen todos los puntos de conexión privados aprobados, la cuenta estará abierta a toda la red.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Actualización de un punto de conexión privado al agregar o quitar una región

La adición o eliminación de regiones a una cuenta de Azure Cosmos requiere que se agreguen o quiten las entradas DNS para esa cuenta. Actualice estos cambios en consecuencia en el punto de conexión privado mediante los siguientes pasos:

1. Cuando el administrador de Azure Cosmos DB agrega o quita regiones, el administrador de red obtiene una notificación sobre los cambios pendientes. En el caso del punto de conexión privado asignado a una cuenta de Azure Cosmos, el valor de la propiedad `ActionsRequired` cambia de `None` a `Recreate`. Después, el administrador de red actualiza el punto de conexión privado mediante la emisión de una solicitud PUT con la misma carga de Resource Manager que se usó para crearlo.

1. Una vez actualizado el punto de conexión privado, podrá actualizar la zona DNS privada de la subred para reflejar las entradas DNS agregadas o eliminadas, así como sus direcciones IP privadas correspondientes.

Por ejemplo, imagine que implementa una cuenta de Azure Cosmos en tres regiones: "Oeste de EE. UU.", "Centro de EE. UU." y "Oeste de Europa". Cuando crea un punto de conexión privado para la cuenta, se reservan cuatro direcciones IP privadas en la subred. Hay una dirección IP para cada una de las tres regiones y una para el punto de conexión global o independiente de la región.

Más adelante, podría agregar una nueva región (por ejemplo, "Este de EE. UU.") a la cuenta de Azure Cosmos. de forma predeterminada, no se puede acceder a la nueva región desde el punto de conexión privado existente. El administrador de la cuenta de Azure Cosmos debe actualizar la conexión al punto de conexión privado antes de tener acceso a él desde la nueva región. 

Al ejecutar el comando ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, el resultado del comando contiene el parámetro `actionsRequired`. Este parámetro se establece en `Recreate`. Este valor indica que el punto de conexión privado debe actualizarse. Después, el administrador de la cuenta de Azure Cosmos ejecuta el comando `Set-AzPrivateEndpoint` para desencadenar la actualización del punto de conexión privado.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Una nueva dirección IP privada se reserva automáticamente en la subred de este punto de conexión privado. El valor de `actionsRequired` se convierte en `None`. Si no tiene ninguna integración de zona DNS privada (es decir, si usa una zona DNS privada personalizada), debe configurar la zona DNS privada para agregar un nuevo registro DNS para la dirección IP privada correspondiente a la nueva región.

Puede seguir los mismos pasos al quitar una región. La dirección IP privada de la región que se ha quitado se reclama automáticamente, y la marca `actionsRequired` se convierte en `None`. Si no tiene ninguna integración de zona DNS privada, debe configurar la zona DNS privada para quitar el registro DNS de la región que se ha quitado.

Los registros DNS de la zona DNS privada no se quitan automáticamente cuando se elimina un punto de conexión privado o se quita una región de la cuenta de Azure Cosmos. Debe quitar manualmente los registros DNS.

## <a name="current-limitations"></a>Limitaciones actuales

Al usar Private Link con una cuenta de Azure Cosmos se aplican las siguientes limitaciones:

* La compatibilidad de Private Link para las cuentas y redes virtuales de Azure Cosmos solo está disponible en determinadas regiones. Para obtener una lista de las regiones admitidas, consulte la sección [Regiones disponibles](../private-link/private-link-overview.md#availability) del artículo de Private Link. 

  > [!NOTE]
  > Para crear un punto de conexión privado, asegúrese de que tanto la red virtual como la cuenta de Azure Cosmos se encuentran en regiones admitidas.

* Al usar Private Link con una cuenta de Azure Cosmos mediante conexión de modo directo, solo puede usar el protocolo TCP. Aún no se admite el protocolo HTTP.

* Cuando se usa la API de Azure Cosmos DB para las cuentas de MongoDB, solo se admite un punto de conexión privado para las cuentas en el servidor versión 3.6 (es decir, las cuentas que usan el punto de conexión con el formato `*.mongo.cosmos.azure.com`). Private Link no se admite para cuentas en el servidor versión 3.2 (es decir, cuentas que usan el punto de conexión con el formato `*.documents.azure.com`). Para usar Private Link, debe migrar las cuentas anteriores a la nueva versión.

* Al usar la API de Azure Cosmos DB para las cuentas de MongoDB que tienen Private Link, no puede usar herramientas como Robo 3T, Studio 3T y Mongoose. El punto de conexión puede tener compatibilidad con Private Link solo si se especifica el parámetro `appName=<account name>`. Un ejemplo es `replicaSet=globaldb&appName=mydbaccountname`. Dado que estas herramientas no pasan el nombre de la aplicación en la cadena de conexión al servicio, no puede usar Private Link. Sin embargo, todavía puede tener acceso a estas cuentas con controladores de SDK con la versión 3.6.

* No puede mover ni eliminar una red virtual si contiene Private Link.

* No puede eliminar una cuenta de Azure Cosmos si está asociada a un punto de conexión privado.

* No puede conmutar por error una cuenta de Azure Cosmos a una región que no esté asignada a todos los puntos de conexión privados conectados a esta cuenta.

* Debe concederse a un administrador de red al menos el permiso "*/PrivateEndpointConnectionsApproval" en el ámbito de la cuenta de Azure Cosmos para crear puntos de conexión privados aprobados automáticamente.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitaciones de la integración de la zona DNS privada

Los registros DNS de la zona DNS privada no se quitan automáticamente cuando se elimina un punto de conexión privado o se quita una región de la cuenta de Azure Cosmos. Debe quitar manualmente los registros DNS antes de realizar las siguientes actividades:

* Agregar un nuevo punto de conexión privado conectado a esta zona DNS privada.
* Agregar una nueva región a cualquier cuenta de base de datos que tenga puntos de conexión privados conectados a esta zona DNS privada.

Si no limpia los registros DNS, pueden producirse problemas del plano de datos inesperados. Entre estos problemas se incluye la interrupción de datos en regiones agregadas tras la eliminación de puntos de conexión privados o regiones.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las características de seguridad de Azure Cosmos DB, consulte los siguientes artículos:

* Para configurar un firewall para Azure Cosmos DB, consulte [Compatibilidad con firewalls](firewall-support.md).

* Para aprender a configurar un punto de conexión de servicio de red virtual para su cuenta de Azure Cosmos, consulte [Configuración del acceso desde redes virtuales](how-to-configure-vnet-service-endpoint.md).

* Para obtener más información sobre Private Link, consulte la documentación de [Azure Private Link](../private-link/private-link-overview.md).
