---
title: Jak wyłączyć funkcje w Azure Functions
description: Dowiedz się, jak wyłączyć i włączyć funkcje w Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1ad484804f66a2e2d4d0f1da4a37cf0d6c485f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584741"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w Azure Functions. Aby *wyłączyć* funkcję oznacza, że środowisko uruchomieniowe zignoruje automatyczny wyzwalacz, który jest zdefiniowany dla funkcji. Dzięki temu można zapobiec uruchamianiu określonej funkcji bez zatrzymywania całej aplikacji funkcji.

Zalecanym sposobem wyłączenia funkcji jest ustawienie aplikacji w formacie `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawionym na `true` . To ustawienie aplikacji można utworzyć i zmodyfikować na wiele sposobów, w tym przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/) oraz z karty **przegląd** funkcji w [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Po wyłączeniu funkcji wyzwalanej przez protokół HTTP przy użyciu metod opisanych w tym artykule punkt końcowy może nadal być dostępny, gdy działa na komputerze lokalnym.  

## <a name="disable-a-function"></a>Wyłączanie funkcji

# <a name="portal"></a>[Portal](#tab/portal)

Użyj przycisków **Włącz** i **Wyłącz** na stronie **Przegląd** funkcji. Te przyciski działają, zmieniając wartość `AzureWebJobs.<FUNCTION_NAME>.Disabled` Ustawienia aplikacji. To ustawienie specyficzne dla funkcji jest tworzone po raz pierwszy. 

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

Nawet w przypadku publikowania w aplikacji funkcji z projektu lokalnego można nadal używać portalu do wyłączania funkcji w aplikacji funkcji. 

> [!NOTE]  
> Funkcja testowania zintegrowanego z portalem ignoruje `Disabled` ustawienie. Oznacza to, że wyłączona funkcja nadal działa po uruchomieniu z okna **testowego** w portalu. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

W interfejsie wiersza polecenia platformy Azure można użyć [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) polecenie, aby utworzyć i zmodyfikować ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled` i ustawienie jej na `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie o wartości `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Polecenie dodaje lub aktualizuje ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled` i ustawienie jej na `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie o wartości `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funkcje w gnieździe

Domyślnie ustawienia aplikacji mają zastosowanie również do aplikacji uruchamianych w miejscach wdrożenia. Można jednak zastąpić ustawienie aplikacji używane przez gniazdo, ustawiając ustawienie aplikacji specyficzne dla gniazda. Na przykład może być wymagana aktywna funkcja w środowisku produkcyjnym, ale nie podczas testowania wdrożenia, takich jak funkcja wyzwalana przez czasomierz. 

Aby wyłączyć funkcję tylko w miejscu przejściowym:

# <a name="portal"></a>[Portal](#tab/portal)

Przejdź do wystąpienia gniazda aplikacji funkcji, wybierając pozycję miejsca **wdrożenia** w obszarze **wdrażanie**, wybierając miejsce i wybierając pozycję **funkcje** w wystąpieniu gniazda.  Wybierz funkcję, a następnie użyj przycisków **Włącz** i **Wyłącz** na stronie **Przegląd** funkcji. Te przyciski działają, zmieniając wartość `AzureWebJobs.<FUNCTION_NAME>.Disabled` Ustawienia aplikacji. To ustawienie specyficzne dla funkcji jest tworzone po raz pierwszy. 

Można również bezpośrednio dodać ustawienie aplikacji o nazwie `AzureWebJobs.<FUNCTION_NAME>.Disabled` za pomocą wartości `true` w **konfiguracji** wystąpienia gniazda. Po dodaniu ustawienia aplikacji dotyczącej gniazda upewnij się, że jest zaznaczone pole **Ustawienia miejsce wdrożenia** . Pozwala to zachować wartość ustawienia w gnieździe podczas zamiany.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie o wartości `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell obecnie nie obsługuje tej funkcji.

---

Aby dowiedzieć się więcej, zobacz [Azure Functions miejsc wdrożenia](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

Funkcje można wyłączyć w taki sam sposób, jak w przypadku uruchamiania lokalnego. Aby wyłączyć funkcję o nazwie `HttpExample` , Dodaj wpis do kolekcji wartości w local.settings.jsw pliku w następujący sposób:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Inne metody

Mimo że metoda ustawienia aplikacji jest zalecana dla wszystkich języków i wszystkich wersji środowiska uruchomieniowego, istnieje kilka innych sposobów wyłączania funkcji. Te metody, które różnią się w zależności od języka i wersji środowiska uruchomieniowego, są utrzymywane na potrzeby zgodności z poprzednimi wersjami 

### <a name="c-class-libraries"></a>Biblioteki klas języka C#

W funkcji biblioteki klas można również użyć `Disable` atrybutu, aby zapobiec wyzwoleniu funkcji. Ten atrybut umożliwia dostosowanie nazwy ustawienia używanego do wyłączania funkcji. Użyj wersji atrybutu, która pozwala zdefiniować parametr konstruktora, który odwołuje się do ustawienia aplikacji logicznej, jak pokazano w następującym przykładzie:

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

Ta metoda pozwala włączać i wyłączać funkcję przez zmianę ustawienia aplikacji, bez ponownej kompilacji lub ponownego wdrożenia. Zmiana ustawienia aplikacji powoduje, że aplikacja funkcji zostanie ponownie uruchomiona, więc zmiana stanu wyłączenia zostanie natychmiast rozpoznana.

Istnieje również Konstruktor dla parametru, który nie akceptuje ciągu dla nazwy ustawienia. Ta wersja atrybutu nie jest zalecana. W przypadku korzystania z tej wersji należy ponownie skompilować i wdrożyć projekt, aby zmienić stan wyłączenia funkcji.

### <a name="functions-1x---scripting-languages"></a>Functions 1. x — Języki obsługi skryptów

W wersji 1. x można także użyć `disabled` właściwości *function.jsw* pliku, aby poinformować, że środowisko uruchomieniowe nie wyzwala funkcji. Ta metoda działa tylko w przypadku języków skryptów, takich jak skrypt C# i JavaScript. `disabled`Właściwość może mieć wartość `true` lub na nazwę ustawienia aplikacji:

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

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o nazwie IS_DISABLED i jest ustawione na `true` lub 1.

>[!IMPORTANT]  
>Portal używa ustawień aplikacji do wyłączenia funkcji v1. x. Gdy ustawienia aplikacji powodują konflikt z function.jsw pliku, może wystąpić błąd. Należy usunąć `disabled` Właściwość z function.jsw pliku, aby uniknąć błędów. 


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano wyłączanie wyzwalaczy automatycznych. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md).
