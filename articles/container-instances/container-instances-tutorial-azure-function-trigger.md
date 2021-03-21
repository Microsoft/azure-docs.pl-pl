---
title: Samouczek — wyzwalanie grupy kontenerów przez funkcję platformy Azure
description: Tworzenie wyzwalanej przez protokół HTTP bezserwerowej funkcji programu PowerShell w celu zautomatyzowania tworzenia wystąpień kontenera platformy Azure
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935418"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Samouczek: Tworzenie grupy kontenerów za pomocą funkcji platformy Azure wyzwalanej przez protokół HTTP

[Azure Functions](../azure-functions/functions-overview.md) to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie skryptów lub kodu w odpowiedzi na różne zdarzenia, takie jak żądanie HTTP, czasomierz lub komunikat w kolejce usługi Azure Storage.

W tym samouczku utworzysz funkcję platformy Azure, która przyjmuje żądanie HTTP i wyzwala wdrożenie [grupy kontenerów](container-instances-container-groups.md). W tym przykładzie przedstawiono podstawowe informacje dotyczące używania Azure Functions do automatycznego tworzenia zasobów w Azure Container Instances. Zmodyfikuj lub Zwiększ przykład dla bardziej złożonych scenariuszy lub innych wyzwalaczy zdarzeń. 

Omawiane kwestie:

> [!div class="checklist"]
> * Użyj Visual Studio Code z [rozszerzeniem Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) , aby utworzyć podstawową funkcję programu PowerShell wyzwalaną przez protokół http.
> * Włącz tożsamość w aplikacji funkcji i nadaj jej uprawnienia do tworzenia zasobów platformy Azure.
> * Zmodyfikuj i ponownie Opublikuj funkcję programu PowerShell w celu zautomatyzowania wdrożenia grupy kontenerów z jednym kontenerem.
> * Sprawdź wdrożenie wyzwalane przez protokół HTTP.

## <a name="prerequisites"></a>Wymagania wstępne

Zobacz [Tworzenie pierwszej funkcji na platformie Azure, korzystając z Visual Studio Code](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment) wymagań wstępnych dotyczących instalowania i używania Visual Studio Code z rozszerzeniem Azure Functions w systemie operacyjnym.

