---
title: Tworzenie grafiki w usłudze Azure Automation
description: Tworzenie graficzne umożliwia tworzenie śmiób dla usługi Azure Automation bez pracy z kodem. Ten artykuł zawiera wprowadzenie do tworzenia grafiki i wszystkich szczegółów potrzebnych do rozpoczęcia tworzenia graficznego wiązka ś.a.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: cf8ced05066923c94e80628651d8983560601d69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406046"
---
# <a name="graphical-authoring-in-azure-automation"></a>Tworzenie grafiki w usłudze Azure Automation

Tworzenie graficzne umożliwia tworzenie podstawowych podstawowych dla usługi Azure Automation bez złożoności podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Działania można dodawać do kanwy z biblioteki poleceń pochodzielnych i kreśleń runbooków, łączyć je ze sobą i konfigurować w taki sposób, aby tworzyły przepływ pracy. Jeśli kiedykolwiek pracowałeś z System Center Orchestrator lub Service Management Automation (SMA), tworzenie graficzne powinno wyglądać znajomo. Ten artykuł zawiera wprowadzenie do pojęć potrzebnych do rozpoczęcia tworzenia graficznego egoistu.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Graficzne programy runbook

Wszystkie elementy runbook w usłudze Azure Automation są przepływami pracy programu Windows PowerShell. Graficzne elementy runbook i graficzne elementy runbook przepływu pracy programu PowerShell generują kod programu PowerShell uruchamiany przez pracowników automatyzacji, ale nie można go wyświetlać ani modyfikować. Element runbook graficzny można przekonwertować na graficzny element runbook przepływu pracy programu PowerShell i odwrotnie. Nie można jednak przekonwertować tych tekstów na tekstowy projekt runbook. Ponadto edytor graficzny automatyzacji nie może zaimportować tekstowego podstawowego podstawowego ekwiwali.

## <a name="overview-of-graphical-editor"></a>Omówienie edytora graficznego

Edytor graficzny można otworzyć w witrynie Azure portal, tworząc lub edytując graficzny element runbook.

