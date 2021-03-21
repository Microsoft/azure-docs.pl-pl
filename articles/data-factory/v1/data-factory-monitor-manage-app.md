---
title: Monitorowanie potoków danych i zarządzanie nimi — Azure
description: Dowiedz się, jak używać aplikacji do monitorowania i zarządzania do monitorowania fabryk danych platformy Azure i potoków oraz zarządzania nimi.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: be11f742eb99d25cb0e41a3dd0888d62417837dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380376"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorowanie potoków Azure Data Factory i zarządzanie nimi za pomocą aplikacji do monitorowania i zarządzania
> [!div class="op_single_selector"]
> * [Używanie Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Korzystanie z aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [monitorowanie potoków Data Factory w programie i zarządzanie nimi](../monitor-visually.md).

W tym artykule opisano sposób korzystania z aplikacji do monitorowania i zarządzania w celu monitorowania i debugowania potoków Data Factory oraz zarządzania nimi. Możesz zacząć korzystać z aplikacji, obserwując następujące wideo:

> [!NOTE]
> Interfejs użytkownika wyświetlany w wideo może nie być dokładnie zgodny z wyświetlanymi w portalu. Jest nieco starsze, ale koncepcje pozostają takie same. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Uruchamianie aplikacji do monitorowania i zarządzania
Aby uruchomić aplikację monitor i zarządzanie, kliknij kafelek **monitorowanie & zarządzanie** kafelkiem w bloku **Data Factory** dla fabryki danych.

![Kafelek monitorowania na stronie głównej Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Powinna zostać wyświetlona aplikacja monitorowanie i zarządzanie w osobnym oknie.  

![Aplikacja do monitorowania i zarządzania](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Jeśli zobaczysz, że przeglądarka sieci Web jest zablokowana na "autoryzowanie...", wyczyść pole wyboru **Blokuj pliki cookie innych firm i dane lokacji** --lub zaznacz je, Utwórz wyjątek dla **login.microsoftonline.com**, a następnie spróbuj ponownie otworzyć aplikację.


Na liście okna działania w środkowym okienku zobaczysz okno działania dla każdego uruchomienia działania. Jeśli na przykład masz zaplanowaną aktywność do uruchomienia co pięć godzin, zobaczysz pięć okien działania skojarzonych z pięcioma wycinkami danych. Jeśli na liście nie ma okna działania, wykonaj następujące czynności:
 
- Zaktualizuj filtry **godzina rozpoczęcia** i **godzina zakończenia** w górnej części, aby dopasować czas rozpoczęcia i zakończenia potoku, a następnie kliknij przycisk **Zastosuj** .  
- Lista okien działania nie jest odświeżana automatycznie. Kliknij przycisk **Odśwież** na pasku narzędzi na liście **okna działania** .  

Jeśli nie masz aplikacji Data Factory do przetestowania tych kroków w programie, wykonaj czynności opisane w samouczku: [Kopiowanie danych z BLOB Storage do SQL Database przy użyciu Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Omówienie aplikacji do monitorowania i zarządzania
Po lewej stronie znajdują się trzy karty: **Eksplorator zasobów**, **widoki monitorowania** i **alerty**. Pierwsza karta (**Eksplorator zasobów**) jest domyślnie zaznaczona.

### <a name="resource-explorer"></a>Eksplorator zasobów
Zobaczysz następujące elementy:

* **Widok drzewa** Eksplorator zasobów w lewym okienku.
* **Widok diagramu** u góry w środkowym okienku.
* Lista **okien działania** u dołu w środkowym okienku.
* **Właściwości**, **Eksplorator okna działania** i karty **skryptów** w okienku po prawej stronie.

W Eksplorator zasobów widoczne są wszystkie zasoby (potoki, zestawy danych, połączone usługi) w fabryce danych w widoku drzewa. Po wybraniu obiektu w Eksplorator zasobów:

* Skojarzona jednostka Data Factory zostanie wyróżniona w widoku diagramu.
* [Skojarzone okna działania](data-factory-scheduling-and-execution.md) są wyróżnione na liście okna działania u dołu.  
* Właściwości wybranego obiektu są wyświetlane w okno Właściwości w okienku po prawej stronie.
* Definicja JSON wybranego obiektu jest wyświetlana, jeśli ma zastosowanie. Na przykład: połączona usługa, zestaw danych lub potok.

![Eksplorator zasobów](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Szczegółowe informacje o pojęciach dotyczących okien aktywności można znaleźć w artykule dotyczącym [planowania i wykonywania](data-factory-scheduling-and-execution.md) .

### <a name="diagram-view"></a>Widok diagramu
Widok diagramu fabryki danych zapewnia pojedyncze okienko szkła do monitorowania fabryki danych i jej zasobów oraz zarządzania nią. Po wybraniu jednostki Data Factory (zestawu danych/potoku) w widoku diagramu:

* Jednostka fabryki danych została wybrana w widoku drzewa.
* Skojarzone okna działania są wyróżnione na liście okna działania.
* Właściwości wybranego obiektu są wyświetlane w okno Właściwości.

Gdy potok jest włączony (nie w stanie wstrzymania), jest pokazywany w zielonej linii:

![Potok uruchomiony](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Można wstrzymywać, wznawiać lub kończyć potok, zaznaczając go w widoku diagramu i używając przycisków na pasku poleceń.

![Wstrzymywanie/wznawianie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Dla potoku w widoku diagramu istnieją trzy przyciski paska poleceń. Możesz użyć drugiego przycisku, aby wstrzymać potok. Wstrzymywanie nie przerywa aktualnie uruchomionych działań i pozwala na ich zakończenie. Trzeci przycisk wstrzymuje potok i kończy istniejące działania wykonywane. Pierwszy przycisk wznawia potok. Gdy potok jest wstrzymany, kolor potoku zostanie zmieniony. Na przykład wstrzymany potok wygląda jak na poniższym obrazie: 

![Potok wstrzymany](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Można wybrać dwa lub więcej potoków przy użyciu klawisza CTRL. Za pomocą przycisków paska poleceń można wstrzymywać i wznawiać wiele potoków jednocześnie.

Możesz również kliknąć prawym przyciskiem myszy potok i wybrać opcje wstrzymania, wznowienia lub zakończenia potoku. 

![Menu kontekstowe dla potoku](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknij opcję **Otwórz potok** , aby wyświetlić wszystkie działania w potoku. 

![Menu Otwórz potok](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

W widoku otwarty potok zobaczysz wszystkie działania w potoku. W tym przykładzie istnieje tylko jedno działanie: działanie Copy (kopiowanie). 

![Otwarty potok](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Aby powrócić do poprzedniego widoku, kliknij nazwę fabryki danych w menu stron nadrzędnych u góry.

W widoku potoku, gdy wybierzesz wyjściowy zestaw danych lub przesuniesz wskaźnik myszy nad wyjściowy zestaw danych, zobaczysz okno podręczne działania systemu Windows dla tego zestawu danych.

![Okno podręczne działania systemu Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Możesz kliknąć okno działania, aby wyświetlić jego szczegóły w oknie **Właściwości** w okienku po prawej stronie.

![Właściwości okna działania](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

W okienku po prawej stronie przejdź do karty **Eksplorator okna działania** , aby wyświetlić więcej szczegółów.

![Zrzut ekranu pokazujący, jak uzyskać dostęp do karty Eksploratora okna działania.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Wyświetlane są również **zmienne rozpoznane** dla każdej próby uruchomienia dla działania w sekcji **próby** .

![Rozpoznane zmienne](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Przejdź do karty **skrypt** , aby wyświetlić definicję skryptu JSON dla wybranego obiektu.   

![Karta Skrypt](./media/data-factory-monitor-manage-app/ScriptTab.png)

Okna działania można zobaczyć w trzech miejscach:

* Okna podręczne działania w widoku diagramu (środkowym).
* Eksplorator okna działania w okienku po prawej stronie.
* Lista okien działania w dolnym okienku.

W oknie podręcznym działania okna podręcznego w systemie Windows można przewijać do poprzedniego tygodnia i w następnym tygodniu przy użyciu strzałek w lewo i w prawo.

![Eksplorator okna działania — strzałki w lewo/w prawo](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

W dolnej części widoku diagramu zobaczysz następujące przyciski: Powiększ, Pomniejsz, Powiększ, Dopasuj do rozmiaru, Powiększ do 100%, zablokuj układ. Przycisk **blokowania układu** uniemożliwia przypadkowe przeniesienie tabel i potoków w widoku diagramu. Jest on domyślnie włączony. Można ją wyłączyć i przenieść jednostki na diagramie. Po wyłączeniu tej opcji możesz użyć ostatniego przycisku, aby automatycznie pozycjonować tabele i potoki. Możesz również powiększyć lub pomniejszyć, używając kółka myszy.

![Polecenia powiększenia widoku diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Okna działania
Lista okna działania u dołu środkowego okienka przedstawia wszystkie okna działania dla zestawu danych, który został wybrany w Eksplorator zasobów lub widok diagramu. Domyślnie lista znajduje się w kolejności malejącej, co oznacza, że w górnej części okna działania są widoczne.

![Lista Okna działania](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ta lista nie jest odświeżana automatycznie, dlatego użyj przycisku Odśwież na pasku narzędzi, aby ręcznie odświeżyć go.  

Okna działania mogą mieć jeden z następujących stanów:

<table>
<tr>
    <th align="left">Stan</th><th align="left">Substatus</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas nie ma czasu na uruchomienie okna działania.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności nadrzędne nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte w innych oknach działania.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie może uruchamiać okien działania, dopóki nie zostanie wznowione.</td>
</tr>
<tr>
<td>Ponów próbę</td><td>Trwa ponawianie wykonywania działania.</td>
</tr>
<tr>
<td>Walidacja</td><td>Sprawdzanie poprawności nie zostało jeszcze rozpoczęte.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Trwa oczekiwanie na ponowną próbę weryfikacji.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Toku</td><td>Ponownego</td><td>Walidacja jest w toku.</td>
</tr>
<td>-</td>
<td>Trwa przetwarzanie okna działania.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>TimedOut</td><td>Wykonanie działania trwało dłużej niż to, co jest dozwolone przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Okno działania zostało anulowane przez akcję użytkownika.</td>
</tr>
<tr>
<td>Walidacja</td><td>Walidacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Nie można wygenerować lub sprawdzić poprawności okna działania.</td>
</tr>
<td>Gotowy</td><td>-</td><td>Okno działania jest gotowe do użycia.</td>
</tr>
<tr>
<td>Pominięto</td><td>-</td><td>Okno działania nie zostało przetworzone.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Okno działania używane do istnienia z innym stanem, ale zostało zresetowane.</td>
</tr>
</table>


Po kliknięciu okna działania na liście zostaną wyświetlone szczegółowe informacje o nim w **Eksploratorze Windows działania** lub oknie **Właściwości** po prawej stronie.

![Zrzut ekranu pokazujący sposób wyświetlania szczegółowych informacji o oknie działania.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Odśwież okna działania
Szczegóły nie są odświeżane automatycznie, więc Użyj przycisku Odśwież (drugi przycisk) na pasku poleceń, aby ręcznie odświeżyć listę okien działania.  

### <a name="properties-window"></a>Okno właściwości
Okno Właściwości znajduje się w prawym okienku aplikacji monitorowanie i zarządzanie.

![Okno właściwości](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Wyświetla właściwości dla elementu wybranego w Eksplorator zasobów (widok drzewa), widok diagramu lub lista okien działania.

### <a name="activity-window-explorer"></a>Eksplorator okna działania
Okno **Eksploratora okna działania** znajduje się w prawym okienku aplikacji monitorowanie i zarządzanie. Wyświetla szczegółowe informacje o oknie działania wybranym w oknie podręcznym działania systemu Windows lub na liście okna działania.

![Eksplorator okna działania](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Możesz przełączyć się do innego okna działania, klikając je w widoku kalendarza u góry. Możesz również użyć przycisków strzałki w lewo/Strzałka w prawo u góry, aby zobaczyć okna aktywności z poprzedniego tygodnia lub w następnym tygodniu.

Możesz użyć przycisków paska narzędzi w dolnym okienku, aby ponownie uruchomić okno działania lub odświeżyć szczegóły w okienku.

### <a name="script"></a>Skrypt
Karta **skrypt** służy do wyświetlania definicji JSON wybranej jednostki Data Factory (połączonej usługi, zestawu danych lub potoku).

![Karta Skrypt](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Korzystanie z widoków systemowych
Aplikacja do monitorowania i zarządzania zawiera wstępnie skompilowane widoki systemowe (**ostatnie okna działania**, **zakończone niepowodzeniem** okna działania, **okna działania w toku**), które umożliwiają wyświetlanie okien działania ostatnich/zakończonych niepowodzeniem/w toku dla fabryki danych.

Przejdź do karty **widoki monitorowania** po lewej stronie, klikając ją.

![Karta widoki monitorowania](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Obecnie są obsługiwane trzy widoki systemowe. Wybierz opcję, aby zobaczyć ostatnie okna działania, okna działania zakończone niepowodzeniem lub okna działania w toku na liście okna działania (w dolnej części środkowego okienka).

Po wybraniu opcji **okna ostatnie działanie** zobaczysz wszystkie ostatnie okna działania w kolejności malejącej od **czasu ostatniej próby**.

Możesz użyć widoku **okna działania zakończonego niepowodzeniem** , aby zobaczyć wszystkie okna działania zakończonego niepowodzeniem na liście. Wybierz okno działania zakończonego niepowodzeniem na liście, aby wyświetlić szczegółowe informacje o nim w oknie **Właściwości** lub w **Eksploratorze okna działania**. Możesz również pobrać wszystkie dzienniki dla okna działania zakończonego niepowodzeniem.

## <a name="sort-and-filter-activity-windows"></a>Sortuj i Filtruj okna działań
Zmień ustawienia **godzina rozpoczęcia** i **godzina zakończenia** na pasku poleceń, aby filtrować okna aktywności. Po zmianie czasu rozpoczęcia i czasu zakończenia kliknij przycisk obok godziny zakończenia, aby odświeżyć listę okien działania.

![Czasy rozpoczęcia i zakończenia](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Obecnie wszystkie czasy są w formacie UTC w aplikacji do monitorowania i zarządzania.
>
>

Na **liście okna działania** kliknij nazwę kolumny (na przykład: stan).

![Menu kolumny listy okien działania](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Można wykonać następujące czynności:

* Sortuj w kolejności rosnącej.
* Sortuj w kolejności malejącej.
* Filtruj według co najmniej jednej wartości (gotowe, oczekujące itd.).

Gdy określisz filtr dla kolumny, zobaczysz przycisk filtru włączony dla tej kolumny, co oznacza, że wartości w kolumnie są filtrowane wartości.

![Filtrowanie według kolumny listy okien działania](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Możesz użyć tego samego okna podręcznego do czyszczenia filtrów. Aby wyczyścić wszystkie filtry dla listy okien działania, kliknij przycisk Wyczyść filtr na pasku poleceń.

![Wyczyść wszystkie filtry dla listy okien działania](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Wykonywanie akcji wsadowych
### <a name="rerun-selected-activity-windows"></a>Uruchom ponownie wybrane okna działania
Wybierz okno działania, kliknij strzałkę w dół dla pierwszego przycisku paska poleceń, a następnie wybierz pozycję **Uruchom** ponownie  /  **za pomocą nadrzędnego w potoku**. Po wybraniu opcji **Uruchom ponownie za pomocą nadrzędnego w potoku** jest również wykonywane ponowne uruchomienie wszystkich okien działania nadrzędnego.
    ![Uruchom ponownie okno działania](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Możesz również zaznaczyć wiele okien aktywności na liście i uruchomić je ponownie w tym samym czasie. Możesz chcieć filtrować okna aktywności na podstawie stanu (na przykład: **Niepowodzenie**)--, a następnie ponownie uruchomić okna działania zakończonego niepowodzeniem po usunięciu problemu powodującego niepowodzenie działania systemu Windows. Zapoznaj się z sekcją w poniższej sekcji, aby uzyskać szczegółowe informacje na temat filtrowania okien aktywności na liście.  

### <a name="pauseresume-multiple-pipelines"></a>Wstrzymywanie/wznawianie wielu potoków
Można wybrać dwa lub więcej potoków przy użyciu klawisza CTRL. Możesz użyć przycisków paska poleceń (które są wyróżnione na czerwono prostokąt na poniższym obrazie), aby je wstrzymać/wznowić.

![Wstrzymywanie/wznawianie na pasku poleceń](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
