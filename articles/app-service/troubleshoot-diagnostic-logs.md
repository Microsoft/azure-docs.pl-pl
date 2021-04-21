---
title: Włączanie rejestrowania diagnostycznego
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833854"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service
## <a name="overview"></a>Omówienie
Platforma Azure udostępnia wbudowaną diagnostykę, która pomaga w [debugowaniu App Service aplikacji.](overview.md) Z tego artykułu dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.

W tym artykule do pracy [z dziennikami Azure Portal](https://portal.azure.com) i interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje na temat pracy z dziennikami diagnostyczni przy użyciu Visual Studio, zobacz Troubleshooting Azure in Visual Studio (Rozwiązywanie problemów z platformą [Azure w programie Visual Studio).](troubleshoot-dotnet-visual-studio.md)

> [!NOTE]
> Oprócz instrukcji rejestrowania w tym artykule istnieje nowa, zintegrowana funkcja rejestrowania z usługą Azure Monitoring. Więcej informacji na temat tej funkcji można znaleźć w sekcji Wysyłanie dzienników do Azure Monitor [(wersja zapoznawcza).](#send-logs-to-azure-monitor-preview) 
>
>

|Typ|Platforma|Lokalizacja|Opis|
|-|-|-|-|
| Rejestrowanie aplikacji | Windows, Linux | App Service plików i/lub obiektów blob usługi Azure Storage | Rejestruje komunikaty generowane przez kod aplikacji. Komunikaty mogą być generowane przez wybieraną platformę internetową lub z poziomu kodu aplikacji bezpośrednio przy użyciu standardowego wzorca rejestrowania języka. Każdy komunikat ma przypisaną jedną z następujących kategorii: **Krytyczne,** **Błąd,** **Ostrzeżenie,** **Informacje,** **Debugowanie** i **Śledzenie**. Możesz wybrać poziom szczegółowości rejestrowania, ustawiając poziom ważności po włączeniu rejestrowania aplikacji.|
| Rejestrowanie serwera internetowego| Windows | App Service plików lub obiektów blob usługi Azure Storage| Nieprzetworzone dane żądania HTTP w [rozszerzonym formacie pliku dziennika W3C.](/windows/desktop/Http/w3c-logging) Każdy komunikat dziennika zawiera dane, takie jak metoda HTTP, identyfikator URI zasobu, adres IP klienta, port klienta, agent użytkownika, kod odpowiedzi i tak dalej. |
| Szczegółowe komunikaty o błędach| Windows | App Service systemu plików | Kopie stron błędów *.htm,* które zostałyby wysłane do przeglądarki klienta. Ze względów bezpieczeństwa strony szczegółowych błędów nie powinny być wysyłane do klientów w środowisku produkcyjnym, ale program App Service może zapisać stronę błędu za każdym razem, gdy wystąpi błąd aplikacji z kodem HTTP 400 lub większym. Strona może zawierać informacje, które mogą pomóc w ustaleniu, dlaczego serwer zwraca kod błędu. |
| Śledzenie żądań, które zakończyło się niepowodzeniem | Windows | App Service systemu plików | Szczegółowe informacje o śledzeniu żądań, które zakończyły się niepowodzeniem, w tym ślad składników usług IIS używanych do przetwarzania żądania oraz czas w każdym składniku. Jest to przydatne, jeśli chcesz zwiększyć wydajność lokacji lub odizolować określony błąd HTTP. Dla każdego żądania, które zakończyło się niepowodzeniem, jest generowany jeden folder, który zawiera plik dziennika XML, oraz arkusz stylów XSL do wyświetlania pliku dziennika. |
| Rejestrowanie wdrożenia | Windows, Linux | App Service systemu plików | Rejestruje podczas publikowania zawartości w aplikacji. Rejestrowanie wdrożenia odbywa się automatycznie i nie ma żadnych konfigurowalnych ustawień rejestrowania wdrożenia. Pomaga określić, dlaczego wdrożenie nie powiodło się. Jeśli na przykład używasz niestandardowego skryptu wdrażania [,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)możesz użyć rejestrowania wdrożenia, aby określić przyczynę awarii skryptu. |

> [!NOTE]
> App Service udostępnia dedykowane, interaktywne narzędzie diagnostyczne, które pomaga rozwiązywać problemy z aplikacją. Aby uzyskać więcej informacji, [zobacz Azure App Service omówienie diagnostyki.](overview-diagnostics.md)
>
> Ponadto możesz użyć innych usług platformy Azure, aby ulepszyć funkcje rejestrowania i monitorowania aplikacji, takie [jak Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Włączanie rejestrowania aplikacji (Windows)

> [!NOTE]
> Rejestrowanie aplikacji dla magazynu obiektów blob może używać kont magazynu tylko w tym samym regionie co App Service

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Windows w Azure Portal [,](https://portal.azure.com)przejdź do aplikacji i wybierz pozycję **App Service dziennikach.**

Wybierz pozycję **Wł., aby Application Logging (system plików)** **lub Application Logging (obiekt blob)** lub oba te typy.  

Opcja **System plików** służy do tymczasowego debugowania i wyłącza się po 12 godzinach. Opcja **Obiekt blob** jest dostępna na potrzeby rejestrowania długoterminowego i wymaga kontenera magazynu obiektów blob do zapisu dzienników.  Opcja **Obiekt blob** zawiera również dodatkowe informacje w komunikatach dziennika, takie jak identyfikator wystąpienia maszyny wirtualnej źródła komunikatu dziennika (), identyfikator wątku ( ) i bardziej szczegółowy znacznik czasu ( `InstanceId` `Tid` [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Obecnie w magazynie obiektów blob można zapisywać tylko dzienniki aplikacji .NET. Dzienniki aplikacji java, PHP, Node.js i Python mogą być przechowywane tylko w systemie plików App Service (bez modyfikacji kodu zapisu dzienników w magazynie zewnętrznym).
>
> Ponadto w przypadku [ponownego generowania](../storage/common/storage-account-create.md)kluczy dostępu konta magazynu należy zresetować odpowiednią konfigurację rejestrowania, aby używać zaktualizowanych kluczy dostępu. W tym celu:
>
> 1. Na karcie **Konfigurowanie** ustaw odpowiednią funkcję rejestrowania na **wartość Wyłączone.** Zapisz ustawienie.
> 2. Ponownie włącz rejestrowanie w obiekcie blob konta magazynu. Zapisz ustawienie.
>
>

Wybierz **poziom** lub poziom szczegółów do zalogowania. W poniższej tabeli przedstawiono kategorie dzienników uwzględnione na każdym poziomie:

| Poziom | Uwzględnione kategorie |
|-|-|
|**Disabled** | Brak |
|**Błąd** | Błąd, Krytyczny |
|**Ostrzeżenie** | Ostrzeżenie, Błąd, Krytyczne|
|**Informacje** | Informacje, ostrzeżenie, błąd, krytyczne|
|**Pełne** | Śledzenie, debugowanie, informacje, ostrzeżenie, błąd, krytyczne (wszystkie kategorie) |

Po zakończeniu wybierz pozycję **Zapisz.**

## <a name="enable-application-logging-linuxcontainer"></a>Włączanie rejestrowania aplikacji (Linux/kontener)

Aby włączyć rejestrowanie aplikacji dla aplikacji systemu Linux lub niestandardowych aplikacji kontenera w Azure Portal [,](https://portal.azure.com)przejdź do swojej aplikacji i wybierz pozycję **App Service dziennikach.**

W **rejestrowaniu aplikacji wybierz** pozycję System **plików**.

W **limitach przydziału (MB)** określ limit przydziału dysku dla dzienników aplikacji. W **części Okres przechowywania (dni)** ustaw liczbę dni przechowywania dzienników.

Po zakończeniu wybierz pozycję **Zapisz.**

## <a name="enable-web-server-logging"></a>Włączanie rejestrowania serwera internetowego

Aby włączyć rejestrowanie serwera internetowego dla aplikacji systemu Windows w Azure Portal [,](https://portal.azure.com)przejdź do aplikacji i wybierz pozycję **App Service dzienniki.**

W **przypadku rejestrowania serwera sieci** Web wybierz opcję **Magazyn** do przechowywania dzienników w magazynie obiektów blob lub **System** plików do przechowywania dzienników w App Service plików. 

W **części Okres przechowywania (dni)** ustaw liczbę dni przechowywania dzienników.

> [!NOTE]
> W przypadku [ponownego generowania](../storage/common/storage-account-create.md)kluczy dostępu konta magazynu należy zresetować odpowiednią konfigurację rejestrowania, aby używać zaktualizowanych kluczy. W tym celu:
>
> 1. Na karcie **Konfigurowanie** ustaw odpowiednią funkcję rejestrowania na **wartość Wył.**. Zapisz ustawienie.
> 2. Ponownie włącz rejestrowanie do obiektu blob konta magazynu. Zapisz ustawienie.
>
>

Po zakończeniu wybierz pozycję **Zapisz.**

## <a name="log-detailed-errors"></a>Rejestrowanie szczegółowych błędów

Aby zapisać stronę błędu lub śledzenie żądań dla aplikacji systemu Windows [Azure Portal,](https://portal.azure.com)przejdź do aplikacji i wybierz **pozycję App Service dziennikach.**

W **obszarze Szczegółowe rejestrowanie błędów lub** Śledzenie **żądań, które zakończyło się niepowodzeniem,** wybierz pozycję **Wł.,** a następnie wybierz pozycję **Zapisz**.

Oba typy dzienników są przechowywane w App Service plików. Zachowywanych jest do 50 błędów (pliki/foldery). Gdy liczba plików HTML przekracza 50, automatycznie usuwanych jest 26 najstarszych błędów.

## <a name="add-log-messages-in-code"></a>Dodawanie komunikatów dziennika w kodzie

W kodzie aplikacji używa się zwykłych funkcji rejestrowania do wysyłania komunikatów dziennika do dzienników aplikacji. Na przykład:

- ASP.NET mogą używać klasy [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) do logowania informacji do dziennika diagnostycznego aplikacji. Na przykład:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Domyślnie program ASP.NET Core używa dostawcy [rejestrowania Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Aby uzyskać więcej informacji, [zobacz ASP.NET Core logging in Azure (Rejestrowanie podstawowe na platformie Azure).](/aspnet/core/fundamentals/logging/) Aby uzyskać informacje na temat rejestrowania zestawu SDK usługi WebJobs, zobacz [Wprowadzenie do zestawu AZURE WEBJOBS SDK](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Przed rozpoczęciem przesyłania strumieniowego dzienników w czasie rzeczywistym włącz typ dziennika, którego potrzebujesz. Wszystkie informacje zapisywane w plikach kończących się na txt, log lub htm, które są przechowywane w *katalogu /LogFiles* (d:/home/logfiles), są przesyłane strumieniowo App Service.

> [!NOTE]
> Niektóre typy zapisu buforu rejestrowania w pliku dziennika, które mogą powodować zdarzenia poza kolejnością w strumieniu. Na przykład wpis dziennika aplikacji, który występuje, gdy użytkownik odwiedzi stronę, może być wyświetlany w strumieniu przed odpowiednim wpisem dziennika HTTP dla żądania strony.
>

### <a name="in-azure-portal"></a>W Azure Portal

Aby przesyłać strumieniowo dzienniki [Azure Portal](https://portal.azure.com), przejdź do aplikacji i wybierz **pozycję Strumień dzienników.** 

### <a name="in-cloud-shell"></a>W Cloud Shell

Aby przesyłać strumieniowo dzienniki [na żywo Cloud Shell](../cloud-shell/overview.md), użyj następującego polecenia:

> [!IMPORTANT]
> To polecenie może nie działać z aplikacjami internetowymi hostowanych w planie usługi App Service dla systemu Linux.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Aby filtrować określone typy dzienników, takie jak HTTP, użyj **parametru --Provider.** Na przykład:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>W terminalu lokalnym

Aby przesyłać strumieniowo dzienniki w konsoli lokalnej, [zainstaluj interfejs wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure i zaloguj się do swojego [konta.](/cli/azure/authenticate-azure-cli) Po zalogowaniu się należy przestrzegać [instrukcji dotyczących Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Uzyskiwanie dostępu do plików dziennika

W przypadku skonfigurowania opcji obiektów blob usługi Azure Storage dla typu dziennika potrzebne jest narzędzie klienta, które współpracuje z usługą Azure Storage. Aby uzyskać więcej informacji, zobacz [Narzędzia klienta usługi Azure Storage.](../storage/common/storage-explorers.md)

W przypadku dzienników przechowywanych w App Service plików najprostszym sposobem jest pobranie pliku ZIP w przeglądarce pod:

- Aplikacje dla systemu Linux/kontenera: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplikacje systemu Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

W przypadku aplikacji systemu Linux/kontenera plik ZIP zawiera dzienniki wyjściowe konsoli zarówno dla hosta platformy Docker, jak i kontenera platformy Docker. W przypadku aplikacji skalowanej w zewnątrz plik ZIP zawiera jeden zestaw dzienników dla każdego wystąpienia. W App Service plików te pliki dziennika są zawartością *katalogu /home/LogFiles.*

W przypadku aplikacji systemu Windows plik ZIP zawiera zawartość katalogu *D:\Home\LogFiles* w App Service plików. Ma następującą strukturę:

| Typ dziennika | Katalog | Opis |
|-|-|-|
| **Dzienniki aplikacji** |*/LogFiles/Application/* | Zawiera co najmniej jeden plik tekstowy. Format komunikatów dziennika zależy od dostawcy rejestrowania, który jest używany. |
| **Ślady żądań, które zakończyły się niepowodzeniem** | */LogFiles/W3SVC#########/* | Zawiera pliki XML i plik XSL. Sformatowane pliki XML można wyświetlić w przeglądarce. |
| **Szczegółowe dzienniki błędów** | */LogFiles/DetailedErrors/* | Zawiera pliki błędów HTM. Pliki HTM można wyświetlić w przeglądarce.<br/>Innym sposobem wyświetlenia śladów żądań, które zakończyły się niepowodzeniem, jest przejście do strony aplikacji w portalu. W menu po lewej stronie wybierz pozycję Diagnozowanie i rozwiązywanie **problemów,** a następnie wyszukaj pozycję **Dzienniki** śledzenia żądań, które zakończyły się niepowodzeniem, a następnie kliknij ikonę , aby przeglądać i wyświetlać dane śledzenia. |
| **Dzienniki serwera sieci Web** | */LogFiles/http/RawLogs/* | Zawiera pliki tekstowe sformatowane przy użyciu [rozszerzonego formatu pliku dziennika W3C](/windows/desktop/Http/w3c-logging). Te informacje można odczytać za pomocą edytora tekstów lub narzędzia, takiego jak [Log Parser.](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>App Service nie obsługuje pól `s-computername` `s-ip` , ani `cs-version` . |
| **Dzienniki wdrażania** | */LogFiles/Git/* i */deployments/* | Zawierają dzienniki generowane przez wewnętrzne procesy wdrażania, a także dzienniki wdrożeń usługi Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Wysyłanie dzienników do Azure Monitor (wersja zapoznawcza)

Dzięki nowej [integracji Azure Monitor można](https://aka.ms/appsvcblog-azmon)tworzyć ustawienia diagnostyczne (wersja zapoznawcza) w celu wysyłania dzienników do kont magazynu, Event Hubs i usługi Log Analytics. [](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) 

> [!div class="mx-imgBorder"]
> ![Ustawienia diagnostyczne (wersja zapoznawcza)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Obsługiwane typy dzienników

W poniższej tabeli przedstawiono obsługiwane typy i opisy dzienników: 

| Typ dziennika | Windows | Kontener systemu Windows | Linux | Kontener systemu Linux | Opis |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Tak | Tak | Tak | Standardowe dane wyjściowe i błąd standardowy |
| AppServiceHTTPLogs | Tak | Tak | Tak | Tak | Dzienniki serwera internetowego |
| AppServiceEnvironmentPlatformLogs | Tak | Nie dotyczy | Tak | Tak | App Service Environment: skalowanie, zmiany konfiguracji i dzienniki stanu|
| AppServiceAuditLogs | Tak | Tak | Tak | Tak | Aktywność logowania za pośrednictwem protokołu FTP i usługi Kudu |
| AppServiceFileAuditLogs | Tak | Tak | Tba | Tba | Zmiany plików wprowadzone w zawartości witryny; **dostępne tylko dla warstwy Premium i wyższych** |
| AppServiceAppLogs | ASP.NET & Java Tomcat <sup>1</sup> | ASP.NET & Java Tomcat <sup>1</sup> | Obrazy & Tomcat w <sup></sup> języku Java SE | Obrazy & Tomcat w <sup></sup> języku Java SE | Dzienniki aplikacji |
| AppServiceIPSecAuditLogs  | Tak | Tak | Tak | Tak | Żądania z reguł adresów IP |
| AppServicePlatformLogs  | Tba | Tak | Tak | Tak | Dzienniki operacji kontenera |
| AppServiceAntivirusScanAuditLogs | Tak | Tak | Tak | Tak | [Dzienniki skanowania antywirusowego przy użyciu](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) usługi Microsoft Defender; **dostępne tylko dla warstwy Premium** | 

<sup>1</sup> W przypadku aplikacji Java Tomcat dodaj wartość "TOMCAT_USE_STARTUP_BAT" do ustawień aplikacji i ustaw ją na wartość false lub 0. Musi być w *najnowszej wersji* programu Tomcat i *używać pliku java.util.logging.*

<sup>2</sup> W przypadku aplikacji Java SE dodaj wartość "$WEBSITE_AZMON_PREVIEW_ENABLED" do ustawień aplikacji i ustaw ją na wartość true lub 1.

## <a name="next-steps"></a><a name="nextsteps"></a> Następne kroki
* [Wykonywanie zapytań o dzienniki przy użyciu Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Jak monitorować Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w umacie HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
