---
title: 'Samouczek: Rozwiązywanie problemów z Azure Monitor'
description: Dowiedz się, jak Azure Monitor i Log Analytics ułatwiają monitorowanie aplikacji sieci Web App Service. Azure Monitor maksymalizuje dostępność, dostarczając kompleksowe rozwiązanie do monitorowania środowiska.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 049adac5521efd68ae8aa77af2d1007f9dfe0c0e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586996"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Samouczek: Rozwiązywanie problemów z aplikacją App Service przy użyciu Azure Monitor

> [!NOTE]
> Integracja Azure Monitor z App Service jest w [wersji zapoznawczej](https://aka.ms/appsvcblog-azmon).
>

W tym samouczku pokazano, jak rozwiązywać problemy z aplikacją [App Service](overview.md) przy użyciu [Azure monitor](../azure-monitor/overview.md). Przykładowa aplikacja zawiera kod przeznaczony do wyczerpania pamięci i powoduje błędy HTTP 500, dzięki czemu można zdiagnozować i rozwiązać problem przy użyciu Azure Monitor. Po zakończeniu będziesz mieć przykładową aplikację działającą na App Service w systemie Linux zintegrowanym z [Azure monitor](../azure-monitor/overview.md).

[Azure monitor](../azure-monitor/overview.md) maksymalizuje dostępność i wydajność aplikacji i usług, udostępniając kompleksowe rozwiązanie do zbierania, analizowania i działania na telemetrii z chmur i środowisk lokalnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie aplikacji sieci Web za pomocą Azure Monitor
> * Wyślij dzienniki konsoli do Log Analytics
> * Używanie zapytań dzienników do identyfikowania i rozwiązywania problemów z błędami aplikacji sieci Web

Kroki opisane w tym samouczku można wykonać w systemie macOS, Linux i Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności w tym samouczku potrzebne są:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Narzędzia](https://git-scm.com/)

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Najpierw uruchom kilka poleceń lokalnie, aby skonfigurować przykładową aplikację do użycia z tym samouczkiem. Polecenia klonować przykładowej aplikacji, tworzenia zasobów platformy Azure, tworzenia użytkownika wdrożenia i wdrażania aplikacji na platformie Azure. Zostanie wyświetlony monit o podanie hasła dostarczonego w ramach tworzenia użytkownika wdrożenia. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Konfigurowanie Azure Monitor (wersja zapoznawcza)

### <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego Log Analytics

Po wdrożeniu przykładowej aplikacji do Azure App Service skonfigurujesz możliwość monitorowania w celu rozwiązywania problemów z aplikacją w przypadku wystąpienia problemów. Azure Monitor przechowuje dane dzienników w obszarze roboczym Log Analytics. Obszar roboczy to kontener zawierający dane i informacje o konfiguracji.

W tym kroku utworzysz obszar roboczy Log Analytics, aby skonfigurować Azure Monitor za pomocą aplikacji.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [W przypadku Azure Monitor Log Analytics płacisz za pozyskiwanie i przechowywanie danych.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne

Za pomocą ustawień diagnostycznych można zbierać metryki dla określonych usług platformy Azure w Azure Monitor dzienników do analizy z innymi danymi monitorowania przy użyciu zapytań dzienników. W tym samouczku zostanie włączony serwer sieci Web i dzienniki standardowego wyjścia/błędów. Aby uzyskać pełną listę typów dzienników i opisów, zobacz [obsługiwane typy dzienników](./troubleshoot-diagnostic-logs.md#supported-log-types) .

Uruchom następujące polecenia, aby utworzyć ustawienia diagnostyczne dla AppServiceConsoleLogs (wyjście standardowe/błąd) i AppServiceHTTPLogs (Dzienniki serwera sieci Web). Zastąp _\<app-name>_ _\<workspace-name>_ wartości i wartościami. 

> [!NOTE]
> Pierwsze dwa polecenia `resourceID` i `workspaceID` , są zmiennymi, które mają być używane w `az monitor diagnostic-settings create` poleceniu. Aby uzyskać więcej informacji na temat tego polecenia, zobacz [Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli) .
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Rozwiązywanie problemów z aplikacją

Przejdź na stronę `http://<app-name>.azurewebsites.net`.

Przykładowa aplikacja, ImageConverter, konwertuje dołączone obrazy z `JPG` do `PNG` . Błąd został celowo umieszczony w kodzie dla tego samouczka. W przypadku wybrania wystarczającej liczby obrazów aplikacja generuje błąd HTTP 500 podczas konwersji obrazu. Wyobraź sobie, że ten scenariusz nie został uwzględniony w fazie opracowywania. Aby rozwiązać ten problem, użyj Azure Monitor.

### <a name="verify-the-app-is-works"></a>Sprawdź, czy aplikacja działa

Aby skonwertować obrazy, kliknij `Tools` i wybierz `Convert to PNG` .

![Kliknij pozycję "narzędzia" i wybierz pozycję "Konwertuj na PNG"](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Wybierz dwa pierwsze obrazy, a następnie kliknij przycisk `convert` . Konwersja zostanie zakończona pomyślnie.

![Wybierz pierwsze dwa obrazy](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Przerwij aplikację

Teraz, gdy aplikacja została zweryfikowana przez konwersję dwóch obrazów, spróbujemy skonwertować pierwsze pięć obrazów.

![Konwertuj pierwsze pięć obrazów](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Ta akcja kończy się niepowodzeniem i generuje `HTTP 500` błąd, który nie został przetestowany podczas opracowywania.

![Wynikiem konwersji będzie błąd HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Używanie zapytania dziennika do wyświetlania dzienników Azure Monitor

Zobaczmy, jakie dzienniki są dostępne w obszarze roboczym Log Analytics. 

Kliknij [link log Analytics obszaru roboczego](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) , aby uzyskać dostęp do obszaru roboczego w Azure Portal.

W Azure Portal wybierz obszar roboczy Log Analytics.

### <a name="log-queries"></a>Rejestrowanie zapytań

Zapytania dzienników ułatwiają całkowite wykorzystanie wartości danych zebranych w dziennikach Azure Monitor. Zapytania dzienników służą do identyfikowania dzienników zarówno AppServiceHTTPLogs, jak i AppServiceConsoleLogs. Aby uzyskać więcej informacji na temat zapytań dzienników, zobacz [Omówienie zapytania dziennika](../azure-monitor/log-query/log-query-overview.md) .

### <a name="view-appservicehttplogs-with-log-query"></a>Wyświetl AppServiceHTTPLogs z zapytaniem dziennika

Po uzyskaniu dostępu do aplikacji wyświetlmy dane skojarzone z żądaniami HTTP, które znajdują się w temacie `AppServiceHTTPLogs` .

1. Kliknij `Logs` w lewym okienku nawigacji.

![Dzienniki dzienników Anlytics Worksace](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Wyszukaj `appservice` i kliknij dwukrotnie `AppServiceHTTPLogs` .

![Tabele obszarów roboczych usługi log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Kliknij pozycję `Run` (Dalej).

![Log Analytics obszarze roboczym App Service dzienników HTTP](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs`Zapytanie zwraca wszystkie żądania w ciągu ostatnich 24 godzin. Kolumna `ScStatus` zawiera stan http. Aby zdiagnozować `HTTP 500` błędy, Ogranicz `ScStatus` do 500 i uruchom zapytanie, jak pokazano poniżej:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Wyświetl AppServiceConsoleLogs z zapytaniem dziennika

Po potwierdzeniu HTTP 500S Przyjrzyjmy się standardowym wyjściem/błędom aplikacji. Te dzienniki znajdują się w "AppServiceConsoleLogs".

(1) kliknij `+` , aby utworzyć nowe zapytanie. 

(2) kliknij dwukrotnie `AppServiceConsoleLogs` tabelę, a następnie kliknij przycisk `Run` . 

Ze względu na to, że konwertowanie pięciu obrazów powoduje błędy serwera, można sprawdzić, czy aplikacja również zapisuje błędy przez filtrowanie pod `ResultDescription` kątem błędów, jak pokazano poniżej:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

W `ResultDescription` kolumnie zostanie wyświetlony następujący błąd:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Dołącz do AppServiceHTTPLogs i AppServiceConsoleLogs

Teraz, po zidentyfikowaniu zarówno HTTP 500S, jak i standardowych błędów, należy potwierdzić, czy istnieje korelacja między tymi komunikatami. Następnie dołączymy tabele w oparciu o sygnaturę czasową `TimeGenerated` .

> [!NOTE]
> Zapytanie zostało przygotowane dla Ciebie, które wykonuje następujące czynności:
>
> - Filtry HTTPLogs dla błędów 500
> - Dzienniki konsoli zapytań
> - Sprzęga tabele w `TimeGenerated`
>

Uruchom poniższe zapytanie:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

W kolumnie zobaczysz `ResultDescription` następujący błąd w tym samym czasie co błędy serwera sieci Web:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

Pamięć Stany komunikatów została wyczerpana w wierszu 20 z `process.php` . Po potwierdzeniu, że aplikacja wygenerowała błąd podczas błędu HTTP 500. Przyjrzyjmy się kodowi, aby zidentyfikować problem.

## <a name="identify-the-error"></a>Identyfikowanie błędu

W katalogu lokalnym Otwórz `process.php` i zapoznaj się z wierszem 20. 

```php
imagepng($imgArray[$x], $filename);
```

Pierwszym argumentem `$imgArray[$x]` jest zmienna przechowująca wszystkie JPGs (w pamięci) wymagające konwersji. Jednak `imagepng` wymaga tylko przekonwertowania obrazu, a nie wszystkich obrazów. Obrazy przed ładowaniem nie są potrzebne i mogą powodować wyczerpanie pamięci, prowadząc do HTTP 500S. Zaktualizujmy kod w celu załadowania obrazów na żądanie, aby sprawdzić, czy problem został rozwiązany. Następnie poprawisz kod w celu rozwiązania problemu z pamięcią.

## <a name="fix-the-app"></a>Napraw aplikację

### <a name="update-locally-and-redeploy-the-code"></a>Lokalne aktualizowanie i ponowne wdrażanie kodu

`process.php`Aby obsłużyć wyczerpanie pamięci, wprowadź następujące zmiany:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Zatwierdź zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź na stronę `http://<app-name>.azurewebsites.net`. 

Konwersja obrazów nie powinna już dawać błędów HTTP 500.

![Aplikacja PHP uruchomiona w usłudze Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Usuń ustawienie diagnostyczne przy użyciu następującego polecenia:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Skonfigurowano aplikację sieci Web przy użyciu Azure Monitor
> * Wysłano dzienniki do Log Analytics
> * Zapytania dzienników używane do identyfikowania i rozwiązywania problemów z błędami aplikacji sieci Web

## <a name="next-steps"></a><a name="nextsteps"></a> Następne kroki
* [Wysyłanie zapytań do dzienników przy użyciu Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w usłudze HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)