---
title: Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej
titleSuffix: Azure Storage
description: Dowiedz się, jak używać magazynu geograficznie nadmiarowego do projektowania aplikacji o wysokiej dostępności, która jest wystarczająco elastyczna, aby obsłużyć awarię.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c16f8233a2800025a8c6f601e236b86d2fd044fd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480687"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej

Typową cechą infrastruktury opartej na chmurze, takiej jak usługa Azure Storage, jest zapewnienie wysokiej dostępności i trwałej platformy do hostowania danych i aplikacji. Deweloperzy aplikacji opartych na chmurze muszą uważnie rozważyć, jak korzystać z tej platformy, aby zmaksymalizować te korzyści dla swoich użytkowników. Usługa Azure Storage oferuje Geograficznie nadmiarowy magazyn, aby zapewnić wysoką dostępność nawet w przypadku awarii regionalnej. Konta magazynu skonfigurowane na potrzeby replikacji geograficznie nadmiarowej są replikowane synchronicznie w regionie podstawowym, a następnie asynchronicznie replikowane do regionu pomocniczego, który ma setki kilometrów.

Usługa Azure Storage oferuje dwie opcje replikacji geograficznie nadmiarowej. Jedyna różnica między tymi dwiema opcjami polega na tym, jak dane są replikowane w regionie podstawowym:

* [Magazyn Geograficznie nadmiarowy (GZRS)](storage-redundancy.md): dane są replikowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu *magazynu Strefowo nadmiarowego (ZRS)*, a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz strefę geograficzną z dostępem do odczytu (RA-GZRS).

    Firma Microsoft zaleca korzystanie z usługi GZRS/RA-GZRS w scenariuszach wymagających maksymalnej dostępności i trwałości.

* [Magazyn Geograficznie nadmiarowy (GRS)](storage-redundancy.md): dane są replikowane synchronicznie trzy razy w regionie podstawowym przy użyciu *magazynu lokalnie nadmiarowego (LRS)*, a następnie replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS).

W tym artykule przedstawiono sposób projektowania aplikacji w celu obsługi awarii w regionie podstawowym. Jeśli region podstawowy stanie się niedostępny, aplikacja może dostosowywać się do wykonywania operacji odczytu w regionie pomocniczym. Przed rozpoczęciem upewnij się, że konto magazynu jest skonfigurowane dla usługi RA-GRS lub RA-GZRS.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Zagadnienia dotyczące projektowania aplikacji podczas odczytu z pomocniczego

Celem tego artykułu jest przedstawienie sposobu projektowania aplikacji, która będzie nadal działać (choć w ograniczonej pojemności) nawet w przypadku poważnych awarii w podstawowym centrum danych. Możesz zaprojektować aplikację w celu obsługi przejściowych lub długotrwałych problemów, odczytując z regionu pomocniczego, gdy występuje problem zakłócający odczyt z regionu podstawowego. Po ponownym udostępnieniu regionu podstawowego aplikacja może powrócić do odczytu z regionu podstawowego.

Pamiętaj o tych najważniejszych kwestiach podczas projektowania aplikacji dla usługi RA-GRS lub RA-GZRS:

* Usługa Azure Storage przechowuje kopię danych przechowywanych w regionie podstawowym w regionie pomocniczym tylko do odczytu. Jak wspomniano powyżej, usługa Storage określa lokalizację regionu pomocniczego.

* Kopia tylko do odczytu jest [ostatecznie spójna](https://en.wikipedia.org/wiki/Eventual_consistency) z danymi w regionie podstawowym.

* W przypadku obiektów blob, tabel i kolejek można wysłać zapytanie do regionu pomocniczego w celu uzyskania wartości *czasu ostatniej synchronizacji* informującej o wystąpieniu ostatniej replikacji z poziomu podstawowego do regionu pomocniczego. (Nie jest to obsługiwane w przypadku Azure Files, w tej chwili nie ma nadmiarowości RA-GRS).

* Biblioteka klienta magazynu umożliwia odczytywanie i zapisywanie danych w regionie podstawowym lub pomocniczym. Żądania odczytu można również przekierować automatycznie do regionu pomocniczego, jeśli żądanie odczytu do regionu podstawowego przetrwa limit czasu.

* Jeśli region podstawowy stanie się niedostępny, możesz zainicjować tryb failover konta. Po przełączeniu w tryb failover do regionu pomocniczego wpisy DNS wskazujące region podstawowy są zmieniane, aby wskazywały na region pomocniczy. Po zakończeniu pracy w trybie failover dostęp do zapisu jest przywracany dla kont GRS i RA-GRS. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](storage-disaster-recovery-guidance.md).

