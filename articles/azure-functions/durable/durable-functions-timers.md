---
title: 'Temporizadores en Durable Functions: Azure'
description: Aprenda a implementar temporizadores durables en la extensión Durable Functions para Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 463d5e6c253643c82935c82c7dee5996c8e44b5f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706113"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores en Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) proporciona *temporizadores durables* para usarlos en funciones de orquestador para implementar retrasos o configurar tiempos de expiración en acciones asincrónicas. Los temporizadores durables deben usarse en funciones de orquestador en lugar de `Thread.Sleep` y `Task.Delay` (C#), o `setTimeout()` y `setInterval()` (JavaScript).

Puede crear un temporizador durable si llama al método `CreateTimer` (.NET) o al método `createTimer` (JavaScript) del [enlace del desencadenador de orquestación](durable-functions-bindings.md#orchestration-trigger). El método devuelve una tarea que se completa en una fecha y hora especificadas.

## <a name="timer-limitations"></a>Limitaciones de los temporizadores

Cuando se crea un temporizador que expira a las 16:30, la instancia de Durable Task Framework subyacente pone en cola un mensaje que se vuelve visible solo a las 16:30. Al ejecutarse en el plan Consumo de Azure Functions, el mensaje del temporizador ahora visible garantiza que la aplicación de función se active en una máquina virtual adecuada.

> [!NOTE]
> * Los temporizadores durables están limitados actualmente a 7 días. Si se necesitan retrasos más largos, se pueden simular mediante las API del temporizador en un bucle `while`.
> * Use siempre `CurrentUtcDateTime` en lugar de `DateTime.UtcNow` en .NET, o bien `currentUtcDateTime` en lugar de `Date.now` o `Date.UTC` en JavaScript al calcular el tiempo de activación de los temporizadores durables. Para obtener más información, vea el artículo [Restricciones de código de las funciones de orquestador](durable-functions-code-constraints.md).

## <a name="usage-for-delay"></a>Uso para retraso

En el ejemplo siguiente se muestra cómo utilizar temporizadores durables para retrasar la ejecución. En el ejemplo se emite una notificación de facturación cada día durante 10 días.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> El ejemplo de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar `DurableOrchestrationContext` en lugar de `IDurableOrchestrationContext`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

### <a name="javascript-functions-20-only"></a>JavaScript (solo Functions 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evite bucles infinitos en funciones de orquestador. Para más información acerca de cómo implementar de forma segura y eficaz escenarios de bucle infinito, consulte [Orquestaciones infinitas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Uso para tiempo de expiración

En este ejemplo se muestra cómo utilizar temporizadores durables para implementar tiempos de expiración.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> El ejemplo de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar `DurableOrchestrationContext` en lugar de `IDurableOrchestrationContext`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

### <a name="javascript-functions-20-only"></a>JavaScript (solo Functions 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use `CancellationTokenSource` para cancelar un temporizador durable (.NET) o llame a `cancel()` en el elemento `TimerTask` devuelto (JavaScript) si el código no va a esperar a que se complete. Durable Task Framework no cambiará el estado de una orquestación a "Completed" (Completado) hasta que todas las tareas pendientes se hayan completado o cancelado.

Este mecanismo de cancelación no finaliza las ejecuciones de suborquestación o función de actividad en curso. En su lugar, simplemente permite que la función de orquestador pase por alto el resultado y continúe. Si la aplicación de función usa el plan Consumo, se le sigue facturando por el tiempo y la memoria consumidos por la función de actividad abandonada. De manera predeterminada, las funciones en ejecución en el plan de consumo tienen un tiempo de expiración de cinco minutos. Si se supera este límite, el host de Azure Functions se recicla para detener toda la ejecución y evitar una situación de facturación descontrolada. El [tiempo de expiración de la función se puede configurar](../functions-host-json.md#functiontimeout).

Para obtener un ejemplo más detallado de cómo implementar tiempos de espera en funciones de orquestador, vea el artículo [Las interacciones humanas en Durable Functions: comprobación telefónica de ejemplo](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo generar y controlar eventos externos](durable-functions-external-events.md)
