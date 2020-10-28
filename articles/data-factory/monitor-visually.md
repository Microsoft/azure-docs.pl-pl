---
title: Wizualne monitorowanie fabryk danych platformy Azure
description: Dowiedz się, jak wizualnie monitorować fabryki danych platformy Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 48373c9ffc9146b6e62b62fb7d7fe10d571ce27f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638112"
---
# <a name="visually-monitor-azure-data-factory"></a>Wizualne monitorowanie fabryk danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po utworzeniu i opublikowaniu potoku w Azure Data Factory można skojarzyć go z wyzwalaczem lub ręcznie uruchomić przebieg ad hoc. Wszystkie uruchomienia potoków można monitorować natywnie w środowisku użytkownika Azure Data Factory. Aby otworzyć środowisko monitorowania, wybierz pozycję **monitoruj & Zarządzaj** kafelkiem w bloku fabryka danych [Azure Portal](https://portal.azure.com/). Jeśli jesteś już w środowisku APD ADF, kliknij ikonę **monitora** na lewym pasku bocznym.

Wszystkie uruchomienia fabryki danych są wyświetlane w lokalnej strefie czasowej w przeglądarce. Jeśli zmienisz strefę czasową, wszystkie pola daty i godziny są przyciągane do wybranego.

## <a name="monitor-pipeline-runs"></a>Monitorowanie uruchomień potoku

Domyślny widok monitorowania to lista uruchomień potoku w wybranym okresie. Wyświetlane są następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku |
| Akcje | Ikony pozwalające wyświetlać szczegóły działania, anulować lub ponownie uruchomić potok |
| Uruchom uruchomienie | Data i godzina rozpoczęcia dla uruchomienia potoku (MM/DD/RRRR, gg: MM: SS AM/PM) |
| Czas trwania | Czas trwania uruchomienia (HH: MM: SS) |
| Wyzwolone przez | Nazwa wyzwalacza, który uruchomił potok |
| Stan | **Zakończone niepowodzeniem** , **zakończone powodzeniem** , **w toku** , **anulowane** lub **umieszczone w kolejce** |
| Adnotacje | Tagi z możliwością filtrowania skojarzone z potokiem  |
| Parametry | Parametry uruchomienia potoku (pary nazwa/wartość) |
| Błąd | Jeśli potok nie powiódł się, błąd przebiegu |
| Identyfikator przebiegu | Identyfikator uruchomienia potoku |

![Widok listy na potrzeby monitorowania przebiegów potoku](media/monitor-visually/pipeline-runs.png)

Należy ręcznie wybrać przycisk **Odśwież** , aby odświeżyć listę uruchomień potoków i działań. Autoodświeżanie nie jest obecnie obsługiwane.

