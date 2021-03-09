---
title: Tworzenie graficznych elementów Runbook w Azure Automation
description: W tym artykule opisano sposób tworzenia graficznego elementu Runbook bez pracy z kodem.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: bbac794263fec176e03c7148d860c479a2ed9d39
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501232"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Tworzenie graficznych elementów Runbook w Azure Automation

Wszystkie elementy Runbook w Azure Automation są przepływami pracy programu Windows PowerShell. Graficzne elementy Runbook i graficzne elementy Runbook przepływu pracy programu PowerShell generują kod programu PowerShell uruchamiany przez pracowników automatyzacji, ale nie można go wyświetlać ani modyfikować. Możesz skonwertować graficzny element Runbook na graficzny element Runbook przepływu pracy programu PowerShell i na odwrót. Nie można jednak skonwertować tych elementów Runbook do tekstowego elementu Runbook. Ponadto edytor graficzny automatyzacji nie może zaimportować tekstowego elementu Runbook.

Tworzenie graficzne pozwala tworzyć elementy Runbook dla Azure Automation bez złożoności podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Możesz dodawać działania do kanwy z biblioteki poleceń cmdlet i elementów Runbook, łączyć je ze sobą i konfigurować w celu utworzenia przepływu pracy. Jeśli kiedykolwiek pracujesz z programem System Center Orchestrator lub Service Management Automation (SMA), tworzenie grafiki powinno wyglądać znajomo. Ten artykuł zawiera wprowadzenie do pojęć, które należy wykonać, aby rozpocząć tworzenie graficznego elementu Runbook.

## <a name="overview-of-graphical-editor"></a>Przegląd edytora graficznego

Możesz otworzyć Edytor graficzny w Azure Portal, tworząc lub edytując graficzny element Runbook.

