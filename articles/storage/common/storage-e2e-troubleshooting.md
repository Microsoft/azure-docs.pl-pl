---
title: Rozwiązywanie problemów z operacjami danych za pomocą diagnostyki i analizatora komunikatów
titleSuffix: Azure Storage
description: Samouczek przedstawiający kompleksowe Rozwiązywanie problemów z usługami analityka magazynu platformy Azure, AzCopy i Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: ozgun
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: e94810d53bf28aeb03de32a440c56a95232d0c2d
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506348"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Kompleksowe rozwiązywanie problemów przy użyciu metryk i rejestrowania usługi Azure Storage, narzędzia AzCopy i narzędzia Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnozowanie i rozwiązywanie problemów to kluczowa umiejętność tworzenia i obsługi aplikacji klienckich przy użyciu Microsoft Azure Storage. Ze względu na rozproszony charakter aplikacji platformy Azure, diagnozowanie i rozwiązywanie problemów oraz problemy z wydajnością mogą być bardziej skomplikowane niż w tradycyjnych środowiskach.

W tym samouczku pokazano, jak zidentyfikować pewne błędy, które mogą mieć wpływ na wydajność, i rozwiązać te błędy od końca do końca za pomocą narzędzi dostarczonych przez firmę Microsoft i usługę Azure Storage, aby zoptymalizować aplikację kliencką.