![Przycisk Odśwież](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorowanie uruchomień działania

Aby wyświetlić uruchomienia działań dla każdego uruchomienia potoku, wybierz ikonę **Wyświetl uruchomienia działania** w kolumnie **Akcje** . Widok listy pokazuje uruchomienia działania odpowiadające poszczególnym uruchomieniem potoku.

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania w potoku |
| Typ działania | Typ działania, na przykład **copy** , **ExecuteDataFlow** lub **AzureMLExecutePipeline** |
| Akcje | Ikony, które umożliwiają wyświetlanie informacji wejściowych JSON, informacji wyjściowych JSON lub szczegółowych środowisk monitorowania właściwych dla działania | 
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia działania (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Czas trwania | Czas trwania uruchomienia (HH: MM: SS) |
| Stan | **Zakończone niepowodzeniem** , **zakończone powodzeniem** , **w toku** lub **anulowane** |
| Integration Runtime | Integration Runtime działanie zostało uruchomione |
| Właściwości użytkownika | Właściwości działania zdefiniowane przez użytkownika |
| Błąd | Jeśli działanie nie powiodło się, błąd przebiegu |
| Identyfikator przebiegu | Identyfikator uruchomienia działania |

![Widok listy dla uruchomionych działań monitorowania](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Podnieś poziom właściwości użytkownika do monitorowania

Podnieś każdą właściwość działania potoku jako właściwość użytkownika, aby stała się ona jednostką monitorowaną. Na przykład można podwyższyć poziom właściwości **źródłowej** i **docelowej** działania kopiowania w potoku jako właściwości użytkownika. Wybierz pozycję **automatycznie Generuj** , aby wygenerować **źródłową** i **docelową** Właściwość użytkownika dla działania kopiowania.

![Tworzenie właściwości użytkownika](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Można podwyższyć do pięciu właściwości działania potoku jako właściwości użytkownika.

Po utworzeniu właściwości użytkownika można je monitorować w widokach listy monitorowania. Jeśli źródłem działania kopiowania jest nazwa tabeli, można monitorować nazwę tabeli źródłowej jako kolumnę w widoku listy dla uruchomień działania.

![Lista uruchomień działań bez właściwości użytkownika](media/monitor-visually/monitor-user-properties-image2.png)

![Dodawanie kolumn do właściwości użytkownika na liście uruchomień działania](media/monitor-visually/monitor-user-properties-image3.png)

![Lista uruchomień działania z kolumnami dla właściwości użytkownika](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Skonfiguruj widok listy

### <a name="order-and-filter"></a>Kolejność i filtrowanie

Przełączenie przebiegów w kolejności malejącej lub rosnącej według czasu rozpoczęcia przebiegu. Filtry przebiegów potoku przy użyciu następujących kolumn:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Filtruj według nazwy potoku. |
| Uruchom uruchomienie |  Określ zakres czasu wyświetlanych uruchomień potoku. Opcje obejmują szybkie filtry dla **ostatnich 24 godzin** , **ubiegłego tygodnia** i **ostatnich 30 dni** lub do wybierania niestandardowej daty i godziny. |
| Stan uruchomienia | Filtry przebiega według stanu: **zakończone powodzeniem** , **zakończone niepowodzeniem** , do **kolejki** , **anulowane** lub **w toku** . |
| Adnotacje | Filtruj według tagów zastosowanych do każdego potoku |
| Uruchamianie | Filtruj, czy chcesz zobaczyć potoki reran |

![Opcje filtrowania](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Dodawanie lub usuwanie kolumn
Kliknij prawym przyciskiem myszy nagłówek widoku listy i wybierz kolumny, które mają być wyświetlane w widoku listy.

![Opcje kolumn](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Dopasuj szerokości kolumn
Zwiększ i zmniejsz szerokości kolumn w widoku listy, umieszczając kursor na nagłówku kolumny.

## <a name="rerun-activities-inside-a-pipeline"></a>Uruchom ponownie działania wewnątrz potoku

Możesz ponownie uruchomić działania wewnątrz potoku. Wybierz pozycję **Wyświetl uruchomienia działania** , a następnie wybierz działanie w potoku, z którego chcesz ponownie uruchomić potok.

![Wyświetlanie uruchomień działania](media/monitor-visually/rerun-activities-image1.png)

![Wybierz przebieg działania](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Uruchom ponownie z działania zakończonego niepowodzeniem

Jeśli działanie zakończy się niepowodzeniem, przekracza limit czasu lub zostanie anulowane, można ponownie uruchomić potok z działania zakończonego niepowodzeniem, wybierając pozycję **Uruchom ponownie z działania zakończonego niepowodzeniem** .

![Uruchom ponownie działanie zakończone niepowodzeniem](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Wyświetl historię ponownego uruchamiania

Można wyświetlić historię ponownego uruchamiania dla wszystkich uruchomień potoku w widoku listy.

![Wyświetlanie historii](media/monitor-visually/rerun-history-image1.png)

Możesz również wyświetlić historię ponownego uruchamiania dla określonego uruchomienia potoku.

![Wyświetl historię uruchomienia potoku](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorowanie użycia

Możesz zobaczyć zasoby używane przez uruchomienie potoku, klikając ikonę użycie obok przebiegu. 

![Zrzut ekranu pokazujący, gdzie można zobaczyć zasoby używane przez potok.](media/monitor-visually/monitor-consumption-1.png)

Kliknięcie ikony powoduje otwarcie raportu o zużyciu zasobów używanych przez to uruchomienie potoku. 

![Monitorowanie użycia](media/monitor-visually/monitor-consumption-2.png)

Te wartości można podłączyć do [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/details/data-factory/) , aby oszacować koszt uruchomienia potoku. Aby uzyskać więcej informacji na Azure Data Factory cenach, zobacz temat [Omówienie cen](pricing-concepts.md).

> [!NOTE]
> Te wartości zwracane przez kalkulator cen są szacowane. Nie odzwierciedla dokładnej kwoty, która będzie rozliczana Azure Data Factory 

## <a name="gantt-views"></a>Widoki wykresu Gantta

Użyj widoków wykresu Gantta, aby szybko wizualizować potoki i uruchomienia działania.

![Przykład wykresu Gantta](media/monitor-visually/gantt1.png)

Widok wykresu Gantta można przeglądać na potok lub grupować według adnotacji/tagów utworzonych w potokach.

![Adnotacje wykresu Gantta](media/monitor-visually/gantt2.png)

Długość paska informuje o czasie trwania potoku. Możesz również wybrać pasek, aby wyświetlić więcej szczegółów.

![Czas trwania wykresu Gantta](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Przewodniki
Wybierz ikonę **informacji** w lewym dolnym rogu. Następnie wybierz przewodniki **instruktażowe** , aby uzyskać instrukcje krok po kroku dotyczące monitorowania uruchomienia potoku i działania.

![Przewodniki](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alerty

Alerty dla obsługiwanych metryk można zgłaszać w Data Factory. Wybierz pozycję **Monitoruj**  >  **alerty & metryki** na stronie monitorowanie Data Factory, aby rozpocząć pracę.

![Strona monitora fabryki danych](media/monitor-visually/start-page.png)

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Tworzenie alertów

1.  Wybierz pozycję **Nowa reguła alertu** , aby utworzyć nowy Alert.

    ![Przycisk reguły nowego alertu](media/monitor-visually/new-alerts.png)

1.  Określ nazwę reguły i wybierz ważność alertu.

    ![Pola nazwy i ważności reguły](media/monitor-visually/name-and-severity.png)

1.  Wybierz kryteria alertu.

    ![Pole dla kryteriów docelowych](media/monitor-visually/add-criteria-1.png)

    ![Zrzut ekranu pokazujący, gdzie wybierasz jedną metrykę, aby skonfigurować warunek alertu.](media/monitor-visually/add-criteria-2.png)

    ![Lista kryteriów](media/monitor-visually/add-criteria-3.png)

    Możesz tworzyć alerty dla różnych metryk, w tym te, które dotyczą licznika/rozmiaru jednostki ADF, przebiegów działania/potoku/wyzwalacza, Integration Runtime (IR) procesora CPU/liczby węzłów/kolejki, a także dla wykonań pakietów SSIS oraz operacji uruchamiania/zatrzymywania w środowisku SSIS.

1.  Skonfiguruj logikę alertów. Można utworzyć alert dla wybranej metryki dla wszystkich potoków i odpowiednich działań. Możesz również wybrać konkretny typ działania, nazwę działania, nazwę potoku lub typ błędu.

    ![Opcje konfigurowania logiki alertu](media/monitor-visually/alert-logic.png)

1.  Skonfiguruj wiadomości e-mail, wiadomości SMS, wypychania i powiadomień głosowych dla alertu. Utwórz grupę akcji lub wybierz istniejącą dla powiadomień o alertach.

    ![Opcje konfigurowania powiadomień](media/monitor-visually/configure-notification-1.png)

    ![Opcje dodawania powiadomienia](media/monitor-visually/configure-notification-2.png)

1.  Utwórz regułę alertu.

    ![Opcje tworzenia reguły alertu](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania i zarządzania potokami, zobacz artykuł [monitorowanie i zarządzanie potokami programowo](./monitor-programmatically.md) .