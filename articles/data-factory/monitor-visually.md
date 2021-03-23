---
title: Wizualne monitorowanie fabryk danych platformy Azure
description: Dowiedz się, jak wizualnie monitorować fabryki danych platformy Azure
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d177513af9f0ee4fcadb1ea316edf1ad8cb89e5a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783663"
---
# <a name="visually-monitor-azure-data-factory"></a>Wizualne monitorowanie fabryk danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po utworzeniu i opublikowaniu potoku w Azure Data Factory można skojarzyć go z wyzwalaczem lub ręcznie uruchomić przebieg ad hoc. Wszystkie uruchomienia potoków można monitorować natywnie w środowisku użytkownika Azure Data Factory. Aby otworzyć środowisko monitorowania, wybierz pozycję **monitoruj & Zarządzaj** kafelkiem w bloku fabryka danych [Azure Portal](https://portal.azure.com/). Jeśli jesteś już w środowisku APD ADF, kliknij ikonę **monitora** na lewym pasku bocznym.

Domyślnie wszystkie uruchomienia fabryki danych są wyświetlane w lokalnej strefie czasowej w przeglądarce. Jeśli zmienisz strefę czasową, wszystkie pola daty i godziny są przyciągane do wybranego.

## <a name="monitor-pipeline-runs"></a>Monitorowanie uruchomień potoku

Domyślny widok monitorowania to lista wyzwolonych uruchomień potoków w wybranym okresie. Można zmienić zakres czasu i filtrować według stanu, nazwy potoku lub adnotacji. Umieść kursor nad konkretnym uruchomieniem potoku, aby uzyskać działania specyficzne dla uruchomienia, takie jak ponowne uruchomienie i raport zużycia.

![Widok listy na potrzeby monitorowania przebiegów potoku](media/monitor-visually/pipeline-runs.png)

Siatka przebiegu potoku zawiera następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku |
| Uruchom uruchomienie | Data i godzina rozpoczęcia dla uruchomienia potoku (MM/DD/RRRR, gg: MM: SS AM/PM) |
| Koniec przebiegu | Data i godzina zakończenia uruchomienia potoku (MM/DD/RRRR, gg: MM: SS AM/PM) |
| Czas trwania | Czas trwania uruchomienia (HH: MM: SS) |
| Wyzwolone przez | Nazwa wyzwalacza, który uruchomił potok |
| Stan | **Zakończone niepowodzeniem**, **zakończone powodzeniem**, **w toku**, **anulowane** lub **umieszczone w kolejce** |
| Adnotacje | Tagi z możliwością filtrowania skojarzone z potokiem  |
| Parametry | Parametry uruchomienia potoku (pary nazwa/wartość) |
| Błąd | Jeśli potok nie powiódł się, błąd przebiegu |
| Identyfikator przebiegu | Identyfikator uruchomienia potoku |

Należy ręcznie wybrać przycisk **Odśwież** , aby odświeżyć listę uruchomień potoków i działań. Autoodświeżanie nie jest obecnie obsługiwane.

![Przycisk Odśwież](media/monitor-visually/refresh.png)

Aby wyświetlić wyniki przebiegu debugowania, wybierz kartę **debugowanie** .

