---
title: Samouczek — wyzwalanie grupy kontenerów przez funkcję platformy Azure
description: Tworzenie wyzwalanej przez protokół HTTP bezserwerowej funkcji programu PowerShell w celu zautomatyzowania tworzenia wystąpień kontenera platformy Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331029"
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

> [!IMPORTANT]
> Program PowerShell dla Azure Functions jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

Zobacz [Tworzenie pierwszej funkcji na platformie Azure](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) , aby uzyskać wymagania wstępne dotyczące instalowania i używania Visual Studio Code z Azure Functions w systemie operacyjnym.

W niektórych krokach w tym artykule jest używany interfejs wiersza polecenia platformy Azure. Aby wykonać te kroki, można użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Tworzenie podstawowej funkcji programu PowerShell

Wykonaj kroki opisane w temacie [Tworzenie pierwszej funkcji programu PowerShell na platformie Azure](../azure-functions/functions-create-first-function-powershell.md) , aby utworzyć funkcję programu PowerShell przy użyciu szablonu wyzwalacza http. Użyj domyślnej nazwy funkcji platformy Azure **HttpTrigger**. Jak pokazano w przewodniku Szybki Start, Przetestuj funkcję lokalnie i Opublikuj projekt w aplikacji funkcji na platformie Azure. Ten przykład jest podstawową funkcją wyzwalaną przez protokół HTTP, która zwraca ciąg tekstowy. W kolejnych krokach tego artykułu zmodyfikujesz funkcję w celu utworzenia grupy kontenerów.

W tym artykule przyjęto założenie, że projekt jest publikowany przy użyciu nazwy *myfunctionapp*w grupie zasobów platformy Azure automatycznie nazwanej według nazwy aplikacji funkcji (również *myfunctionapp*). W kolejnych krokach Zastąp unikatową nazwę aplikacji funkcji i nazwę grupy zasobów.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Włączanie tożsamości zarządzanej przez platformę Azure w aplikacji funkcji

Teraz Włącz [zarządzaną tożsamość](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) przypisaną przez system w aplikacji funkcji. Host programu PowerShell z uruchomioną aplikacją może automatycznie uwierzytelniać się przy użyciu tej tożsamości, włączając funkcje do wykonywania działań w ramach usług platformy Azure, do których tożsamość uzyskała dostęp. W tym samouczku przyznasz uprawnienia zarządzanej tożsamości do tworzenia zasobów w grupie zasobów aplikacji funkcji. 

Najpierw użyj polecenia [AZ Group Show][az-group-show] , aby uzyskać identyfikator grupy zasobów aplikacji funkcji i zapisać ją w zmiennej środowiskowej. W tym przykładzie przyjęto założenie, że polecenie jest uruchamiane w bash Shell.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Uruchom [AZ functionapp Identity App Assign][az-functionapp-identity-app-assign] , aby przypisać lokalną tożsamość do aplikacji funkcji i przypisać rolę współautor do grupy zasobów. Ta rola umożliwia tożsamości tworzenie dodatkowych zasobów, takich jak grupy kontenerów w grupie zasobów.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modyfikuj funkcję HttpTrigger

Zmodyfikuj kod programu PowerShell dla funkcji **HttpTrigger** , aby utworzyć grupę kontenerów. W pliku `run.ps1` dla funkcji Znajdź następujący blok kodu. Ten kod wyświetla wartość nazwy, jeśli jest ona przenoszona jako ciąg zapytania w adresie URL funkcji:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Zastąp ten kod następującym przykładowym blokiem. W tym miejscu, jeśli wartość nazwy jest przenoszona w ciągu zapytania, służy do napełniania nazw i tworzenia grupy kontenerów za pomocą polecenia cmdlet [New-AzContainerGroup][new-azcontainergroup] . Upewnij się, że nazwa grupy zasobów *myfunctionapp* została zamieniona na nazwę grupy zasobów dla aplikacji funkcji:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Ten przykład tworzy grupę kontenerów składającą się z jednego wystąpienia kontenera z `alpine` uruchomionym obrazem. Kontener uruchamia pojedyncze `echo` polecenie, a następnie kończy działanie. W rzeczywistym przykładzie można wyzwolić utworzenie jednej lub większej liczby grup kontenerów na potrzeby uruchamiania zadania usługi Batch.
 
## <a name="test-function-app-locally"></a>Lokalna aplikacja funkcji testowej

Upewnij się, że funkcja jest uruchamiana prawidłowo lokalnie przed ponownym opublikowaniem projektu aplikacji funkcji na platformie Azure. Jak pokazano na [przewodniku Szybki Start programu PowerShell](../azure-functions/functions-create-first-function-powershell.md), Wstaw lokalny punkt przerwania w skrypcie `Wait-Debugger` programu PowerShell i wywołanie powyżej niego. Wskazówki dotyczące debugowania znajdują się w sekcji [debugowanie programu PowerShell Azure Functions lokalnie](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Ponowne publikowanie aplikacji funkcji platformy Azure

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, należy ponownie opublikować projekt w istniejącej aplikacji funkcji na platformie Azure.

> [!NOTE]
> Pamiętaj, aby usunąć wszystkie wywołania `Wait-Debugger` przed opublikowaniem funkcji na platformie Azure.

1. W Visual Studio Code Otwórz paletę poleceń. Wyszukaj i wybierz `Azure Functions: Deploy to function app...`.
1. Wybierz bieżący folder roboczy do pliku zip i Wdróż.
1. Wybierz subskrypcję, a następnie nazwę istniejącej aplikacji funkcji (*myfunctionapp*). Potwierdź, że chcesz zastąpić poprzednie wdrożenie.

Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym zaktualizowane zasoby platformy Azure.

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

Po pomyślnym zakończeniu wdrożenia Pobierz adres URL funkcji. Na przykład użyj obszaru **Azure: Functions** w programie Visual Studio Code, aby skopiować adres URL funkcji **HttpTrigger** lub uzyskać adres URL funkcji w [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Adres URL funkcji zawiera unikatowy kod i ma postać:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Uruchom funkcję bez przekazywania nazwy

Jako pierwszy test Uruchom `curl` polecenie i przekaż adres URL funkcji bez dołączania ciągu `name` zapytania. Upewnij się, że dołączysz unikatowy kod funkcji.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Funkcja zwraca kod stanu 400 i tekst `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Uruchom funkcję i przekaż nazwę grupy kontenerów

Teraz uruchom `curl` polecenie, dołączając nazwę grupy kontenerów (obiektu*kontenera*) jako ciąg `&name=mycontainergroup`zapytania:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funkcja zwraca kod stanu 200 i wyzwala Tworzenie grupy kontenerów:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Sprawdź, czy kontener został uruchomiony przy użyciu polecenia [AZ Container Logs][az-container-logs] :

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Przykładowe dane wyjściowe:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów utworzonych w tym samouczku, możesz uruchomić polecenie [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby. To polecenie usuwa utworzony rejestr kontenerów, a także uruchomiony kontener i wszystkie powiązane zasoby.

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

Zapoznaj się z [dokumentacją Azure Functions](/azure/azure-functions/) , aby uzyskać szczegółowe wskazówki dotyczące tworzenia usługi Azure Functions i publikowania projektu funkcji. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
