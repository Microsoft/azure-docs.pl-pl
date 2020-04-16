---
title: Mój pierwszy system runbook programu PowerShell w usłudze Azure Automation
description: W tym samouczku przedstawiono procesy tworzenia, testowania i publikowania prostego elementu Runbook programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0df2680a9f17fc0af950b0ce744a655348b4cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406063"
---
# <a name="my-first-powershell-runbook"></a>Mój pierwszy element Runbook programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Zacznij od prostego śmiętu, który można przetestować i opublikować, podczas nauki śledzenia stanu zadania likścień. Następnie zmodyfikuj element runbook, aby faktycznie zarządzać zasobami platformy Azure, w tym przypadku uruchamianie maszyny wirtualnej platformy Azure. Ukończ samouczek, aby uczynić system runbook bardziej niezawodnym, dodając parametry zestawu runbook.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-quickstart-create-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być maszyna wirtualna produkcyjna.
* W razie potrzeby [zaimportuj moduły platformy Azure](shared-resources/modules.md) lub [zaktualizuj moduły](automation-update-azure-modules.md) na podstawie używanych poleceń cmdlet.

## <a name="differences-from-powershell-workflow-runbooks"></a>Różnice w stosunku do uruchomieniu przepływów pracy programu PowerShell

Runbooki programu PowerShell mają taki sam cykl życia, możliwości i zarządzanie jak runbooki przepływu pracy programu PowerShell. Istnieją jednak pewne różnice i ograniczenia.

| Charakterystyka  | Runbooki programu PowerShell | Runy przepływu pracy programu PowerShell |
| ------ | ----- | ----- |
| Szybkość | Uruchom szybko, ponieważ nie używają kroku kompilacji. | Działaj wolniej. |
| Punkty kontrolne | Nie obsługa punktów kontrolnych. Runbook programu PowerShell można wznowić działanie tylko od początku. | Użyj punktów kontrolnych, które umożliwiają skoroszytowi wznowienie operacji z dowolnego punktu. |
| Wykonanie polecenia | Obsługa tylko wykonywania szeregowego. | Obsługa wykonywania szeregowego i równoległego.|
| Obszar runspace | Pojedynczy obszar runspace uruchamia wszystko w skrypcie. | Oddzielny obszar działania może służyć do działania, polecenia lub bloku skryptów. |

