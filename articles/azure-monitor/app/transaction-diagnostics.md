---
title: Diagnostyka transakcji Application Insights platformy Azure | Microsoft Docs
description: Application Insights kompleksowej diagnostyki transakcji
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746101"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Ujednolicona Diagnostyka transakcji między składnikami

Ujednolicone środowisko diagnostyki automatycznie skorelowanie danych telemetrycznych po stronie serwera ze wszystkich Application Insights monitorowanych składników w jednym widoku. Nie ma znaczenia, czy masz wiele zasobów z osobnymi kluczami Instrumentacji. Application Insights wykrywa podstawową relację i pozwala łatwo zdiagnozować składnik aplikacji, zależność lub wyjątek, który spowodował spowolnienie lub niepowodzenie transakcji.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie wdrażane części aplikacji rozproszonej/mikrousług. Deweloperzy i zespoły operacyjne mają widoczność na poziomie kodu lub uzyskują dostęp do danych telemetrycznych generowanych przez te składniki aplikacji.

* Składniki różnią się od "obserwowanych" zależności zewnętrznych, takich jak SQL, EventHub itp., do których zespół/organizacja może nie mieć dostępu (kod lub dane telemetryczne).
* Składniki są uruchamiane na dowolnej liczbie wystąpień serwera/roli/kontenera.
* Składnikiem programu mogą być oddzielne klucze Instrumentacji Application Insights (nawet jeśli subskrypcje są różne) lub różne role zgłaszają do jednego klucza Instrumentacji Application Insights. Nowe środowisko pokazuje szczegóły wszystkich składników, niezależnie od tego, jak zostały skonfigurowane.