![Graficzny obszar roboczy](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano kontrolki w edytorze graficznym.

### <a name="canvas-control"></a>Kontrolka kanwy

Kontrolka kanwy umożliwia zaprojektowanie elementu Runbook. Możesz dodać działania z węzłów w kontrolce biblioteki do elementu Runbook i połączyć je z linkami do definiowania logiki elementu Runbook. W dolnej części kanwy znajdują się kontrolki, które umożliwiają powiększanie i pomniejszanie.

### <a name="library-control"></a>Kontrolka biblioteki

Kontrolka Biblioteka umożliwia wybranie [działań](#use-activities) , które mają zostać dodane do elementu Runbook. Należy je dodać do kanwy, gdzie można połączyć je z innymi działaniami. Kontrolka biblioteki zawiera sekcje zdefiniowane w poniższej tabeli.

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Wszystkie polecenia cmdlet, które mogą być używane w elemencie Runbook. Polecenia cmdlet są zorganizowane według modułu. Dostępne są wszystkie moduły zainstalowane na koncie usługi Automation. |
| Elementy Runbook |Elementy Runbook na koncie usługi Automation. Te elementy Runbook można dodać do kanwy, aby można było ich używać jako podrzędnych elementów Runbook. Wyświetlane są tylko elementy Runbook tego samego typu podstawowego co edytowany element Runbook. W przypadku graficznych elementów Runbook są wyświetlane tylko elementy Runbook oparte na programie PowerShell. Dla graficznych elementów Runbook przepływu pracy programu PowerShell są wyświetlane tylko elementy Runbook oparte na przepływie pracy programu PowerShell. |
| Elementy zawartości |[Zasoby usługi Automation](/previous-versions/azure/dn939988(v=azure.100)) na koncie usługi Automation, których można używać w elemencie Runbook. Dodanie elementu zawartości do elementu Runbook powoduje dodanie działania przepływu pracy, które pobiera wybrany zasób. W przypadku zmiennych zasobów można wybrać, czy dodać działanie, aby uzyskać zmienną, czy ustawić zmienną. |
| Sterowanie elementem runbook |Działania kontroli, które mogą być używane w bieżącym elemencie Runbook. Działanie rozgałęzienia przyjmuje wiele danych wejściowych i oczekuje na ukończenie wszystkich operacji przed kontynuowaniem przepływu pracy. Działanie Code uruchamia co najmniej jeden wiersz programu PowerShell lub kodu przepływu pracy programu PowerShell, w zależności od typu graficznego elementu Runbook. To działanie służy do niestandardowego kodu lub dla funkcjonalności, która jest trudna do osiągnięcia z innymi działaniami. |

### <a name="configuration-control"></a>Kontrola konfiguracji

Formant konfiguracji umożliwia podanie szczegółowych informacji o obiekcie wybranym na kanwie. Właściwości dostępne w tym formancie zależą od typu wybranego obiektu. Po wybraniu opcji w kontrolce konfiguracja zostanie otwarta dodatkowa Kaseta, aby uzyskać więcej informacji.

### <a name="test-control"></a>Kontrola testu

Kontrolka testu nie jest wyświetlana, gdy edytor graficzny jest uruchamiany po raz pierwszy. Jest ona otwierana w przypadku interaktywnego testowania graficznego elementu Runbook.

## <a name="use-activities"></a>Korzystanie z działań

Działania są blokami konstrukcyjnymi elementu Runbook. Działaniem może być polecenie cmdlet programu PowerShell, podrzędny element Runbook lub przepływ pracy. Możesz dodać działanie do elementu Runbook, klikając go prawym przyciskiem myszy w kontrolce biblioteka i wybierając pozycję **Dodaj do kanwy**. Następnie możesz kliknąć i przeciągnąć działanie, aby umieścić je w dowolnym miejscu na kanwie. Lokalizacja działania na kanwie nie ma wpływu na działanie elementu Runbook. Można określić układ elementu Runbook w dowolny sposób, który jest najbardziej odpowiedni do wizualizacji swojej operacji.

![Dodaj do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Wybierz działanie na kanwie, aby skonfigurować jego właściwości i parametry w bloku konfiguracja. Można zmienić etykietę działania na nazwę, która znajduje się w opisie. Element Runbook nadal uruchamia oryginalne polecenie cmdlet. Wystarczy zmienić nazwę wyświetlaną używaną przez edytor graficzny. Należy zauważyć, że etykieta musi być unikatowa w elemencie Runbook.

### <a name="parameter-sets"></a>Zestawy parametrów

Zestaw parametrów definiuje obowiązkowe i opcjonalne parametry, które akceptują wartości dla konkretnego polecenia cmdlet. Wszystkie polecenia cmdlet mają co najmniej jeden zestaw parametrów, a niektóre z nich mają kilka zestawów. Jeśli polecenie cmdlet ma wiele zestawów parametrów, należy wybrać tę, która ma zostać użyta, zanim będzie można skonfigurować parametry. Zestaw parametrów używany przez działanie można zmienić, wybierając pozycję **zestaw parametrów** i wybierając inny zestaw. W takim przypadku wszystkie wartości parametrów, które zostały już skonfigurowane, zostaną utracone.

W poniższym przykładzie polecenie cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) ma trzy zestawy parametrów. W przykładzie zastosowano jeden zestaw o nazwie **ListVirtualMachineInResourceGroupParamSet** z jednym opcjonalnym parametrem w celu zwrócenia wszystkich maszyn wirtualnych w grupie zasobów. W przykładzie jest również wykorzystywany zestaw parametrów **GetVirtualMachineInResourceGroupParamSet** do określania maszyny wirtualnej do zwrócenia. Ten zestaw ma dwa obowiązkowe parametry i jeden opcjonalny parametr.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametrów

Po określeniu wartości parametru, należy wybrać źródło danych w celu określenia sposobu określania wartości. Źródła danych, które są dostępne dla określonego parametru, zależą od prawidłowych wartości dla tego parametru. Na przykład wartość null nie jest dostępna dla parametru, który nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Stała wartość |Wpisz wartość parametru. To źródło danych jest dostępne tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, Switch. |
| Dane wyjściowe działania |Użyj danych wyjściowych z działania poprzedzającego bieżące działanie w przepływie pracy. Wszystkie prawidłowe działania są wymienione na liście. Dla wartości parametru należy użyć tylko działania, które generuje dane wyjściowe. Jeśli działanie wyprowadza obiekt z wieloma właściwościami, można wpisać nazwę konkretnej właściwości po zaznaczeniu działania. |
| Dane wejściowe elementu Runbook |Wybierz dane wejściowe elementu Runbook jako dane wejściowe dla parametru działania. |
| Zasób zmiennej |Wybierz zmienną automatyzacji jako dane wejściowe. |
| Zasób poświadczenia |Wybierz poświadczenia automatyzacji jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat automatyzacji jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie automatyzacji jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ proste [wyrażenie programu PowerShell](#work-with-powershell-expressions). Wyrażenie jest oceniane przed działaniem, a wynik jest używany dla wartości parametru. Możesz użyć zmiennych, aby odwołać się do danych wyjściowych działania lub parametru wejściowego elementu Runbook. |
| Nieskonfigurowane |Wyczyść wszystkie wcześniej skonfigurowane wartości. |

#### <a name="optional-additional-parameters"></a>Opcjonalne dodatkowe parametry

Wszystkie polecenia cmdlet mają opcję dostarczania dodatkowych parametrów. Są to typowe parametry programu PowerShell lub inne parametry niestandardowe. Edytor graficzny przedstawia pole tekstowe, w którym można podać parametry za pomocą składni programu PowerShell. Na przykład, aby użyć `Verbose` wspólnego parametru, należy określić `-Verbose:$True` .

### <a name="retry-activity"></a>Działanie ponawiania

Funkcja ponawiania prób dla działania umożliwia jego uruchomienie wiele razy do czasu spełnienia określonego warunku, podobnie jak pętla. Tej funkcji można użyć w przypadku działań, które powinny być uruchamiane wiele razy, są podatne na błędy, może być konieczne więcej niż jedna próba sukcesu lub Przetestuj informacje wyjściowe działania dotyczące prawidłowych danych.

Po włączeniu opcji ponów dla działania można ustawić opóźnienie i warunek. Opóźnienie to czas (w sekundach lub minutach), przez jaki element Runbook czeka przed ponownym uruchomieniem działania. Jeśli nie określisz opóźnienia, działanie zostanie uruchomione ponownie natychmiast po jego zakończeniu.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Zrzut ekranu przedstawiający ustawienia funkcji Włącz ponowną próbę.":::

Warunek ponawiania jest wyrażeniem programu PowerShell, które jest oceniane po każdym uruchomieniu działania. Jeśli wyrażenie jest rozpoznawane jako true, działanie zostanie uruchomione ponownie. Jeśli wyrażenie ma wartość false, działanie nie zostanie uruchomione ponownie, a element Runbook przejdzie do następnego działania.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Zrzut ekranu przedstawiający ponowienie próby, dopóki ten warunek jest prawdziwy pola i Przykłady wyrażeń programu PowerShell, których można użyć w warunku ponawiania.":::

Warunek ponawiania może używać zmiennej o nazwie `RetryData` , która zapewnia dostęp do informacji o ponownych próbach działania. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| `NumberOfAttempts` |Liczba uruchomień działania. |
| `Output` |Dane wyjściowe z ostatniego uruchomienia działania. |
| `TotalDuration` |Upłynęło czasu, gdy działanie zostało uruchomione po raz pierwszy. |
| `StartedAt` |Czas (w formacie UTC), gdy działanie zostało po raz pierwszy uruchomione. |

Poniżej przedstawiono przykłady warunków ponowienia działania.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Po skonfigurowaniu warunku ponawiania dla działania działanie obejmuje dwie podpowiedzi wizualne do przypominania. Jeden z nich jest prezentowany w działaniu, a drugi jest wyświetlany podczas przeglądania konfiguracji działania.

![Wskaźniki wizualizacji ponowienia działania](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontrolka skryptu przepływu pracy

Kontrolka skryptu przepływu pracy to specjalne działanie, które akceptuje skrypt środowiska PowerShell lub przepływu pracy programu PowerShell, w zależności od typu graficznego elementu Runbook, który jest tworzony. Ta kontrolka udostępnia funkcje, które mogą nie być dostępne w inny sposób. Nie może akceptować parametrów, ale może używać zmiennych dla danych wyjściowych działań i parametrów wejściowych elementu Runbook. Wszystkie dane wyjściowe działania są dodawane do magistrali danych. Wyjątek jest wyprowadzany bez łącza wychodzącego, w tym przypadku dane wyjściowe są dodawane do danych wyjściowych elementu Runbook.

Na przykład poniższy kod wykonuje obliczenia dat przy użyciu zmiennej wejściowej elementu Runbook o nazwie `NumberOfDays` . Następnie wysyła ona obliczoną wartość DateTime jako dane wyjściowe, która będzie używana przez kolejne działania w elemencie Runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Używanie linków dla przepływu pracy

Link w graficznym elemencie Runbook łączy dwa działania. Jest wyświetlana na kanwie jako strzałka wskazująca aktywność źródłową do działania docelowego. Działania są uruchamiane w kierunku strzałki z działaniem docelowym rozpoczynającym się po zakończeniu działania źródłowego.

### <a name="create-a-link"></a>Utwórz łącze

Można utworzyć łącze między dwoma działaniami, zaznaczając aktywność źródłową i klikając okrąg u dołu kształtu. Przeciągnij strzałkę do działania docelowego i zwolnij.

![Utwórz łącze](media/automation-graphical-authoring-intro/create-link-options.png)

Wybierz link, aby skonfigurować jego właściwości w bloku konfiguracja. Właściwości obejmują typ łącza, który jest opisany w poniższej tabeli.

| Typ łącza | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane jeden raz dla każdego obiektu wyjściowego z działania źródłowego. Działanie docelowe nie działa, jeśli działanie źródłowe nie powoduje żadnych danych wyjściowych. Dane wyjściowe z działania źródłowego są dostępne jako obiekt. |
| Sequence |Działanie docelowe jest uruchamiane tylko raz, gdy odbierze dane wyjściowe z działania źródłowego. Dane wyjściowe działania źródłowego są dostępne jako tablica obiektów. |

### <a name="start-runbook-activity"></a>Uruchom działanie elementu Runbook

Graficzny element Runbook rozpoczyna się od wszystkich działań, które nie mają linku przychodzącego. Istnieje często tylko jedno działanie, które działa jako działanie uruchamiania dla elementu Runbook. Jeśli wiele działań nie ma linku przychodzącego, element Runbook uruchamia się równolegle. Poniższe linki umożliwiają uruchomienie innych działań w miarę ich ukończenia.

### <a name="specify-link-conditions"></a>Określ warunki łącza

Gdy określisz warunek dla linku, działanie docelowe jest uruchamiane tylko wtedy, gdy warunek zostanie spełniony. Zwykle `ActivityOutput` do pobrania danych wyjściowych z działania źródłowego jest używana zmienna w stanie.

Dla łącza potoku należy określić warunek dla pojedynczego obiektu. Element Runbook oblicza warunek dla każdego obiektu wyjściowego przez działanie źródłowe. Następnie uruchamia działanie docelowe dla każdego obiektu, który spełnia warunek. Na przykład w przypadku działania źródłowego programu `Get-AzVM` można użyć następującej składni dla linku potoku warunkowego, aby pobrać tylko maszyny wirtualne w grupie zasobów o nazwie grupa1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

W przypadku linku sekwencji element Runbook oblicza tylko warunek raz, ponieważ zwracana jest pojedyncza tablica zawierająca wszystkie obiekty z działania źródłowego. W związku z tym element Runbook nie może użyć linku sekwencji do filtrowania, tak jak w przypadku linku potoku. Link sekwencji może po prostu określić, czy następne działanie jest uruchamiane.

Na przykład wykonaj następujące czynności w naszym elemencie Runbook **startowej maszyny wirtualnej** :

![Link warunkowy z sekwencjami](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Element Runbook używa trzech różnych linków sekwencji, które weryfikują wartości parametrów wejściowych `VMName` i `ResourceGroupName` określają odpowiednią akcję do wykonania. Możliwe akcje to uruchamianie pojedynczej maszyny wirtualnej, uruchamianie wszystkich maszyn wirtualnych w grupie zasobów lub uruchamianie wszystkich maszyn wirtualnych w ramach subskrypcji. W przypadku linku sekwencji między `Connect to Azure` i `Get single VM` , poniżej znajduje się logika warunku:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Korzystając z linku warunkowego, dane dostępne z działania źródłowego do innych działań w tej gałęzi są filtrowane według warunku. Jeśli działanie jest źródłem dla wielu linków, dane dostępne dla działań w poszczególnych gałęziach są zależne od warunku linku łączącego się z tą gałęzią.

Na przykład `Start-AzVM` działanie w elemencie Runbook poniżej uruchamia wszystkie maszyny wirtualne. Ma dwie linki warunkowe. Pierwsze łącze warunkowe używa wyrażenia, `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` Aby przefiltrować, czy `Start-AzVM` działanie zostało zakończone pomyślnie. Drugie łącze warunkowe używa wyrażenia, `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` Aby odfiltrować, czy `Start-AzVm` działanie nie może uruchomić maszyny wirtualnej.

![Przykład łącza warunkowego](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Każde działanie, które następuje po pierwszym łączu i używa danych wyjściowych działania `Get-AzureVM` Pobiera tylko te maszyny wirtualne, które zostały uruchomione w momencie `Get-AzureVM` uruchomienia. Każde działanie, które następuje po drugim linku, pobiera tylko maszyny wirtualne, które zostały zatrzymane w chwili `Get-AzureVM` uruchomienia. Każda aktywność po trzecim łączu pobiera wszystkie maszyny wirtualne niezależnie od ich stanu uruchomienia.

### <a name="use-junctions"></a>Użyj połączeń

Skrzyżowanie to specjalne działanie, które czeka na zakończenie wszystkich rozgałęzień przychodzących. Dzięki temu elementowi Runbook można równolegle uruchamiać wiele działań i upewnić się, że wszystkie zadania zostały ukończone przed przejściem dalej.

Chociaż połączenie może mieć nieograniczoną liczbę linków przychodzących, tylko jedno z tych linków może być potokiem. Liczba linków sekwencji przychodzących nie jest ograniczona. Można utworzyć połączenie z wieloma przychodzącymi łączami potokowymi i zapisać element Runbook, ale nie będzie on działać, gdy zostanie uruchomiony.

Poniższy przykład jest częścią elementu Runbook, który uruchamia zestaw maszyn wirtualnych, jednocześnie pobierając poprawki do zastosowania do tych maszyn. Używa połączenia, aby upewnić się, że oba procesy są wykonywane przed kontynuowaniem elementu Runbook.

![Połączenie](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Pracuj z cyklami

Cykl jest tworzony, gdy działanie docelowe łączy się z powrotem z jego aktywnością źródłową lub innym działaniem, które ostatecznie łączy z powrotem ze źródłem. Tworzenie graficzne nie obsługuje obecnie cykli. Jeśli element Runbook ma cykl, jest on prawidłowo zapisywany, ale po jego uruchomieniu otrzymuje błąd.

![Cykliczny](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Udostępnianie danych między działaniami

Wszystkie dane wysyłane przez działanie za pomocą linku wychodzącego są zapisywane w magistrali danych dla elementu Runbook. Wszystkie działania w elemencie Runbook mogą używać danych z magistrali danych do wypełniania wartości parametrów lub dołączać do kodu skryptu. Działanie może uzyskać dostęp do danych wyjściowych poprzedniego działania w przepływie pracy.

Sposób zapisywania danych w magistrali danych zależy od typu łącza w działaniu. W przypadku linku potoku dane są wyprowadzane jako wiele obiektów. W przypadku linku sekwencji dane są wyprowadzane jako tablica. Jeśli istnieje tylko jedna wartość, jest ona wyjściowa jako tablica jednoelementowa.

Element Runbook ma dwa sposoby dostępu do danych w magistrali danych: 
* Użyj wyjściowego źródła danych działania.
* Użyj źródła danych wyrażenia programu PowerShell.

Pierwszy mechanizm używa źródła danych wyjściowych działania do wypełnienia parametru innego działania. Jeśli dane wyjściowe są obiektem, element Runbook może określić jedną właściwość.

![dane wyjściowe działania](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Drugi mechanizm dostępu do danych pobiera dane wyjściowe działania w źródle danych wyrażenia programu PowerShell lub działanie skryptu przepływu pracy ze `ActivityOutput` zmienną przy użyciu składni pokazanej poniżej. Jeśli dane wyjściowe są obiektem, element Runbook może określić jedną właściwość.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Używanie punktów kontrolnych

[Punkty kontrolne](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) można ustawić w graficznym elemencie Runbook przepływu pracy programu PowerShell, wybierając **element Runbook Checkpoint** w dowolnym działaniu. Powoduje to, że punkt kontrolny zostanie ustawiony po uruchomieniu działania.

![Punkt kontrolny](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są włączone tylko w graficznych elementach Runbook przepływu pracy programu PowerShell i nie są dostępne w graficznych elementach Runbook. Jeśli element Runbook używa poleceń cmdlet platformy Azure, powinien postępować zgodnie z działaniami kontrolnymi z `Connect-AzAccount` działaniem. Operacja Connect jest używana na wypadek wstrzymania elementu Runbook i musi zostać ponownie uruchomiona z tego punktu kontrolnego w innym procesie roboczym.

## <a name="handle-runbook-input"></a>Obsługa danych wejściowych elementu Runbook

Element Runbook wymaga wprowadzenia danych przez użytkownika, który uruchamia element Runbook za pośrednictwem Azure Portal lub z innego elementu Runbook, jeśli jest on używany jako element podrzędny. Na przykład dla elementu Runbook, który tworzy maszynę wirtualną, może być konieczne podanie takich informacji jako nazwy maszyny wirtualnej i innych właściwości za każdym razem, gdy element Runbook zostanie uruchomiony.

Element Runbook akceptuje dane wejściowe przez zdefiniowanie co najmniej jednego parametru wejściowego. Użytkownik dostarcza wartości tych parametrów za każdym razem, gdy element Runbook zostanie uruchomiony. Gdy użytkownik uruchamia element Runbook przy użyciu Azure Portal, użytkownik jest monitowany o podanie wartości dla każdego parametru wejściowego obsługiwanego przez element Runbook.

Podczas tworzenia elementu Runbook możesz uzyskać dostęp do jego parametrów wejściowych, klikając pozycję **dane wejściowe i wyjściowe** na pasku narzędzi elementu Runbook. Spowoduje to otwarcie kontrolki dane wejściowe i wyjściowe, w której można edytować istniejący parametr wejściowy lub utworzyć nowy, klikając przycisk **Dodaj dane wejściowe**.

![Dodawanie danych wejściowych](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest definiowany przez właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Nazwa | Wymagane. Nazwa parametru. Nazwa musi być unikatowa w elemencie Runbook. Musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i znaki podkreślenia. Nazwa nie może zawierać spacji. |
| Opis |Opcjonalny. Opis celu dla parametru wejściowego. |
| Typ | Opcjonalny. Oczekiwano typu danych dla wartości parametru. Azure Portal zapewnia odpowiednią kontrolę dla typu danych dla każdego parametru podczas monitowania o dane wejściowe. Obsługiwane typy parametrów to String, Int32, Int64, decimal, Boolean, DateTime i Object. Jeśli typ danych nie jest zaznaczony, domyślnie jest to ciąg.|
| Obowiązkowy | Opcjonalny. Ustawienie określające, czy należy podać wartość dla parametru. W przypadku wybrania `yes` tej opcji należy podać wartość, gdy element Runbook zostanie uruchomiony. W przypadku wybrania `no` tej opcji wartość nie jest wymagana, gdy element Runbook jest uruchomiony, i można użyć wartości domyślnej. Nie można uruchomić elementu Runbook, jeśli nie podasz wartości dla każdego obowiązkowego parametru, który nie ma zdefiniowanej wartości domyślnej. |
| Wartość domyślna | Opcjonalny. Wartość użyta dla parametru, jeśli nie jest ona przenoszona podczas uruchamiania elementu Runbook. Aby ustawić wartość domyślną, wybierz opcję `Custom` . Wybierz `None` , jeśli nie chcesz podawać żadnej wartości domyślnej. |

## <a name="handle-runbook-output"></a>Obsługa danych wyjściowych elementu Runbook

Tworzenie graficzne zapisuje dane utworzone przez dowolne działanie, które nie ma linku wychodzącego do [danych wyjściowych elementu Runbook](./automation-runbook-output-and-messages.md). Dane wyjściowe są zapisywane wraz z zadaniem elementu Runbook i są dostępne dla nadrzędnego elementu Runbook, gdy element Runbook jest używany jako element podrzędny.

## <a name="work-with-powershell-expressions"></a>Współpraca z wyrażeniami programu PowerShell

Jedną z zalet tworzenia grafiki jest możliwość tworzenia elementu Runbook z minimalną wiedzą o programie PowerShell. Obecnie trzeba znać bit programu PowerShell służący do wypełniania pewnych [wartości parametrów](#use-activities) oraz ustawiania [warunków łączy](#use-links-for-workflow). Ta sekcja zawiera krótkie wprowadzenie do wyrażeń programu PowerShell. Pełne szczegóły programu PowerShell są dostępne w [skryptach przy użyciu programu Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Używanie wyrażenia programu PowerShell jako źródła danych

Możesz użyć wyrażenia programu PowerShell jako źródła danych, aby wypełnić wartość [parametru działania](#use-activities) wynikami kodu programu PowerShell. Wyrażenie może być pojedynczym wierszem kodu, który wykonuje prostą funkcję lub wiele wierszy, które wykonują pewne skomplikowane logiki. Wszystkie dane wyjściowe polecenia, które nie są przypisane do zmiennej, są wyprowadzane do wartości parametru.

Na przykład następujące polecenie wyprowadza bieżącą datę.

```powershell-interactive
Get-Date
```

Następny fragment kodu kompiluje ciąg z bieżącej daty i przypisuje go do zmiennej. Kod wysyła zawartość zmiennej do danych wyjściowych.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Następujące polecenia szacują bieżącą datę i zwracają ciąg wskazujący, czy bieżący dzień to weekend czy dzień tygodnia.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Użyj danych wyjściowych działania

Aby użyć danych wyjściowych z poprzedniego działania w elemencie Runbook, użyj `ActivityOutput` zmiennej z poniższą składnią.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Można na przykład mieć działanie z właściwością wymagającą nazwy maszyny wirtualnej. W takim przypadku element Runbook może użyć następującego wyrażenia.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Jeśli właściwość wymaga obiektu maszyny wirtualnej, a nie tylko nazwy, element Runbook zwróci cały obiekt przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Element Runbook może używać danych wyjściowych działania w bardziej złożonym wyrażeniu, takim jak poniższe. To wyrażenie łączy tekst z nazwą maszyny wirtualnej.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Porównaj wartości

[Operatory porównania](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) służą do porównywania wartości lub określania, czy wartość jest zgodna z określonym wzorcem. Porównanie zwraca wartość true lub false.

Na przykład, poniższy warunek określa, czy maszyna wirtualna z działania o nazwie `Get-AzureVM` jest obecnie zatrzymana.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Poniższy warunek określa, czy ta sama maszyna wirtualna ma stan inny niż zatrzymany.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

W elemencie Runbook można sprzęgać wiele warunków przy użyciu [operatora logicznego](/powershell/module/microsoft.powershell.core/about/about_logical_operators), takiego jak `-and` lub `-or` . Na przykład poniższe warunki sprawdzają, czy maszyna wirtualna w poprzednim przykładzie jest w stanie zatrzymania lub zatrzymywania.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Używanie tablic skrótów

[Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) są parami nazw-wartości, które są przydatne do zwracania zestawu wartości. Zobaczysz również tablicę skrótów, która jest nazywana słownikiem. Właściwości niektórych działań oczekują elementu Hashtable zamiast prostej wartości.

Utwórz tablicę skrótów przy użyciu następującej składni. Może zawierać dowolną liczbę wpisów, ale każda z nich jest definiowana przez nazwę i wartość.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Na przykład następujące wyrażenie tworzy tablicę skrótów, która ma być używana jako źródło danych dla parametru działania, który oczekuje na tablicę wartości dla wyszukiwania internetowego.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Poniższy przykład używa danych wyjściowych z działania o nazwie, `Get Twitter Connection` Aby wypełnić tablicę skrótów.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Uwierzytelnianie w zasobach platformy Azure

Elementy Runbook w Azure Automation zarządzające zasobami platformy Azure wymagają uwierzytelniania na platformie Azure. [Konto Uruchom jako](./automation-security-overview.md), nazywane również jednostką usługi, jest domyślnym mechanizmem używanym przez element Runbook automatyzacji do uzyskiwania dostępu do Azure Resource Manager zasobów w ramach subskrypcji. Tę funkcję można dodać do graficznego elementu Runbook poprzez dodanie `AzureRunAsConnection` zasobu połączenia, który używa polecenia cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) programu PowerShell na kanwie. Możesz również dodać polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Ten scenariusz przedstawiono w poniższym przykładzie.

![Uruchom jako działania uwierzytelniania](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection`Działanie lub `Get-AutomationConnection` , jest skonfigurowane ze źródłem danych o stałej wartości o nazwie `AzureRunAsConnection` .

![Konfiguracja połączenia Uruchom jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Następne działanie, `Connect-AzAccount` dodaje uwierzytelnione konto Uruchom jako do użycia w elemencie Runbook.

![Zestaw parametrów Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy zauważyć, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może korzystać tylko z `Connect-AzAccount` siebie.

Dla **pól parametrów**, **CERTIFICATETHUMBPRINT** i **TENANTID**, określ nazwę właściwości dla ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami. W przeciwnym razie, gdy element Runbook zostanie wykonany, kończy się niepowodzeniem podczas próby uwierzytelnienia. Jest to wymaganie co najmniej minimalne uwierzytelnianie elementu Runbook za pomocą konta Uruchom jako.

Niektórzy subskrybenci tworzą konto usługi Automation przy użyciu [konta użytkownika usługi Azure AD](./shared-resources/credentials.md) do zarządzania klasycznym wdrożeniem platformy Azure lub zasobami Azure Resource Manager. Aby zachować zgodność z poprzednimi wersjami dla tych subskrybentów, mechanizm uwierzytelniania do użycia w elemencie Runbook jest `Add-AzureAccount` poleceniem cmdlet z [zasobem poświadczenia](./shared-resources/credentials.md). Element zawartości reprezentuje Active Directory użytkownika z dostępem do konta platformy Azure.

Tę funkcję można włączyć dla graficznego elementu Runbook, dodając zasób poświadczenia do kanwy, a następnie `Add-AzureAccount` działanie, które korzysta z zasobu poświadczenia dla danych wejściowych. Zobacz poniższy przykład.

![Działania uwierzytelniania](media/automation-graphical-authoring-intro/authentication-activities.png)

Element Runbook musi być uwierzytelniany na początku i po każdym punkcie kontrolnym. W tym celu należy użyć `Add-AzureAccount` działania po dowolnych `Checkpoint-Workflow` działaniach. Nie trzeba używać dodatkowego działania związanego z poświadczeniem.

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Eksportowanie graficznego elementu Runbook

Można wyeksportować tylko opublikowaną wersję graficznego elementu Runbook. Jeśli element Runbook nie został jeszcze opublikowany, przycisk **Eksportuj** jest wyłączony. Po kliknięciu przycisku **Eksportuj** element Runbook zostanie pobrany na komputer lokalny. Nazwa pliku jest zgodna z nazwą elementu Runbook z rozszerzeniem **. graphrunbook** .

## <a name="import-a-graphical-runbook"></a>Importowanie graficznego elementu Runbook

Możesz zaimportować graficzny lub graficzny plik Runbook przepływu pracy programu PowerShell, wybierając opcję **importowania** podczas dodawania elementu Runbook. Po wybraniu pliku do zaimportowania można zachować tę samą nazwę lub podać nową. W polu **Typ elementu Runbook** jest wyświetlany typ elementu Runbook po dokonaniu oceny wybranego pliku. Jeśli spróbujesz wybrać inny typ, który nie jest poprawny, w edytorze graficznym zostanie wyświetlony komunikat informujący, że istnieją potencjalne konflikty, a podczas konwersji mogą wystąpić błędy składniowe.

![Importuj element Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testowanie graficznego elementu Runbook

Każdy graficzny element Runbook w Azure Automation ma wersję roboczą i opublikowaną wersję. Można uruchomić tylko opublikowaną wersję, podczas gdy można edytować wersję roboczą. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowa do użycia, publikuje ją, która zastępuje bieżącą opublikowaną wersję wersją roboczą.

Możesz przetestować wersję roboczą elementu Runbook w Azure Portal, pozostawiając opublikowaną wersję bez zmian. Alternatywnie można przetestować nowy element Runbook przed jego opublikowaniem, aby można było sprawdzić, czy element Runbook działa prawidłowo przed wszelkimi zamiennikami wersji. Testowanie elementu Runbook powoduje wykonanie wersji roboczej i gwarantuje, że wszystkie wykonywane działania zostały ukończone. Nie zostanie utworzona historia zadań, ale w okienku danych wyjściowych testu są wyświetlane dane wyjściowe.

Otwórz kontrolkę test dla graficznego elementu Runbook, otwierając element Runbook do edycji, a następnie klikając pozycję **okienko testowania**. Kontrolka testu monituje o parametry wejściowe i można uruchomić element Runbook, klikając przycisk **Uruchom**.

## <a name="publish-a-graphical-runbook"></a>Publikowanie graficznego elementu Runbook

Opublikuj graficzny element Runbook, otwierając element Runbook do edycji, a następnie klikając pozycję **Publikuj**. Możliwe stany elementu Runbook to:

* Nowy — element Runbook nie został jeszcze opublikowany. 
* Opublikowano — element Runbook został opublikowany.
* W obszarze Edycja — element Runbook został edytowany po opublikowaniu, a wersja robocza i opublikowana są różne.

![Stany elementów Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Istnieje możliwość przywrócenia opublikowanej wersji elementu Runbook. Ta operacja powoduje odrzucanie wszelkich zmian wprowadzonych od czasu ostatniej publikacji elementu Runbook. Zastępuje wersję roboczą elementu Runbook wersją opublikowaną.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Samouczek: Tworzenie graficznego elementu Runbook](learn/automation-tutorial-runbook-graphical.md).
* Aby dowiedzieć się więcej o typach elementów Runbook i ich zaletach i ograniczeniach, zobacz [Azure Automation typów elementów Runbook](automation-runbook-types.md).
* Aby zrozumieć sposób uwierzytelniania przy użyciu konta Uruchom jako usługi Automation, zobacz [konto Uruchom jako](automation-security-overview.md#run-as-account).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/#automation).