### <a name="using-eventually-consistent-data"></a>Przy użyciu ostatecznie spójnych danych

Proponowane rozwiązanie zakłada, że możliwe jest zwrócenie potencjalnie starych danych do aplikacji wywołującej. Ponieważ dane w regionie pomocniczym są ostatecznie spójne, możliwe, że region podstawowy może stać się niedostępny, zanim aktualizacja do regionu pomocniczego zakończy replikację.

Załóżmy na przykład, że klient prześle aktualizację pomyślnie, ale region podstawowy kończy się niepowodzeniem przed propagacją aktualizacji do regionu pomocniczego. Gdy klient zażąda odczytania danych z powrotem, otrzymuje nieodświeżone dane z regionu pomocniczego zamiast zaktualizowanych danych. Podczas projektowania aplikacji należy zdecydować, czy jest to akceptowalne, a jeśli tak, jak zostanie wyświetlony komunikat klienta. 

W dalszej części tego artykułu pokazujemy, jak sprawdzić czas ostatniej synchronizacji danych pomocniczych, aby sprawdzić, czy pomocnicza jest aktualna.

### <a name="handling-services-separately-or-all-together"></a>Obsługa usług osobno lub ze sobą

Chociaż jest to mało prawdopodobne, istnieje możliwość, że jedna usługa stanie się niedostępna, a pozostałe usługi są nadal w pełni funkcjonalne. Można obsłużyć ponowną próbę i tryb tylko do odczytu dla każdej usługi (obiekty blob, kolejki, tabele) lub można obsłużyć ponownych prób obsługi ogólnie dla wszystkich usług magazynu.

Na przykład jeśli używasz kolejek i obiektów BLOB w aplikacji, możesz zdecydować się na umieszczenie w osobnym kodzie, aby obsłużyć błędy powtarzające się dla każdego z nich. Następnie w przypadku ponowienia próby z usługi BLOB Service, gdy usługa kolejki nadal działa, będzie to miało wpływ tylko na część aplikacji, która obsługuje obiekty blob. Jeśli użytkownik zdecyduje się na ogólne obsłużenie wszystkich ponownych prób usługi magazynu, a wywołanie usługi BLOB zwróci błąd, a następnie wpłynie na żądania zarówno do usługi BLOB Service, jak i usługi kolejki.

Ostatecznie jest to zależne od złożoności aplikacji. Możesz zrezygnować z obsługi błędów według usługi, ale zamiast przekierowywać żądania odczytu dla wszystkich usług magazynu do regionu pomocniczego i uruchamiać aplikację w trybie tylko do odczytu po wykryciu problemu z usługą magazynu w regionie podstawowym.

### <a name="other-considerations"></a>Inne zagadnienia

Są to inne zagadnienia, które omówiono w dalszej części tego artykułu.

* Obsługa ponownych prób żądania odczytu przy użyciu wzorca wyłącznika

* Ostatecznie spójne dane i czas ostatniej synchronizacji

* Testowanie

## <a name="running-your-application-in-read-only-mode"></a>Uruchamianie aplikacji w trybie tylko do odczytu

Aby efektywnie przygotować się do przestoju w regionie podstawowym, musisz mieć możliwość obsługi zarówno żądań odczytu zakończonych niepowodzeniem, jak i nieudanych żądań aktualizacji (z aktualizacją w tym przypadku oznacza Wstawianie, aktualizacje i usunięcia). Jeśli region podstawowy ulegnie awarii, żądania odczytu mogą zostać przekierowane do regionu pomocniczego. Jednak żądania aktualizacji nie mogą zostać przekierowane do pomocniczego, ponieważ pomocniczy jest tylko do odczytu. Z tego powodu należy zaprojektować aplikację do uruchamiania w trybie tylko do odczytu.

