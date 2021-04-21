---
title: Tworzenie graficznych elementów Runbook w Azure Automation
description: W tym artykule opisano sposób tworzenia graficznego elementów Runbook bez pracy z kodem.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88492d914b710c7a738dd6d7f501e22d490065b6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833818"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Tworzenie graficznych elementów Runbook w Azure Automation

Wszystkie runbook w Azure Automation są Windows PowerShell przepływami pracy. Graficzne elementy Runbook i graficzne elementy Runbook przepływu pracy programu PowerShell generują kod programu PowerShell uruchamiany przez procesy robocze automatyzacji, ale nie można go wyświetlać ani modyfikować. Graficzny element Runbook można przekonwertować na graficzny element Runbook przepływu pracy programu PowerShell i na odwrót. Nie można jednak przekonwertować tych runbook na tekstowy. Ponadto edytor graficzny usługi Automation nie może zaimportować tekstowego runbook.

Tworzenie graficzne umożliwia tworzenie elementów Runbook dla Azure Automation bez skomplikowanych elementów podstawowych Windows PowerShell kodu przepływu pracy programu PowerShell. Działania można dodawać do kanwy z biblioteki cmdlet i runbook, łączyć je ze sobą i konfigurować tak, aby tworzyły przepływ pracy. Jeśli kiedykolwiek wcześniej pracowaliśmy z System Center Orchestrator lub Service Management Automation (SMA), tworzenie graficzne powinno wyglądać znajomo. Ten artykuł zawiera wprowadzenie do pojęć potrzebnych do rozpoczęcia tworzenia graficznego elementów Runbook.

## <a name="overview-of-graphical-editor"></a>Omówienie edytora graficznego

Edytor graficzny można otworzyć w Azure Portal, tworząc lub edytując graficzny element Runbook.

