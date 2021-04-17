---
title: Jak wyłączyć funkcje w programie Azure Functions
description: Dowiedz się, jak wyłączać i włączać funkcje w Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ef3886c4b9b73f87238bf386d1320ecbac8ad181
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374900"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w programie Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w Azure Functions. Aby *wyłączyć* funkcję, oznacza to, że środowisko uruchomieniowe ignoruje wyzwalacz automatyczny zdefiniowany dla funkcji. Pozwala to zapobiec uruchamianiu określonej funkcji bez zatrzymywania całej aplikacji funkcji.

Zalecanym sposobem wyłączenia funkcji jest ustawienie aplikacji w formacie `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawionym na `true` . To ustawienie aplikacji można utworzyć i zmodyfikować na wiele sposobów, w tym  przy użyciu interfejsu wiersza polecenia platformy [Azure](/cli/azure/) i na karcie Przegląd funkcji w Azure Portal [.](https://portal.azure.com) 

> [!NOTE]  
> Jeśli wyłączysz funkcję wyzwalaną przez protokół HTTP przy użyciu metod opisanych w tym artykule, punkt końcowy może być nadal dostępny podczas uruchamiania na komputerze lokalnym.  

## <a name="disable-a-function"></a>Wyłączanie funkcji

# <a name="portal"></a>[Portal](#tab/portal)

Użyj **przycisków Włącz** i **Wyłącz** na stronie **Przegląd** funkcji. Te przyciski działają, zmieniając wartość `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawienia aplikacji. To ustawienie specyficzne dla funkcji jest tworzone przy pierwszym wyłączeniu. 

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

Nawet w przypadku publikowania w aplikacji funkcji z projektu lokalnego można nadal używać portalu do wyłączania funkcji w aplikacji funkcji. 

> [!NOTE]  
> Funkcja testowania zintegrowana z portalem ignoruje `Disabled` to ustawienie. Oznacza to, że wyłączona funkcja nadal jest uruchamiana po jej wystartu w **oknie Test** w portalu. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

W interfejsie wiersza polecenia platformy Azure użyj [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) polecenia , aby utworzyć i zmodyfikować ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie i ustawienie go na `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, ponownie uruchomić to samo polecenie z wartością `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Polecenie [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) dodaje lub aktualizuje ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie i ustawienie go na `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Aby ponownie włączyć funkcję, ponownie uruchomić to samo polecenie z wartością `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funkcje w miejscu

Domyślnie ustawienia aplikacji mają również zastosowanie do aplikacji uruchomionych w miejscach wdrożenia. Można jednak zastąpić ustawienie aplikacji używane przez miejsce, ustawiając ustawienie aplikacji specyficzne dla miejsca. Na przykład możesz chcieć, aby funkcja była aktywna w środowisku produkcyjnym, ale nie podczas testowania wdrażania, na przykład funkcji wyzwalanych czasomierzem. 

Aby wyłączyć funkcję tylko w miejscu przejściowym:

# <a name="portal"></a>[Portal](#tab/portal)

Przejdź do wystąpienia miejsca aplikacji funkcji, wybierając pozycję Miejsca wdrożenia w obszarze **Wdrożenie,** wybierając miejsce i wybierając pozycję **Funkcje** w wystąpieniu miejsca.   Wybierz funkcję, a następnie użyj **przycisków Włącz** i **Wyłącz** na stronie **Przegląd** funkcji. Te przyciski działają, zmieniając wartość `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawienia aplikacji. To ustawienie specyficzne dla funkcji jest tworzone przy pierwszym wyłączeniu. 

Możesz również bezpośrednio dodać ustawienie aplikacji o nazwie `AzureWebJobs.<FUNCTION_NAME>.Disabled` z wartością `true` w **konfiguracji** dla wystąpienia miejsca. Podczas dodawania ustawienia aplikacji specyficznego dla miejsca pamiętaj o zaznaczeniu **pola Ustawienie miejsca** wdrożenia. Dzięki temu jest zachowywana wartość ustawienia z miejscem podczas zamian.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Aby ponownie włączyć funkcję, ponownie uruchomić to samo polecenie z wartością `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell obecnie nie obsługuje tej funkcji.

---

Aby dowiedzieć się więcej, [zobacz Azure Functions Wdrożenia.](functions-deployment-slots.md)

## <a name="localsettingsjson"></a>local.settings.json

Funkcje można wyłączyć w taki sam sposób podczas uruchamiania lokalnego. Aby wyłączyć funkcję o nazwie , dodaj wpis do kolekcji Values w `HttpExample` local.settings.jspliku w następujący sposób:

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

Chociaż metoda ustawienia aplikacji jest zalecana dla wszystkich języków i wszystkich wersji środowiska uruchomieniowego, istnieje kilka innych sposobów wyłączania funkcji. Te metody, które różnią się w zależności od języka i wersji środowiska uruchomieniowego, są utrzymywane w celu zachowania zgodności z poprzednimi wersjami. 

### <a name="c-class-libraries"></a>Biblioteki klas języka C#

W funkcji biblioteki klas można również użyć atrybutu , aby `Disable` zapobiec wyzwoleniu funkcji. Ten atrybut umożliwia dostosowanie nazwy ustawienia używanego do wyłączania funkcji. Użyj wersji atrybutu , która umożliwia zdefiniowanie parametru konstruktora, który odwołuje się do ustawienia aplikacji logicznych, jak pokazano w poniższym przykładzie:

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

Ta metoda umożliwia włączenie i wyłączenie funkcji przez zmianę ustawienia aplikacji bez ponownego kompilowania lub ponownego kompilowania. Zmiana ustawienia aplikacji powoduje ponowne uruchomienie aplikacji funkcji, więc zmiana stanu wyłączonego jest rozpoznawana natychmiast.

Istnieje również konstruktor dla parametru, który nie akceptuje ciągu dla nazwy ustawienia. Ta wersja atrybutu nie jest zalecana. Jeśli używasz tej wersji, musisz ponownie skompilować i ponownie wykonać projekt, aby zmienić stan wyłączenia funkcji.

### <a name="functions-1x---scripting-languages"></a>Funkcje 1.x — języki skryptowe

W wersji 1.x można również użyć właściwościfunction.jsw pliku, aby poinformować środowisko uruchomieniowe, aby nie `disabled` wyzwalało funkcji.  Ta metoda działa tylko w przypadku języków skryptowych, takich jak skrypt C# i JavaScript. Właściwość `disabled` można ustawić na lub na nazwę ustawienia `true` aplikacji:

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

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o nazwie IS_DISABLED i ustawione na `true` wartość lub 1.

>[!IMPORTANT]  
>Portal używa ustawień aplikacji, aby wyłączyć funkcje w wersji 1.x. Gdy ustawienie aplikacji powoduje konflikt z function.jspliku, może wystąpić błąd. Należy usunąć właściwość `disabled` z pliku function.js, aby zapobiec błędom. 


## <a name="next-steps"></a>Następne kroki

Ten artykuł dotyczy wyłączania wyzwalaczy automatycznych. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [Wyzwalacze i powiązania](functions-triggers-bindings.md).