Ten samouczek zawiera praktyczne badanie kompleksowego scenariusza rozwiązywania problemów. Szczegółowe wskazówki dotyczące rozwiązywania problemów z aplikacjami usługi Azure Storage można znaleźć w temacie [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Narzędzia do rozwiązywania problemów z aplikacjami usługi Azure Storage

Aby rozwiązać problemy z aplikacjami klienckimi przy użyciu Microsoft Azure Storage, można użyć kombinacji narzędzi do określenia, kiedy wystąpił problem, oraz przyczyny problemu. Do tych narzędzi należą:

* **Analityka magazynu platformy Azure**. [Analityka magazynu platformy Azure](/rest/api/storageservices/Storage-Analytics) udostępnia metryki i rejestrowanie w usłudze Azure Storage.

  * **Metryki magazynu** śledzą metryki transakcji i metryki pojemności dla konta magazynu. Korzystając z metryk, można określić, jak działa aplikacja w oparciu o różne miary. Zobacz [analityka magazynu metryki schematu tabeli](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) , aby uzyskać więcej informacji na temat typów metryk śledzonych przez analityka magazynu.
  * **Rejestrowanie magazynu** rejestruje każde żądanie do usług Azure Storage w dzienniku po stronie serwera. Dziennik śledzi szczegółowe dane dla każdego żądania, w tym wykonywane operacje, stan operacji i informacje o opóźnieniu. Aby uzyskać więcej informacji na temat żądania i danych odpowiedzi zapisywana w dziennikach przez analityka magazynu, zobacz [analityka magazynu format dziennika](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Azure Portal**. Metryki i rejestrowanie dla konta magazynu można skonfigurować w [Azure Portal](https://portal.azure.com). Możesz również wyświetlić wykresy i wykresy, które pokazują, jak działa aplikacja z upływem czasu, i skonfigurować alerty w celu powiadomienia użytkownika o nieoczekiwanym przeprowadzeniu przez określoną metrykę.

    Aby uzyskać informacje o konfigurowaniu monitorowania w Azure Portal, zobacz [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md) .
* **AzCopy**. Dzienniki serwera dla usługi Azure Storage są przechowywane jako obiekty blob, dlatego można użyć AzCopy do skopiowania obiektów BLOB dziennika do katalogu lokalnego na potrzeby analizy za pomocą programu Microsoft Message Analyzer. Aby uzyskać więcej informacji na temat AzCopy, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md) .
* **Analizator komunikatów firmy Microsoft**. Analizator komunikatów jest narzędziem, które zużywa pliki dzienników i wyświetla dane dzienników w formacie wizualizacji, który ułatwia filtrowanie, wyszukiwanie i grupowanie danych dzienników w przydatnych zestawach, których można użyć do analizowania błędów i problemów z wydajnością. Więcej informacji o analizatorze komunikatów można znaleźć w [Przewodniku obsługi programu Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>Przykładowy scenariusz — informacje

Na potrzeby tego samouczka sprawdzimy scenariusz, w którym metryki usługi Azure Storage wskazują niską stawkę sukcesu dla aplikacji, która wywołuje usługę Azure Storage. Metryka niskiego procentu współczynnika sukcesu (pokazana jako **PercentSuccess** w [Azure Portal](https://portal.azure.com) i w tabelach metryk) śledzi operacje, które zakończyły się powodzeniem, ale zwracają kod stanu HTTP większy niż 299. W plikach dziennika magazynu po stronie serwera te operacje są rejestrowane ze stanem transakcji **ClientOtherErrors**. Aby uzyskać więcej informacji na temat metryki sukcesu o niskiej wartości procentowej, zobacz [metryki Pokaż wpisy dziennika o niskiej PercentSuccess lub analizie zawierają operacje ze stanem transakcji ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operacje usługi Azure Storage mogą zwracać kody stanu HTTP większe niż 299 w ramach ich normalnej funkcjonalności. Jednak te błędy w niektórych przypadkach wskazują, że można zoptymalizować aplikację kliencką w celu zwiększenia wydajności.

W tym scenariuszu zajmiemy się niską częstotliwością powodzeń równą wartości procentowej poniżej 100%. Można jednak wybrać inny poziom metryki, zgodnie z potrzebami. Zalecamy, aby podczas testowania aplikacji określić tolerancję bazową dla kluczowych metryk wydajności. Na przykład można określić, na podstawie testowania, czy aplikacja powinna mieć spójną stawkę procentową sukcesu wynoszącą 90%, lub 85%. Jeśli dane metryk pokazują, że aplikacja odnosi się od tej liczby, można sprawdzić, co może powodować wzrost.

W naszym przykładowym scenariuszu po ustaleniu, że Metryka procentu sukcesu jest niższa od 100%, będziemy przeanalizować dzienniki, aby znaleźć błędy, które są skorelowane z metrykami, i użyć ich do ustalenia, co jest przyczyną obniżenia poziomu sukcesu poniżej. Będziemy szukać błędów w zakresie 400. Następnie będziemy bardziej uważnie zbadać błędy 404 (nie znaleziono).

### <a name="some-causes-of-400-range-errors"></a>Przyczyny błędów z 400 zakresu

W poniższych przykładach pokazano próbkowanie niektórych błędów z zakresu 400 dla żądań dotyczących usługi Azure Blob Storage i ich możliwych przyczyn. Każdy z tych błędów, a także błędy w zakresie 300 i zakresu 500, mogą współdziałać z niską częstotliwością powodzeń (%).

Należy zauważyć, że listy poniżej są daleko od końca. Aby uzyskać szczegółowe informacje na temat ogólnych błędów usługi Azure Storage i informacji dotyczących błędów specyficznych dla poszczególnych usług magazynu, zobacz artykuł dotyczący [stanu i kodów błędów](https://msdn.microsoft.com/library/azure/dd179382.aspx) w witrynie MSDN.

#### <a name="status-code-404-not-found-examples"></a>Przykłady kodu stanu 404 (nie znaleziono)

Występuje, gdy operacja odczytu do kontenera lub obiektu BLOB nie powiedzie się, ponieważ nie znaleziono obiektu BLOB lub kontenera.

* Występuje, gdy kontener lub obiekt BLOB został usunięty przez innego klienta przed tym żądaniem.
* Występuje, jeśli używasz wywołania interfejsu API, które tworzy kontener lub obiekt BLOB po sprawdzeniu, czy istnieje. Interfejsy API metodę createifnotexists tworzą najpierw połączenie główne, aby sprawdzić istnienie kontenera lub obiektu BLOB; Jeśli nie istnieje, zwracany jest błąd 404, a następnie jest wykonywane drugie wywołanie w celu zapisania kontenera lub obiektu BLOB.

#### <a name="status-code-409-conflict-examples"></a>Przykłady kodu stanu 409 (konflikt)

* Występuje w przypadku utworzenia nowego kontenera lub obiektu BLOB przy użyciu interfejsu Create API bez sprawdzania istnienia, gdy kontener lub obiekt BLOB o tej nazwie już istnieje.
* Występuje, gdy kontener jest usuwany i próbujesz utworzyć nowy kontener o tej samej nazwie przed ukończeniem operacji usuwania.
* Występuje, gdy zostanie określona dzierżawa w kontenerze lub obiekcie blob, a już istnieje dzierżawa.

#### <a name="status-code-412-precondition-failed-examples"></a>Kod stanu 412 (niepowodzenie warunku wstępnego) przykłady

* Występuje, gdy warunek określony przez nagłówek warunkowy nie został spełniony.
* Występuje, gdy określony identyfikator dzierżawy nie jest zgodny z IDENTYFIKATORem dzierżawy kontenera lub obiektu BLOB.

## <a name="generate-log-files-for-analysis"></a>Generuj pliki dzienników do analizy

W tym samouczku będziemy używać analizatora komunikatów do pracy z trzema różnymi typami plików dziennika, chociaż można wybrać jedną z następujących czynności:

* **Dziennik serwera**, który jest tworzony podczas włączania rejestrowania w usłudze Azure Storage. Dziennik serwera zawiera dane dotyczące każdej operacji wywoływanej względem jednej z usług Azure Storage — obiektów blob, kolejek, tabel i plików. Dziennik serwera wskazuje, która operacja została wywołana i jaki kod stanu został zwrócony, a także inne szczegóły dotyczące żądania i odpowiedzi.
* **Dziennik klienta platformy .NET**tworzony po włączeniu rejestrowania po stronie klienta z poziomu aplikacji .NET. Dziennik klienta zawiera szczegółowe informacje o sposobie przygotowania żądania przez klienta i odebraniu i przetworzeniu odpowiedzi.
* **Dziennik śledzenia sieci http**, który zbiera dane dotyczące żądania HTTP/HTTPS i danych odpowiedzi, w tym dla operacji w usłudze Azure Storage. W tym samouczku wygenerujemy śledzenie sieci za pośrednictwem analizatora komunikatów.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurowanie rejestrowania i metryk po stronie serwera

Najpierw będziemy musieli skonfigurować rejestrowanie i metryki usługi Azure Storage, aby umożliwić analizowanie danych ze strony serwisowej. Rejestrowanie i metryki można skonfigurować na różne sposoby — za pośrednictwem [Azure Portal](https://portal.azure.com)przy użyciu programu PowerShell lub programowo. Zobacz [Włączanie metryk](storage-analytics-metrics.md#enable-metrics-by-using-the-azure-portal) i [Włączanie rejestrowania](storage-analytics-logging.md#enable-storage-logging) , aby uzyskać szczegółowe informacje o konfigurowaniu rejestrowania i metryk.

### <a name="configure-net-client-side-logging"></a>Konfigurowanie rejestrowania po stronie klienta platformy .NET

Aby skonfigurować rejestrowanie po stronie klienta dla aplikacji .NET, Włącz diagnostykę .NET w pliku konfiguracyjnym aplikacji (web.config lub app.config). Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie po stronie klienta przy użyciu biblioteki klienckiej usługi .NET Storage](https://msdn.microsoft.com/library/azure/dn782839.aspx) i [rejestrowania po stronie klienta za pomocą zestawu Microsoft Azure Storage SDK dla języka Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) w witrynie MSDN.

Dziennik po stronie klienta zawiera szczegółowe informacje o sposobie przygotowania żądania przez klienta i odebraniu i przetworzeniu odpowiedzi.

Biblioteka klienta magazynu przechowuje dane dziennika po stronie klienta w lokalizacji określonej w pliku konfiguracyjnym aplikacji (web.config lub app.config).

### <a name="collect-a-network-trace"></a>Zbieranie danych śledzenia sieci

Za pomocą analizatora komunikatów można zbierać dane śledzenia sieci HTTP/HTTPS, gdy aplikacja kliencka jest uruchomiona. Analizator komunikatów używa [programu Fiddler](https://www.telerik.com/fiddler) na zapleczu. Przed zebraniem śledzenia sieci zalecamy skonfigurowanie programu Fiddler do rejestrowania nieszyfrowanego ruchu HTTPS:

1. Zainstaluj [programu Fiddler](https://www.telerik.com/download/fiddler).
2. Uruchom programu Fiddler.
3. Wybierz **Narzędzia | Opcje programu Fiddler**.
4. W oknie dialogowym Opcje upewnij się, że jest zaznaczona opcja **przechwytywania połączeń HTTPS** i **odszyfrowywania ruchu https** , jak pokazano poniżej.

![Konfigurowanie opcji programu Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Na potrzeby samouczka Zbierz i Zapisz ślad sieciowy w programie Message Analyzer, a następnie utwórz sesję analizy w celu przeanalizowania śledzenia i dzienników. Aby zebrać dane śledzenia sieci w analizatorze komunikatów:

1. W oknie Analizator komunikatów wybierz pozycję **plik | Szybkie śledzenie | Nieszyfrowany protokół HTTPS**.
2. Śledzenie rozpocznie się natychmiast. Wybierz pozycję **Zatrzymaj** , aby zatrzymać śledzenie, aby można było skonfigurować go do śledzenia tylko ruchu magazynu.
3. Wybierz pozycję **Edytuj** , aby edytować sesję śledzenia.
4. Wybierz link **Konfiguruj** z prawej strony dostawcy ETW **Microsoft-PEF-WebProxy** .
5. W oknie dialogowym **Ustawienia zaawansowane** kliknij kartę **dostawca** .
6. W polu **Filtr nazwy hosta** Określ punkty końcowe magazynu, rozdzielone spacjami. Na przykład można określić punkty końcowe w następujący sposób: Zmień `storagesample` nazwę konta magazynu:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Zamknij okno dialogowe, a następnie kliknij przycisk **Uruchom ponownie** , aby rozpocząć zbieranie śladu z filtrem nazwy hosta, tak aby tylko ruch sieciowy usługi Azure Storage został uwzględniony w śledzeniu.

> [!NOTE]
> Po zakończeniu zbierania danych śledzenia sieci zdecydowanie zalecamy przywrócenie ustawień, które mogły zostać zmienione w programu Fiddler w celu odszyfrowania ruchu HTTPS. W oknie dialogowym Opcje programu Fiddler Usuń zaznaczenie pól wyboru **Przechwytuj połączenia HTTPS** i **Odszyfruj ruch https** .

Aby uzyskać więcej informacji [, zobacz Korzystanie z funkcji śledzenia sieci](https://technet.microsoft.com/library/jj674819.aspx) w witrynie TechNet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Przejrzyj dane metryk w Azure Portal

Po uruchomieniu aplikacji przez pewien czas możesz przejrzeć Wykresy metryk, które pojawiają się w [Azure Portal](https://portal.azure.com) , aby obserwować, jak działa usługa.

Najpierw przejdź do swojego konta magazynu w Azure Portal. Domyślnie wykres monitorowania z metryką **procentu sukcesu** jest wyświetlany w bloku konta. Jeśli wcześniej zmodyfikowano wykres w celu wyświetlenia różnych metryk, Dodaj metrykę **procentową sukcesu** .

Na wykresie monitorowania będzie teraz widoczny **procent sukcesu** , wraz z innymi metrykami, które mogły zostać dodane. W scenariuszu będziemy badać dalej, analizując dzienniki w analizatorze komunikatów, Częstotliwość powodzeń (%) jest nieco niższa poniżej 100%.

Aby uzyskać więcej informacji na temat dodawania i dostosowywania wykresów metryk, zobacz [Dostosowywanie wykresów metryk](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Po włączeniu metryk magazynu może upłynąć trochę czasu, zanim dane metryk będą widoczne w Azure Portal. Jest to spowodowane tym, że metryki godzinowe za poprzednią godzinę nie są wyświetlane w Azure Portal do momentu wyczerpania bieżącej godziny. Ponadto metryki minut nie są aktualnie wyświetlane w Azure Portal. Dlatego w zależności od tego, kiedy włączasz metryki, wyświetlanie danych metryk może trwać do dwóch godzin.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Użyj AzCopy do kopiowania dzienników serwera do katalogu lokalnego

Usługa Azure Storage zapisuje dane dziennika serwera w obiektach Blob, natomiast metryki są zapisywane w tabelach. Obiekty blob dziennika są dostępne w dobrze znanym `$logs` kontenerze dla konta magazynu. Obiekty blob dziennika są nazywane hierarchicznie według roku, miesiąca, dnia i godziny, dzięki czemu można łatwo zlokalizować zakres czasu, który chcesz zbadać. Na przykład na `storagesample` koncie kontener obiektów BLOB dziennika dla 01/02/2015, od 8-9 am, wynosi `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` . Poszczególne obiekty blob w tym kontenerze są nazywane sekwencyjnie, począwszy od `000000.log` .

Możesz użyć narzędzia wiersza polecenia AzCopy, aby pobrać te pliki dzienników po stronie serwera do wybranej lokalizacji na komputerze lokalnym. Na przykład można użyć poniższego polecenia, aby pobrać pliki dziennika dla operacji obiektu BLOB, które miały miejsce w dniu 2 stycznia 2015 do folderu `C:\Temp\Logs\Server` ; Zastąp `<storageaccountname>` nazwą swojego konta magazynu:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy jest dostępna do pobrania na stronie [pliki do pobrania na platformie Azure](https://azure.microsoft.com/downloads/) . Aby uzyskać szczegółowe informacje na temat korzystania z AzCopy, zobacz [transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md).

Aby uzyskać dodatkowe informacje na temat pobierania dzienników po stronie serwera, zobacz [pobieranie danych dziennika rejestrowania magazynu](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analizowanie danych dzienników przy użyciu programu Microsoft Message Analyzer

Microsoft Message Analyzer to narzędzie do przechwytywania, wyświetlania i analizowania ruchu komunikatów, zdarzeń i innych komunikatów systemowych lub aplikacji w scenariuszach rozwiązywania problemów i diagnostyki. Analizator komunikatów umożliwia również ładowanie, agregowanie i analizowanie danych z dzienników i zapisanych plików śledzenia. Aby uzyskać więcej informacji na temat analizatora komunikatów, zobacz [Przewodnik obsługi programu Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Analizator komunikatów zawiera zasoby dla usługi Azure Storage, które ułatwiają analizowanie dzienników serwera, klienta i sieci. W tej sekcji omówiono sposób użycia tych narzędzi w celu rozwiązania problemu z niską wartością procentową sukcesu w dziennikach magazynu.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Pobieranie i Instalowanie programu Message Analyzer oraz zasobów usługi Azure Storage

1. Pobierz [Analizator komunikatów](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Uruchom Analizator komunikatów.
3. W menu **Narzędzia** wybierz pozycję **Menedżer zasobów**. W oknie dialogowym **Menedżer zasobów** wybierz pozycję **pliki do pobrania**, a następnie odfiltruj w **usłudze Azure Storage**. Zostaną wyświetlone zasoby usługi Azure Storage, jak pokazano na poniższej ilustracji.
4. Kliknij pozycję **Synchronizuj wszystkie wyświetlane elementy** , aby zainstalować zasoby usługi Azure Storage. Dostępne są następujące zasoby:
   * **Reguły dotyczące kolorów usługi Azure Storage:** Reguły kolorów usługi Azure Storage umożliwiają definiowanie specjalnych filtrów, które używają koloru, tekstu i stylów czcionki do wyróżniania komunikatów, które zawierają określone informacje w śladzie.
   * **Wykresy usługi Azure Storage:** Wykresy usługi Azure Storage to wstępnie zdefiniowane wykresy przedstawiające dane dziennika serwera programu Graph. Należy pamiętać, że aby korzystać z wykresów usługi Azure Storage w tym momencie, można załadować tylko dziennik serwera do siatki analizy.
   * **Analizatory usługi Azure Storage:** Analizatory usługi Azure Storage analizują klienta usługi Azure Storage, serwer i dzienniki HTTP w celu wyświetlenia ich w siatce analizy.
   * **Filtry usługi Azure Storage:** Filtry usługi Azure Storage są wstępnie zdefiniowanymi kryteriami, których można użyć do wykonywania zapytań dotyczących danych w siatce analizy.
   * **Układy widoków usługi Azure Storage:** Układy widoków usługi Azure Storage to wstępnie zdefiniowane układy kolumn i grupowania w siatce analizy.
5. Po zainstalowaniu zasobów należy ponownie uruchomić analizatora komunikatów.

![Menedżer zasobów analizatora komunikatów](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Zainstaluj wszystkie zasoby usługi Azure Storage widoczne na potrzeby tego samouczka.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Zaimportuj pliki dzienników do analizatora komunikatów

Wszystkie zapisane pliki dziennika (po stronie serwera, po stronie klienta i sieci) można zaimportować do jednej sesji w programie Microsoft Message Analyzer na potrzeby analizy.

1. W menu **plik** w programie Microsoft Message Analyzer kliknij pozycję **Nowa sesja**, a następnie kliknij pozycję **pusta sesja**. W oknie dialogowym **Nowa sesja** wprowadź nazwę sesji analizy. W panelu **szczegóły sesji** kliknij przycisk **pliki** .
2. Aby załadować dane śledzenia sieci wygenerowane przez analizator komunikatów, kliknij pozycję **Dodaj pliki**, przejdź do lokalizacji, w której zapisano plik. matp w sesji śledzenia sieci Web, wybierz plik matp, a następnie kliknij przycisk **Otwórz**.
3. Aby załadować dane dziennika po stronie serwera, kliknij przycisk **Dodaj pliki**, przejdź do lokalizacji, w której pobrano dzienniki po stronie serwera, wybierz pliki dziennika dla zakresu czasu, który chcesz analizować, a następnie kliknij przycisk **Otwórz**. Następnie w panelu **szczegóły sesji** Ustaw listę rozwijaną **Konfiguracja dziennika tekstowego** dla każdego pliku dziennika po stronie serwera do **AzureStorageLog** , aby upewnić się, że program Microsoft Message Analyzer może prawidłowo przeanalizować plik dziennika.
4. Aby załadować dane dziennika po stronie klienta, kliknij pozycję **Dodaj pliki**, przejdź do lokalizacji, w której zapisano dzienniki po stronie klienta, wybierz pliki dziennika, które chcesz analizować, a następnie kliknij przycisk **Otwórz**. Następnie w panelu **szczegóły sesji** Ustaw listę rozwijaną **Konfiguracja dziennika tekstowego** dla każdego pliku dziennika po stronie klienta na **AzureStorageClientDotNetV4** , aby upewnić się, że program Microsoft Message Analyzer może prawidłowo przeanalizować plik dziennika.
5. Kliknij przycisk **Uruchom** w oknie dialogowym **Nowa sesja** , aby załadować i przeanalizować dane dziennika. Dane dziennika są wyświetlane w siatce analizy analizatora wiadomości.

Na poniższej ilustracji przedstawiono przykładową sesję skonfigurowaną z plikami dzienników serwera, klienta i sieci.

![Skonfiguruj sesję analizatora komunikatów](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Należy zauważyć, że analizator komunikatów ładuje pliki dzienników do pamięci. W przypadku dużego zestawu danych dziennika należy je filtrować w celu uzyskania najlepszej wydajności z poziomu analizatora komunikatów.

Najpierw Ustal przedział czasu, który ma być przeglądany, i Zachowaj ten przedział czasu, jak to możliwe. W wielu przypadkach warto przejrzeć okres co najmniej minut lub godzin. Zaimportuj najmniejszy zbiór dzienników, który może spełniać Twoje potrzeby.

Jeśli nadal masz dużą ilość danych dziennika, możesz chcieć określić filtr sesji, aby przefiltrować dane dziennika przed załadowaniem go. W polu **Filtr sesji** wybierz przycisk **Biblioteka** , aby wybrać wstępnie zdefiniowany filtr; na przykład możesz wybrać **Filtr czasu globalnego I** z filtrów usługi Azure Storage, aby odfiltrować w przedziale czasu. Następnie można edytować kryteria filtrowania, aby określić początkową i końcową sygnaturę czasową dla interwału, który ma być wyświetlany. Można również filtrować według określonego kodu stanu; można na przykład wybrać opcję ładowania tylko wpisów dziennika, w których kod stanu to 404.

Aby uzyskać więcej informacji na temat importowania danych dziennika do programu Microsoft Message Analyzer, zobacz [pobieranie danych komunikatów](https://technet.microsoft.com/library/dn772437.aspx) w witrynie TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Użyj identyfikatora żądania klienta, aby skorelować dane pliku dziennika

Biblioteka klienta usługi Azure Storage automatycznie generuje unikatowy identyfikator żądania klienta dla każdego żądania. Ta wartość jest zapisywana w dzienniku klienta, dzienniku serwera i funkcji śledzenia sieci, dzięki czemu można za jej pomocą skorelować dane we wszystkich trzech dziennikach w ramach analizatora komunikatów. Zobacz [Identyfikator żądania klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) , aby uzyskać dodatkowe informacje na temat identyfikatora żądania klienta.

W poniższych sekcjach opisano, jak używać wstępnie skonfigurowanych i niestandardowych widoków układu do skorelowania i grupowania danych na podstawie identyfikatora żądania klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Wybierz układ widoku do wyświetlenia w siatce analizy

Zasoby magazynu dla analizatora komunikatów obejmują układy widoków usługi Azure Storage, które są wstępnie skonfigurowanymi widokami, których można użyć do wyświetlania danych przy użyciu przydatnych grup i kolumn dla różnych scenariuszy. Możesz również utworzyć niestandardowe układy widoków i zapisać je do ponownego użycia.

Na poniższej ilustracji przedstawiono menu **Widok układu** dostępne po wybraniu pozycji **Widok układu** na wstążce paska narzędzi. Układy widoków dla usługi Azure Storage są pogrupowane pod węzłem **usługi Azure Storage** w menu. Możesz wyszukać `Azure Storage` w polu wyszukiwania, aby filtrować tylko w układach widoków usługi Azure Storage. Możesz również wybrać gwiazdkę obok układu widoku, aby uczynić go ulubionym i wyświetlać w górnej części menu.

![Wyświetl menu układu](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Aby rozpocząć, wybierz pozycję **pogrupowane według identyfikatorem żądania klienta i modułu**. Ten widok umożliwia grupom rejestrowanie danych ze wszystkich trzech dzienników najpierw według identyfikatora żądania klienta, a następnie według źródłowego pliku dziennika (lub **modułu** w analizatorze komunikatów). W tym widoku możesz przejść do szczegółów określonego identyfikatora żądania klienta i wyświetlić dane ze wszystkich trzech plików dziennika dla tego identyfikatora żądania klienta.

Na poniższym obrazie przedstawiono ten Widok układu stosowany do przykładowych danych dziennika z podzbiorem wyświetlanych kolumn. Można zobaczyć, że dla określonego identyfikatora żądania klienta w siatce analizy są wyświetlane dane z dziennika klienta, dziennika serwera i śledzenia sieci.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Różne pliki dzienników mają różne kolumny, dlatego w przypadku wyświetlenia danych z wielu plików dziennika w siatce analizy niektóre kolumny mogą nie zawierać żadnych danych dla danego wiersza. Na przykład na powyższym obrazie wiersze dziennika klienta nie zawierają żadnych danych dla kolumn **timestamp**, **TimeElapsed**, **Source**i **Destination** , ponieważ te kolumny nie istnieją w dzienniku klienta, ale istnieją w śladach sieci. Podobnie w kolumnie **sygnatura czasowa** są wyświetlane dane sygnatur czasowych z dziennika serwera, ale w kolumnach **TimeElapsed**, **Source**i **Destination** nie są wyświetlane żadne dane, które nie są częścią dziennika serwera.
>
>

Oprócz korzystania z układów widoków usługi Azure Storage można także definiować i zapisywać własne układy widoku. Można wybrać inne żądane pola do grupowania danych i zapisać grupowanie jako część niestandardowego układu.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Zastosuj reguły koloru do siatki analizy

Zasoby magazynu obejmują również reguły koloru, które oferują wizualizację, aby identyfikować różne typy błędów w siatce analizy. Reguły wstępnie zdefiniowanego koloru stosują się do błędów HTTP, dlatego są wyświetlane tylko dla dziennika serwera i śledzenia sieci.

Aby zastosować reguły koloru, wybierz pozycję **reguły koloru** na wstążce paska narzędzi. W menu zostaną wyświetlone reguły kolorów usługi Azure Storage. Na potrzeby samouczka wybierz pozycję **Błędy klienta (StatusCode z zakresu od 400 do 499)**, jak pokazano na poniższej ilustracji.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oprócz używania reguł kolorów usługi Azure Storage można także definiować i zapisywać własne reguły koloru.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Grupowanie i filtrowanie danych dziennika w celu znalezienia błędów z zakresu 400

Następnie będziemy grupować i filtrować dane dziennika, aby znaleźć wszystkie błędy w zakresie 400.

1. Znajdź kolumnę **StatusCode** w siatce analizy, kliknij prawym przyciskiem myszy nagłówek kolumny, a następnie wybierz pozycję **Grupa**.
2. Następnie Grupuj według kolumny **identyfikatorem żądania klienta** . Zobaczysz, że dane w siatce analizy są teraz zorganizowane według kodu stanu i według identyfikatora żądania klienta.
3. Wyświetl okno narzędzi filtru widoku, jeśli nie zostało jeszcze wyświetlone. Na wstążce paska narzędzi wybierz pozycję **narzędzia okna**, a następnie **Wyświetl filtr**.
4. Aby przefiltrować dane dziennika w celu wyświetlenia tylko błędów 400-zakresowych, Dodaj następujące kryteria filtrowania do okna **filtru widoku** i kliknij przycisk **Zastosuj**:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Na poniższej ilustracji przedstawiono wyniki tego grupowania i filtrowania. Rozwijanie pola **identyfikatorem żądania klienta** poniżej grupowania dla kodu stanu 409, na przykład, pokazuje operację, która spowodowała ten kod stanu.

![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po zastosowaniu tego filtru zobaczysz, że wiersze z dziennika klienta zostaną wykluczone, ponieważ dziennik klienta nie zawiera kolumny **StatusCode** . Aby rozpocząć pracę z programem, Przejrzyjmy dzienniki śledzenia serwera i sieci, aby zlokalizować 404 błędów, a następnie powrócimy do dziennika klienta, aby sprawdzić, które z nich przeprowadziły.

> [!NOTE]
> Można filtrować według kolumny **StatusCode** i nadal wyświetlać dane ze wszystkich trzech dzienników, w tym dziennika klienta, w przypadku dodania wyrażenia do filtru zawierającego wpisy dziennika, w których kod stanu ma wartość null. Aby skonstruować to wyrażenie filtru, użyj:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ten filtr zwraca wszystkie wiersze z dziennika klienta i tylko wiersze z dziennika serwera i dziennika HTTP, w których kod stanu jest większy niż 400. Jeśli zastosujesz go do układu widoku pogrupowanego według identyfikatora i modułu żądania klienta, możesz wyszukiwać lub przewijać wpisy dziennika, aby znaleźć, gdzie reprezentowane są wszystkie trzy dzienniki.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrowanie danych dziennika w celu znalezienia błędów 404

Zasoby magazynu obejmują wstępnie zdefiniowane filtry, których można użyć do zawężenia danych dziennika w celu znalezienia błędów lub trendów, których szukasz. Następnie zastosujemy dwa wstępnie zdefiniowane filtry: jeden, który filtruje serwer i dzienniki śledzenia sieci dla błędów 404, i jeden filtruje dane w określonym zakresie czasu.

1. Wyświetl okno narzędzi filtru widoku, jeśli nie zostało jeszcze wyświetlone. Na wstążce paska narzędzi wybierz pozycję **narzędzia okna**, a następnie **Wyświetl filtr**.
2. W oknie Filtr widoku wybierz opcję **Biblioteka**i Wyszukaj w `Azure Storage` celu znalezienia filtrów usługi Azure Storage. Wybierz filtr dla **komunikatów 404 (nieznalezionych) we wszystkich dziennikach**.
3. Ponownie Wyświetl menu **Biblioteka** i Znajdź i wybierz **Filtr czasu globalnego**.
4. Edytuj sygnatury czasowe wyświetlane w filtrze do zakresu, który chcesz wyświetlić. Pomoże to zawęzić zakres danych do przeanalizowania.
5. Filtr powinien wyglądać podobnie do poniższego przykładu. Kliknij przycisk **Zastosuj** , aby zastosować filtr do siatki analizy.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Układ widoku usługi Azure Storage](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analizowanie danych dziennika

Teraz, gdy dane zostały pogrupowane i przefiltrowane, możesz przejrzeć szczegóły poszczególnych żądań, które wygenerowały błędy 404. W bieżącym układzie widoku dane są pogrupowane według identyfikatora żądania klienta, a następnie według źródła dziennika. Ponieważ są filtrowane żądania, w których pole StatusCode zawiera 404, zobaczysz tylko dane śledzenia serwera i sieci, a nie dane dziennika klienta.

Na poniższym obrazie przedstawiono konkretne żądanie, w którym operacja pobierania obiektu BLOB wydawała 404, ponieważ obiekt BLOB nie istnieje. Należy zauważyć, że niektóre kolumny zostały usunięte ze standardowego widoku, aby można było wyświetlić odpowiednie dane.

![Filtrowane dzienniki śledzenia serwera i sieci](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Następnie będziemy skorelować ten identyfikator żądania klienta z danymi dziennika klienta, aby zobaczyć akcje podejmowane przez klienta po wystąpieniu błędu. Możesz wyświetlić nowy widok siatki analizy dla tej sesji, aby wyświetlić dane dziennika klienta, które są otwierane na drugiej karcie:

1. Najpierw skopiuj wartość pola **identyfikatorem żądania klienta** do Schowka. Możesz to zrobić, wybierając dowolny wiersz, lokalizując pole **identyfikatorem żądania klienta** , klikając prawym przyciskiem myszy wartość danych i wybierając polecenie **Kopiuj "identyfikatorem żądania klienta"**.
2. Na wstążce paska narzędzi wybierz pozycję **Nowa przeglądarka**, a następnie wybierz pozycję **analiza siatki** , aby otworzyć nową kartę. Nowa karta zawiera wszystkie dane w plikach dziennika, bez grupowania, filtrowania lub reguł kolorów.
3. Na wstążce paska narzędzi wybierz pozycję **Widok układu**, a następnie wybierz pozycję **wszystkie kolumny klienta .NET** w sekcji **Azure Storage** . Ten układ widoku przedstawia dane z dziennika klienta oraz dzienniki śledzenia serwera i sieci. Domyślnie jest ono sortowane w kolumnie **MessageNumber** .
4. Następnie wyszukaj w dzienniku klienta identyfikator żądania klienta. Na wstążce paska narzędzi wybierz pozycję **Znajdź komunikaty**, a następnie w polu **Znajdź** określ filtr niestandardowy dla identyfikatora żądania klienta. Użyj tej składni dla filtru, określając własny identyfikator żądania klienta:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Analizator komunikatów lokalizuje i wybiera pierwszy wpis dziennika, w którym kryteria wyszukiwania są zgodne z IDENTYFIKATORem żądania klienta. W dzienniku klienta istnieje kilka wpisów dla każdego identyfikatora żądania klienta, dlatego warto pogrupować je w polu **identyfikatorem żądania klienta** , aby ułatwić ich jednoczesne wyświetlanie. Na poniższej ilustracji przedstawiono wszystkie komunikaty w dzienniku klienta dla określonego identyfikatora żądania klienta.

![Dziennik klienta przedstawiający błędy 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Korzystając z danych pokazanych w widoku układów na tych dwóch kartach, można analizować dane żądania, aby określić, co mogło być przyczyną błędu. Możesz również sprawdzić żądania, które poprzedzają ten element, aby sprawdzić, czy poprzednie zdarzenie mogło spowodować błąd 404. Na przykład można przejrzeć wpisy dziennika klienta poprzedzające ten identyfikator żądania klienta, aby określić, czy obiekt BLOB mógł zostać usunięty, lub jeśli błąd był spowodowany przez aplikację kliencką wywołującą interfejs API metodę createifnotexists w kontenerze lub obiekcie blob. W dzienniku klienta można znaleźć adres obiektu BLOB w polu **Opis** . w dziennikach śledzenia serwera i sieci te informacje są wyświetlane w polu **Podsumowanie** .

Po poznaniu adresu obiektu BLOB, który obsłużył błąd 404, można dokładniej zbadać. W przypadku przeszukiwania wpisów dziennika dla innych komunikatów skojarzonych z operacjami w tym samym obiekcie BLOB można sprawdzić, czy klient usunął wcześniej jednostkę.

## <a name="analyze-other-types-of-storage-errors"></a>Analizowanie innych typów błędów magazynu

Teraz, gdy wiesz już, jak używać analizatora komunikatów do analizowania danych dziennika, możesz analizować inne typy błędów za pomocą układów widoku, reguł kolorów i wyszukiwania/filtrowania. W poniższych tabelach przedstawiono niektóre problemy, które mogą wystąpić, oraz kryteria filtrowania, za pomocą których można je zlokalizować. Aby uzyskać więcej informacji na temat konstruowania filtrów i języka filtrowania analizatora komunikatów, zobacz [filtrowanie danych komunikatów](https://technet.microsoft.com/library/jj819365.aspx).

| Aby zbadać... | Użyj wyrażenia filtru... | Wyrażenie dotyczy dziennika (klient, serwer, Sieć, wszystkie) |
| --- | --- | --- |
| Nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce |AzureStorageClientDotNetV4. Description zawiera "ponawianie nieudanej operacji". |Klient |
| Zwiększenie HTTP w wzrost percentthrottlingerror |Protokoły. Response. StatusCode = = 500 &#124;&#124; HTTP. Odpowiedź. StatusCode = = 503 |Sieć |
| Zwiększ w wzrost percenttimeouterror |Protokoły. Odpowiedź. StatusCode = = 500 |Sieć |
| Zwiększ w wzrost percenttimeouterror (wszystko) |* StatusCode = = 500 |Wszystko |
| Zwiększ w wzrost percentnetworkerror |AzureStorageClientDotNetV4. EventLogEntry. Level < 2 |Klient |
| Komunikaty HTTP 403 (Zabronione) |Protokoły. Odpowiedź. StatusCode = = 403 |Sieć |
| Komunikaty HTTP 404 (Nie znaleziono) |Protokoły. Odpowiedź. StatusCode = = 404 |Sieć |
| 404 (wszystkie) |* StatusCode = = 404 |Wszystko |
| Problem z autoryzacją sygnatury dostępu współdzielonego |AzureStorageLog. stanem żądania = = "SASAuthorizationError" |Sieć |
| Komunikaty HTTP 409 (Konflikt) |Protokoły. Odpowiedź. StatusCode = = 409 |Sieć |
| 409 (wszystkie) |* StatusCode = = 409 |Wszystko |
| Niskie PercentSuccess lub wpisy dziennika analizy zawierają operacje ze stanem transakcji ClientOtherErrors |AzureStorageLog. stanem żądania = = "ClientOtherError" |Serwer |
| Ostrzeżenie nagle |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) oraz (AzureStorageLog. RequestPacketSize <1460) i (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS >= 200) |Serwer |
| Zakres czasu w dziennikach serwera i sieci |#Timestamp >= 2014-10-20T16:36:38 i #Timestamp <= 2014-10-20T16:36:39 |Serwer, Sieć |
| Zakres czasu w dziennikach serwera |AzureStorageLog. timestamp >= 2014-10-20T16:36:38 i AzureStorageLog. timestamp <= 2014-10-20T16:36:39 |Serwer |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z kompleksowym scenariuszem w usłudze Azure Storage, zobacz następujące zasoby:

* [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Analityka magazynu](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Instrukcja obsługi narzędzia Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
