---
title: ¿Qué es un centro virtual protegido?
description: Información sobre centros virtuales protegidos
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512009"
---
# <a name="what-is-a-secured-virtual-hub"></a>¿Qué es un centro virtual protegido?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Un centro virtual es una red virtual administrada por Microsoft que permite la conectividad desde otros recursos. Cuando se crea un centro virtual desde una WAN virtual en Azure Portal, se crean como componentes una red virtual del centro virtual y puertas de enlace (opcionales).

Un centro virtual *protegido* es un [centro de conectividad de Azure Virtual WAN](../virtual-wan/virtual-wan-about.md#resources) con directivas de enrutamiento y seguridad asociadas configuradas por Azure Firewall Manager. Use centros virtuales protegidos para crear fácilmente arquitecturas radiales y transitivas con servicios de seguridad nativos para la protección y la gobernanza del tráfico. 

Puede usar un centro virtual protegido como red virtual central administrada sin conectividad local. Reemplaza la red virtual central que se necesitaba anteriormente para una implementación de Azure Firewall. Dado que el centro virtual protegido proporciona enrutamiento automatizado, no es necesario configurar sus propias UDR (rutas definidas por el usuario) para enrutar el tráfico a través del firewall.

También es posible usar los centros virtuales protegidos como parte de una arquitectura de Virtual WAN completa. Esta arquitectura proporciona una conectividad de rama protegida, optimizada y automatizada desde y hacia Azure. Puede elegir los servicios para proteger y controlar el tráfico de red, incluido Azure Firewall y otros proveedores de seguridad como servicio (SECaaS) externos.

## <a name="create-a-secured-virtual-hub"></a>Crear un centro virtual protegido

Con Firewall Manager en Azure Portal, puede crear un nuevo centro virtual protegido o convertir un centro virtual existente creado anteriormente con Azure Virtual WAN.

## <a name="next-steps"></a>Pasos siguientes

Para crear un centro virtual protegido y usarlo para proteger y controlar una red radial, consulte [Tutorial: Proteja su red en la nube con la versión preliminar de Azure Firewall Manager en Azure Portal](secure-cloud-network.md).