Na przykład można ustawić flagę, która jest sprawdzana przed przesłaniem żądań aktualizacji do usługi Azure Storage. Po przejściu jednego z żądań aktualizacji można go pominąć i zwrócić do klienta odpowiednią odpowiedź. Niektóre funkcje mogą być nawet całkowicie wyłączone do momentu rozwiązania problemu i powiadomienia użytkowników o tym, że te funkcje są tymczasowo niedostępne.

Jeśli użytkownik zdecyduje się osobno obsługiwać błędy każdej usługi, będzie również musiał obsługiwać możliwość uruchamiania aplikacji w trybie tylko do odczytu przez usługę. Na przykład mogą istnieć flagi tylko do odczytu dla każdej usługi, która może być włączona i wyłączona. Następnie można obsłużyć flagę w odpowiednich miejscach w kodzie.

Możliwość uruchamiania aplikacji w trybie tylko do odczytu ma inną korzyść z boku — daje możliwość zapewnienia ograniczonej funkcjonalności podczas głównego uaktualniania aplikacji. Możesz wyzwolić aplikację do uruchamiania w trybie tylko do odczytu i wskazać pomocnicze centrum danych, co gwarantuje, że nikt nie uzyskuje dostępu do danych w regionie podstawowym podczas przeprowadzania uaktualnień.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Obsługa aktualizacji podczas działania w trybie tylko do odczytu

Istnieje wiele sposobów obsługi żądań aktualizacji podczas działania w trybie tylko do odczytu. Nie zajmiemy się tym wszechstronnie, ale ogólnie istnieje kilka wzorców, które należy wziąć pod uwagę.

1. Możesz odpowiedzieć na użytkownika i powiedzieć, że obecnie nie są akceptowane aktualizacje. Na przykład system zarządzania kontaktami może umożliwić klientom dostęp do informacji kontaktowych, ale nie do aktualizacji.

2. Aktualizacje można umieścić w kolejce w innym regionie. W takim przypadku należy napisać oczekujące żądania aktualizacji do kolejki w innym regionie, a następnie przetworzyć te żądania po ponownym przejściu do trybu online głównego centrum danych. W tym scenariuszu należy poinformować klienta, że żądana aktualizacja została umieszczona w kolejce do późniejszego przetwarzania.

3. Aktualizacje można zapisywać na koncie magazynu w innym regionie. Gdy główne centrum danych powróci do trybu online, możesz mieć możliwość scalenia tych aktualizacji z danymi podstawowymi w zależności od struktury danych. Na przykład w przypadku tworzenia oddzielnych plików z sygnaturą daty/godziny w nazwie można skopiować te pliki z powrotem do regionu podstawowego. Działa to w przypadku niektórych obciążeń, takich jak rejestrowanie i dane iOT.

## <a name="handling-retries"></a>Obsługa ponownych prób

Biblioteka klienta usługi Azure Storage pomaga określić, które błędy mogą być ponawiane. Na przykład błąd 404 (nie znaleziono zasobu), ponieważ jego próba nie powiedzie się. Z drugiej strony można ponowić próbę 500, ponieważ jest to błąd serwera, a problem może po prostu być przejściowym problemem. Aby uzyskać więcej informacji, zapoznaj się z [kodem Open Source klasy ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) w bibliotece klienta usługi Storage. (Poszukaj metody ShouldRetry).

### <a name="read-requests"></a>Żądania odczytu

