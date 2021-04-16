---
title: Diagnostyka transakcji usługi Azure Application Insights | Microsoft Docs
description: Application Insights end-to-end transaction diagnostics
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 60365079c295e154ff0a38277c9ccdec35157e6e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481399"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Ujednolicona diagnostyka transakcji między składnikami

Ujednolicone środowisko diagnostyki automatycznie koreluje dane telemetryczne po stronie serwera ze wszystkich Application Insights monitorowanych składników w jednym widoku. Nie ma znaczenia, czy masz wiele zasobów z oddzielnymi kluczami instrumentacji. Application Insights wykrywa podstawową relację i umożliwia łatwe diagnozowanie składnika aplikacji, zależności lub wyjątku, który spowodował spowolnienie lub niepowodzenie transakcji.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie wdrażalnymi częściami aplikacji rozproszonej/mikrousług. Deweloperzy i zespoły operacyjne mają wgląd na poziomie kodu lub dostęp do danych telemetrycznych generowanych przez te składniki aplikacji.

* Składniki różnią się od "zaobserwowanych" zależności zewnętrznych, takich jak SQL, EventHub itp., do których Twój zespół/organizacja może nie mieć dostępu (kod lub telemetria).
* Składniki działają na dowolnej liczbie wystąpień serwera/roli/kontenera.
* Składniki mogą być oddzielone Application Insights instrumentacji (nawet jeśli subskrypcje są różne) lub różne role raportują do pojedynczego Application Insights instrumentacji. Nowe środowisko zawiera szczegółowe informacje dotyczące wszystkich składników, niezależnie od sposobu ich skonfigurowania.