> [!NOTE]
> * **Brak linków do powiązanego elementu?** Wszystkie powiązane dane telemetryczne znajdują się u [góry](#cross-component-transaction-chart) i [u dołu](#all-telemetry-with-this-operation-id) części po lewej stronie. 

## <a name="transaction-diagnostics-experience"></a>Środowisko diagnostyki transakcji
Ten widok zawiera cztery kluczowe części: listę wyników, wykres transakcji między składnikami, listę sekwencji czasowych wszystkich danych telemetrycznych związanych z tą operacją oraz okienko szczegółów dla każdego wybranego elementu telemetrii po lewej stronie.

![Części klucza](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Wykres transakcji między składnikami

Ten wykres zawiera oś czasu z poziomymi paskami dla czasu trwania żądań i zależności między składnikami. Wszystkie zebrane wyjątki są również oznaczone na osi czasu.

* Górny wiersz na tym wykresie reprezentuje punkt wejścia, żądanie przychodzące do pierwszego składnika o nazwie w tej transakcji. Czas trwania to łączny czas wykonania transakcji.
* Wszystkie wywołania zależności zewnętrznych są prostymi niezwijanymi wierszami z ikonami reprezentującymi typ zależności.
* Wywołania innych składników są zwijane. Każdy wiersz odpowiada określonej operacji wywoływanej w składniku.
* Domyślnie zostanie wyświetlone żądanie, zależność lub wyjątek, który został wybrany po prawej stronie.
* Zaznacz dowolny wiersz, aby wyświetlić jego [szczegóły po prawej stronie](#details-of-the-selected-telemetry). 

> [!NOTE]
> Wywołania innych składników mają dwa wiersze: jeden wiersz reprezentuje wywołanie wychodzące (zależność) ze składnika wywołującego, a drugi wiersz odpowiada żądaniu przychodzącemu dla wywoływanego składnika. Ikona wiodąca i różne style pasków czasu trwania pomagają rozróżnić między nimi.

## <a name="all-telemetry-with-this-operation-id"></a>Wszystkie dane telemetryczne z tym identyfikatorem operacji

Ta sekcja zawiera widok płaskiej listy w sekwencji czasowej wszystkich danych telemetrycznych związanych z tą transakcją. Przedstawiono w nim także zdarzenia niestandardowe i ślady, które nie są wyświetlane na wykresie transakcji. Możesz filtrować tę listę do telemetrii wygenerowanej przez określony składnik/wywołanie. Możesz wybrać dowolny element telemetrii na tej liście, aby zobaczyć odpowiednie [szczegóły po prawej stronie](#details-of-the-selected-telemetry).

![Sekwencja czasu dla wszystkich danych telemetrycznych](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Szczegóły wybranej telemetrii

To okienko zwijane pokazuje szczegóły dowolnego wybranego elementu z wykresu transakcji lub listy. "Pokaż wszystko" wyświetla wszystkie standardowe atrybuty, które są zbierane. Wszystkie atrybuty niestandardowe są osobno wymienione poniżej zestawu standardowego. Kliknij "..." poniżej okna śledzenia stosu, aby pobrać opcję kopiowania śladu. "Otwarte ślady profilera" lub "Otwórz migawkę debugowania" pokazuje diagnostykę na poziomie kodu w odpowiednich okienkach szczegółów.

![Szczegóły wyjątku](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Wyniki wyszukiwania

To okienko zwijane pokazuje inne wyniki, które spełniają kryteria filtrowania. Kliknij dowolny wynik, aby zaktualizować odpowiednie szczegóły do 3 sekcji wymienionych powyżej. Spróbujemy znaleźć przykłady, które najprawdopodobniej będą mieć szczegółowe informacje ze wszystkich składników, nawet jeśli próbkowanie jest stosowane w którymkolwiek z nich. Są one wyświetlane jako "sugerowane" przykłady.

![Wyniki wyszukiwania](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler i debuger migawek

[Application Insights profilerem](./profiler.md) lub [debugerem migawek](snapshot-debugger.md) pomoc w diagnostyce na poziomie kodu problemów z wydajnością i błędami. Korzystając z tego środowiska, można wyświetlać ślady profilera lub migawki z dowolnego składnika za pomocą jednego kliknięcia.

Jeśli nie możesz uzyskać pracy profilera, skontaktuj się z firmą **serviceprofilerhelp \@ Microsoft.com**

Jeśli nie możesz uzyskać Snapshot Debugger pracy, skontaktuj się z firmą **snapshothelp \@ Microsoft.com**

![Integracja profilera](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Często zadawane pytania

*Widzę pojedynczy składnik na wykresie, a inne są wyświetlane tylko jako zależności zewnętrzne, bez żadnych szczegółowych informacji o tym, co się stało w tych składnikach.*

Możliwe przyczyny:

* Czy inne składniki są Instrumentacją Application Insights?
* Czy używają najnowszych stabilnych Application Insights SDK?
* Jeśli te składniki są oddzielnymi Application Insightsymi zasobami, należy [uzyskać dostęp](resources-roles-access-control.md) , jeśli masz dostęp, a składniki są z najnowszymi Application Insights zestawami SDK, daj nam znać za pośrednictwem odpowiedniego kanału opinii.

*Widzę zduplikowane wiersze dla zależności. Czy jest to oczekiwane?*

Obecnie pokazywane jest wywołanie zależności wychodzącej niezależnie od żądania przychodzącego. Zazwyczaj dwa wywołania wyglądają identycznie z tylko wartością czasu trwania, która jest różna z powodu rundy sieci. Ikona wiodąca i różne style pasków czasu trwania pomagają rozróżnić między nimi. Czy ta prezentacja danych jest myląca? Przekaż nam swoją opinię.

*Co informacje o zegarach są rozchylone między różnymi wystąpieniami składników?*

Osie czasu są dostosowywane pod kątem obchylania zegara na wykresie transakcji. Dokładne znaczniki czasu można zobaczyć w okienku szczegółów lub przy użyciu funkcji analizy.

*Dlaczego nowe środowisko nie ma większości zapytań dotyczących elementów pokrewnych?*

Jest to celowe. Wszystkie powiązane elementy, we wszystkich składnikach, są już dostępne po lewej stronie (sekcje górne i dolne). Nowe środowisko zawiera dwa pokrewne elementy, które nie obejmują: wszystkie dane telemetryczne z pięciu minut przed tym zdarzeniem i po nim.