Oprócz tych różnic, runbooki programu PowerShell mają pewne [różnice składniowe](https://technet.microsoft.com/magazine/dn151046.aspx) od uruchomieniu przepływów pracy programu PowerShell.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Zacznij od utworzenia prostego podstawowego `Hello World`podstawowego podstawowego tekstu .

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmięty.
3. Utwórz nowy projekt runbook, wybierając **pozycję Utwórz projekt runbook**.
4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-PowerShell**.
5. W takim przypadku zamierzasz utworzyć [projekt runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks). Wybierz pozycję **PowerShell** dla **typu uruchomieniu**.
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W tym samouczku wpiszesz kod bezpośrednio w bieśbiu.

1. Twój numer runbook jest obecnie pusty. Wpisz `Write-Output "Hello World"` w treści skryptu.

   ![Witaj, świecie](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Krok 3 - Testowanie runbooka

Przed opublikowaniem likemi, aby udostępnić go w produkcji, należy przetestować go, aby upewnić się, że działa poprawnie. Testowanie uruchomieniu uruchamia jego wersji roboczej i umożliwia interaktywne wyświetlanie jego danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
3. Należy zauważyć, że [zadanie runbook](automation-runbook-execution.md) jest tworzony i jego stan jest wyświetlany w okienku.

   Stan zadania rozpoczyna się jako w kolejce, co oznacza, że zadanie oczekuje na udostępnienie pracownika przewodniczącego w chmurze. Stan zmienia się na Rozpoczęcie, gdy pracownik żąda zadania. Na koniec stan staje się uruchomiony, gdy runbook faktycznie zaczyna działać.

4. Po zakończeniu zadania runbook okienko Test wyświetla jego dane wyjściowe. W takim przypadku `Hello World`widzisz .

   ![Dane wyjściowe w okienku testowania](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony projekt runbook jest nadal w trybie roboczym. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.
1. Przewiń w lewo, aby wyświetlić runbook na stronie Runbooks, i zwróć uwagę, że wartość **Stan autora** jest ustawiona na **Opublikowano**.
1. Przewiń z powrotem w prawo, aby wyświetlić stronę **myfirstRunbook-PowerShell**.
   
   Opcje u góry umożliwiają teraz uruchomienie elementu runbook, zaplanowanie przyszłej godziny rozpoczęcia lub utworzenie [elementu webhook,](automation-webhooks.md) aby można było uruchomić element runbook za pośrednictwem wywołania HTTP.
1. Wybierz **przycisk Start,** a następnie **przycisk Tak** po wyświetleniu monitu o uruchomienie śmięty. 
1. Okienko Zadania jest otwierane dla zadania uruchomieniu, które zostało utworzone. Chociaż możesz zamknąć to okienko, pozostaw je teraz otwarte, aby można było obserwować postęp zadania. Stan zadania jest wyświetlany w **podsumowaniu zadań,** a możliwe stany są opisane do testowania przewodniczącego.

   ![Podsumowanie zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Gdy stan runbooka zostanie wyświetlony, kliknij przycisk **Dane wyjściowe,** aby otworzyć stronę Wyjście, na której można wyświetlić `Hello World` wyświetlone.

   ![Dane wyjściowe zadania](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zamknij stronę Dane wyjściowe.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być `Hello World` widoczny tylko w strumieniu wyjściowym.

    Należy zauważyć, że okienko Strumienie można wyświetlić inne strumienie dla zadania przewodniczącego, takich jak pełne i strumienie błędów, jeśli runbook zapisuje do nich.

   ![Wszystkie dzienniki](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zamknij okienko Strumienie i okienko zadania, aby powrócić do strony MyFirstRunbook-PowerShell.
1. W obszarze **Szczegóły**kliknij pozycję **Zadania,** aby otworzyć stronę Zadania dla tego liksu. Ta strona zawiera listę wszystkich zadań utworzonych przez system runbook. Na liście powinna być wyświetlana tylko jedno zadanie, ponieważ zadanie zostało uruchomione tylko raz.

   ![Lista zadań](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które zostało wyświetlone podczas uruchamiania likwidu. To okienko służy do wyświetlania szczegółów każdego zadania utworzonego dla egonatu.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Aby to zrobić, system runbook musi być w stanie uwierzytelnić przy użyciu uruchom jako konto, które zostało automatycznie utworzone podczas tworzenia konta automatyzacji.

Jak pokazano w poniższym przykładzie, połączenie Uruchom jako jest nawiązywane za pomocą polecenia cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Jeśli zarządzasz zasobami w wielu subskrypcjach, `AzContext` musisz użyć parametru z [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Otwórz edytor tekstu, klikając pozycję **Edytuj** na stronie MyFirstRunbook-PowerShell.
1. Nie potrzebujesz już `Write-Output` tej linii. Po prostu śmiało i usuń go.
1. Wpisz lub skopiuj i wklej następujący kod, który obsługuje uwierzytelnianie za pomocą konta Automation Run As.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Kliknij **okienko testu,** aby można było przetestować projekt runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po jego zakończeniu powinieneś zobaczyć dane wyjściowe podobne do następujących, wyświetlające podstawowe informacje z konta. To dane wyjściowe potwierdza, że uruchom jako konto jest prawidłowy.

   ![Uwierzytelnianie](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy twój identyfikator runbook uwierzytelnia się w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Na razie możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i po prostu zakodować tę nazwę w rundzie runbook.

1. Do skryptu umnienia należy dodać polecenie cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) aby uruchomić maszynę wirtualną. Jak pokazano poniżej, polecenie cmdlet uruchamia `VMName` maszynę wirtualną `ResourceGroupName`o nazwie i z grupą zasobów o nazwie .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Zapisz runbook, a następnie kliknij **okienko testu,** aby można było go przetestować.
1. Kliknij **przycisk Start,** aby rozpocząć test. Po jego zakończeniu upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter"></a>Krok 7 - Dodawanie parametru wejściowego

Twój projekt runbook obecnie uruchamia maszynę wirtualną, która została zakodowane na stałe w bieślicie. Podręcznik jest bardziej przydatne, jeśli określisz maszynę wirtualną, gdy jest uruchamiany projekt runbook. Dodajmy parametry wejściowe do zestawu runbook, aby zapewnić tę funkcjonalność.

1. W edytorze tekstowym zmodyfikuj `Start-AzVM` polecenie cmdlet, aby użyć zmiennych dla parametrów `VMName` i `ResourceGroupName`. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
1. Zamknij okienko testowania.
1. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
1. Zatrzymaj maszynę wirtualną, która została uruchomiona wcześniej.
1. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. 
1. Wpisz wartości **VMNAME** i **RESOURCEGROUPNAME** dla maszyny wirtualnej, którą zamierzasz uruchomić, a następnie kliknij przycisk **OK**.<br><br> ![Przekazywanie parametru](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Po zakończeniu uruchomieniu upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów referencyjnych i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](automation-first-runbook-graphical.md).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell Workflow runbook](automation-first-runbook-textual.md).
* Aby dowiedzieć się więcej o typach elementów runbook oraz ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [Natywna obsługa skryptów programu PowerShell w usłudze Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
