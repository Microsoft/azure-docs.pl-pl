---
title: Jak wyłączyć funkcje w usłudze Azure Functions
description: Dowiedz się, jak wyłączyć i włączyć funkcje w usłudze Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 11585e92e7d239731b02d06c5093f979cd65cfba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686883"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w usłudze Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w usłudze Azure Functions. Aby *wyłączyć* funkcję oznacza, aby środowisko uruchomieniowe zignorować automatyczny wyzwalacz, który jest zdefiniowany dla funkcji. Dzięki temu można zapobiec uruchamianie określonej funkcji bez zatrzymywania całej aplikacji funkcji.

Zalecanym sposobem wyłączenia funkcji jest użycie ustawienia `AzureWebJobs.<FUNCTION_NAME>.Disabled`aplikacji w formacie . To ustawienie aplikacji można tworzyć i modyfikować na wiele sposobów, w tym za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i karty Zarządzanie **funkcją** w [witrynie Azure portal](https://portal.azure.com). 

> [!NOTE]  
> Po wyłączeniu funkcji wyzwalanej http przy użyciu metod opisanych w tym artykule, punkt końcowy może nadal dostępne podczas uruchamiania na komputerze lokalnym.  

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

W interfejsu wiersza polecenia [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) platformy Azure można użyć polecenia do tworzenia i modyfikowania ustawienia aplikacji. Następujące polecenie wyłącza funkcję `QueueTrigger` o nazwie, tworząc `AzureWebJobs.QueueTrigger.Disabled` ustawienie `true`aplikacji o nazwie ustawiło ją na . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, uruchom ponownie to samo `false`polecenie o wartości .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Korzystanie z portalu

Można również użyć przełącznika **Stan funkcji** na karcie **Zarządzanie** funkcją. Przełącznik działa przez utworzenie i usunięcie `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawienia aplikacji.

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

> [!NOTE]  
> Funkcja testowania zintegrowana z `Disabled` portalem ignoruje to ustawienie. Oznacza to, że wyłączona funkcja nadal działa po uruchomieniu z okna **testowego** w portalu. 

## <a name="other-methods"></a>Inne metody

Chociaż metoda ustawiania aplikacji jest zalecana dla wszystkich języków i wszystkich wersji środowiska uruchomieniowego, istnieje kilka innych sposobów wyłączania funkcji. Te metody, które różnią się w zależności od języka i wersji środowiska uruchomieniowego, są obsługiwane w celu zapewnienia zgodności z powrotem. 

### <a name="c-class-libraries"></a>Biblioteki klas języka C#

W funkcji biblioteki klas można `Disable` również użyć atrybutu, aby zapobiec wyzwalaniu funkcji. Można użyć atrybutu bez parametru konstruktora, jak pokazano w poniższym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Atrybut bez parametru konstruktora wymaga ponownej kompilacji i ponownego rozmieszczenia projektu w celu zmiany stanu wyłączonej funkcji. Bardziej elastycznym sposobem użycia atrybutu jest dołączenie parametru konstruktora, który odwołuje się do ustawienia aplikacji logicznej, jak pokazano w poniższym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Ta metoda umożliwia włączenie i wyłączenie funkcji, zmieniając ustawienie aplikacji bez ponownego komppilowania lub ponownego rozmieszczenia. Zmiana ustawienia aplikacji powoduje ponowne uruchomienie aplikacji funkcji, dzięki czemu zmiana stanu wyłączonego jest rozpoznawana natychmiast.

> [!IMPORTANT]
> Atrybut `Disabled` jest jedynym sposobem, aby wyłączyć funkcję biblioteki klas. Wygenerowany plik *function.json* dla funkcji biblioteki klas nie jest przeznaczony do bezpośredniej edycji. Jeśli edytujesz ten plik, `disabled` cokolwiek zrobisz z właściwością nie będzie miało wpływu.
>
> To samo dotyczy przełącznika **stan funkcji** na karcie **Zarządzaj,** ponieważ działa on poprzez zmianę pliku *function.json.*
>
> Należy również zauważyć, że portal może wskazywać, że funkcja jest wyłączona, gdy nie jest.

### <a name="functions-1x---scripting-languages"></a>Funkcje 1.x - języki skryptów

W wersji 1.x można również `disabled` użyć właściwości pliku *function.json,* aby poinformować środowisko wykonawcze, aby nie wyzwalać funkcji. Ta metoda działa tylko w językach skryptów, takich jak skrypt C# i JavaScript. Właściwość `disabled` można ustawić `true` lub nazwę ustawienia aplikacji:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
lub 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o `true` nazwie IS_DISABLED i jest ustawiona na lub 1.

Można edytować plik w witrynie Azure portal lub użyć **przełącznika stan funkcji** na karcie **Zarządzanie** funkcją. Przełącznik portalu działa poprzez zmianę pliku *function.json.*

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Następne kroki

Ten artykuł dotyczy wyłączania automatycznych wyzwalaczy. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [Wyzwalacze i powiązania](functions-triggers-bindings.md).