W dodatkowych krokach w tym artykule użyto Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić program, zobacz [instalowanie Azure PowerShell][azure-powershell-install] i [Logowanie do platformy Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Tworzenie podstawowej funkcji programu PowerShell

Wykonaj kroki opisane w temacie [Tworzenie pierwszej funkcji programu PowerShell na platformie Azure](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) , aby utworzyć funkcję programu PowerShell przy użyciu szablonu wyzwalacza http. Użyj domyślnej nazwy funkcji platformy Azure **HttpTrigger**. Jak pokazano w przewodniku Szybki Start, Przetestuj funkcję lokalnie i Opublikuj projekt w aplikacji funkcji na platformie Azure. Ten przykład jest podstawową funkcją wyzwalaną przez protokół HTTP, która zwraca ciąg tekstowy. W kolejnych krokach tego artykułu zmodyfikujesz funkcję w celu utworzenia grupy kontenerów.

W tym artykule przyjęto założenie, że projekt jest publikowany przy użyciu nazwy *myfunctionapp* w grupie zasobów platformy Azure automatycznie nazwanej według nazwy aplikacji funkcji (również *myfunctionapp*). W kolejnych krokach Zastąp unikatową nazwę aplikacji funkcji i nazwę grupy zasobów.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Włączanie tożsamości zarządzanej przez platformę Azure w aplikacji funkcji

Następujące polecenia umożliwiają włączenie [zarządzanej tożsamości](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) przypisanej do systemu w aplikacji funkcji. Host programu PowerShell z uruchomioną aplikacją może automatycznie uwierzytelniać się na platformie Azure przy użyciu tej tożsamości, włączając funkcje do wykonywania działań w ramach usług platformy Azure, do których uzyskuje się dostęp. W tym samouczku przyznasz uprawnienia zarządzanej tożsamości do tworzenia zasobów w grupie zasobów aplikacji funkcji. 

[Dodaj tożsamość](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) do aplikacji funkcji:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Przypisz tożsamość rola współautor objęta zakresem do grupy zasobów:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Modyfikuj funkcję HttpTrigger

Zmodyfikuj kod programu PowerShell dla funkcji **HttpTrigger** , aby utworzyć grupę kontenerów. W pliku `run.ps1` dla funkcji Znajdź następujący blok kodu. Ten kod wyświetla wartość nazwy, jeśli jest ona przenoszona jako ciąg zapytania w adresie URL funkcji:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Zastąp ten kod następującym przykładowym blokiem. W tym miejscu, jeśli wartość nazwy jest przenoszona w ciągu zapytania, służy do napełniania nazw i tworzenia grupy kontenerów za pomocą polecenia cmdlet [New-AzContainerGroup][new-azcontainergroup] . Upewnij się, że nazwa grupy zasobów *myfunctionapp* została zamieniona na nazwę grupy zasobów dla aplikacji funkcji:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Ten przykład tworzy grupę kontenerów składającą się z jednego wystąpienia kontenera z uruchomionym `alpine` obrazem. Kontener uruchamia pojedyncze `echo` polecenie, a następnie kończy działanie. W rzeczywistym przykładzie można wyzwolić utworzenie jednej lub większej liczby grup kontenerów na potrzeby uruchamiania zadania usługi Batch.
 
## <a name="test-function-app-locally"></a>Lokalna aplikacja funkcji testowej

Upewnij się, że funkcja jest uruchamiana lokalnie przed ponownym opublikowaniem projektu aplikacji funkcji na platformie Azure. W przypadku uruchamiania lokalnego funkcja nie tworzy zasobów platformy Azure. Można jednak testować przepływ funkcji za pomocą i bez przekazywania wartości nazwy w ciągu zapytania. Aby debugować funkcję, zobacz [debugowanie programu PowerShell Azure Functions lokalnie](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Ponowne publikowanie aplikacji funkcji platformy Azure

Po sprawdzeniu, że funkcja jest uruchamiana lokalnie, opublikuj ponownie projekt w istniejącej aplikacji funkcji na platformie Azure.

1. W programie Visual Studio Code otwórz paletę poleceń. Wyszukaj i wybierz `Azure Functions: Deploy to Function App...` .
1. Wybierz bieżący folder roboczy do pliku zip i Wdróż.
1. Wybierz subskrypcję, a następnie nazwę istniejącej aplikacji funkcji (*myfunctionapp*). Potwierdź, że chcesz zastąpić poprzednie wdrożenie.

Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym zaktualizowane zasoby platformy Azure.

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

Po pomyślnym zakończeniu wdrożenia Pobierz adres URL funkcji. Na przykład użyj obszaru **Azure: Functions** w Visual Studio Code, aby skopiować adres URL funkcji **HttpTrigger** lub uzyskać adres URL funkcji w [Azure Portal](../azure-functions/functions-get-started.md).

Adres URL funkcji ma postać:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Uruchom funkcję bez przekazywania nazwy

Jako pierwszy test Uruchom `curl` polecenie i przekaż adres URL funkcji bez dołączania `name` ciągu zapytania. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

Funkcja zwraca kod stanu 200 i tekst `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` :

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Uruchom funkcję i przekaż nazwę grupy kontenerów

Teraz uruchom `curl` polecenie i Dołącz nazwę grupy kontenerów (obiektu *kontenera*) jako ciąg zapytania `?name=mycontainergroup` :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

Funkcja zwraca kod stanu 200 i wyzwala Tworzenie grupy kontenerów:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Sprawdź, czy kontener został uruchomiony przy użyciu polecenia [Get-AzContainerInstanceLog][get-azcontainerinstancelog] :

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Przykładowe dane wyjściowe:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już żadnych zasobów utworzonych w tym samouczku, możesz wykonać polecenie [AZ Group DELETE] [AZ-Group-DELETE], aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby. To polecenie usuwa utworzoną aplikację funkcji, a także uruchomiony kontener i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono funkcję platformy Azure, która przyjmuje żądanie HTTP i wyzwala wdrożenie grupy kontenerów. W tym samouczku omówiono:

> [!div class="checklist"]
> * Użyj Visual Studio Code z rozszerzeniem Azure Functions, aby utworzyć podstawową funkcję programu PowerShell wyzwalaną przez protokół HTTP.
> * Włącz tożsamość w aplikacji funkcji i nadaj jej uprawnienia do tworzenia zasobów platformy Azure.
> * Zmodyfikuj kod funkcji programu PowerShell w celu zautomatyzowania wdrożenia grupy kontenerów z jednym kontenerem.
> * Sprawdź wdrożenie wyzwalane przez protokół HTTP.

Aby zapoznać się ze szczegółowym przykładem uruchamiania i monitorowania zadania kontenerowego, zapoznaj się z informacjami o [kontenerach Bezserwerowych opartych na zdarzeniach za pomocą programu PowerShell Azure Functions i Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) i [kodu](https://github.com/anthonychu/functions-powershell-run-aci)towarzyszącego.

Zapoznaj się z [dokumentacją Azure Functions](../azure-functions/index.yml) , aby uzyskać szczegółowe wskazówki dotyczące tworzenia usługi Azure Functions i publikowania projektu funkcji. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