Żądania odczytu można przekierować do magazynu pomocniczego, jeśli występuje problem z magazynem podstawowym. Jak wspomniano powyżej [przy użyciu ostatecznie spójnych danych](#using-eventually-consistent-data), musi być to możliwe, aby aplikacja mogła odczytać stare dane. Jeśli używasz biblioteki klienta usługi Storage do uzyskiwania dostępu do danych z lokacji dodatkowej, możesz określić zachowanie ponowienia żądania odczytu, ustawiając wartość właściwości **locationmode** na jedną z następujących wartości:

* **PrimaryOnly** (wartość domyślna)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Po ustawieniu **lokalizacjimode** na **PrimaryThenSecondary**, jeśli początkowe żądanie odczytu do podstawowego punktu końcowego kończy się niepowodzeniem z powodu błędu, który może zostać ponowiony, klient automatycznie wprowadzi inne żądanie odczytu do pomocniczego punktu końcowego. Jeśli błąd to limit czasu serwera, klient będzie musiał czekać, aż upłynie limit czasu, zanim odbierze błąd z usługi.

Istnieją zasadniczo dwa scenariusze, które należy wziąć pod uwagę podczas podejmowania decyzji o tym, jak odpowiedzieć na błąd powtarzania:

* Jest to problem izolowany, a kolejne żądania kierowane do podstawowego punktu końcowego nie będą zwracać błędu z możliwością ponowienia. Przykładem sytuacji, w której może się zdarzyć, gdy występuje przejściowy błąd sieciowy.

    W tym scenariuszu nie ma żadnej znaczącej kary wydajności w przypadku, gdymode **ma ustawioną** wartość **PrimaryThenSecondary** , ponieważ dzieje się tak rzadko.

* Jest to problem z co najmniej jedną z usług magazynu w regionie podstawowym, a wszystkie kolejne żądania do tej usługi w regionie podstawowym prawdopodobnie zwracają błędy powtarzające się w danym okresie. Przykładem jest to, że region podstawowy jest całkowicie niedostępny.

    W tym scenariuszu występuje spadek wydajności, ponieważ wszystkie żądania odczytu najpierw będą podejmować próby podstawowego punktu końcowego, poczekaj na wygaśnięcie limitu czasu, a następnie przejdź do pomocniczego punktu końcowego.

W tych scenariuszach należy określić, że występuje problem z podstawowym punktem końcowym i wysyłać wszystkie żądania odczytu bezpośrednio do pomocniczego punktu końcowego przez ustawienie właściwości **locationmode** na **SecondaryOnly**. W tej chwili należy również zmienić aplikację tak, aby była uruchamiana w trybie tylko do odczytu. Takie podejście jest znane jako [wzorzec wyłącznika](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Żądania aktualizacji

Wzorzec wyłącznika może być również stosowany w przypadku żądań aktualizacji. Żądania aktualizacji nie można jednak przekierować do magazynu pomocniczego, który jest tylko do odczytu. Dla tych żądań należy pozostawić Właściwość **locationmode** ustawioną na wartość **PrimaryOnly** (domyślnie). Aby obsłużyć te błędy, można zastosować metrykę do tych żądań — na przykład 10 błędów w wierszu — a po spełnieniu progu należy przełączyć aplikację do trybu tylko do odczytu. Możesz użyć tych samych metod do powrotu do trybu aktualizacji, jak opisano poniżej w następnej sekcji dotyczącej wzorca wyłącznika.

## <a name="circuit-breaker-pattern"></a>Wzorzec wyłącznika

Użycie wzorca wyłącznika w aplikacji może uniemożliwić jego ponawianie próby wykonania operacji, która prawdopodobnie nie powiedzie się. Dzięki temu aplikacja może nadal działać zamiast czasu, gdy operacja zostanie przetestowana wykładniczo. Wykrywa także, kiedy błąd został naprawiony, podczas którego aplikacja może spróbować ponownie wykonać operację.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Jak zaimplementować wzorzec wyłącznika

Aby określić, że występuje problem z podstawowym punktem końcowym, można monitorować częstotliwość, z jaką klient napotyka błędy powtarzające się. Ponieważ każdy przypadek jest inny, należy zdecydować o progu, który ma być używany do podjęcia decyzji o przełączeniu do pomocniczego punktu końcowego i uruchomić aplikację w trybie tylko do odczytu. Na przykład można zdecydować się na przełączenie, jeśli w wierszu występuje 10 błędów bez powodzenia. Innym przykładem jest przełączenie, jeśli 90% żądań w okresie 2-minutowy nie powiedzie się.

W pierwszym scenariuszu można po prostu zachować liczbę błędów i jeśli występuje sukces przed osiągnięciem maksimum, należy ustawić liczbę z powrotem na zero. W drugim scenariuszu jednym ze sposobów implementacji jest użycie obiektu elemencie MemoryCache (w programie .NET). Dla każdego żądania Dodaj CacheItem do pamięci podręcznej, ustaw wartość na powodzenie (1) lub niepowodzenie (0), a następnie ustaw czas wygaśnięcia na 2 minuty od teraz (lub bez ograniczeń czasowych). Po osiągnięciu czasu wygaśnięcia wpisu wpis zostanie automatycznie usunięty. Spowoduje to przetoczenie 2-minutowy okna. Za każdym razem, gdy tworzysz żądanie do usługi Storage, najpierw Użyj zapytania LINQ w obiekcie elemencie MemoryCache, aby obliczyć procent sukcesu przez zsumowanie wartości i dzielenie przez licznik. Gdy procent sukcesu spadnie poniżej pewnego progu (na przykład 10%), przed kontynuowaniem ustaw właściwość **locationmode** dla żądań odczytu na **SecondaryOnly** i Przełącz aplikację do trybu tylko do odczytu.

Próg błędów służący do określania, kiedy przełącznik może się różnić od usługi do usługi w aplikacji, dlatego należy rozważyć uczynienie ich konfigurowalnymi parametrami. Jest to również miejsce, w którym zdecydujesz się obsłużyć błędy powtarzające się z każdej usługi osobno lub jako jedną z omówionych wcześniej.

Inną kwestią jest sposób obsługi wielu wystąpień aplikacji oraz czynności, które należy wykonać, gdy wykrywane są błędy powtarzające się w każdym wystąpieniu. Na przykład możesz mieć 20 maszyn wirtualnych z uruchomioną tą samą aplikacją. Czy każde wystąpienie jest obsługiwane osobno? Jeśli jedno wystąpienie ma problemy, czy chcesz ograniczyć odpowiedź tylko do jednego wystąpienia, czy chcesz spróbować, aby wszystkie wystąpienia odpowiadały w ten sam sposób, gdy jedno wystąpienie ma problem? Obsługa wystąpień osobno jest znacznie prostsza niż próba koordynowania odpowiedzi między nimi, ale w zależności od tego, jak to zrobisz, zależy od architektury aplikacji.

### <a name="options-for-monitoring-the-error-frequency"></a>Opcje monitorowania częstotliwości błędów

Istnieją trzy główne opcje monitorowania częstotliwości ponawiania prób w regionie podstawowym, aby określić, kiedy należy przełączyć się do regionu pomocniczego i zmienić aplikację tak, aby była uruchamiana w trybie tylko do odczytu.

* Dodaj procedurę obsługi dla [**ponawiania**](/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) zdarzenia dla obiektu [**OperationContext**](/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) przekazanego do żądań magazynu — jest to metoda wyświetlana w tym artykule i używana w towarzyszącej próbce. Te zdarzenia są wyzwalane za każdym razem, gdy klient ponawia próbę żądania, umożliwiając śledzenie, jak często klient napotyka błędy powtarzające się w podstawowym punkcie końcowym.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* W metodzie [**szacowania**](/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) w niestandardowych zasadach ponawiania można uruchomić kod niestandardowy za każdym razem, gdy zostanie ponowiona ponowna próba. Oprócz rejestrowania w przypadku ponowienia próby, pozwala to również na modyfikowanie zachowania ponowienia próby.

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* Trzecia metoda polega na zaimplementowaniu niestandardowego składnika monitorowania w aplikacji, który stale wysyła polecenie ping do podstawowego punktu końcowego magazynu przy użyciu fikcyjnych żądań odczytu (takich jak odczytywanie małego obiektu BLOB) w celu określenia jego kondycji. Spowoduje to zajęcie niektórych zasobów, ale nie znaczną ilością. Po wykryciu problemu, który osiągnie wartość progową, można wykonać operację przełączania do **SecondaryOnly** i trybu tylko do odczytu.

W pewnym momencie zechcesz przełączyć się z powrotem do korzystania z podstawowego punktu końcowego i zezwalać na aktualizacje. Jeśli używasz jednej z dwóch pierwszych metod wymienionych powyżej, możesz po prostu przełączyć się z powrotem do podstawowego punktu końcowego i włączyć tryb aktualizacji po arbitralnie wybranej ilości czasu lub liczbie operacji. Następnie można ponownie przejść przez logikę ponownych prób. Jeśli problem został rozwiązany, nadal będzie można używać podstawowego punktu końcowego i zezwalać na aktualizacje. Jeśli nadal występuje problem, zostanie on przełączony ponownie z dodatkowym punktem końcowym i trybem tylko do odczytu po niepowodzeniu określonych kryteriów.

W trzecim scenariuszu, gdy polecenie ping do podstawowego punktu końcowego magazynu zakończy się pomyślnie, można wyzwolić przełącznik z powrotem do **PrimaryOnly** i nadal zezwalać na aktualizacje.

## <a name="handling-eventually-consistent-data"></a>Obsługa ostatecznie spójnych danych

Magazyn Geograficznie nadmiarowy działa przez replikowanie transakcji z podstawowego do regionu pomocniczego. Ten proces replikacji gwarantuje, że dane w regionie pomocniczym są *ostatecznie spójne*. Oznacza to, że wszystkie transakcje w regionie podstawowym zostaną ostatecznie wyświetlone w regionie pomocniczym, ale może istnieć opóźnienie przed ich wyświetleniem i że nie ma gwarancji, że transakcje docierają do regionu pomocniczego w tej samej kolejności, w jakiej zostały pierwotnie zastosowane w regionie podstawowym. Jeśli transakcje dotarły do regionu pomocniczego poza kolejnością, możesz *uznać, że dane* w regionie pomocniczym mają być w stanie niespójnym do momentu przechwycenia usługi.

W poniższej tabeli przedstawiono przykład takiej sytuacji, w której mogą wystąpić informacje o tym, co się stanie w przypadku zaktualizowania szczegółów pracownika, aby uczynić je członkiem roli *administratorzy* . Na potrzeby tego przykładu należy zaktualizować jednostkę **Employee** i zaktualizować jednostkę **roli administratora** z liczbą całkowitej liczby administratorów. Zwróć uwagę, w jaki sposób aktualizacje są stosowane poza kolejnością w regionie pomocniczym.

| **Godzina** | **Transakcja**                                            | **Replikacja**                       | **Czas ostatniej synchronizacji** | **Wynik** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakcja A: <br> Wstaw pracownika <br> jednostka w podstawowym |                                   |                    | Transakcja wstawiona do elementu podstawowego,<br> jeszcze nie zreplikowane. |
| T1       |                                                            | Transakcja A <br> zreplikowane do<br> dodatkowych | T1 | Transakcja jest replikowana do pomocniczej. <br>Czas ostatniej synchronizacji został zaktualizowany.    |
| T2       | Transakcja B:<br>Aktualizacja<br> Jednostka Employee<br> w podstawowym  |                                | T1                 | Transakcja B zapisywana w podstawowym,<br> jeszcze nie zreplikowane.  |
| T3       | Transakcja C:<br> Aktualizacja <br>administrator<br>Jednostka roli w<br>głównym |                    | T1                 | Transakcja C została zapisywana na podstawową,<br> jeszcze nie zreplikowane.  |
| *T4*     |                                                       | Transakcja C <br>zreplikowane do<br> dodatkowych | T1         | Transakcja C została zreplikowana do pomocniczej.<br>LastSyncTime nie został zaktualizowany, ponieważ <br>transakcja B nie została jeszcze zreplikowana.|
| *Otrzymując*     | Odczytaj jednostki <br>z pomocniczego                           |                                  | T1                 | Otrzymujesz nieodświeżoną wartość dla pracownika <br> jednostka, ponieważ transakcja B nie została <br> zreplikowane jeszcze. Otrzymujesz nową wartość dla<br> Jednostka roli administratora, ponieważ C ma<br> powtórzon. Czas ostatniej synchronizacji nadal nie<br> Zaktualizowano, ponieważ transakcja B<br> nie zreplikowane. Możesz powiedzieć<br>Jednostka roli administratora jest niespójna <br>ponieważ data/godzina jednostki przypada po <br>Czas ostatniej synchronizacji. |
| *T6*     |                                                      | Transakcja B<br> zreplikowane do<br> dodatkowych | T6                 | *T6* — wszystkie transakcje za poorednictwem języka C <br>zreplikowane, czas ostatniej synchronizacji<br> został zaktualizowany. |

W tym przykładzie Załóżmy, że klient przełącza się do odczytu z regionu pomocniczego w T5. Teraz może pomyślnie odczytać jednostkę **roli administrator** , ale jednostka zawiera wartość dla liczby administratorów, która nie jest spójna z liczbą jednostek **pracowników** , które są w tym momencie oznaczone jako administratorzy w regionie pomocniczym. Klient może po prostu wyświetlić tę wartość, z ryzykiem, że informacje są niespójne. Klient może także próbować określić, że **rola administratora** jest w stanie potencjalnie niespójnym, ponieważ aktualizacje wystąpiły poza kolejnością, a następnie informują użytkownika o tym fakcie.

Aby rozpoznać, że ma ona potencjalnie niespójne dane, klient może użyć wartości *czasu ostatniej synchronizacji* , który można uzyskać w dowolnym momencie, wykonując zapytania dotyczące usługi magazynu. Oznacza to czas, w którym dane w regionie pomocniczym były ostatnio spójne i gdy usługa zastosowała wszystkie transakcje przed tym punktem w czasie. W powyższym przykładzie, gdy usługa wstawia jednostkę **Employee** w regionie pomocniczym, czas ostatniej synchronizacji jest ustawiany na *T1*. Pozostanie o *T1* do momentu, aż usługa zaktualizuje jednostkę **pracownika** w regionie pomocniczym, gdy zostanie ustawiona na *T6*. Jeśli klient Pobiera czas ostatniej synchronizacji, gdy odczytuje jednostkę w *T5*, może ją porównać z sygnaturą czasową w jednostce. Jeśli sygnatura czasowa w jednostce jest późniejsza niż godzina ostatniej synchronizacji, jednostka jest w stanie potencjalnie niespójnym i można wykonać dowolną czynność dla aplikacji. Użycie tego pola wymaga, aby wiadomo, kiedy Ostatnia aktualizacja podstawowa została ukończona.

Aby dowiedzieć się, jak sprawdzić czas ostatniej synchronizacji, zobacz [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md).

## <a name="testing"></a>Testowanie

Ważne jest, aby sprawdzić, czy aplikacja działa zgodnie z oczekiwaniami, gdy napotka błędy powtarzające się. Na przykład należy sprawdzić, czy aplikacja przełączy się na pomocniczą i w trybie tylko do odczytu, gdy wykryje problem, a następnie przełącza się z powrotem po ponownym udostępnieniu regionu podstawowego. Aby to zrobić, musisz mieć możliwość symulowania błędów z możliwością ponowienia i kontroli częstotliwości ich występowania.

Możesz użyć [programu Fiddler](https://www.telerik.com/fiddler) , aby przechwycić i zmodyfikować odpowiedzi HTTP w skrypcie. Ten skrypt może identyfikować odpowiedzi pochodzące z podstawowego punktu końcowego i zmieniać Kod stanu HTTP na taki, który Biblioteka klienta magazynu rozpoznaje jako błąd powtarzający operację. Ten fragment kodu przedstawia prosty przykład skryptu programu Fiddler, który przechwytuje odpowiedzi na żądania odczytu do tabeli **employeeData** w celu zwrócenia stanu 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Ten przykład można rozszerzać, aby przechwycić szerszego zakresu żądań i zmienić **responseCode** na niektórych z nich w celu lepszego symulowania rzeczywistego scenariusza. Więcej informacji o dostosowywaniu skryptów programu Fiddler można znaleźć w temacie [Modyfikowanie żądania lub odpowiedzi](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) w dokumentacji programu Fiddler.

Jeśli zostały wprowadzone progi do przełączania aplikacji w tryb tylko do odczytu, będzie łatwiej przetestować zachowanie przy użyciu woluminów transakcji nieprodukcyjnych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać kompletny przykład pokazujący, jak przełączać się między podstawowymi i pomocniczymi punktami końcowymi, zobacz [przykłady platformy Azure — Używanie wzorca wyłącznika z magazynem RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