> [!NOTE]
> * **Brak linków powiązanych elementów?** Wszystkie powiązane dane telemetryczne znajdują się w [górnej](#cross-component-transaction-chart) i [dolnej](#all-telemetry-with-this-operation-id) części lewej strony. 

## <a name="transaction-diagnostics-experience"></a>Środowisko diagnostyki transakcji
Ten widok ma cztery kluczowe części: listę wyników, wykres transakcji między składnikami, listę sekwencji czasu wszystkich danych telemetrycznych związanych z tą operacją oraz okienko szczegółów dla dowolnego wybranego elementu telemetrii po lewej stronie.

![Kluczowe części](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Wykres transakcji między składnikami

Ten wykres zawiera oś czasu z poziomymi słupkami czasu trwania żądań i zależności między składnikami. Wszelkie zbierane wyjątki są również oznaczane na osi czasu.

* Górny wiersz na tym wykresie reprezentuje punkt wejścia, żądanie przychodzące do pierwszego składnika o nazwie w tej transakcji. Czas trwania to łączny czas trwania transakcji.
* Wszystkie wywołania zależności zewnętrznych są prostymi, niezwiązalnymi wierszami, z ikonami reprezentujących typ zależności.
* Wywołania do innych składników to zwijane wiersze. Każdy wiersz odpowiada konkretnej operacji wywoływanej w składniku.
* Domyślnie wybrane żądanie, zależność lub wyjątek są wyświetlane po prawej stronie.
* Wybierz dowolny wiersz, aby wyświetlić [jego szczegóły po prawej stronie](#details-of-the-selected-telemetry). 

> [!NOTE]
> Wywołania innych składników mają dwa wiersze: jeden wiersz reprezentuje wywołanie wychodzące (zależność) ze składnika wywołującego, a drugi wiersz odpowiada żądaniu przychodzącemu w wywołaniu składnika. Wiodąca ikona i odrębne style pasków czasu trwania pomagają je rozróżnić.

## <a name="all-telemetry-with-this-operation-id"></a>Wszystkie dane telemetryczne z tym identyfikatorem operacji

W tej sekcji przedstawiono widok listy płaskiej w sekwencji czasu wszystkich danych telemetrycznych związanych z tą transakcją. Wyświetlane są również zdarzenia niestandardowe i ślady, które nie są wyświetlane na wykresie transakcji. Tę listę można filtrować do telemetrii wygenerowanej przez określony składnik/wywołanie. Możesz wybrać dowolny element telemetrii na tej liście, aby wyświetlić [odpowiednie szczegóły po prawej stronie](#details-of-the-selected-telemetry).

![Sekwencja czasowa wszystkich danych telemetrycznych](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Szczegóły wybranej telemetrii

To zwijane okienko zawiera szczegóły dowolnego wybranego elementu z wykresu transakcji lub listy. "Pokaż wszystkie" wyświetla wszystkie standardowe atrybuty, które są zbierane. Wszystkie atrybuty niestandardowe są oddzielnie wymienione poniżej zestawu standardowego. Kliknij przycisk "..." poniżej okna śledzenia stosu, aby uzyskać opcję kopiowania śledzenia. Polecenie "Otwórz ślady profilera" lub "Otwórz migawkę debugowania" pokazuje diagnostykę na poziomie kodu w odpowiednich okienkach szczegółów.

![Szczegóły wyjątku](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Wyniki wyszukiwania

To zwijane okienko zawiera inne wyniki spełniające kryteria filtrowania. Kliknij dowolny wynik, aby zaktualizować odpowiednie szczegóły w 3 sekcjach wymienionych powyżej. Próbujemy znaleźć przykłady, które najprawdopodobniej będą mieć dostępne szczegóły ze wszystkich składników, nawet jeśli próbkowanie jest w mocy w dowolnym z nich. Są one wyświetlane jako "sugerowane" przykłady.

![Wyniki wyszukiwania](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler i debuger migawek

[Application Insights profilera lub](./profiler.md) [debugera migawek](snapshot-debugger.md) pomagają w diagnostyce wydajności i błędów na poziomie kodu. Dzięki temu doświadczeniu można jednym kliknięciem wyświetlić ślady lub migawki profilera z dowolnego składnika.

Jeśli nie można uzyskać działania profilera, skontaktuj się z **pomocą microsoft.com \@**

Jeśli nie możesz uzyskać informacji o Snapshot Debugger, skontaktuj się z **snapshothelp \@ microsoft.com**

![Integracja profilera](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Często zadawane pytania

*Widzę jeden składnik na wykresie, a inne są wyświetlane tylko jako zależności zewnętrzne bez żadnych szczegółowych informacji o tym, co się stało w ramach tych składników.*

Potencjalne przyczyny:

* Czy inne składniki są instrumentowane za pomocą Application Insights?
* Czy korzysta z najnowszej stabilnej wersji Application Insights SDK?
* Jeśli te składniki są Application Insights zasobów, czy [](resources-roles-access-control.md) masz wymagany dostęp Jeśli masz dostęp, a składniki są instrumentowane przy użyciu najnowszych zestawów SDK usługi Application Insights, daj nam znać za pośrednictwem prawego górnego kanału opinii.

*Widzę zduplikowane wiersze zależności. Czy jest to oczekiwane?*

W tej chwili pokazujemy wywołanie zależności wychodzącej oddzielone od żądania przychodzącego. Zazwyczaj dwa wywołania wyglądają identycznie, a tylko wartość czasu trwania jest inna z powodu rundy sieciowej. Wiodąca ikona i odrębne style pasków czasu trwania pomagają je rozróżnić. Czy ta prezentacja danych jest myląca? Daj nam swoją opinię!

*A co z nieschypami zegara w różnych wystąpieniach składników?*

Osie czasu są dostosowywane dla niesyłania zegara na wykresie transakcji. Dokładne znaczniki czasu można wyświetlić w okienku szczegółów lub przy użyciu usługi Analytics.

*Dlaczego w nowym środowisko brakuje większości zapytań dotyczących powiązanych elementów?*

Jest to celowe. Wszystkie powiązane elementy we wszystkich składnikach są już dostępne po lewej stronie (w górnej i dolnej sekcji). Nowe środowisko zawiera dwa powiązane elementy, których po lewej stronie nie obejmuje: wszystkie dane telemetryczne z pięciu minut przed i po tym zdarzeniu oraz oś czasu użytkownika.

*Podczas korzystania z zestawu SDK języka JavaScript widzę więcej zdarzeń niż oczekiwano Application Insights w diagnostyce transakcji. Czy istnieje sposób, aby zobaczyć mniej zdarzeń na transakcję?*

Środowisko diagnostyki transakcji pokazuje wszystkie dane telemetryczne w [jednej operacji,](correlation.md#data-model-for-telemetry-correlation) która ma [identyfikator operacji](data-model-context.md#operation-id). Domyślnie zestaw SDK Application Insights dla języka JavaScript tworzy nową operację dla każdego unikatowego widoku strony. W aplikacji jednostronicowej (SPA) zostanie wygenerowane tylko jedno zdarzenie widoku strony i zostanie użyty jeden identyfikator operacji dla wszystkich wygenerowanych danych telemetrycznych, co może spowodować skorelowanie wielu zdarzeń z tą samą operacją. W tych scenariuszach możesz użyć funkcji automatycznego śledzenia tras, aby automatycznie tworzyć nowe operacje nawigacji w aplikacji jednostronicowej. Należy włączyć opcję [enableAutoRouteTracking,](javascript.md#single-page-applications) aby widok strony był generowany przy każdej aktualizacji trasy adresu URL (jest to widok strony logicznej). Jeśli chcesz ręcznie odświeżyć identyfikator operacji, możesz to zrobić, `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` wywołując . Ręczne wyzwolenie zdarzenia PageView spowoduje również zresetowanie identyfikatora operacji.