![Graficzny obszar roboczy](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

W poniższych sekcjach opisano formanty w edytorze graficznym.

### <a name="canvas-control"></a>Kontrolka kanwy

Kontrolka Kanwa umożliwia projektowanie śmiętu. Działania z węzłów w formancie Biblioteka można dodawać do wiązki i łączyć je za pomocą łączy w celu zdefiniowania logiki wiązki uruchomieniu. I W dolnej części obszaru roboczego znajdują się kontrolki, które umożliwiają powiększanie i pomniejszanie.

### <a name="library-control"></a>Kontrolka biblioteki

Kontrolka Biblioteka umożliwia [wybranie działań, które](#activities) mają być dodane do ego księgi runbook. Dodaj je do kanwy, gdzie można połączyć je z innymi działaniami. Formant Biblioteka zawiera sekcje zdefiniowane w poniższej tabeli.

| Sekcja | Opis |
|:--- |:--- |
| Polecenia cmdlet |Wszystkie polecenia cmdlet, które mogą być używane w bieśdmiętce. Polecenia cmdlet są uporządkowane według modułu. Dostępne są wszystkie moduły zainstalowane na koncie automatyzacji. |
| Elementy Runbook |Elementy runbook na koncie automatyzacji. Można dodać te elementy runbook do kanwy, które mają być używane jako elementy runbook podrzędnych. Wyświetlane są tylko programy runbook tego samego typu rdzenia co edytowany element runbook. W przypadku czągn. W przypadku graficznych uruchomieniu przepływów pracy programu PowerShell wyświetlane są tylko uruchomieniu programy PowerShell Workflow. |
| Elementy zawartości |[Zasoby automatyzacji na koncie](/previous-versions/azure/dn939988(v=azure.100)) automatyzacji, których można użyć w liczebie. Dodanie zasobu do elementu runbook dodaje działanie przepływu pracy, które pobiera wybrany zasób. W przypadku zasobów zmiennych można wybrać, czy chcesz dodać działanie, aby uzyskać zmienną, czy ustawić zmienną. |
| Sterowanie elementem runbook |Kontrola działań, które mogą być używane w bieżącym uruchomieniu. 10. Działanie junction przyjmuje wiele danych wejściowych i czeka, aż wszystkie zostały zakończone przed kontynuowaniem przepływu pracy. Działanie Kod uruchamia co najmniej jeden wiersz kodu przepływu pracy programu PowerShell lub Programu PowerShell, w zależności od typu graficznego egoiscie. Tego działania można użyć dla kodu niestandardowego lub funkcji, które są trudne do osiągnięcia z innymi działaniami. |

### <a name="configuration-control"></a>Kontrola konfiguracji

Formant Konfiguracji umożliwia podanie szczegółów dla obiektu wybranego na kanwie. Właściwości dostępne w tym formancie zależą od typu wybranego obiektu. Po wybraniu opcji w configuration kontroli, otwiera dodatkowe bloków, aby zapewnić więcej informacji.

### <a name="test-control"></a>Kontrola testu

Test kontrolki nie jest wyświetlany po pierwszym uruchomieniu edytora graficznego. Jest on otwierany podczas interaktywnego testowania graficznego śmiękacza.

## <a name="activities"></a>Działania

Działania są elementami konstrukcyjnymi elementów runbook. Działaniem może być polecenie cmdlet programu PowerShell, element runbook podrzędny lub przepływ pracy. Działanie można dodać do księgi runbook, klikając je prawym przyciskiem myszy w formancie Biblioteka i wybierając pozycję **Dodaj do obszaru roboczego**. Następnie możesz kliknąć i przeciągnąć aktywność, aby umieścić je w dowolnym miejscu na kanwie, który ci się podoba. Lokalizacja działania na kanwie nie wpływa na działanie uruchomieniu.tu. Możesz ułożyć swój runbook w dowolny sposób, który jest najbardziej odpowiedni do wizualizacji jego działania.

![Dodaj do kanwy](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Wybierz działanie na kanwie, aby skonfigurować jego właściwości i parametry w bloku Konfiguracja. Etykietę działania można zmienić na nazwę, która jest oznaczona opisem. Projekt runbook nadal uruchamia oryginalne polecenie cmdlet. Po prostu zmieniasz nazwę wyświetlaną używanej przez edytor graficzny. Należy zauważyć, że etykieta musi być unikatowa w ramach śmigu.

### <a name="parameter-sets"></a>Zestawy parametrów

Zestaw parametrów definiuje parametry obowiązkowe i opcjonalne, które akceptują wartości dla określonego polecenia cmdlet. Wszystkie polecenia cmdlet mają co najmniej jeden zestaw parametrów, a niektóre mają kilka zestawów. Jeśli polecenie cmdlet ma wiele zestawów parametrów, należy wybrać ten, który ma być używany, zanim będzie można skonfigurować parametry. Zestaw parametrów używany przez działanie można zmienić, wybierając **pozycję Zestaw parametrów** i wybierając inny zestaw. W takim przypadku wszystkie wartości parametrów, które zostały już skonfigurowane są tracone.

W poniższym przykładzie polecenie cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) ma trzy zestawy parametrów. W przykładzie użyto jednego zestawu o nazwie **ListVirtualMachineInResourceGroupParamSet**, z jednym parametrem opcjonalnym, do zwracania wszystkich maszyn wirtualnych w grupie zasobów. W przykładzie użyto również **GetVirtualMachineInResourceGroupParamSet** parametr zestaw do określania maszyny wirtualnej do zwrócenia. Ten zestaw ma dwa parametry obowiązkowe i jeden parametr opcjonalny.

![Zestaw parametrów](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Wartości parametrów

Po określeniu wartości parametru należy wybrać źródło danych, aby określić sposób określania wartości. Źródła danych, które są dostępne dla określonego parametru zależą od prawidłowych wartości dla tego parametru. Na przykład Null nie jest dostępną opcją dla parametru, który nie zezwala na wartości null.

| Źródło danych | Opis |
|:--- |:--- |
| Stała wartość |Wpisz wartość parametru. To źródło danych jest dostępne tylko dla następujących typów danych: Int32, Int64, String, Boolean, DateTime, Switch. |
| Dane wyjściowe aktywności |Użyj danych wyjściowych z działania poprzedzającego bieżące działanie w przepływie pracy. Wszystkie prawidłowe działania są wymienione. Dla wartości parametru należy użyć tylko działania, które produkuje dane wyjściowe. Jeśli działanie wyprowadza obiekt o wielu właściwościach, można wpisać nazwę określonej właściwości po wybraniu działania. |
| Wprowadzanie eksmisji |Wybierz dane wejściowe przewodniczącego jako dane wejściowe dla parametru działania. |
| Środek trwały zmienny |Wybierz zmienną automatyzacji jako dane wejściowe. |
| Zasób poświadczeń |Wybierz poświadczenie automatyzacji jako dane wejściowe. |
| Zasób certyfikatu |Wybierz certyfikat automatyzacji jako dane wejściowe. |
| Zasób połączenia |Wybierz połączenie automatyzacji jako dane wejściowe. |
| Wyrażenie programu PowerShell |Określ proste [wyrażenie programu PowerShell](#powershell-expressions). Wyrażenie jest oceniane przed działaniem, a wynik jest używany dla wartości parametru. Za pomocą zmiennych można odwoływać się do danych wyjściowych działania lub parametru wejściowego wiązanego. |
| Nieskonfigurowane |Wyczyść dowolną wartość, która została wcześniej skonfigurowana. |

#### <a name="optional-additional-parameters"></a>Opcjonalne parametry dodatkowe

Wszystkie polecenia cmdlet mają możliwość podania dodatkowych parametrów. Są to parametry wspólne programu PowerShell lub inne parametry niestandardowe. Edytor graficzny przedstawia pole tekstowe, w którym można podać parametry przy użyciu składni programu PowerShell. Na przykład, aby `Verbose` użyć wspólnego parametru, należy określić `-Verbose:$True`.

### <a name="retry-activity"></a>Ponów próbę działania

Ponów próbę funkcji dla działania umożliwia uruchamianie wiele razy, dopóki określony warunek jest spełniony, podobnie jak pętla. Tej funkcji można użyć dla działań, które powinny być uruchamiane wiele razy, są podatne na błędy, może wymagać więcej niż jednej próby powodzenia lub przetestować informacje wyjściowe działania dla prawidłowych danych.

Po włączeniu ponowiania prób dla działania, można ustawić opóźnienie i warunek. Opóźnienie to czas (mierzony w sekundach lub minutach), który trwa od momentu ponownego uruchomienia. Jeśli nie określisz opóźnienia, działanie zostanie ponownie uruchamiane natychmiast po jej zakończeniu.

![Opóźnienie ponawiania próby działania](media/automation-graphical-authoring-intro/retry-delay.png)

Warunek ponawiania jest wyrażenie programu PowerShell, który jest oceniany po każdym uruchomieniu działania. Jeśli wyrażenie jest rozpoznawane jako True, działanie zostanie ponownie uruchamiane. Jeśli wyrażenie jest rozpoznawane jako False, działanie nie zostanie uruchomione ponownie, a projekt runbook przejdzie do następnego działania.

![Opóźnienie ponawiania próby działania](media/automation-graphical-authoring-intro/retry-condition.png)

Warunek ponawiania można `RetryData` użyć zmiennej o nazwie, która zapewnia dostęp do informacji o ponownych próbach działania. Ta zmienna ma właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| `NumberOfAttempts` |Liczba uruchomiono działanie. |
| `Output` |Dane wyjściowe z ostatniego uruchomienia działania. |
| `TotalDuration` |Czas upłynął od rozpoczęcia działania po raz pierwszy. |
| `StartedAt` |Czas (w formacie UTC) podczas pierwszego rozpoczęcia działania. |

Poniżej przedstawiono przykłady warunków ponawiania próby działania.

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

Po skonfigurowaniu warunku ponawiania dla działania, działanie zawiera dwa wizualne wskazówki, aby przypomnieć. Jeden jest przedstawiony w działaniu, a drugi jest wyświetlany podczas przeglądania konfiguracji działania.

![Ponowij próbę działania Wskaźniki wizualne](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontrolka skryptu przepływu pracy

Formant skryptu przepływu pracy to specjalne działanie, które akceptuje skrypt przepływu pracy programu PowerShell lub programu PowerShell, w zależności od typu tworzenia autorskie graficznego obiektu runbook. Ten formant zapewnia funkcje, które mogą nie być dostępne w inny sposób. Nie może akceptować parametrów, ale może używać zmiennych dla parametrów wejściowych danych wyjściowych i runbook. Wszelkie dane wyjściowe działania są dodawane do databus. Wyjątkiem jest dane wyjściowe bez łącza wychodzącego, w którym to przypadku dane wyjściowe są dodawane do danych wyjściowych wiązki uruchomieniu.

Na przykład poniższy kod wykonuje obliczenia daty przy `NumberOfDays`użyciu zmiennej wejściowej księgi runbook o nazwie . Następnie wysyła obliczoną wartość DateTime jako dane wyjściowe do użycia przez kolejne działania w liczebie.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Łącza i przepływ pracy

Łącze w graficznym wiązanym uruchomieniu element łączy dwa działania. Jest on wyświetlany na kanwie jako strzałka wskazująca działanie źródłowe do działania docelowego. Działania są uruchamiane w kierunku strzałki z działaniem docelowym rozpoczynającym się po zakończeniu działania źródłowego.

### <a name="link-creation"></a>Tworzenie łączy

Można utworzyć łącze między dwoma działaniami, zaznaczając działanie źródłowe i klikając okrąg u dołu kształtu. Przeciągnij strzałkę do działania docelowego i zwolnij.

![Tworzenie łącza](media/automation-graphical-authoring-intro/create-link-options.png)

Wybierz łącze, aby skonfigurować jego właściwości w bloku Konfiguracja. Właściwości obejmują typ łącza, który jest opisany w poniższej tabeli.

| Typ łącza | Opis |
|:--- |:--- |
| Potok |Działanie docelowe jest uruchamiane raz dla każdego obiektu wyjściowego z działania źródłowego. Działanie docelowe nie jest uruchamiane, jeśli działanie źródłowe nie powoduje żadnych danych wyjściowych. Dane wyjściowe z działania źródłowego jest dostępny jako obiekt. |
| Sequence |Działanie docelowe jest uruchamiane tylko raz, gdy odbiera dane wyjściowe z działania źródłowego. Dane wyjściowe z działania źródłowego jest dostępna jako tablica obiektów. |

### <a name="start-of-activity"></a>Początek aktywności

Graficzny element runbook rozpoczyna się od wszystkich działań, które nie mają łącza przychodzącego. Często istnieje tylko jedno działanie, które działa jako działanie początkowe dla przewodniczącego. Jeśli wiele działań nie ma łącza przychodzącego, element runbook rozpoczyna się od uruchomienia ich równolegle. Wynika z łączy do uruchamiania innych działań, jak każdy kończy.

### <a name="link-conditions"></a>Warunki łącza

Po określeniu warunku dla łącza działanie docelowe jest uruchamiane tylko wtedy, gdy warunek jest rozpoznawany jako True. Zazwyczaj używasz `ActivityOutput` zmiennej w warunku, aby pobrać dane wyjściowe z działania źródłowego.

Dla łącza potoku należy określić warunek dla pojedynczego obiektu. Runbook ocenia warunek dla każdego obiektu wyjściowego przez działanie źródłowe. Następnie uruchamia działanie docelowe dla każdego obiektu, który spełnia warunek. Na przykład z działaniem `Get-AzVM`źródłowym programu , można użyć następującej składni dla łącza potoku warunkowego, aby pobrać tylko maszyny wirtualne w grupie zasobów o nazwie Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Dla łącza sekwencji runbook tylko ocenia warunek raz, ponieważ zwracana jest pojedyncza tablica zawierająca wszystkie obiekty z działania źródłowego. Z tego powodu element runbook nie może używać łącza sekwencji do filtrowania, jak to może z łącza potoku. Łącze sekwencji można po prostu określić, czy następne działanie jest uruchamiane.

Na przykład weź następujący zestaw działań w naszym **zestawie startu maszyny Wirtualnej:**

![Łącze warunkowe z sekwencjami](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Projekt runbook używa trzech różnych łączy sekwencji, `VMName` `ResourceGroupName` które weryfikują wartości parametrów wejściowych i określają odpowiednie działania do podjęcia. Możliwe akcje to uruchamianie pojedynczej maszyny wirtualnej, uruchamianie wszystkich maszyn wirtualnych w grupie zasobów lub uruchamianie wszystkich maszyn wirtualnych w ramach subskrypcji. Dla połączenia sekwencji między `Connect to Azure` i `Get single VM`, oto logika warunku:

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

Korzystając z łącza warunkowego, dane dostępne z działania źródłowego do innych działań w tej gałęzi jest filtrowany przez warunek. Jeśli działanie jest źródłem wielu łączy, dane dostępne dla działań w każdej gałęzi zależy od warunku w łączu łączącym się z tą gałęzią.

Na przykład `Start-AzVM` działanie w uruchomieniu poniżej uruchamia wszystkie maszyny wirtualne. Posiada dwa łącza warunkowe. Pierwsze łącze warunkowe `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` używa wyrażenia `Start-AzVM` do filtrowania, jeśli działanie zakończy się pomyślnie. Drugie łącze warunkowe `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` używa wyrażenia `Start-AzVm` do filtrowania, jeśli działanie nie uruchamia maszyny wirtualnej.

![Przykład łącza warunkowego](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Każde działanie, które następuje po pierwszym `Get-AzureVM` łączu i używa danych wyjściowych `Get-AzureVM` działania tylko pobiera maszyny wirtualne, które zostały uruchomione w czasie, gdy został uruchomiony. Każde działanie, które następuje po drugim łączu pobiera `Get-AzureVM` tylko maszyny wirtualne, które zostały zatrzymane w czasie, gdy został uruchomiony. Każde działanie następujące trzecie łącze pobiera wszystkie maszyny wirtualne, niezależnie od ich stanu działania.

### <a name="junctions"></a>Skrzyżowania

Skrzyżowanie jest specjalną aktywnością, która czeka, aż wszystkie przychodzące gałęzie zostaną ukończone. Dzięki temu elementu runbook do uruchamiania wielu działań równolegle i upewnij się, że wszystkie zostały zakończone przed przejściem dalej.

Podczas gdy połączenie może mieć nieograniczoną liczbę łączy przychodzących, tylko jedno z tych łączy może być potokiem. Liczba przychodzących łączy sekwencji nie jest ograniczona. Można utworzyć połączenie z wieloma przychodzącymi łączami potoku i zapisać projekt runbook, ale zakończy się niepowodzeniem po jego uruchomieniu.

Poniższy przykład jest częścią zestawu runbook, który uruchamia zestaw maszyn wirtualnych podczas jednoczesnego pobierania poprawek, które mają być stosowane do tych maszyn. Używa połączenia, aby upewnić się, że oba procesy są zakończone przed kontynuowaniem uruchomieniu. 105 00.

![Połączenie](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykli

Cykl jest tworzony, gdy działanie docelowe łączy się z powrotem do jego działania źródłowego lub do innego działania, które ostatecznie łączy się z powrotem do źródła. Tworzenie graficzne nie obsługuje obecnie cykli. Jeśli element runbook ma cykl, zapisuje poprawnie, ale odbiera błąd podczas jego uruchamiania.

![Cykliczny](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Udostępnianie danych między działaniami

Wszystkie dane, które działanie wyprowadza z łączem wychodzącym, są zapisywane w magistrali danych dla ego księgi runbook. Każde działanie w elemencie runbooka może używać danych w magistrali danych do wypełniania wartości parametrów lub dołączania do kodu skryptu. Działanie może uzyskać dostęp do danych wyjściowych dowolnego poprzedniego działania w przepływie pracy.

Sposób zapisywania danych w magistrali danych zależy od typu łącza w działaniu. Dla łącza potoku dane są dane wyjściowe jako wiele obiektów. Dla łącza sekwencji dane są dane wyjściowe jako tablica. Jeśli istnieje tylko jedna wartość, jest ono dane wyjściowe jako tablica jednoelementowa.

Program runbook ma dwa sposoby uzyskiwania dostępu do danych w magistrali danych: 
* Użyj źródła danych wyjściowych działania.
* Użyj źródła danych wyrażenia programu PowerShell.

Pierwszy mechanizm używa źródła danych wyjściowych działania do wypełniania parametru innego działania. Jeśli dane wyjściowe jest obiektem, element runbook można określić jedną właściwość.

![produkcja aktywność](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Drugi mechanizm dostępu do danych pobiera dane wyjściowe działania w źródle danych wyrażenia programu `ActivityOutput` PowerShell lub działania skryptu przepływu pracy ze zmienną, przy użyciu składni pokazanej poniżej. Jeśli dane wyjściowe jest obiektem, element runbook można określić jedną właściwość.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Punkty kontrolne

[Punkty kontrolne](automation-powershell-workflow.md#checkpoints) można ustawić w graficznym uruchomieniu dla programu PowerShell, wybierając **element runbook punktu kontrolnego** dla dowolnej aktywności. Powoduje to, że punkt kontrolny ma być ustawiony po uruchomieniu działania.

![Punkt kontrolny](media/automation-graphical-authoring-intro/set-checkpoint.png)

Punkty kontrolne są włączone tylko w graficznych elementach runbook przepływu pracy programu PowerShell i nie są dostępne w graficznych elementach runbook. Jeśli projekt runbook używa poleceń cmdlet platformy Azure, `Connect-AzAccount` należy wykonać wszelkie działania punktu kontrolnego z działaniem. Operacja connect jest używana w przypadku, gdy system runbook jest zawieszony i musi zostać ponownie uruchomiony z tego punktu kontrolnego na inny proces roboczy.

## <a name="runbook-input-and-output"></a>Wprowadzanie i wyjście eksmisji

### <a name="runbook-input"></a>Wprowadzanie eksmisji<a name="runbook-input"></a>

Grupa runbook wymaga danych wejściowych od użytkownika rozpoczynającego runbook za pośrednictwem witryny Azure portal lub z innego systemu runbook, jeśli bieżący jest używany jako dziecko. Na przykład dla elementu runbook, który tworzy maszynę wirtualną, użytkownik może być konieczne podanie informacji, takich jak nazwa maszyny wirtualnej i inne właściwości przy każdym uruchomieniu elementu runbook.

Projekt runbook akceptuje dane wejściowe, definiując jeden lub więcej parametrów wejściowych. Użytkownik podaje wartości dla tych parametrów przy każdym uruchomieniu zestawu runbook. Gdy użytkownik uruchamia runbook przy użyciu portalu Azure, użytkownik jest monitowany o podanie wartości dla każdego parametru wejściowego obsługiwanego przez grupę runbook.

Podczas tworzenia ego księgi runbook można uzyskać dostęp do jego parametrów wejściowych, klikając **pozycję Dane i dane wyjściowe** na pasku narzędzi zestawu runbook. Spowoduje to otwarcie formantu Wejście i Wyjście, w którym można edytować istniejący parametr wejściowy lub utworzyć nowy, klikając przycisk **Dodaj dane wejściowe**.

![Dodawanie danych wejściowych](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Każdy parametr wejściowy jest definiowany przez właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Nazwa | Wymagany. Nazwa parametru. Nazwa musi być unikatowa w obrębie śmitu runbook. Musi zaczynać się od litery i może zawierać tylko litery, cyfry i podkreślenia. Nazwa nie może zawierać spacji. |
| Opis |Element opcjonalny. Opis celu parametru wejściowego. |
| Typ | Element opcjonalny. Typ danych oczekiwany dla wartości parametru. Portal Azure zapewnia odpowiednią kontrolę dla typu danych dla każdego parametru podczas monitowania o dane wejściowe. Obsługiwane typy parametrów to String, Int32, Int64, Decimal, Boolean, DateTime i Object. Jeśli typ danych nie jest zaznaczony, domyślnie ma wartość Ciąg.|
| Obowiązkowy | Element opcjonalny. Ustawienie określające, czy dla parametru musi być podana wartość. Jeśli wybierzesz `yes`opcję, wartość musi być podana podczas uruchamiania śmiętu. Jeśli wybierzesz `no`opcję, wartość nie jest wymagana podczas uruchamiania wiązka runbooka i można użyć wartości domyślnej. Element runbook nie może się uruchomić, jeśli nie podasz wartości dla każdego parametru obowiązkowego, który nie ma zdefiniowanej wartości domyślnej. |
| Wartość domyślna | Element opcjonalny. Wartość używana dla parametru, jeśli nie jest przekazywana podczas uruchamiania śmiętu. Aby ustawić wartość domyślną, wybierz opcję `Custom`. Wybierz, `None` jeśli nie chcesz podawać żadnej wartości domyślnej. |

### <a name="runbook-output"></a>Wynik uruchomienia elementu Runbook

Tworzenie graficzne zapisuje dane utworzone przez każde działanie, które nie ma łącza wychodzącego do [danych wyjściowych elementa runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Dane wyjściowe są zapisywane z zadaniem runbook i są dostępne dla nadrzędnego przewodniczącego, gdy element runbook jest używany jako element podrzędny.

## <a name="powershell-expressions"></a>Wyrażenia programu PowerShell

Jedną z zalet tworzenia grafiki jest to, że pozwala na tworzenie śmiętu z minimalną znajomością programu PowerShell. Obecnie jednak musisz znać trochę programu PowerShell do wypełniania niektórych [wartości parametrów](#activities) i ustawiania [warunków łącza.](#links-and-workflow) Ta sekcja zawiera szybkie wprowadzenie do wyrażeń programu PowerShell. Pełne informacje o programie PowerShell są dostępne w [programie Scripting z programem Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Źródło danych wyrażenia programu PowerShell

Wyrażenie programu PowerShell służy jako źródło danych do wypełniania wartości [parametru działania](#activities) wynikami kodu programu PowerShell. Wyrażenie może być pojedynczy wiersz kodu, który wykonuje prostą funkcję lub wiele wierszy, które wykonują niektóre złożone logiki. Wszystkie dane wyjściowe z polecenia, które nie jest przypisane do zmiennej jest dane wyjściowe do wartości parametru.

Na przykład następujące polecenie wyprowadza bieżącą datę.

```powershell-interactive
Get-Date
```

Następny fragment kodu tworzy ciąg od bieżącej daty i przypisuje go do zmiennej. Kod wysyła zawartość zmiennej do danych wyjściowych.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Następujące polecenia oceniają bieżącą datę i zwracają ciąg wskazujący, czy bieżący dzień to weekend, czy dzień tygodnia.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Dane wyjściowe aktywności

Aby użyć danych wyjściowych z poprzedniego działania `ActivityOutput` w żyłaku, należy użyć zmiennej z następującą składnią.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Na przykład można mieć działanie z właściwością, która wymaga nazwy maszyny wirtualnej. W takim przypadku system runbook można użyć następującego wyrażenia.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Jeśli właściwość wymaga obiektu maszyny wirtualnej, a nie tylko nazwy, runbook zwraca cały obiekt przy użyciu następującej składni.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Element runbook można użyć danych wyjściowych działania w wyrażenia bardziej złożonych, takich jak następujące. To wyrażenie łączy tekst z nazwą maszyny wirtualnej.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Warunki

Operatory [porównania](https://technet.microsoft.com/library/hh847759.aspx) służy do porównywania wartości lub określania, czy wartość jest zgodna z określonym wzorcem. Porównanie zwraca wartość True lub False.

Na przykład następujący warunek określa, czy maszyna wirtualna z działania o nazwie `Get-AzureVM` jest obecnie zatrzymana.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Poniższy warunek określa, czy ta sama maszyna wirtualna jest w dowolnym stanie innym niż zatrzymany.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Można dołączyć do wielu warunków w bieście `-and` runbook za pomocą [operatora logicznego](https://technet.microsoft.com/library/hh847789.aspx), takich jak lub `-or`. Na przykład następujący warunek sprawdza, czy maszyna wirtualna w poprzednim przykładzie jest w stanie zatrzymane lub zatrzymanie.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabele skrótów

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) są pary nazwa wartość, które są przydatne do zwracania zestaw wartości. Może również pojawić się hashtable dalej słownika. Właściwości dla niektórych działań oczekują mieszania zamiast prostej wartości.

Utwórz mieszadł przy użyciu następującej składni. Może zawierać dowolną liczbę wpisów, ale każdy jest zdefiniowany przez nazwę i wartość.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Na przykład następujące wyrażenie tworzy tabelę mieszania, która ma być używana jako źródło danych dla parametru aktywności, który oczekuje skrótu wartości dla wyszukiwania w Internecie.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

W poniższym przykładzie użyto `Get Twitter Connection` danych wyjściowych z działania wywoływanego do wypełniania tabeli mieszania.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Uwierzytelnianie zasobów platformy Azure

Elementy runbook w usłudze Azure Automation, które zarządzają zasobami platformy Azure, wymagają uwierzytelniania na platformie Azure. [Konto Uruchom jako](automation-create-runas-account.md), nazywane również jednostką usługi, jest domyślnym mechanizmem używanym przez element runbook automatyzacji do uzyskiwania dostępu do zasobów usługi Azure Resource Manager w ramach subskrypcji. Tę funkcję można dodać do graficznego zestawu `AzureRunAsConnection` runbooka, dodając do obszaru roboczego zasób połączenia, który używa polecenia cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) programu PowerShell. Można również dodać polecenie cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Ten scenariusz jest zilustrowany w poniższym przykładzie.

![Uruchamianie jako działania uwierzytelniania](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Działanie `Get Run As Connection` lub `Get-AutomationConnection`, jest skonfigurowany ze źródłem `AzureRunAsConnection`danych o stałej wartości o nazwie .

![Uruchom jako konfigurację połączenia](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Następne działanie `Connect-AzAccount`, dodaje uwierzytelnione uruchom jako konto do użycia w uruchomieniu. 1075.

![Zestaw parametrów Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Należy pamiętać, że te aliasy nie są dostępne dla cząstek graficznych. Graficzny element runbook może `Connect-AzAccount` używać tylko siebie.

Dla pól parametrów **APPLICATIONID**, **CERTIFICATETHUMBPRINT**i **TENANTID**, należy określić nazwę właściwości ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami. W przeciwnym razie podczas wykonywania śmięcia nie powiedzie się podczas próby uwierzytelnienia. Jest to, czego potrzebujesz co najmniej do uwierzytelnienia ego runbook z uruchom jako konto.

Niektórzy subskrybenci tworzą konto automatyzacji przy użyciu [konta użytkownika usługi Azure AD](automation-create-aduser-account.md) do zarządzania klasycznym wdrożeniem platformy Azure lub zasobami usługi Azure Resource Manager. Aby zachować zgodność z powrotem dla tych subskrybentów, mechanizm `Add-AzureAccount` uwierzytelniania używany w liczebiu jest poleceniem cmdlet z [zasobem poświadczeń](automation-credentials.md). Zasób reprezentuje użytkownika usługi Active Directory z dostępem do konta platformy Azure.

Tę funkcję można włączyć dla graficznego elementa runbook, dodając zasób poświadczeń do kanwy, a następnie `Add-AzureAccount` działanie, które używa zasobu poświadczeń dla jego danych wejściowych. Zobacz poniższy przykład.

![Działania związane z uwierzytelnianiem](media/automation-graphical-authoring-intro/authentication-activities.png)

Element runbook musi uwierzytelnić się na początku i po każdym punkcie kontrolnym. W związku z `Add-AzureAccount` tym `Checkpoint-Workflow` należy użyć działania po każdym działaniu. Nie trzeba używać dodatkowych poświadczeń działania.

![Dane wyjściowe aktywności](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Eksportowanie i importowanie graficznego podręcznika runbook

Można wyeksportować tylko opublikowaną wersję graficznego eksmisji. Jeśli projekt runbook nie został jeszcze opublikowany, przycisk **Eksportuj** jest wyłączony. Po kliknięciu przycisku **Eksportuj** program runbook zostanie pobrany na komputer lokalny. Nazwa pliku jest zgodna z nazwą systemu runbook z rozszerzeniem **.graphrunbook.**

Można zaimportować graficzny lub graficzny plik runbooka przepływu pracy programu PowerShell, wybierając opcję **Importuj** podczas dodawania ekwiwania. Po wybraniu pliku do zaimportowania można zachować tę samą nazwę lub podać nową. Pole **Typ elementu runbook** wyświetla typ elementu runbook po dokonaniu oceny wybranego pliku. Jeśli spróbujesz wybrać inny typ, który nie jest poprawny, edytor graficzny przedstawia komunikat, w którym zwraca informację, że występują potencjalne konflikty i mogą występować błędy składni podczas konwersji.

![Importowanie ekwidu](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Testowanie graficznego podręcznika runbooka

Każdy graficzny element runbook w usłudze Azure Automation ma wersję roboczą i opublikowaną wersję. Można uruchomić tylko opublikowaną wersję, podczas gdy można edytować tylko wersję roboczą. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza jest gotowa do użycia, należy ją opublikować, która zastępuje bieżącą opublikowaną wersję wersją roboczą.

Wersję roboczą podręcznika runbook można przetestować w witrynie Azure portal, pozostawiając opublikowaną wersję bez zmian. Alternatywnie można przetestować nowy system runbook przed jego opublikowaniem, dzięki czemu można sprawdzić, czy system runbook działa poprawnie przed zastąpieniem wersji. Testowanie śmigiełki wykonuje wersję roboczą i zapewnia, że wszystkie akcje, które wykonuje są zakończone. Nie jest tworzona żadna historia zadań, ale w okienku Dane wyjściowe testu są wyświetlane dane wyjściowe.

Otwórz kontrolkę Testuj dla graficznego uruchomienia, otwierając element runbook do edycji, a następnie klikając **okienko testu**. Formant Testuj monituje o parametry wejściowe i można uruchomić program runbook, klikając przycisk **Start**.

## <a name="publishing-a-graphical-runbook"></a>Publikowanie graficznego podręcznika runbooka

Opublikuj graficzny element runbook, otwierając element runbook do edycji, a następnie klikając pozycję **Publikuj**. Możliwe stany dla ego księgi runbook to:

* Nowy - projekt nie został jeszcze opublikowany. 
* Opublikowany -- projekt został opublikowany.
* W edycji — element runbook został edytowany po jego opublikowaniu, a wersje robocze i opublikowane są różne.

![Stany eksuszu](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Można przywrócić opublikowaną wersję ślikmy likemi. Ta operacja odrzuca wszelkie zmiany wprowadzone od czasu ostatniego opublikowania ślikmu likemi. Zastępuje wersję roboczą podręcznika runbooka wersją opublikowaną.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell Workflow runbook](automation-first-runbook-textual.md).
* Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](automation-first-runbook-graphical.md).
* Aby dowiedzieć się więcej o typach elementów runbook oraz ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](automation-runbook-types.md).
* Aby dowiedzieć się, jak uwierzytelnić się przy użyciu konta Uruchamianie automatyzacji jako, zobacz [Konfigurowanie usługi Azure Run As Account](automation-sec-configure-azure-runas-account.md).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
