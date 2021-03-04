---
title: Włączanie rejestrowania diagnostycznego
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 6008304ea7c1d17363587a4fa5bf6017cb0903f9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049240"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service
## <a name="overview"></a>Omówienie
Platforma Azure udostępnia wbudowaną diagnostykę, która pomaga w debugowaniu [aplikacji App Service](overview.md). W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.

W tym artykule są wykorzystywane [Azure Portal](https://portal.azure.com) i interfejs wiersza polecenia platformy Azure do pracy z dziennikami diagnostycznymi. Aby uzyskać informacje dotyczące pracy z dziennikami diagnostycznymi przy użyciu programu Visual Studio, zobacz [Rozwiązywanie problemów z platformą Azure w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)

> [!NOTE]
> Oprócz instrukcji dotyczących rejestrowania w tym artykule istnieją nowe, zintegrowane możliwości rejestrowania przy użyciu monitorowania platformy Azure. Więcej informacji na ten temat można znaleźć w sekcji [wysyłanie dzienników do Azure monitor (wersja zapoznawcza)](#send-logs-to-azure-monitor-preview) . 
>
>

|Typ|Platforma|Lokalizacja|Opis|
|-|-|-|-|
| Rejestrowanie aplikacji | Windows, Linux | App Service system plików i/lub obiekty blob usługi Azure Storage | Rejestruje komunikaty generowane przez kod aplikacji. Komunikaty mogą być generowane przez wybrany przez siebie platformę sieci Web lub z kodu aplikacji bezpośrednio przy użyciu standardowego wzorca rejestrowania języka. Każdy komunikat ma przypisaną jedną z następujących kategorii: **krytyczny**, **błąd**, **Ostrzeżenie**, **informacje**, **debugowanie** i **śledzenie**. Aby określić, jak ma być pełne rejestrowanie, należy ustawić poziom ważności po włączeniu rejestrowania aplikacji.|
| Rejestrowanie serwera sieci Web| Windows | App Service systemu plików lub obiektów BLOB usługi Azure Storage| Nieprzetworzone dane żądania HTTP w [rozszerzonym formacie W3C plików dziennika](/windows/desktop/Http/w3c-logging). Każdy komunikat dziennika zawiera dane, takie jak metoda HTTP, identyfikator URI zasobu, adres IP klienta, Port klienta, agent użytkownika, kod odpowiedzi itd. |
| Szczegółowe komunikaty o błędach| Windows | System plików App Service | Kopie stron błędów *. htm* , które zostałyby wysłane do przeglądarki klienta. Ze względów bezpieczeństwa szczegółowe strony błędów nie powinny być wysyłane do klientów w środowisku produkcyjnym, ale App Service mogą zapisać stronę błędu za każdym razem, gdy wystąpi błąd aplikacji, który ma kod HTTP 400 lub nowszy. Strona może zawierać informacje, które mogą pomóc w ustaleniu, dlaczego serwer zwraca kod błędu. |
| Śledzenie nieudanych żądań | Windows | System plików App Service | Szczegółowe informacje o śledzeniu żądań zakończonych niepowodzeniem, w tym śledzenia składników usług IIS używanych do przetwarzania żądania oraz czasu wykonywanego w poszczególnych składnikach. Jest to przydatne, jeśli chcesz zwiększyć wydajność lokacji lub odizolować określony błąd HTTP. Dla każdego żądania zakończonego niepowodzeniem jest generowany jeden folder, który zawiera plik dziennika XML, i arkusz stylów XSL, w którym ma być wyświetlany plik dziennika. |
| Rejestrowanie wdrożenia | Windows, Linux | System plików App Service | Rejestruje informacje o publikowaniu zawartości w aplikacji. Rejestrowanie wdrożenia odbywa się automatycznie i nie ma konfigurowalnych ustawień rejestrowania wdrożenia. Pomaga określić przyczynę niepowodzenia wdrożenia. Na przykład jeśli używasz [niestandardowego skryptu wdrożenia](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), możesz użyć rejestrowania wdrożenia, aby określić, dlaczego skrypt kończy się niepowodzeniem. |

> [!NOTE]
> App Service udostępnia dedykowane, interaktywne narzędzie diagnostyczne pomagające w rozwiązywaniu problemów z aplikacją. Aby uzyskać więcej informacji, zobacz [Omówienie diagnostyki Azure App Service](overview-diagnostics.md).
>
> Ponadto możesz użyć innych usług platformy Azure, aby usprawnić funkcje rejestrowania i monitorowania aplikacji, takie jak [Azure monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Włącz rejestrowanie aplikacji (Windows)

> [!NOTE]
> Funkcja rejestrowania aplikacji dla magazynu obiektów BLOB może używać tylko kont magazynu w tym samym regionie co App Service

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Windows w [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **dzienniki App Service**.

Wybierz opcję **włączone** dla **rejestrowania aplikacji (system plików)** lub **rejestrowania aplikacji (BLOB)** lub obu tych metod. 

Opcja systemu **plików** służy do tymczasowego debugowania i wyłącza się w ciągu 12 godzin. Opcja **obiektu BLOB** służy do rejestrowania długoterminowego i wymaga kontenera magazynu obiektów BLOB do zapisu dzienników.  Opcja **obiektu BLOB** zawiera również dodatkowe informacje w komunikatach dziennika, takie jak identyfikator wystąpienia pierwotnej maszyny wirtualnej komunikatu dziennika ( `InstanceId` ), identyfikator wątku ( `Tid` ) i bardziej szczegółowe sygnatura czasowa ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Obecnie tylko Dzienniki aplikacji .NET mogą być zapisywane w magazynie obiektów BLOB. Java, PHP, Node.js, Dzienniki aplikacji Python mogą być przechowywane tylko w systemie plików App Service (bez modyfikacji kodu do zapisu dzienników do magazynu zewnętrznego).
>
> Ponadto, jeśli [ponownie wygenerujesz klucze dostępu konta magazynu](../storage/common/storage-account-create.md), należy zresetować odpowiednią konfigurację rejestrowania, aby użyć zaktualizowanych kluczy dostępu. W tym celu:
>
> 1. Na karcie **Konfiguracja** Ustaw odpowiednią funkcję rejestrowania na **off**. Zapisz ustawienie.
> 2. Ponownie Włącz rejestrowanie do obiektu BLOB konta magazynu. Zapisz ustawienie.
>
>

Wybierz **poziom** lub poziom szczegółów do zarejestrowania. W poniższej tabeli przedstawiono kategorie dzienników uwzględnione na każdym poziomie:

| Poziom | Uwzględnione kategorie |
|-|-|
|**Disabled** | Brak |
|**Błąd** | Błąd, krytyczny |
|**Ostrzeżenie** | Ostrzeżenie, błąd, krytyczny|
|**Informacje** | Informacje, ostrzeżenie, błąd, krytyczne|
|**Pełne** | Trace, Debug, info, Warning, Error, krytyczny (wszystkie kategorie) |

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="enable-application-logging-linuxcontainer"></a>Włącz rejestrowanie aplikacji (Linux/Container)

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Linux lub niestandardowych aplikacji kontenera w [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **dzienniki App Service**.

W obszarze **Rejestrowanie aplikacji** wybierz pozycję **system plików**.

W obszarze **limit przydziału (MB)** Określ przydział dysku dla dzienników aplikacji. W obszarze **okres przechowywania (dni)** Ustaw liczbę dni przechowywania dzienników.

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="enable-web-server-logging"></a>Włącz rejestrowanie serwera sieci Web

Aby włączyć rejestrowanie serwera sieci Web dla aplikacji systemu Windows w [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **dzienniki App Service**.

W przypadku **rejestrowania serwera sieci Web** wybierz pozycję **Magazyn** do przechowywania dzienników w usłudze BLOB Storage lub **system plików** do przechowywania dzienników w systemie plików App Service. 

W obszarze **okres przechowywania (dni)** Ustaw liczbę dni przechowywania dzienników.

> [!NOTE]
> W przypadku ponownego [wygenerowania kluczy dostępu konta magazynu](../storage/common/storage-account-create.md)należy zresetować odpowiednią konfigurację rejestrowania, aby użyć zaktualizowanych kluczy. W tym celu:
>
> 1. Na karcie **Konfiguracja** Ustaw odpowiednią funkcję rejestrowania na **off**. Zapisz ustawienie.
> 2. Ponownie Włącz rejestrowanie do obiektu BLOB konta magazynu. Zapisz ustawienie.
>
>

Po zakończeniu wybierz pozycję **Zapisz**.

## <a name="log-detailed-errors"></a>Rejestruj szczegółowe błędy

Aby zapisać stronę błędów lub śledzenie nieudanych żądań dla aplikacji systemu Windows w [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **dzienniki App Service**.

W obszarze **szczegółowe rejestrowanie błędów** lub **śledzenie nieudanych żądań** wybierz pozycję **włączone**, a następnie wybierz pozycję **Zapisz**.

Oba typy dzienników są przechowywane w systemie plików App Service. Błędy do 50 (pliki/foldery) są zachowywane. Gdy liczba plików HTML przekracza 50, najstarszych 26 błędów zostanie automatycznie usunięta.

## <a name="add-log-messages-in-code"></a>Dodawanie komunikatów dziennika w kodzie

W kodzie aplikacji należy używać zwykłych funkcji rejestrowania do wysyłania komunikatów dziennika do dzienników aplikacji. Na przykład:

- Aplikacje ASP.NET mogą używać klasy [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) do rejestrowania informacji w dzienniku diagnostyki aplikacji. Na przykład:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Domyślnie ASP.NET Core używa dostawcy rejestrowania [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Aby uzyskać więcej informacji, zobacz [rejestrowanie ASP.NET Core na platformie Azure](/aspnet/core/fundamentals/logging/). Informacje o rejestrowaniu zestawu SDK zadań WebJob znajdują się w temacie Wprowadzenie do [zestawu sdk Azure WebJobs](/azure/app-service/webjobs-sdk-get-started#enable-console-logging)

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Przed przeprowadzeniem przesyłania strumieniowego dzienników należy włączyć żądany typ dziennika. Wszelkie informacje zapisane w plikach kończących się na. txt,. log lub. htm, które są przechowywane w katalogu */LogFiles* (d:/Home/LogFiles), są przesyłane strumieniowo przez App Service.

> [!NOTE]
> Niektóre typy buforów rejestrowania zapisu w pliku dziennika, co może spowodować brak kolejności zdarzeń w strumieniu. Na przykład wpis dziennika aplikacji, który występuje, gdy użytkownik odwiedzi stronę, może zostać wyświetlony w strumieniu przed odpowiednim wpisem dziennika HTTP dla żądania strony.
>

### <a name="in-azure-portal"></a>W Azure Portal

Aby przesłać strumieniowo dzienniki w [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz pozycję **strumień dzienników**. 

### <a name="in-cloud-shell"></a>W Cloud Shell

Aby przesłać strumieniowo dzienniki na żywo w [Cloud Shell](../cloud-shell/overview.md), użyj następującego polecenia:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Aby odfiltrować określone zdarzenia, takie jak błędy, użyj parametru **--Filter** . Na przykład:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Aby filtrować określone typy dzienników, takie jak HTTP, użyj parametru **--Path** . Na przykład:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>W terminalu lokalnym

Aby przesłać strumieniowo dzienniki w lokalnej konsoli, [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się na swoim koncie](/cli/azure/authenticate-azure-cli). Po zalogowaniu postępować zgodnie z [instrukcjami dotyczącymi Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Pliki dziennika dostępu

Jeśli skonfigurujesz opcję obiektów BLOB usługi Azure Storage dla typu dziennika, potrzebujesz narzędzia klienckiego, które współpracuje z usługą Azure Storage. Aby uzyskać więcej informacji, zobacz [narzędzia klienckie usługi Azure Storage](../storage/common/storage-explorers.md).

W przypadku dzienników przechowywanych w systemie plików App Service Najprostszym sposobem jest pobranie pliku ZIP z przeglądarki w:

- Aplikacje dla systemu Linux/kontenera: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplikacje systemu Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

W przypadku aplikacji systemu Linux/kontenera plik ZIP zawiera dzienniki danych wyjściowych konsoli dla hosta platformy Docker i kontenera Docker. Dla aplikacji skalowanej w poziomie plik ZIP zawiera jeden zestaw dzienników dla każdego wystąpienia. W systemie plików App Service te pliki dzienników to zawartość katalogu */Home/LogFiles* .

W przypadku aplikacji systemu Windows plik ZIP zawiera zawartość katalogu *D:\Home\LogFiles* w systemie plików App Service. Ma następującą strukturę:

| Typ dziennika | Katalog | Opis |
|-|-|-|
| **Dzienniki aplikacji** |*/LogFiles/Application/* | Zawiera co najmniej jeden plik tekstowy. Format komunikatów dziennika zależy od używanego dostawcy rejestrowania. |
| **Ślady nieudanych żądań** | */LogFiles/W3SVC#########/* | Zawiera pliki XML i plik XSL. Można wyświetlić sformatowane pliki XML w przeglądarce. |
| **Szczegółowe dzienniki błędów** | */LogFiles/DetailedErrors/* | Zawiera pliki błędów HTM. Pliki HTM można wyświetlić w przeglądarce.<br/>Innym sposobem wyświetlania śladów niepomyślnych żądań jest przechodzenie do strony aplikacji w portalu. Z menu po lewej stronie wybierz opcję **Diagnozuj i rozwiąż problemy**, a następnie wyszukaj **dzienniki śledzenia niepomyślnych żądań**, a następnie kliknij ikonę, aby przeglądać i wyświetlić odpowiedni ślad. |
| **Dzienniki serwera sieci Web** | */LogFiles/http/RawLogs/* | Zawiera pliki tekstowe sformatowane przy użyciu [rozszerzonego formatu W3C plików dziennika](/windows/desktop/Http/w3c-logging). Te informacje można odczytać za pomocą edytora tekstu lub narzędzia, takiego jak [parser dzienników](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>App Service nie obsługuje `s-computername` pól, `s-ip` , ani `cs-version` . |
| **Dzienniki wdrożenia** | */LogFiles/git/* i */Deployments/* | Zawiera dzienniki wygenerowane przez wewnętrzne procesy wdrażania, a także dzienniki wdrożeń usługi git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Wyślij dzienniki do Azure Monitor (wersja zapoznawcza)

Przy użyciu nowej [integracji Azure monitor](https://aka.ms/appsvcblog-azmon)można [utworzyć ustawienia diagnostyczne (wersja zapoznawcza)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) w celu wysyłania dzienników do kont magazynu, Event Hubs i log Analytics. 

> [!div class="mx-imgBorder"]
> ![Ustawienia diagnostyczne (wersja zapoznawcza)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Obsługiwane typy dzienników

W poniższej tabeli przedstawiono obsługiwane typy i opisy dzienników: 

| Typ dziennika | Windows | Kontener systemu Windows | Linux | Kontener systemu Linux | Opis |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Tak | Tak | Tak | Standardowe wyjście i standardowy błąd |
| AppServiceHTTPLogs | Tak | Tak | Tak | Tak | Dzienniki serwera sieci Web |
| AppServiceEnvironmentPlatformLogs | Tak | Nie dotyczy | Tak | Tak | App Service Environment: skalowanie, zmiany konfiguracji i dzienniki stanu|
| AppServiceAuditLogs | Tak | Tak | Tak | Tak | Działanie logowania za pośrednictwem protokołu FTP i kudu |
| AppServiceFileAuditLogs | Tak | Tak | TBA | TBA | Zmiany plików wprowadzone do zawartości witryny; **dostępne tylko dla warstwy Premium i wyższych** |
| AppServiceAppLogs | ASP .NET | ASP .NET | Java SE & Tomcat zalecany obrazy <sup>1</sup> | Java SE & Tomcat zalecany obrazy <sup>1</sup> | Dzienniki aplikacji |
| AppServiceIPSecAuditLogs  | Tak | Tak | Tak | Tak | Żądania z reguł adresów IP |
| AppServicePlatformLogs  | TBA | Tak | Tak | Tak | Dzienniki operacji kontenera |
| AppServiceAntivirusScanAuditLogs | Tak | Tak | Tak | Tak | [Dzienniki skanowania antywirusowego](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) przy użyciu usługi Microsoft Defender; **dostępne tylko dla warstwy Premium** | 

<sup>1</sup> w przypadku aplikacji Java SE Dodaj do ustawień aplikacji pozycję "$Website _AZMON_PREVIEW_ENABLED" i ustaw ją na wartość 1 lub na true.

## <a name="next-steps"></a><a name="nextsteps"></a> Następne kroki
* [Wysyłanie zapytań do dzienników przy użyciu Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Jak monitorować Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w usłudze HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