![Graficzny obszar roboczy](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano kontrolki w edytorze graficznym.

### <a name="canvas-control"></a>Kontrolka kanwy

Kontrolka Kanwa umożliwia projektowanie runbook. Możesz dodać działania z węzłów w kontrolce Biblioteka do runbook i połączyć je za pomocą linków w celu zdefiniowania logiki runbook. W dolnej części kanwy znajdują się kontrolki, które umożliwiają powiększanie i pomniejszanie.

### <a name="library-control"></a>Kontrolka biblioteki

Kontrolka Biblioteka umożliwia wybieranie działań [do](#use-activities) dodania do runbook. Można je dodać do kanwy, gdzie można połączyć je z innymi działaniami. Kontrolka Biblioteka zawiera sekcje zdefiniowane w poniższej tabeli.

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Wszystkie polecenia cmdlet, które mogą być używane w twoim runbook. Polecenia cmdlet są zorganizowane według modułu. Dostępne są wszystkie moduły zainstalowane na koncie usługi Automation. |
| Elementy Runbook |Runbook na twoim koncie usługi Automation. Te podręczniki Runbook można dodać do kanwy, aby były używane jako podrzędne. Wyświetlane są tylko te same podstawowe typy, co edytowany element Runbook. W przypadku graficznych elementów Runbook wyświetlane są tylko elementy Runbook oparte na programie PowerShell. W przypadku graficznych elementów Runbook przepływu pracy programu PowerShell wyświetlane są tylko elementy Runbook oparte na przepływie pracy programu PowerShell. |
| Elementy zawartości |Zasoby [automatyzacji](/previous-versions/azure/dn939988(v=azure.100)) na koncie usługi Automation, których można używać w swoim runbooku. Dodanie zasobu do element runbook powoduje dodanie działania przepływu pracy, które pobiera wybrany element zawartości. W przypadku zasobów zmiennych możesz wybrać, czy dodać działanie, aby pobrać zmienną, czy ustawić zmienną. |
| Sterowanie elementem runbook |Kontroluj działania, które mogą być używane w bieżącym podręczniku Runbook. Działanie Połączenie przyjmuje wiele danych wejściowych i czeka na ukończenie wszystkich operacji przed kontynuowaniem przepływu pracy. Działanie Code uruchamia co najmniej jeden wiersze kodu programu PowerShell lub przepływu pracy programu PowerShell, w zależności od typu graficznego elementów Runbook. Tego działania można użyć dla kodu niestandardowego lub funkcji, które są trudne do osiągnięcia w przypadku innych działań. |

### <a name="configuration-control"></a>Kontrola konfiguracji

Kontrolka Konfiguracja umożliwia podanie szczegółów dla obiektu wybranego na kanwie. Właściwości dostępne w tej kontrolce zależą od wybranego typu obiektu. Wybranie opcji w kontrolce Konfiguracja powoduje otwarcie dodatkowych list z dodatkowymi informacjami.

### <a name="test-control"></a>Kontrolka testu

Kontrolka Test nie jest wyświetlana podczas pierwszego rozpoczęcia edytora graficznego. Jest on otwierany podczas interaktywnego testowania graficznego elementów Runbook.

## <a name="use-activities"></a>Korzystanie z działań

Działania to bloki konstrukcyjne elementów Runbook. Działaniem może być polecenie cmdlet programu PowerShell, podrzędny element Runbook lub przepływ pracy. Działanie można dodać do tego element runbook, klikając je prawym przyciskiem myszy w kontrolce Biblioteka i wybierając **pozycję Dodaj do kanwy.** Następnie możesz kliknąć i przeciągnąć działanie, aby umieścić je w dowolnym miejscu na kanwie, która Ci się podoba. Lokalizacja działania na kanwie nie ma wpływu na działanie runbook. Możesz rozłożyć swój runbook w dowolny sposób, który najlepiej nadaje się do wizualizacji jego działania.

![Dodawanie do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Wybierz działanie na kanwie, aby skonfigurować jego właściwości i parametry w bloku Konfiguracja. Etykietę działania można zmienić na nazwę opisową. W tym programie Runbook nadal jest uruchamiane oryginalne polecenie cmdlet. Po prostu zmieniasz nazwę wyświetlaną używaną przez edytor graficzny. Należy pamiętać, że etykieta musi być unikatowa w obrębie tego runbook.

### <a name="parameter-sets"></a>Zestawy parametrów

Zestaw parametrów definiuje obowiązkowe i opcjonalne parametry, które akceptują wartości dla określonego polecenia cmdlet. Wszystkie polecenia cmdlet mają co najmniej jeden zestaw parametrów, a niektóre mają kilka zestawów. Jeśli polecenie cmdlet ma wiele zestawów parametrów, należy wybrać ten, który ma być do użycia, zanim będzie można skonfigurować parametry. Zestaw parametrów używany przez działanie można zmienić, wybierając pozycję **Zestaw parametrów** i wybierając inny zestaw. W takim przypadku wszystkie wartości parametrów, które zostały już skonfigurowane, zostaną utracone.

W poniższym przykładzie polecenie cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) ma trzy zestawy parametrów. W przykładzie użyto jednego zestawu o nazwie **ListVirtualMachineInResourceGroupParamSet** z pojedynczym opcjonalnym parametrem do zwracania wszystkich maszyn wirtualnych w grupie zasobów. W przykładzie użyto również zestawu parametrów **GetVirtualMachineInResourceGroupParamSet** w celu określenia maszyny wirtualnej do zwrócenia. Ten zestaw ma dwa obowiązkowe parametry i jeden parametr opcjonalny.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametrów

Podczas określania wartości parametru należy wybrać źródło danych, aby określić sposób określania wartości. Źródła danych dostępne dla określonego parametru zależą od prawidłowych wartości tego parametru. Na przykład wartość Null nie jest dostępną opcją dla parametru, który nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Wartość stała |Wpisz wartość parametru . To źródło danych jest dostępne tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, Switch. |
| Dane wyjściowe działania |Użyj danych wyjściowych działania poprzedzającego bieżące działanie w przepływie pracy. Zostaną wyświetlone wszystkie prawidłowe działania. Dla wartości parametru użyj tylko działania, które generuje dane wyjściowe. Jeśli działanie wyprowadza obiekt z wieloma właściwościami, możesz wpisać nazwę określonej właściwości po wybraniu działania. |
| Dane wejściowe runbook |Wybierz dane wejściowe runbook jako dane wejściowe dla parametru działania. |
| Zasób zmiennej |Wybierz zmienną automatyzacji jako dane wejściowe. |
| Zasób poświadczeń |Wybierz poświadczenie usługi Automation jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat automatyzacji jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie usługi Automation jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ proste wyrażenie [programu PowerShell.](#work-with-powershell-expressions) Wyrażenie jest obliczane przed działaniem, a wynik jest używany dla wartości parametru. Zmienne mogą odwoływać się do danych wyjściowych działania lub parametru wejściowego runbook. |
| Nieskonfigurowane |Wyczyść wszystkie wartości, które zostały wcześniej skonfigurowane. |

#### <a name="optional-additional-parameters"></a>Opcjonalne parametry dodatkowe

Wszystkie polecenia cmdlet mają możliwość podania dodatkowych parametrów. Są to typowe parametry programu PowerShell lub inne parametry niestandardowe. Edytor graficzny zawiera pole tekstowe, w którym można podać parametry przy użyciu składni programu PowerShell. Aby na przykład użyć wspólnego `Verbose` parametru, należy określić parametr `-Verbose:$True` .

### <a name="retry-activity"></a>Działanie ponawiania próby

Funkcja ponawiania dla działania umożliwia jego wielokrotne uruchamianie do momentu, aż określony warunek zostanie spełniony, podobnie jak pętla. Tej funkcji można używać w przypadku działań, które powinny być uruchamiane wiele razy, są podatne na błędy, mogą wymagać więcej niż jednej próby powodzenia lub przetestować informacje wyjściowe działania pod uwagę prawidłowych danych.

Po włączeniu ponawiania próby dla działania można ustawić opóźnienie i warunek. Opóźnienie to czas (mierzony w sekundach lub minutach), który runbook czeka, zanim ponownie uruchomi działanie. Jeśli nie określisz opóźnienia, działanie zostanie ponownie uruchomiony natychmiast po jego zakończeniu.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Zrzut ekranu przedstawiający ustawienia włączania funkcji ponawiania prób.":::

Warunek ponawiania to wyrażenie programu PowerShell, które jest obliczane po każdym zakończeniu działania. Jeśli wyrażenie zostanie rozwiązane z wartością True, działanie zostanie ponownie uruchomiony. Jeśli wyrażenie zostanie rozwiązane z wartością False, działanie nie zostanie uruchomione ponownie i zostanie przeniesiony do następnego działania.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Zrzut ekranu przedstawiający pole Ponów próbę do momentu, aż ten warunek będzie mieć wartość true, oraz przykłady wyrażeń programu PowerShell, których można użyć w warunku ponawiania.":::

Warunek ponawiania może używać zmiennej o nazwie , która zapewnia dostęp do `RetryData` informacji o ponawianych próbach działania. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| `NumberOfAttempts` |Liczba razy, gdy działanie zostało uruchomione. |
| `Output` |Dane wyjściowe z ostatniego uruchomienia działania. |
| `TotalDuration` |Upłynął czas od pierwszego rozpoczęcia działania. |
| `StartedAt` |Godzina (w formacie UTC) pierwszego rozpoczęcia działania. |

Poniżej przedstawiono przykłady warunków ponawiania działania.

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

Po skonfigurowaniu warunku ponawiania dla działania działanie zawiera dwie wskazówki wizualne przypominające. Jedno z nich jest prezentowane w działaniu, a drugie jest wyświetlane podczas przeglądania konfiguracji działania.

![Wskaźniki wizualne ponawiania próby działania](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontrolka Skrypt przepływu pracy

Kontrolka skryptów przepływu pracy to specjalne działanie, które akceptuje skrypt przepływu pracy programu PowerShell lub programu PowerShell w zależności od typu graficznego element runbook, który jest tworzyny. Ta kontrolka zapewnia funkcje, które mogą być niedostępne w inny sposób. Nie może akceptować parametrów, ale może używać zmiennych dla danych wyjściowych działania i parametrów wejściowych runbook. Wszystkie dane wyjściowe działania są dodawane do magistrali danych. Wyjątkiem są dane wyjściowe bez połączenia wychodzącego. W takim przypadku dane wyjściowe są dodawane do danych wyjściowych tego runbook.

Na przykład poniższy kod wykonuje obliczenia daty przy użyciu zmiennej wejściowej runbook o nazwie `NumberOfDays` . Następnie wysyła obliczoną wartość DateTime jako dane wyjściowe do późniejszego działania w ramach runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Używanie linków dla przepływu pracy

Link w graficznym elementie Runbook łączy dwa działania. Jest ona wyświetlana na kanwie jako strzałka wskazująca działanie źródłowe na działanie docelowe. Działania są uruchamiane w kierunku strzałki, a działanie docelowe rozpoczyna się po zakończeniu działania źródłowego.

### <a name="create-a-link"></a>Tworzenie linku

Połączenie między dwoma działaniami można utworzyć, wybierając działanie źródłowe i klikając okrąg u dołu kształtu. Przeciągnij strzałkę do działania docelowego i wydania.

![Tworzenie linku](media/automation-graphical-authoring-intro/create-link-options.png)

Wybierz link, aby skonfigurować jego właściwości w bloku Konfiguracja. Właściwości obejmują typ linku, który został opisany w poniższej tabeli.

| Typ łącza | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane raz dla każdego obiektu wyjściowego działania źródłowego. Działanie docelowe nie jest uruchamiane, jeśli działanie źródłowe nie zawiera żadnych danych wyjściowych. Dane wyjściowe działania źródłowego są dostępne jako obiekt . |
| Sequence |Działanie docelowe jest uruchamiane tylko raz, gdy odbiera dane wyjściowe z działania źródłowego. Dane wyjściowe działania źródłowego są dostępne jako tablica obiektów. |

### <a name="start-runbook-activity"></a>Działanie uruchamiania runbook

Graficzny element Runbook rozpoczyna się od wszelkich działań, które nie mają linku przychodzącego. Często istnieje tylko jedno działanie, które działa jako działanie początkowe dla tego runbook. Jeśli wiele działań nie ma linku przychodzącego, element Runbook jest uruchamiany równolegle. Następuje on po linkach, aby uruchamiać inne działania po zakończeniu każdego z nich.

### <a name="specify-link-conditions"></a>Określanie warunków połączenia

Po określeniu warunku linku działanie docelowe jest uruchamiane tylko wtedy, gdy warunek jest rozpoznany jako Prawda. Zwykle używa się zmiennej `ActivityOutput` w warunku, aby pobrać dane wyjściowe z działania źródłowego.

W przypadku łącza potoku należy określić warunek dla pojedynczego obiektu. Ten runbook ocenia warunek dla każdego obiektu wyjściowego przez działanie źródłowe. Następnie uruchamia działanie docelowe dla każdego obiektu, który spełnia warunek. Na przykład w przypadku działania źródłowego , można użyć następującej składni dla linku potoku warunkowego, aby pobrać tylko maszyny wirtualne w grupie zasobów `Get-AzVM` o nazwie Grupa1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

W przypadku linku sekwencji runbook ocenia warunek tylko raz, ponieważ zwracana jest pojedyncza tablica zawierająca wszystkie obiekty z działania źródłowego. W związku z tym do filtrowania nie można użyć linku sekwencji, tak jak w przypadku linku potoku. Link sekwencji może po prostu określić, czy zostanie uruchomione następne działanie.

Na przykład skorzystaj z następującego zestawu działań w naszym **runbooku Uruchamiania maszyny wirtualnej:**

![Połączenie warunkowe z sekwencjami](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Ten typ runbook używa trzech różnych linków sekwencji, które weryfikują wartości parametrów wejściowych i określają `VMName` `ResourceGroupName` odpowiednią akcję do podjęcia. Możliwe akcje to uruchomienie pojedynczej maszyny wirtualnej, uruchomienie wszystkich maszyn wirtualnych w grupie zasobów lub uruchomienie wszystkich maszyn wirtualnych w subskrypcji. W przypadku połączenia sekwencji `Connect to Azure` między i poniżej znajduje się `Get single VM` logika warunku:

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

W przypadku użycia linku warunkowego dane dostępne z działania źródłowego do innych działań w tej gałęzi są filtrowane według warunku. Jeśli działanie jest źródłem wielu linków, dane dostępne dla działań w każdej gałęzi zależą od warunku w linku łączącym się z gałęzią.

Na przykład działanie `Start-AzVM` w poniższym runbook uruchamia wszystkie maszyny wirtualne. Ma dwa linki warunkowe. Pierwszy link warunkowy używa wyrażenia do `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` filtrowania, jeśli `Start-AzVM` działanie zakończy się pomyślnie. Drugi link warunkowy używa wyrażenia do filtrowania, `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` jeśli działanie nie może uruchomić maszyny `Start-AzVm` wirtualnej.

![Przykład linku warunkowego](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Każde działanie, które następuje po pierwszym linku i używa danych wyjściowych działania z , pobiera tylko maszyny wirtualne, które zostały uruchomione w czasie `Get-AzureVM` `Get-AzureVM` uruchamiania. Każde działanie, które następuje po drugim linku, pobiera tylko maszyny wirtualne, które zostały zatrzymane w czasie `Get-AzureVM` uruchamiania. Każde działanie następujące po trzecim linku pobiera wszystkie maszyny wirtualne niezależnie od ich stanu działania.

### <a name="use-junctions"></a>Korzystanie z łączników

Połączenie to specjalne działanie, które czeka na ukończenie wszystkich gałęzi przychodzących. Dzięki temu można uruchomić równolegle wiele działań i upewnić się, że wszystkie działania zostały ukończone przed przejściem dalej.

Chociaż połączenie może mieć nieograniczoną liczbę łączy przychodzących, tylko jedno z tych łączy może być potokiem. Liczba przychodzących linków sekwencji nie jest ograniczona. Można utworzyć połączenie z wieloma przychodzącymi linkami potoku i zapisać go, ale jego uruchomienie nie powiedzie się.

Poniższy przykład jest częścią element runbook, który uruchamia zestaw maszyn wirtualnych podczas jednoczesnego pobierania poprawek do zastosowania do tych maszyn. Używa ona rozgałęzienia, aby upewnić się, że oba procesy zostały ukończone przed kontynuowaniem pracy runbook.

![Połączenie](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Praca z cyklami

Cykl jest tworzymy, gdy działanie docelowe łączy się z jego działaniem źródłowym lub z innym działaniem, które ostatecznie łączy się z jego źródłem. Tworzenie graficzne nie obsługuje obecnie cykli. Jeśli element Runbook ma cykl, jest zapisywany prawidłowo, ale podczas jego działania otrzymuje błąd.

![Cykliczny](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Udostępnianie danych między działaniami

Wszystkie dane wyjściowe działania z łączem wychodzącym są zapisywane w magistrali danych dla tego runbook. Każde działanie w runbook może używać danych w magistrali danych do wypełniania wartości parametrów lub dołączania ich do kodu skryptu. Działanie może uzyskać dostęp do danych wyjściowych dowolnego poprzedniego działania w przepływie pracy.

Sposób, w jaki dane są zapisywane w magistrali danych, zależy od typu łącza w działaniu. W przypadku łącza potoku dane są wyprowadzane jako wiele obiektów. W przypadku linku sekwencji dane są wyprowadzane jako tablica. Jeśli istnieje tylko jedna wartość, jest to dane wyjściowe jako tablica jedno elementowa.

Aby uzyskać dostęp do danych w magistrali danych, można uzyskać dostęp do tego komputera runbook: 
* Użyj źródła danych wyjściowych działania.
* Użyj źródła danych wyrażenia programu PowerShell.

Pierwszy mechanizm używa źródła danych wyjściowych działania do wypełnienia parametru innego działania. Jeśli dane wyjściowe są obiektem, element Runbook może określić pojedynczą właściwość.

![dane wyjściowe działania](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Drugi mechanizm dostępu do danych pobiera dane wyjściowe działania w źródle danych wyrażenia programu PowerShell lub działaniu skryptu przepływu pracy ze zmienną przy użyciu składni `ActivityOutput` przedstawionej poniżej. Jeśli dane wyjściowe są obiektem, element Runbook może określić pojedynczą właściwość.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Korzystanie z punktów kontrolnych

Punkty kontrolne można [ustawić w graficznym elementie](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) Runbook przepływu pracy programu PowerShell, wybierając element **Runbook punktu kontrolnego** dla dowolnego działania. Spowoduje to ustawienie punktu kontrolnego po zakończeniu działania.

![Punkt kontrolny](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są włączone tylko w graficznych elementach Runbook przepływu pracy programu PowerShell i nie są dostępne w graficznych elementach Runbook. Jeśli w ramach tego działania są używane polecenia cmdlet platformy Azure, powinien on śledzić wszystkie działania z punktem `Connect-AzAccount` kontrolnym. Operacja łączenia jest używana w przypadku, gdy runbook jest wstrzymany i musi zostać uruchomiony ponownie z tego punktu kontrolnego w innym procesu roboczego.

## <a name="handle-runbook-input"></a>Obsługa danych wejściowych runbook

Jeśli bieżący jest używany jako podrzędny, musi on wprowadzić dane wejściowe od użytkownika uruchamiając go za pośrednictwem Azure Portal lub z innego. Na przykład w przypadku obiektu Runbook, który tworzy maszynę wirtualną, użytkownik może potrzebować podania takich informacji, jak nazwa maszyny wirtualnej i inne właściwości przy każdym uruchomieniu.

Runbook akceptuje dane wejściowe przez zdefiniowanie co najmniej jednego parametru wejściowego. Użytkownik podaje wartości tych parametrów przy każdym uruchomieniu runbook. Gdy użytkownik uruchamia program Runbook przy użyciu Azure Portal, zostanie wyświetlony monit o podanie wartości dla każdego parametru wejściowego obsługiwanego przez ten typ.

Podczas tworzenia swojego runbook możesz uzyskać dostęp do jego parametrów wejściowych, klikając pozycję **Dane** wejściowe i wyjściowe na pasku narzędzi. Spowoduje to otwarcie kontrolki Dane wejściowe i wyjściowe, w której można edytować istniejący parametr wejściowy lub utworzyć nowy, klikając pozycję **Dodaj dane wejściowe**.

![Dodawanie danych wejściowych](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest definiowany przez właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Nazwa | Wymagane. Nazwa parametru. Nazwa musi być unikatowa w obrębie tego runbook. Musi zaczynać się od litery i może zawierać tylko litery, cyfry i podkreślenia. Nazwa nie może zawierać spacji. |
| Opis |Opcjonalny. Opis przeznaczenia parametru wejściowego. |
| Typ | Opcjonalny. Typ danych oczekiwany dla wartości parametru. Parametr Azure Portal odpowiednią kontrolkę dla typu danych dla każdego parametru podczas monitowania o dane wejściowe. Obsługiwane typy parametrów to String, Int32, Int64, Decimal, Boolean, DateTime i Object. Jeśli typ danych nie jest wybrany, wartość domyślna to Ciąg.|
| Obowiązkowy | Opcjonalny. Ustawienie, które określa, czy należy podać wartość dla parametru . W przypadku `yes` wybrania opcji należy podać wartość podczas uruchamiania tego runbook. W przypadku wybrania opcji wartość nie jest wymagana po uruchomieniu runbook i można `no` użyć wartości domyślnej. Nie można uruchomić element Runbook, jeśli nie po określono wartości dla każdego obowiązkowego parametru, który nie ma zdefiniowanej wartości domyślnej. |
| Wartość domyślna | Opcjonalny. Wartość używana dla parametru , jeśli nie zostanie przekazana podczas uruchamiania runbook. Aby ustawić wartość domyślną, wybierz pozycję `Custom` . Wybierz `None` tę opcję, jeśli nie chcesz po prostu podać żadnej wartości domyślnej. |

## <a name="handle-runbook-output"></a>Obsługa danych wyjściowych runbook

Graficzne tworzenie zapisuje dane utworzone przez dowolne działanie, które nie ma wychodzącego linku do danych [wyjściowych runbook.](./automation-runbook-output-and-messages.md) Dane wyjściowe są zapisywane wraz z zadaniem elementu Runbook i są dostępne dla nadrzędnego elementu Runbook, gdy element Runbook jest używany jako element podrzędny.

## <a name="work-with-powershell-expressions"></a>Praca z wyrażeniami programu PowerShell

Jedną z zalet tworzenia graficznego jest możliwość tworzenia elementów Runbook przy minimalnej znajomości programu PowerShell. Obecnie jednak musisz znać nieco programu PowerShell do wypełniania niektórych wartości parametrów i ustawiania [warunków linku](#use-links-for-workflow). [](#use-activities) Ta sekcja zawiera krótkie wprowadzenie do wyrażeń programu PowerShell. Pełne szczegóły programu PowerShell są dostępne na [stronie Scripting with Windows PowerShell (Wykonywanie](/powershell/scripting/overview)skryptów za pomocą Windows PowerShell).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Używanie wyrażenia programu PowerShell jako źródła danych

Wyrażenie programu PowerShell może być źródłem danych, aby [](#use-activities) wypełnić wartość parametru działania wynikami kodu programu PowerShell. Wyrażenie może być pojedynczym wierszem kodu, który wykonuje prostą funkcję lub wiele wierszy wykonujących złożoną logikę. Wszystkie dane wyjściowe polecenia, które nie są przypisane do zmiennej, są dane wyjściowe wartości parametru.

Na przykład następujące polecenie wyprowadza bieżącą datę.

```powershell-interactive
Get-Date
```

Następny fragment kodu tworzy ciąg z bieżącej daty i przypisuje go do zmiennej. Kod wysyła zawartość zmiennej do danych wyjściowych.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Następujące polecenia oceniają bieżącą datę i zwracają ciąg wskazujący, czy bieżący dzień jest weekendem, czy dniem tygodnia.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Korzystanie z danych wyjściowych działania

Aby użyć danych wyjściowych z poprzedniego działania w twoim runbook, użyj `ActivityOutput` zmiennej o następującej składni.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Na przykład można mieć działanie z właściwością, która wymaga nazwy maszyny wirtualnej. W takim przypadku elementy Runbook mogą używać następującego wyrażenia.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Jeśli właściwość wymaga obiektu maszyny wirtualnej, a nie tylko nazwy, runbook zwraca cały obiekt przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Element Runbook może używać danych wyjściowych działania w bardziej złożonym wyrażeniu, takim jak poniższe. To wyrażenie łączy tekst z nazwą maszyny wirtualnej.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Porównywanie wartości

Użyj [operatorów porównania,](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) aby porównać wartości lub określić, czy wartość pasuje do określonego wzorca. Porównanie zwraca wartość True lub False.

Na przykład następujący warunek określa, czy maszyna wirtualna z działania o nazwie `Get-AzureVM` jest obecnie zatrzymana.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

Poniższy warunek określa, czy ta sama maszyna wirtualna jest w dowolnym stanie innym niż zatrzymana.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

Możesz połączyć wiele warunków w swoim runbook za pomocą [operatora logicznego,](/powershell/module/microsoft.powershell.core/about/about_logical_operators)takiego `-and` jak lub `-or` . Na przykład poniższy warunek sprawdza, czy maszyna wirtualna w poprzednim przykładzie ma stan Zatrzymana, czy Zatrzymywanie.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Używanie tablic skrótów

[Tablice skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables) to pary nazwa-wartość, które są przydatne do zwracania zestawu wartości. Może również zostać wyświetlony tablica skrótów nazywana słownikiem. Właściwości dla niektórych działań oczekują tablicy skrótów zamiast prostej wartości.

Utwórz tabelę skrótów przy użyciu następującej składni. Może zawierać dowolną liczbę wpisów, ale każdy z nich jest definiowany przez nazwę i wartość.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Na przykład poniższe wyrażenie tworzy tabelę skrótów, która będzie używana jako źródło danych dla parametru działania, który oczekuje tablicy skrótów wartości dla wyszukiwania w Internecie.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

W poniższym przykładzie użyto danych wyjściowych z działania o nazwie `Get Twitter Connection` w celu wypełnienia tablicy skrótów.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Uwierzytelnianie w zasobach platformy Azure

W przypadku Azure Automation, które zarządzają zasobami platformy Azure, wymagane jest uwierzytelnianie na platformie Azure. Konto [Uruchom jako](./automation-security-overview.md), nazywane również jednostką usługi, jest domyślnym mechanizmem używanym przez element Runbook usługi Automation do uzyskiwania dostępu do Azure Resource Manager zasobów w ramach subskrypcji. Tę funkcję można dodać do graficznego element runbook, dodając do kanwy element zawartości połączenia, który używa polecenia `AzureRunAsConnection` cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) programu PowerShell. Możesz również dodać polecenie cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Ten scenariusz został zilustrowany w poniższym przykładzie.

![Działania uwierzytelniania Uruchom jako](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Działanie, czyli , jest skonfigurowane przy użyciu źródła danych `Get Run As Connection` `Get-AutomationConnection` o stałej wartości o nazwie `AzureRunAsConnection` .

![Konfiguracja połączenia Uruchom jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Następne działanie, `Connect-AzAccount` , dodaje uwierzytelnione konto Uruchom jako do użycia w tym runbook.

![Connect-AzAccount zestaw parametrów](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>W przypadku podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy pamiętać, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może używać tylko `Connect-AzAccount` samego siebie.

Dla pól parametrów **APPLICATIONID**, **CERTIFICATETHUMBPRINT** i **TENANTID** określ nazwę właściwości ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami. W przeciwnym razie wykonanie runbook kończy się niepowodzeniem podczas próby uwierzytelnienia. Jest to co najmniej to, czego potrzebujesz do uwierzytelnienia swojego runbook przy użyciu konta Uruchom jako.

Niektórzy subskrybenci tworzą konto usługi Automation przy użyciu konta użytkownika usługi [Azure AD](./shared-resources/credentials.md) w celu zarządzania klasycznym wdrożeniem platformy Azure lub Azure Resource Manager zasobów. Aby zachować zgodność z poprzednimi wersjami dla tych subskrybentów, mechanizmem uwierzytelniania, który ma być używane w twoim runbook, jest polecenie `Add-AzureAccount` cmdlet z [zasobem poświadczeń](./shared-resources/credentials.md). Zasób reprezentuje użytkownika usługi Active Directory z dostępem do konta platformy Azure.

Tę funkcję można włączyć dla graficznego element runbook, dodając element zawartości poświadczeń do kanwy, a następnie działanie, które używa zasobu poświadczeń `Add-AzureAccount` do wprowadzania danych wejściowych. Zobacz poniższy przykład.

![Działania związane z uwierzytelnianiem](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook musi uwierzytelniać się podczas uruchamiania i po każdym punkcie kontrolnym. W związku z tym należy użyć `Add-AzureAccount` działania po każdym `Checkpoint-Workflow` działaniu. Nie trzeba używać dodatkowego działania poświadczeń.

![Dane wyjściowe działania](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Eksportowanie graficznego elementów Runbook

Można eksportować tylko opublikowaną wersję graficznego runbook. Jeśli runbook nie został jeszcze opublikowany, przycisk **Eksportuj** jest wyłączony. Po kliknięciu **przycisku Eksportuj** elementy Runbook są pobierane na komputer lokalny. Nazwa pliku odpowiada nazwie runbook z rozszerzeniem **.graphrunbook.**

## <a name="import-a-graphical-runbook"></a>Importowanie graficznego element runbook

Możesz zaimportować graficzny lub graficzny plik runbook przepływu pracy programu PowerShell, wybierając opcję **Importuj** podczas dodawania elementów Runbook. Po wybraniu pliku do zaimportowania możesz zachować tę samą nazwę lub podać nową. W **polu Typ runbook** jest wyświetlany typ runbook po ocenie wybranego pliku. Jeśli spróbujesz wybrać inny typ, który nie jest poprawny, edytor graficzny przedstawi komunikat informujący o potencjalnych konfliktach i mogą wystąpić błędy składniowe podczas konwersji.

![Importowanie runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testowanie graficznego element runbook

Każdy graficzny element Runbook w Azure Automation ma wersję roboczą i opublikowaną. Można uruchomić tylko wersję opublikowaną, podczas gdy można edytować tylko wersję roboczą. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowa do użycia, należy ją opublikować, co spowoduje zastąpienie bieżącej opublikowanej wersji wersją roboczą.

Wersję roboczą runbook można przetestować na stronie Azure Portal bez zmian w opublikowanej wersji. Alternatywnie można przetestować nowy podręcznik Runbook przed jego opublikowaniem, aby można było sprawdzić, czy działa prawidłowo przed zastąpieniem wersji. Testowanie runbook wykonuje wersję roboczą i zapewnia, że wszystkie wykonywane przez niego akcje zostaną zakończone. Historia zadań nie jest tworzona, ale w okienku Dane wyjściowe testu są wyświetlane dane wyjściowe.

Otwórz kontrolkę Test dla graficznego element runbook, otwierając element Runbook do edycji, a następnie klikając **okienko Test.** Kontrolka Test wyświetla monit o parametry wejściowe. Aby uruchomić ten podręcznik, kliknij pozycję **Uruchom.**

## <a name="publish-a-graphical-runbook"></a>Publikowanie graficznego element runbook

Opublikuj graficzny element Runbook, otwierając element Runbook do edycji, a następnie klikając pozycję **Opublikuj**. Możliwe stany dla tego runbook to:

* Nowość — nie został jeszcze opublikowany. 
* Opublikowano — został opublikowany.
* W trybie edycji — po opublikowaniu został edytowany jego runbook, a wersje wersja robocza i opublikowana są inne.

![Stany runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Możesz przywrócić opublikowaną wersję runbook. Ta operacja zrzuca wszelkie zmiany wprowadzone od czasu ostatniej publikacji tego runbook. Zastępuje wersję roboczą tego runbook opublikowaną wersją.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Samouczek: tworzenie graficznego element runbook.](learn/automation-tutorial-runbook-graphical.md)
* Aby dowiedzieć się więcej o typach elementów Runbook oraz ich zaletach i ograniczeniach, zobacz [Azure Automation runbook](automation-runbook-types.md).
* Aby dowiedzieć się, jak uwierzytelniać się przy użyciu konta Uruchom jako usługi Automation, zobacz [Konto Uruchom jako.](automation-security-overview.md#run-as-account)
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation/#automation).
