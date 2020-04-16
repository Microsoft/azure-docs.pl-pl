---
title: Wizualne monitorowanie fabryki danych platformy Azure
description: Dowiedz się, jak wizualnie monitorować fabryki danych platformy Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419446"
---
# <a name="visually-monitor-azure-data-factory"></a>Wizualne monitorowanie fabryki danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po utworzeniu i opublikowaniu potoku w usłudze Azure Data Factory można skojarzyć go z wyzwalaczem lub ręcznie rozpocząć uruchamianie ad hoc. Można monitorować wszystkie uruchomień potoku natywnie w środowisku użytkownika usługi Azure Data Factory. Aby otworzyć środowisko monitorowania, wybierz kafelek **Monitor & Zarządzaj** w bloku fabryki danych [w witrynie Azure portal](https://portal.azure.com/). Jeśli jesteś już w ux ADF, kliknij ikonę **Monitor** na lewym pasku bocznym.

Wszystkie uruchomienia fabryki danych są wyświetlane w lokalnej strefie czasowej przeglądarki. Jeśli zmienisz strefę czasową, wszystkie pola daty/godziny zostaną przyciągnięte do wybranego pola.

## <a name="monitor-pipeline-runs"></a>Monitorowanie uruchomień potoku

Domyślnym widokiem monitorowania jest lista przebiegów potoku w wybranym okresie. Wyświetlane są następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa rurociągu |
| Akcje | Ikony umożliwiające wyświetlanie szczegółów aktywności, anulowanie lub ponowne uruchomienie potoku |
| Uruchom start | Data i godzina rozpoczęcia uruchomienia potoku (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Czas trwania | Czas trwania biegu (HH:MM:SS) |
| Wyzwalane przez | Nazwa wyzwalacza, który uruchomił potok |
| Stan | **Nie powiodło się**, **powiodło się**, **w toku**, **Anulowano**lub **w kolejce** |
| Adnotacje | Znaczniki do filtrowania skojarzone z potokiem  |
| Parametry | Parametry uruchomienia potoku (pary nazwy/wartości) |
| Błąd | Jeśli potok nie powiódł się, błąd uruchamiania |
| Identyfikator przebiegu | Identyfikator uruchomienia potoku |

![Widok listy do monitorowania przebiegów potoku](media/monitor-visually/pipeline-runs.png)

Aby odświeżyć listę przebiegów potoku i działania, należy ręcznie wybrać przycisk **Odśwież.** Autorefresh nie jest obecnie obsługiwany.

![Przycisk Odśwież](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorowanie uruchomień działania

Aby wyświetlić działanie uruchamiane dla każdego uruchomienia potoku, wybierz ikonę **Wyświetl działanie uruchamia w** kolumnie **Akcje.** Widok listy pokazuje przebiegi działania, które odpowiadają każdemu uruchomieniu potoku.

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania wewnątrz potoku |
| Typ działania | Typ działania, takiego jak **Copy**, **ExecuteDataFlow**lub **AzureMLExecutePipeline** |
| Akcje | Ikony umożliwiające wyświetlanie informacji wejściowych JSON, informacji wyjściowych JSON lub szczegółowych środowisk monitorowania specyficznych dla aktywności | 
| Uruchom start | Data i godzina rozpoczęcia uruchomienia działania (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Czas trwania | Czas trwania biegu (HH:MM:SS) |
| Stan | **Nie powiodło** **się, powiodło się**, **w toku**lub **anulowano** |
| Środowisko wykonawcze integracji | Na którym środowisko uruchomieniowe integracji działanie zostało uruchomione |
| Właściwości użytkownika | Właściwości działania zdefiniowane przez użytkownika |
| Błąd | Jeśli działanie nie powiodło się, błąd uruchamiania |
| Identyfikator przebiegu | Identyfikator uruchomienia działania |

![Widok listy do monitorowania przebiegów działań](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Podwyższanie właściwości użytkownika w celu monitorowania

Promuj dowolną właściwość działania potoku jako właściwość użytkownika, tak aby stała się jednostką monitorowaną. Na przykład można podwyższyć nazwy **źródła** i **miejsca docelowego** działania kopiowania w potoku jako właściwości użytkownika. Wybierz **opcję Automatyczne generowanie,** aby wygenerować właściwości użytkownika **źródłowego** i **docelowego** dla działania kopiowania.

![Tworzenie właściwości użytkownika](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Można promować tylko maksymalnie pięć właściwości działania potoku jako właściwości użytkownika.

Po utworzeniu właściwości użytkownika można je monitorować w widokach listy monitorowania. Jeśli źródłem działania kopiowania jest nazwa tabeli, można monitorować nazwę tabeli źródłowej jako kolumnę w widoku listy dla działań uruchamiane.

![Lista uruchomień aktywności bez właściwości użytkownika](media/monitor-visually/monitor-user-properties-image2.png)

![Dodawanie kolumn dla właściwości użytkownika do listy przebiegów działania](media/monitor-visually/monitor-user-properties-image3.png)

![Lista uruchomień aktywności z kolumnami właściwości użytkownika](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Konfigurowanie widoku listy

### <a name="order-and-filter"></a>Zamawianie i filtrowanie

Przełącz, czy przebiegi potoku będą malejąco lub rosnąco zgodnie z czasem rozpoczęcia wykonywania. Potok filtru jest uruchamiany przy użyciu następujących kolumn:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Filtruj według nazwy potoku. |
| Uruchom start |  Określ zakres czasu wyświetlanych przebiegów potoku. Opcje obejmują szybkie filtry dla **ostatnich 24 godzin**, **Ostatni tydzień**i **Ostatnie 30 dni** lub wybrać niestandardową datę i godzinę. |
| Stan uruchomienia | Filtr jest **uruchamiany**według stanu: Niepowodzenie , Niepowodzenie , **Kolejkowanie,** **Anulowane**lub **W toku**. **Failed** |
| Adnotacje | Filtrowanie według znaczników zastosowanych do każdego potoku |
| Uruchamianie | Filtrowanie, czy chcesz zobaczyć potoki reran |

![Opcje filtrowania](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Dodawanie lub usuwanie kolumn
Kliknij prawym przyciskiem myszy nagłówek widoku listy i wybierz kolumny, które mają być wyświetlane w widoku listy.

![Opcje dla kolumn](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Dopasowywanie szerokości kolumn
Zwiększ i zmniejsz szerokość kolumn w widoku listy, najeżdżając kursorem na nagłówek kolumny.

## <a name="rerun-activities-inside-a-pipeline"></a>Ponowne uruchomienie działań wewnątrz potoku

Można ponownie uruchomić działania wewnątrz potoku. Wybierz **Wyświetl działanie jest uruchamiane,** a następnie wybierz działanie w potoku, z którego punktu chcesz ponownie uruchomić potok.

![Wyświetlanie uruchomień działania](media/monitor-visually/rerun-activities-image1.png)

![Wybieranie uruchomienia aktywności](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Uruchom ponownie z działania po awarii

Jeśli działanie nie powiedzie się, przekroczeniem czasu lub zostanie anulowane, można ponownie uruchomić potok z tego działania, którego nie powiodło się, wybierając **opcję Uruchom ponownie z działania nie powiodło się.**

![Ponowne uruchomienie działania nie powiodło się](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Wyświetl historię ponownego uruchomienia

Można wyświetlić historię ponownego uruchamiania dla wszystkich uruchomień potoku w widoku listy.

![Wyświetlanie historii](media/monitor-visually/rerun-history-image1.png)

Można również wyświetlić historię ponownego uruchamiania dla określonego uruchomienia potoku.

![Wyświetlanie historii dla przebiegu potoku](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Widoki Gantta

Widoki Gantta umożliwia szybkie wizualizowanie potoków i przebiegów aktywności.

![Przykład wykresu Gantta](media/monitor-visually/gantt1.png)

Widok Gantta na potok lub grupę można przeglądać według adnotacji/tagów utworzonych w potokach.

![Adnotacje wykresu Gantta](media/monitor-visually/gantt2.png)

Długość paska informuje o czasie trwania potoku. Można również wybrać pasek, aby wyświetlić więcej szczegółów.

![Czas trwania wykresu Gantta](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Przewodnikiem
Wybierz ikonę **Informacje** w lewym dolnym dolnym roku. Następnie wybierz **przewodnik Wycieczki,** aby uzyskać instrukcje krok po kroku dotyczące monitorowania przebiegów potoku i aktywności.

![Przewodnikiem](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alerty

Alerty dotyczące obsługiwanych metryk można zgłaszać w układzie danych. Wybierz **opcję Monitoruj** > **alerty & metryki** na stronie monitorowania fabryki danych, aby rozpocząć.

![Strona Monitora fabryki danych](media/monitor-visually/alerts01.png)

Aby zapoznać się z siedmiominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Tworzenie alertów

1.  Wybierz **pozycję Nowa reguła alertu,** aby utworzyć nowy alert.

    ![Przycisk Nowa reguła alertu](media/monitor-visually/alerts02.png)

1.  Określ nazwę reguły i wybierz ważność alertu.

    ![Pola dla nazwy i ważności reguły](media/monitor-visually/alerts03.png)

1.  Wybierz kryteria alertu.

    ![Pole dla kryteriów docelowych](media/monitor-visually/alerts04.png)

    ![Lista kryteriów](media/monitor-visually/alerts05.png)

1.  Skonfiguruj logikę alertu. Można utworzyć alert dla wybranej metryki dla wszystkich potoków i odpowiednich działań. Można również wybrać określony typ działania, nazwę działania, nazwę potoku lub typ błędu.

    ![Opcje konfigurowania logiki alertów](media/monitor-visually/alerts06.png)

1.  Skonfiguruj powiadomienia e-mail, SMS, push i voice dla alertu. Utwórz grupę akcji lub wybierz istniejącą dla powiadomień alertów.

    ![Opcje konfigurowania powiadomień](media/monitor-visually/alerts07.png)

    ![Opcje dodawania powiadomienia](media/monitor-visually/alerts08.png)

1.  Utwórz regułę alertu.

    ![Opcje tworzenia reguły alertu](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz artykuł [Programowo Monitoruj potoki i zarządzaj nimi.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