![Wybierz ikonę Wyświetl aktywne uruchomienia debugowania](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Monitorowanie uruchomień działania

Aby uzyskać szczegółowy widok poszczególnych przebiegów działania określonego potoku, kliknij nazwę potoku.

![Wyświetlanie uruchomień działania](media/monitor-visually/view-activity-runs.png)

Widok listy pokazuje uruchomienia działania odpowiadające poszczególnym uruchomieniem potoku. Umieść kursor nad określonym uruchomieniem działania, aby uzyskać informacje dotyczące uruchamiania, takie jak dane wejściowe JSON, dane wyjściowe JSON i szczegółowe środowisko monitorowania specyficzne dla działania.

![Istnieją informacje o SalesAnalyticsMLPipeline, a następnie listę uruchomień działania.](media/monitor-visually/activity-runs.png)

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania w potoku |
| Typ działania | Typ działania, na przykład **copy**, **ExecuteDataFlow** lub **AzureMLExecutePipeline** |
| Akcje | Ikony, które umożliwiają wyświetlanie informacji wejściowych JSON, informacji wyjściowych JSON lub szczegółowych środowisk monitorowania właściwych dla działania | 
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia działania (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Czas trwania | Czas trwania uruchomienia (HH: MM: SS) |
| Stan | **Zakończone niepowodzeniem**, **zakończone powodzeniem**, **w toku** lub **anulowane** |
| Środowisko Integration Runtime | Integration Runtime działanie zostało uruchomione |
| Właściwości użytkownika | Właściwości działania zdefiniowane przez użytkownika |
| Błąd | Jeśli działanie nie powiodło się, błąd przebiegu |
| Identyfikator przebiegu | Identyfikator uruchomienia działania |

Jeśli działanie nie powiodło się, zobaczysz szczegółowy komunikat o błędzie, klikając ikonę w kolumnie błędu. 

![Zostanie wyświetlone powiadomienie zawierające szczegóły błędu, w tym kod błędu, typ błędu i szczegóły błędu.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Podnieś poziom właściwości użytkownika do monitorowania

Podnieś każdą właściwość działania potoku jako właściwość użytkownika, aby stała się ona jednostką monitorowaną. Na przykład można podwyższyć poziom właściwości **źródłowej** i **docelowej** działania kopiowania w potoku jako właściwości użytkownika.

> [!NOTE]
> Można podwyższyć do pięciu właściwości działania potoku jako właściwości użytkownika.

![Tworzenie właściwości użytkownika](media/monitor-visually/promote-user-properties.png)

Po utworzeniu właściwości użytkownika można je monitorować w widokach listy monitorowania.

![Dodawanie kolumn do właściwości użytkownika na liście uruchomień działania](media/monitor-visually/choose-user-properties.png)

 Jeśli źródłem działania kopiowania jest nazwa tabeli, można monitorować nazwę tabeli źródłowej jako kolumnę w widoku listy dla uruchomień działania.

![Lista uruchomień działania z kolumnami dla właściwości użytkownika](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Uruchom ponownie potoki i działania

Aby ponownie uruchomić potok, który został wcześniej uruchomiony z poziomu ekranu startowego, umieść kursor nad określonym uruchomieniem potoku i wybierz pozycję **Uruchom ponownie**. W przypadku wybrania wielu potoków można uruchomić je wszystkie przy użyciu przycisku **Uruchom ponownie** .

![Uruchom ponownie potok](media/monitor-visually/rerun-pipeline.png)

Jeśli chcesz ponownie uruchomić program, rozpoczynając od określonego punktu, możesz to zrobić w widoku uruchomienia działania. Wybierz działanie, z którego chcesz się rozpocząć, a następnie wybierz pozycję **Uruchom ponownie z działania**. 

![Uruchom ponownie uruchomienie działania](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Uruchom ponownie z działania zakończonego niepowodzeniem

Jeśli działanie zakończy się niepowodzeniem, przekracza limit czasu lub zostanie anulowane, można ponownie uruchomić potok z działania zakończonego niepowodzeniem, wybierając pozycję **Uruchom ponownie z działania zakończonego niepowodzeniem**.

![Uruchom ponownie działanie zakończone niepowodzeniem](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Wyświetl historię ponownego uruchamiania

Można wyświetlić historię ponownego uruchamiania dla wszystkich uruchomień potoku w widoku listy.

![Wyświetlanie historii](media/monitor-visually/rerun-history-1.png)

Możesz również wyświetlić historię ponownego uruchamiania dla określonego uruchomienia potoku.

![Wyświetl historię uruchomienia potoku](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Monitorowanie użycia

Możesz zobaczyć zasoby używane przez uruchomienie potoku, klikając ikonę użycie obok przebiegu. 

![Zrzut ekranu pokazujący, gdzie można zobaczyć zasoby używane przez potok.](media/monitor-visually/monitor-consumption-1.png)

Kliknięcie ikony powoduje otwarcie raportu o zużyciu zasobów używanych przez to uruchomienie potoku. 

![Monitorowanie użycia](media/monitor-visually/monitor-consumption-2.png)

Te wartości można podłączyć do [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/details/data-factory/) , aby oszacować koszt uruchomienia potoku. Aby uzyskać więcej informacji na Azure Data Factory cenach, zobacz temat [Omówienie cen](pricing-concepts.md).

> [!NOTE]
> Te wartości zwracane przez kalkulator cen są szacowane. Nie odzwierciedla dokładnej kwoty, która będzie rozliczana Azure Data Factory 

## <a name="gantt-views"></a>Widoki wykresu Gantta

Wykres Gantta to widok, który umożliwia wyświetlenie historii uruchamiania w przedziale czasu. Po przełączeniu do widoku wykresu Gantta zobaczysz wszystkie uruchomienia potoków pogrupowane według nazwy wyświetlanej jako słupki względem czasu trwania przebiegu. Możesz również grupować według adnotacji/tagów utworzonych w potoku. Widok wykresu Gantta jest również dostępny na poziomie uruchomienia działania.

![Przykład wykresu Gantta](media/monitor-visually/select-gantt.png)

Długość paska informuje o czasie trwania potoku. Możesz również wybrać pasek, aby wyświetlić więcej szczegółów.

![Czas trwania wykresu Gantta](media/monitor-visually/view-gantt-run.png)

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