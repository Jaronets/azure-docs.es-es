---
title: Configuración de alertas en métricas de Azure VPN Gateway
description: Pasos para configurar alertas en métricas de VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605226"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configuración de alertas en métricas de VPN Gateway

Este artículo le ayuda a configurar alertas en métricas de Azure VPN Gateway. Azure Monitor proporciona la capacidad de configurar alertas para los recursos de Azure. Puede configurar alertas para las puertas de enlace de red virtual del tipo "VPN".


|**Métrica**   | **Unidad** | **Granularidad** | **Descripción** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes por segundo  | 5 minutos| Promedio de uso de ancho de banda combinado de todas las conexiones de sitio a sitio en la puerta de enlace.     |
|**P2SBandwidth**| Bytes por segundo  | 1 minuto  | Promedio de uso de ancho de banda combinado de todas las conexiones de punto a sitio en la puerta de enlace.    |
|**P2SConnectionCount**| Recuento  | 1 minuto  | Recuento de las conexiones de punto a sitio en la puerta de enlace.   |
|**TunnelAverageBandwidth** | Bytes por segundo    | 5 minutos  | Promedio de utilización del ancho de banda de los túneles creados en la puerta de enlace. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfico saliente en los túneles creados en la puerta de enlace.   |
|**TunnelEgressPackets** | Recuento | 5 minutos | Recuento de los paquetes salientes en los túneles creados en la puerta de enlace.   |
|**TunnelEgressPacketDropTSMismatch** | Recuento | 5 minutos | Recuento de los paquetes salientes eliminados de los túneles por un error de coincidencia del selector de tráfico. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfico entrante en los túneles creados en la puerta de enlace.   |
|**TunnelIngressPackets** | Recuento | 5 minutos | Recuento de los paquetes entrantes en los túneles creados en la puerta de enlace.   |
|**TunnelIngressPacketDropTSMismatch** | Recuento | 5 minutos | Recuento de los paquetes entrantes eliminados de los túneles por un error de coincidencia del selector de tráfico. |


## <a name="setup"></a>Configuración de alertas de Azure Monitor basadas en métricas mediante Azure Portal

En los pasos del ejemplo siguiente se creará una alerta en una puerta de enlace para:

- **Métrica:** TunnelAverageBandwidth
- **Condición:** Ancho de banda > 10 bytes por segundo
- **Ventana:** 5 minutos
- **Acción de alerta:** Email



1. Vaya al recurso de puerta de enlace de red virtual y seleccione **Alertas** en la pestaña **Supervisión**. Después, cree una regla de alertas o modifique una existente.

   ![Selecciones para crear una regla de alertas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Crear")

2. Seleccione la instancia de VPN Gateway como el recurso.

   ![El botón Seleccionar y la instancia de VPN Gateway en la lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Seleccionar")

3. Seleccione una métrica para configurar la alerta.

   ![Métrica seleccionada en la lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Seleccionar")
4. Configure la lógica de señal. Hay tres componentes:

    a. **Dimensiones** Si la métrica tiene dimensiones, puede seleccionar valores de dimensión específicos para que la alerta solo evalúe los datos de esa dimensión. Estos son opcionales.

    b. **Condición**: Es la operación para evaluar el valor de métrica.

    c. **Tiempo**: Especifique la granularidad de los datos de la métrica y el período de tiempo para evaluar la alerta.

   ![Detalles de configuración de la lógica de señal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Seleccionar")

5. Para ver las reglas configuradas, seleccione **Administrar reglas de alertas**.

   ![Botón para administrar las reglas de alertas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Seleccionar")

## <a name="next-steps"></a>Pasos siguientes

Para configurar alertas en los registros de diagnóstico de túnel, vea [Configuración de alertas en los registros de diagnóstico de VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
