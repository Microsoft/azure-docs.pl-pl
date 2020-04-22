---
title: Włączanie rejestrowania diagnostycznego
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji zarejestrowanych przez platformę Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770000"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service
## <a name="overview"></a>Omówienie
Platforma Azure udostępnia wbudowaną diagnostykę ułatwiając debugowanie [aplikacji usługi App Service.](overview.md) W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji zarejestrowanych przez platformę Azure.

W tym artykule użyto [witryny Azure portal](https://portal.azure.com) i interfejsu wiersza polecenia platformy Azure do pracy z dziennikami diagnostycznymi. Aby uzyskać informacje dotyczące pracy z dziennikami diagnostycznymi przy użyciu programu Visual Studio, zobacz Rozwiązywanie problemów z [platformą Azure w programie Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Oprócz instrukcji rejestrowania w tym artykule istnieje nowa, zintegrowana funkcja rejestrowania za pomocą usługi Azure Monitoring. Więcej informacji na temat tej możliwości znajdziesz w sekcji [Wyślij dzienniki do usługi Azure Monitor (wersja zapoznawcza).](#send-logs-to-azure-monitor-preview) 
>
>

|Typ|Platforma|Lokalizacja|Opis|
|-|-|-|-|
| Rejestrowanie aplikacji | Windows, Linux | System plików usługi App Service i/lub obiekty blob usługi Azure Storage | Rejestruje komunikaty generowane przez kod aplikacji. Wiadomości mogą być generowane przez platformę sieci web, którą wybierzesz lub z kodu aplikacji bezpośrednio przy użyciu standardowego wzorca rejestrowania języka. Każdej wiadomości jest przypisywana jedna z następujących kategorii: **Krytyczna**, **Błąd**, **Ostrzeżenie**, **Informacje**, **Debug**i **Śledzenie**. Można wybrać, jak pełne, że rejestrowanie ma być przez ustawienie poziomu ważności po włączeniu rejestrowania aplikacji.|
| Rejestrowanie serwera sieci Web| Windows | System plików usługi App Service lub obiekty blob usługi Azure Storage| Surowe dane żądania HTTP w [rozszerzonym formacie pliku dziennika W3C](/windows/desktop/Http/w3c-logging). Każdy komunikat dziennika zawiera dane, takie jak metoda HTTP, identyfikator URI zasobu, adres IP klienta, port klienta, agent użytkownika, kod odpowiedzi i tak dalej. |
| Szczegółowe komunikaty o błędach| Windows | System plików usługi App Service | Kopie stron błędu *.htm,* które zostałyby wysłane do przeglądarki klienta. Ze względów bezpieczeństwa szczegółowe strony błędów nie powinny być wysyłane do klientów w produkcji, ale usługa App Service może zapisać stronę błędu za każdym razem, gdy wystąpi błąd aplikacji, który ma kod HTTP 400 lub większy. Strona może zawierać informacje, które mogą pomóc w określeniu, dlaczego serwer zwraca kod błędu. |
| Śledzenie żądań nie powiodło się | Windows | System plików usługi App Service | Szczegółowe informacje o śledzeniu żądań, w tym śledzenie składników usług IIS używanych do przetwarzania żądania i czasu poświęconego każdemu składnikowi. Jest to przydatne, jeśli chcesz poprawić wydajność witryny lub wyizolować określony błąd HTTP. Jeden folder jest generowany dla każdego żądania, które nie powiodło się, który zawiera plik dziennika XML i arkusz stylów XSL, aby wyświetlić plik dziennika. |
| Rejestrowanie wdrażania | Windows, Linux | System plików usługi App Service | Dzienniki podczas publikowania zawartości w aplikacji. Rejestrowanie wdrażania odbywa się automatycznie i nie ma żadnych konfigurowalnych ustawień rejestrowania wdrażania. Pomaga określić, dlaczego wdrożenie nie powiodło się. Na przykład jeśli używasz [niestandardowego skryptu wdrażania,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)można użyć rejestrowania wdrażania, aby ustalić, dlaczego skrypt jest wada. |

> [!NOTE]
> Usługa App Service udostępnia dedykowane, interaktywne narzędzie diagnostyczne ułatwiające rozwiązywanie problemów z aplikacją. Aby uzyskać więcej informacji, zobacz [omówienie diagnostyki usługi Azure App Service](overview-diagnostics.md).
>
> Ponadto można użyć innych usług platformy Azure, aby poprawić możliwości rejestrowania i monitorowania aplikacji, takie jak [Azure Monitor.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Włącz rejestrowanie aplikacji (Windows)

> [!NOTE]
> Rejestrowanie aplikacji dla magazynu obiektów blob może używać tylko kont magazynu w tym samym regionie co usługa app service

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Windows w [portalu Azure,](https://portal.azure.com)przejdź do aplikacji i wybierz **dzienniki usługi App Service**.

Wybierz **włącz** dla **rejestrowania aplikacji (Filesystem)** lub **rejestrowania aplikacji (Blob)** lub obu. 

**Opcja System plików** służy do tymczasowego debugowania i wyłącza się w ciągu 12 godzin. Opcja **obiektu Blob** jest do rejestrowania długoterminowego i wymaga kontenera magazynu obiektów blob do zapisu dzienników.  Opcja **obiekt blob** zawiera również dodatkowe informacje w komunikatach dziennika, takie jak identyfikator`InstanceId`początkowego wystąpienia`Tid`maszyny Wirtualnej komunikatu dziennika[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)( ), identyfikator wątku ( ) i bardziej szczegółowy znacznik czasu ( ).

> [!NOTE]
> Obecnie tylko dzienniki aplikacji .NET mogą być zapisywane w magazynie obiektów blob. Dzienniki aplikacji Java, PHP, Node.js, Python mogą być przechowywane tylko w systemie plików Usługi App Service (bez modyfikacji kodu w celu zapisu dzienników do zewnętrznej pamięci).
>
> Ponadto w przypadku [ponownego generowania kluczy dostępu konta magazynu](../storage/common/storage-create-storage-account.md)należy zresetować odpowiednią konfigurację rejestrowania, aby użyć zaktualizowanych kluczy dostępu. W tym celu:
>
> 1. Na karcie **Konfiguruj** ustaw odpowiednią funkcję rejestrowania na **Wyłączone**. Zapisz swoje ustawienie.
> 2. Włącz ponowne rejestrowanie obiektu blob konta magazynu. Zapisz swoje ustawienie.
>
>

Wybierz **poziom**lub poziom szczegółów do zarejestrowania. W poniższej tabeli przedstawiono kategorie dzienników uwzględnione na każdym poziomie:

| Poziom | Uwzględniono kategorie |
|-|-|
|**Wyłączone** | Brak |
|**Błąd** | Błąd, krytyczny |
|**Ostrzeżenie** | Ostrzeżenie, Błąd, Krytyczny|
|**Informacje** | Informacje, ostrzeżenie, błąd, krytyczne|
|**Pełne** | Śledzenie, debugowanie, informacje, ostrzeżenie, błąd, krytyczne (wszystkie kategorie) |

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="enable-application-logging-linuxcontainer"></a>Włącz rejestrowanie aplikacji (Linux/Container)

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Linux lub niestandardowych aplikacji kontenerów w [witrynie Azure portal,](https://portal.azure.com)przejdź do aplikacji i wybierz **dzienniki usługi App Service**.

W **obszarze Rejestrowanie aplikacji**wybierz pozycję System **plików**.

W **obszarze Przydział (MB)** określ przydział dysku dla dzienników aplikacji. W **polu Okres przechowywania (dni)** ustaw liczbę dni, przez które dzienniki powinny zostać zachowane.

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="enable-web-server-logging"></a>Włączanie rejestrowania serwera sieci Web

Aby włączyć rejestrowanie serwerów sieci Web dla aplikacji systemu Windows w [portalu Azure,](https://portal.azure.com)przejdź do aplikacji i wybierz **dzienniki usługi App Service**.

W przypadku **rejestrowania serwerów sieci Web**wybierz **opcję Magazyn** do przechowywania dzienników w magazynie obiektów blob lub **System plików** do przechowywania dzienników w systemie plików usługi App Service. 

W **polu Okres przechowywania (dni)** ustaw liczbę dni, przez które dzienniki powinny zostać zachowane.

> [!NOTE]
> Jeśli [ponownie wygenerujesz klucze dostępu konta magazynu,](../storage/common/storage-create-storage-account.md)musisz zresetować odpowiednią konfigurację rejestrowania, aby użyć zaktualizowanych kluczy. W tym celu:
>
> 1. Na karcie **Konfiguruj** ustaw odpowiednią funkcję rejestrowania na **Wyłączone**. Zapisz swoje ustawienie.
> 2. Włącz ponowne rejestrowanie obiektu blob konta magazynu. Zapisz swoje ustawienie.
>
>

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="log-detailed-errors"></a>Rejestrowanie szczegółowych błędów

Aby zapisać stronę błędu lub nie powiodło się śledzenie żądań dla aplikacji systemu Windows w [witrynie Azure portal,](https://portal.azure.com)przejdź do aplikacji i wybierz **dzienniki usługi App Service**.

W obszarze **Szczegółowe rejestrowanie błędów** lub **śledzenie żądań nie powiodło się**wybierz pozycję **Włączone**, a następnie wybierz pozycję **Zapisz**.

Oba typy dzienników są przechowywane w systemie plików usługi App Service. Zachowano do 50 błędów (plików/folderów). Gdy liczba plików HTML przekracza 50, najstarsze 26 błędów jest automatycznie usuwanych.

## <a name="add-log-messages-in-code"></a>Dodawanie wiadomości dziennika w kodzie

W kodzie aplikacji używasz zwykłych urządzeń rejestrowania do wysyłania komunikatów dziennika do dzienników aplikacji. Przykład:

- ASP.NET aplikacje mogą używać [klasy System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) do rejestrowania informacji w dzienniku diagnostyki aplikacji. Przykład:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Domyślnie ASP.NET Core używa dostawcy rejestrowania [Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Aby uzyskać więcej informacji, zobacz [ASP.NET rejestrowanie podstawowe na platformie Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Przed strumieniem dzienników w czasie rzeczywistym, należy włączyć typ dziennika, który chcesz. Wszelkie informacje zapisywane w plikach kończących się na .txt, log lub .htm, które są przechowywane w katalogu */LogFiles* (d:/home/logfiles) są przesyłane strumieniowo przez usługę App Service.

> [!NOTE]
> Niektóre typy zapisu buforu rejestrowania do pliku dziennika, co może spowodować zdarzenia poza kolejnością w strumieniu. Na przykład wpis dziennika aplikacji, który występuje, gdy użytkownik odwiedza stronę może być wyświetlany w strumieniu przed odpowiednim wpisem dziennika HTTP dla żądania strony.
>

### <a name="in-azure-portal"></a>W witrynie Azure portal

Aby przesyłać strumieniowo dzienniki w [witrynie Azure portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **Strumień dziennika**. 

### <a name="in-cloud-shell"></a>W osłonie chmury

Aby przesyłać strumieniowo dzienniki na żywo w [usłudze Cloud Shell,](../cloud-shell/overview.md)użyj następującego polecenia:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Aby filtrować określone zdarzenia, takie jak błędy, należy użyć parametru **--Filter.** Przykład:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Aby filtrować określone typy dzienników, takie jak HTTP, należy użyć parametru **--Path.** Przykład:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>W lokalnym terminalu

Aby przesyłać strumieniowo dzienniki w konsoli lokalnej, [zainstaluj platformę Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i zaloguj się na swoje [konto](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Po zalogowaniu się, postępował zgodnie z [instrukcjami dotyczącymi usługi Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Dostęp do plików dziennika

Jeśli skonfigurujesz opcję obiektów blob usługi Azure Storage dla typu dziennika, potrzebujesz narzędzia klienta, które współpracuje z usługą Azure Storage. Aby uzyskać więcej informacji, zobacz [Narzędzia klienta usługi Azure Storage](../storage/common/storage-explorers.md).

W przypadku dzienników przechowywanych w systemie plików usługi App Service najprostszym sposobem jest pobranie pliku ZIP w przeglądarce pod adresem:

- Aplikacje dla systemu Linux/container:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplikacje dla systemu Windows:`https://<app-name>.scm.azurewebsites.net/api/dump`

W przypadku aplikacji systemu Linux/container plik ZIP zawiera dzienniki wyjściowe konsoli dla hosta platformy docker i kontenera docker. W przypadku aplikacji skalowane w poziomie plik ZIP zawiera jeden zestaw dzienników dla każdego wystąpienia. W systemie plików usługi App Service te pliki dziennika są zawartością katalogu */home/LogFiles.*

W przypadku aplikacji systemu Windows plik ZIP zawiera zawartość katalogu *D:\Home\LogFiles* w systemie plików usługi App Service. Ma następującą strukturę:

| Typ dziennika | Katalog | Opis |
|-|-|-|
| **Dzienniki aplikacji** |*/LogFiles/Aplikacja/* | Zawiera jeden lub więcej plików tekstowych. Format komunikatów dziennika zależy od dostawcy rejestrowania, którego używasz. |
| **Ślady żądań nie powiodły się** | */LogFiles/W3SVC###########* | Zawiera pliki XML i plik XSL. Sformatowane pliki XML można wyświetlić w przeglądarce. |
| **Szczegółowe dzienniki błędów** | */LogFiles/SzczegółoweErrory/* | Zawiera pliki błędów HTM. Pliki HTM można wyświetlić w przeglądarce.<br/>Innym sposobem wyświetlania śladów żądań nie powiodło się jest przejście do strony aplikacji w portalu. Z lewego menu wybierz **polecenie Diagnozuj i rozwiązuj problemy,** a następnie wyszukaj **dzienniki śledzenia żądań nieudanych,** a następnie kliknij ikonę, aby przeglądać i wyświetlać żądany ślad. |
| **Dzienniki serwera sieci Web** | */LogFiles/http/RawLogs/* | Zawiera pliki tekstowe sformatowane przy użyciu [rozszerzonego formatu pliku dziennika W3C](/windows/desktop/Http/w3c-logging). Informacje te można odczytać za pomocą edytora tekstu lub narzędzia, takiego jak [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>Usługa app service nie `s-computername` `s-ip`obsługuje `cs-version` , lub pól. |
| **Dzienniki wdrażania** | */LogFiles/Git/* i */wdrożenia/* | Zawierają dzienniki generowane przez wewnętrzne procesy wdrażania, a także dzienniki dla wdrożeń Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Wysyłanie dzienników do usługi Azure Monitor (wersja zapoznawcza)

Dzięki nowej [integracji z usługą Azure Monitor](https://aka.ms/appsvcblog-azmon)można utworzyć ustawienia [diagnostyczne (wersja zapoznawcza),](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) aby wysyłać dzienniki do kont magazynu, centrów zdarzeń i usługi Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Ustawienia diagnostyczne (wersja zapoznawcza)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Obsługiwane typy dzienników

W poniższej tabeli przedstawiono obsługiwane typy i opisy dzienników: 

| Typ dziennika | Obsługa systemu Windows | Obsługa systemu Linux (Docker) | Opis |
|-|-|-|
| AppServiceConsoleLogs | Tba | Tak | Standardowe wyjście i błąd standardowy |
| AppServiceHTTPLogs | Tak | Tak | Dzienniki serwera sieci Web |
| AppServiceEnvironmentPlatformLogs | Tak | Tak | Środowisko usługi aplikacji: skalowanie, zmiany konfiguracji i dzienniki stanu|
| AppServiceAuditLogs (Usługi w aplikacji) | Tak | Tak | Aktywność logowania za pośrednictwem FTP i Kudu |
| AppServiceFileAuditLogs | Tak | TBD | Zmiany w plikach za pośrednictwem FTP i Kudu |
| AppServiceAppLogs (AppServiceAppLogs) | Tba | Java SE & Tomcat | Dzienniki aplikacji |

## <a name="next-steps"></a><a name="nextsteps"></a>Kolejne kroki
* [Dzienniki zapytań za pomocą usługi Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Jak monitorować usługę Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów z usługą Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w hdinsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
