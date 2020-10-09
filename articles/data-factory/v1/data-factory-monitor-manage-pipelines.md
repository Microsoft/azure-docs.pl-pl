---
title: Monitorowanie potoków i zarządzanie nimi przy użyciu Azure Portal i programu PowerShell
description: Dowiedz się, jak używać Azure Portal i Azure PowerShell do monitorowania fabryk danych platformy Azure i utworzonych potoków oraz zarządzania nimi.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73666979"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorowanie potoków Azure Data Factory i zarządzanie nimi przy użyciu Azure Portal i programu PowerShell
> [!div class="op_single_selector"]
> * [Używanie Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Korzystanie z aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [monitorowanie potoków Data Factory w programie i zarządzanie nimi](../monitor-visually.md).

W tym artykule opisano sposób monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu Azure Portal i programu PowerShell.

> [!IMPORTANT]
> Aplikacja do zarządzania & monitorowania zapewnia lepszą obsługę monitorowania potoków danych i zarządzania nimi oraz rozwiązywania problemów. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków Data Factory i zarządzanie nimi za pomocą aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory wersja 1 używa teraz nowej [infrastruktury alertów Azure monitor](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Stara infrastruktura alertów jest przestarzała. W związku z tym istniejące alerty skonfigurowane dla fabryk danych w wersji 1 nie będą już działać. Istniejące alerty dla fabryk danych w wersji 1 nie są migrowane automatycznie. Należy ponownie utworzyć te alerty dla nowej infrastruktury alertów. Zaloguj się do Azure Portal i wybierz pozycję **Monitoruj** , aby utworzyć nowe alerty dotyczące metryk (na przykład nieudanych uruchomień lub przebiegów zakończonych powodzeniem) dla fabryk danych w wersji 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Omówienie potoków i Stanów aktywności
Za pomocą Azure Portal można:

* Wyświetl fabrykę danych jako diagram.
* Wyświetlanie działań w potoku.
* Wyświetlanie wejściowych i wyjściowych zestawów danych.

W tej części opisano również sposób przejścia wycinka zestawu danych z jednego stanu do drugiego.   

### <a name="navigate-to-your-data-factory"></a>Przejdź do fabryki danych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **fabryki danych** w menu po lewej stronie. Jeśli go nie widzisz, kliknij pozycję **więcej usług >** a następnie kliknij pozycję **fabryki danych** w kategorii **Analiza i analiza** .

   ![Przeglądaj wszystkie > fabryki danych](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. W bloku **fabryki danych** wybierz fabrykę danych, która Cię interesuje.

    ![Wybieranie fabryki danych](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Powinna zostać wyświetlona strona główna fabryki danych.

   ![Blok Fabryka danych](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Widok diagramu fabryki danych
Widok **diagramu** fabryki danych zapewnia pojedyncze okienko szkła do monitorowania fabryki danych i jej zasobów oraz zarządzania nią. Aby wyświetlić widok **diagramu** fabryki danych, kliknij przycisk **Diagram** na stronie głównej fabryki danych.

![Widok diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Możesz powiększać, pomniejszać, powiększyć, powiększyć do 100%, zablokować układ diagramu i automatycznie pozycjonować potoki i zestawy danych. Możesz również zobaczyć informacje o pozostałej części danych (to znaczy, aby pokazać elementy nadrzędne i podrzędne wybranych elementów).

### <a name="activities-inside-a-pipeline"></a>Działania wewnątrz potoku
1. Kliknij prawym przyciskiem myszy potok, a następnie kliknij pozycję **Otwórz potok** , aby wyświetlić wszystkie działania w potoku, a także zestawy danych wejściowych i wyjściowych działań. Ta funkcja jest przydatna, gdy potok zawiera więcej niż jedno działanie i chcesz zrozumieć jego operacyjną składową.

    ![Menu Otwórz potok](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. W poniższym przykładzie zobaczysz działanie Copy w potoku z danymi wejściowymi i wyjściowymi. 

    ![Działania wewnątrz potoku](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Możesz przejść z powrotem do strony głównej fabryki danych, klikając link **Fabryka danych** w obszarze podłączanym w lewym górnym rogu.

    ![Przejdź z powrotem do fabryki danych](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Wyświetlanie stanu poszczególnych działań w potoku
Bieżący stan działania można wyświetlić, wyświetlając stan dowolnego zestawu danych, który jest wytwarzany przez działanie.

Dwukrotne kliknięcie **OutputBlobTable** na **diagramie**pozwala zobaczyć wszystkie wycinki, które są generowane przez różne uruchomienia w potoku. Można zobaczyć, że działanie kopiowania zostało wykonane pomyślnie przez ostatnie osiem godzin i wygenerowało wycinki w stanie **gotowe** .  

![Stan potoku](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Wycinki zestawu danych w fabryce danych mogą mieć jeden z następujących stanów:

<table>
<tr>
    <th align="left">State</th><th align="left">Podstanu</th><th align="left">Opis</th>
</tr>
<tr>
    <td rowspan="8">Oczekiwanie</td><td>ScheduleTime</td><td>Czas, który nie jest przeznaczony dla wycinka do uruchomienia.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Zależności nadrzędne nie są gotowe.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Zasoby obliczeniowe nie są dostępne.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Wszystkie wystąpienia działania są zajęte działaniem innych wycinków.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Działanie jest wstrzymane i nie może uruchamiać wycinków do momentu wznowienia działania.</td>
</tr>
<tr>
<td>Ponawianie próby</td><td>Trwa ponawianie wykonywania działania.</td>
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
<td>Wycinek jest przetwarzany.</td>
</tr>
<tr>
<td rowspan="4">Niepowodzenie</td><td>TimedOut</td><td>Wykonanie działania trwało dłużej niż to, co jest dozwolone przez działanie.</td>
</tr>
<tr>
<td>Anulowane</td><td>Wycinek został anulowany przez akcję użytkownika.</td>
</tr>
<tr>
<td>Walidacja</td><td>Walidacja nie powiodła się.</td>
</tr>
<tr>
<td>-</td><td>Nie można wygenerować i/lub zweryfikować wycinka.</td>
</tr>
<td>Gotowy</td><td>-</td><td>Wycinek jest gotowy do użycia.</td>
</tr>
<tr>
<td>Pominięto</td><td>Brak</td><td>Wycinek nie jest przetwarzany.</td>
</tr>
<tr>
<td>Brak</td><td>-</td><td>Wycinek używany do istnienia z innym stanem, ale został zresetowany.</td>
</tr>
</table>



Aby wyświetlić szczegóły wycinka, kliknij pozycję wycinka w bloku **ostatnio zaktualizowane wycinki** .

![Szczegóły wycinka](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Jeśli wycinek został wykonany wiele razy, na liście **uruchomień działania** zobaczysz wiele wierszy. Aby wyświetlić szczegółowe informacje o uruchomieniu działania, kliknij wpis Run na liście **uruchomienia działania** . Lista zawiera wszystkie pliki dziennika wraz z komunikatem o błędzie, jeśli istnieje. Ta funkcja jest przydatna do wyświetlania i debugowania dzienników bez konieczności opuszczania fabryki danych.

![Szczegóły uruchamiania działania](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Jeśli wycinek nie jest w stanie **gotowe** , można wyświetlić wycinki, które nie są gotowe i blokują wykonywanie bieżącego wycinka w **wycinkach nadrzędnych, które nie są gotowe** . Ta funkcja jest przydatna, gdy wycinek jest w stanie **oczekiwania** i chcesz zrozumieć zależności nadrzędne, na których wycinek jest oczekiwany.

![Wycinków nadrzędnych, które nie są gotowe](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stanu zestawu danych
Po wdrożeniu fabryki danych, gdy potoki mają prawidłowy aktywny okres, wycinki zestawu danych przechodzą z jednego stanu do drugiego. Obecnie stan wycinka jest następujący:

![Diagram stanu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Przepływ przejścia stanu zestawu danych w usłudze Fabryka danych jest następujący: oczekiwanie na > w toku/w toku (sprawdzanie poprawności) — > gotowe/niepowodzenie.

Wycinek zaczyna się w stanie **oczekiwania** , czekając na spełnienie warunków wstępnych przed wykonaniem. Następnie działanie zostanie uruchomione, a plasterek przechodzi w stan **w toku** . Wykonanie działania może zakończyć się powodzeniem lub niepowodzeniem. Wycinek jest oznaczony jako **gotowy** lub **Niepowodzenie**w oparciu o wynik wykonania.

Możesz zresetować wycinek, aby wrócić do stanu **gotowości** lub stanu **niepowodzenia** na stan **oczekiwania** . Możesz również oznaczyć stan wycinka do **pominięcia**, co uniemożliwia wykonanie działania i nie przetwarza wycinka.

## <a name="pause-and-resume-pipelines"></a>Wstrzymywanie i wznawianie potoków
Potokami można zarządzać przy użyciu Azure PowerShell. Można na przykład wstrzymywać i wznawiać potoki, uruchamiając polecenia cmdlet Azure PowerShell. 

> [!NOTE] 
> Widok diagramu nie obsługuje wstrzymywania i wznawiania potoków. Jeśli chcesz użyć interfejsu użytkownika, użyj aplikacji monitorowanie i zarządzanie. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków Data Factory i zarządzanie nimi za pomocą artykułu monitorowanie i aplikacja do zarządzania](data-factory-monitor-manage-app.md) . 

Potoki można wstrzymywać/wstrzymywanie przy użyciu polecenia cmdlet **Suspend-AzDataFactoryPipeline** programu PowerShell. To polecenie cmdlet jest przydatne, gdy nie chcesz uruchamiać potoków do momentu usunięcia problemu. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po rozwiązaniu problemu z potokiem można wznowić wstrzymany potok, uruchamiając następujące polecenie programu PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Na przykład:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Potoki debugowania
Azure Data Factory oferuje bogate możliwości debugowania i rozwiązywania problemów z potokami przy użyciu Azure Portal i Azure PowerShell.

> [!NOTE] 
> Znacznie łatwiej jest Troubleshot błędy za pomocą aplikacji do zarządzania & monitorowania. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków Data Factory i zarządzanie nimi za pomocą artykułu monitorowanie i aplikacja do zarządzania](data-factory-monitor-manage-app.md) . 

### <a name="find-errors-in-a-pipeline"></a>Znajdowanie błędów w potoku
Jeśli uruchomienie działania nie powiedzie się w potoku, zestaw danych, który jest generowany przez potok, jest w stanie błędu z powodu błędu. Błędy w Azure Data Factory można debugować i rozwiązywać przy użyciu poniższych metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Użyj Azure Portal do debugowania błędu
1. W bloku **tabela** kliknij wycinek problemu, którego **stan** ma wartość **Niepowodzenie**.

   ![Blok tabeli z wycinkem problemu](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. W bloku **wycinka danych** kliknij uruchomienie działania, które nie powiodło się.

   ![Wycinek danych z błędem](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. W bloku **szczegóły uruchomienia działania** można pobrać pliki skojarzone z przetwarzaniem usługi HDInsight. Kliknij pozycję **Pobierz** dla stanu/stderr, aby pobrać plik dziennika błędów, który zawiera szczegółowe informacje o błędzie.

   ![Blok szczegółów uruchamiania działania z błędem](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Debugowanie błędu przy użyciu programu PowerShell
1. Uruchom program **PowerShell**.
2. Uruchom polecenie **Get-AzDataFactorySlice** , aby wyświetlić wycinki i ich Stany. Powinien zostać wyświetlony wycink o stanie **Niepowodzenie**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Na przykład:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Zastąp **StartDateTime** z czasem rozpoczęcia potoku. 
3. Teraz uruchom polecenie cmdlet **Get-AzDataFactoryRun** , aby uzyskać szczegółowe informacje o uruchomieniu działania dla wycinka.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Na przykład:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Wartość StartDateTime to godzina rozpoczęcia wycinka błędu/problemu zanotowanego w poprzednim kroku. Data i godzina powinna być ujęta w cudzysłów.
4. Powinny zostać wyświetlone dane wyjściowe ze szczegółowymi informacjami o błędzie podobnym do poniższego:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Można uruchomić polecenie cmdlet **Save-AzDataFactoryLog** z wartością identyfikatora, która jest wyświetlana w danych wyjściowych, i pobrać pliki dziennika za pomocą polecenia **-DownloadLogsoption** w celu pobrania.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Ponowne uruchamianie niepowodzeń w potoku

> [!IMPORTANT]
> Łatwiej jest rozwiązywać problemy i ponownie uruchamiać wycinków zakończonych niepowodzeniem za pomocą aplikacji do zarządzania & monitorowania. Aby uzyskać szczegółowe informacje na temat korzystania z aplikacji, zobacz [monitorowanie potoków Data Factory i zarządzanie nimi za pomocą aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Po rozwiązaniu błędów i debugowaniu w potoku możesz ponownie uruchomić awarie, przechodząc do wycinka błędu i klikając przycisk **Uruchom** na pasku poleceń.

![Ponowne uruchamianie wycinka zakończonego niepowodzeniem](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Jeśli wycinek nie zakończył walidacji z powodu błędu zasad (na przykład jeśli dane nie są dostępne), możesz naprawić błąd i ponownie sprawdzić poprawność, klikając przycisk **Weryfikuj** na pasku poleceń.

![Usuń błędy i sprawdź poprawność](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Błędy można ponownie uruchomić za pomocą polecenia cmdlet **Set-AzDataFactorySliceStatus** . Zobacz temat [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) , aby poznać składnię i inne szczegółowe informacje o poleceniu cmdlet.

**Przykład:**

Poniższy przykład ustawia stan wszystkich wycinków tabeli "DAWikiAggregatedData" na "oczekiwanie" w fabryce danych Azure "WikiADF".

Element "UpdateType" jest ustawiony na wartość "UpstreamInPipeline", co oznacza, że Stany każdego wycinka tabeli i wszystkich zależnych (nadrzędnych) tabel są ustawione na "oczekiwanie". Inną możliwą wartością dla tego parametru jest "indywidualna".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Tworzenie alertów w Azure Portal

1.  Zaloguj się do Azure Portal i wybierz pozycję **monitorowanie > alerty** , aby otworzyć stronę alerty.

    ![Otwórz stronę alerty.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Wybierz pozycję **+ Nowa reguła alertu** , aby utworzyć nowy Alert.

    ![Utwórz nowy Alert](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Zdefiniuj **warunek alertu**. (Pamiętaj o wybraniu **fabryk danych** w polu **Filtruj według typu zasobu** ). Można również określić wartości **wymiarów**.

    ![Zdefiniuj warunek alertu — wybierz element docelowy](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Zdefiniuj warunek alertu — Dodaj kryteria alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definiowanie warunku alertu — Dodawanie logiki alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Zdefiniuj **szczegóły alertu**.

    ![Zdefiniuj szczegóły alertu](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Zdefiniuj **grupę akcji**.

    ![Definiowanie grupy akcji — Tworzenie nowej grupy akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definiowanie właściwości grupy akcji — Ustawianie](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Zdefiniuj grupę akcji — utworzono nową grupę akcji](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Przenoszenie fabryki danych do innej grupy zasobów lub subskrypcji
Fabrykę danych można przenieść do innej grupy zasobów lub innej subskrypcji przy użyciu przycisku **Przenieś** pasek poleceń na stronie głównej fabryki danych.

![Przenoszenie fabryki danych](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Możesz również przenieść wszystkie powiązane zasoby (takie jak alerty skojarzone z fabryką danych) wraz z fabryką danych.

![Przenoszenie zasobów — okno dialogowe](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
