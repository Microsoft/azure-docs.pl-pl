---
title: Tworzenie runbook programu PowerShell w programie Azure Automation
description: W tym artykule nauczysz się tworzyć, testować i publikować prosty podręcznik programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 958cbf8b7b03e2dd9e4b0e03ba178ffe180bfb48
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831262"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Samouczek: tworzenie runbook programu PowerShell

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](../automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Podręczniki Runbook programu PowerShell są oparte na Windows PowerShell. Kod runbook można edytować bezpośrednio przy użyciu edytora tekstów w Azure Portal.

> [!div class="checklist"]
> * Tworzenie prostego podręcznika programu PowerShell
> * Testowanie i publikowanie runbook
> * Uruchamianie i śledzenie stanu zadania runbook
> * Aktualizowanie runbook w celu uruchomienia maszyny wirtualnej platformy Azure przy użyciu parametrów runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-quickstart-create-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być produkcyjna maszyna wirtualna.
* W razie potrzeby [zaimportuj moduły platformy Azure](../shared-resources/modules.md) [lub zaktualizuj moduły](../automation-update-azure-modules.md) w oparciu o polecenia cmdlet, których używasz.

## <a name="differences-from-powershell-workflow-runbooks"></a>Różnice w perspektywie procesów Runbook przepływu pracy programu PowerShell

Środowiska Runbook programu PowerShell mają taki sam cykl życia, możliwości i zarządzanie jak w przypadku tych samych runbook przepływu pracy programu PowerShell. Istnieją jednak pewne różnice i ograniczenia.

| Charakterystyka  | PowerShell Runbook | Runbook przepływu pracy programu PowerShell |
| ------ | ----- | ----- |
| Szybkość | Uruchamiaj je szybko, ponieważ nie korzystają z kroku kompilacji. | Działa wolniej. |
| Punkty kontrolne | Nie obsługują punktów kontrolnych. Runbook programu PowerShell może wznowić działanie tylko od początku. | Użyj punktów kontrolnych, które umożliwiają wznowienie działania skoroszytu z dowolnego punktu. |
| Wykonywanie polecenia | Obsługa tylko wykonywania szeregowego. | Obsługa wykonywania szeregowego i równoległego.|
| Obszar działania | Pojedynczy obszar działania uruchamia wszystko w skrypcie. | Oddzielnego miejsca uruchamiania można użyć dla działania, polecenia lub bloku skryptu. |

Oprócz tych różnic, niektóre składniowe [](/previous-versions/technet-magazine/dn151046(v=msdn.10)) różnice między nimi występują w przypadku podręczników Runbook przepływu pracy programu PowerShell.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Rozpocznij od utworzenia prostego runbook, który wyprowadza tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. Wybierz **pozycję Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.

3. Utwórz nowy runbook, wybierając **pozycję Utwórz.**

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-PowerShell**.

5. W tym przypadku utworzysz [runbook programu PowerShell.](../automation-runbook-types.md#powershell-runbooks) Wybierz **pozycję PowerShell** jako **typ runbook.**

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W tym samouczku wpiszemy kod bezpośrednio do runbook.

1. Twój runbook jest obecnie pusty. Wpisz `Write-Output "Hello World"` w treści skryptu.

   ![Witaj, świecie](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Krok 3. Testowanie runbook

Przed opublikowaniem podręcznika Runbook w celu jego udostępnić w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementów Runbook uruchamia jego wersję roboczą i umożliwia interaktywne wyświetlanie jego danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Pamiętaj, że [zadanie runbook](../automation-runbook-execution.md) jest tworzone, a jego stan jest wyświetlany w okienku.

   Zadanie ma stan W kolejce, co oznacza, że zadanie oczekuje, aż proces roboczy runbook w chmurze stanie się dostępny. Stan zmienia się na Uruchamianie, gdy proces roboczy oświadczenia zadania. Na koniec stanem będzie Uruchomiony, gdy faktycznie zacznie działać ten runbook.

4. Po zakończeniu zadania runbook w okienku Test zostaną wyświetlone jego dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

   ![Dane wyjściowe w okienku testowania](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony przez Ciebie runbook jest nadal w trybie wersji roboczej. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić ten runbook na stronie Runbook, i zwróć uwagę, że wartość **Stan** tworzenia jest ustawiona na **Opublikowano.**

3. Przewiń w prawo, aby wyświetlić stronę **myFirstRunbook-PowerShell.**
   
   Opcje w górnej części pozwalają uruchomić element Runbook teraz, zaplanować przyszły czas rozpoczęcia lub utworzyć element [webhook,](../automation-webhooks.md) aby element Runbook można było uruchomić za pośrednictwem wywołania HTTP.

4. Wybierz **pozycję Uruchom,** a następnie wybierz pozycję **Tak** po wyświetleniu monitu o uruchomienie runbook. 

5. Zostanie otwarte okienko Zadania dla utworzonego zadania runbook. Mimo że możesz zamknąć to okienko, pozostaw je teraz otwarte, aby można było obserwować postęp zadania. Stan zadania jest wyświetlany w **podsumowaniu zadania,** a możliwe stany są zgodnie z opisem na temat testowania tego runbook.

   ![Podsumowanie zadania](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Gdy stan runbook będzie wyświetlany jako Ukończono, kliknij pozycję **Dane wyjściowe,** aby otworzyć stronę Dane wyjściowe, na której będą wyświetlane `Hello World` informacje.

   ![Dane wyjściowe zadania](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Zamknij stronę Dane wyjściowe.

8. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinny być one dostępne `Hello World` tylko w strumieniu wyjściowym.

    Należy pamiętać, że w okienku Strumienie mogą być wyświetlane inne strumienie dla zadania runbook, takie jak strumienie Pełne i Błędy, jeśli są w nich zapisywane.

   ![Wszystkie dzienniki](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Zamknij okienko Strumienie i okienko Zadania, aby wrócić do MyFirstRunbook-PowerShell strony.

10. W **obszarze Szczegóły** kliknij pozycję **Zadania,** aby otworzyć stronę Zadania dla tego runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez twój runbook. Na liście powinno być wyświetlane tylko jedno zadanie, ponieważ zadanie jest uruchamiane tylko raz.

   ![Lista zadań](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które było przeglądane podczas uruchamiania tego runbook. Użyj tego okienka, aby wyświetlić szczegóły dowolnego zadania utworzonego dla tego runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. W tym celu musi być możliwe uwierzytelnienie przy użyciu konta Uruchom jako, które zostało automatycznie utworzone podczas tworzenia konta usługi Automation.

Jak pokazano w poniższym przykładzie, połączenie Uruchom jako jest nawiązywane za pomocą polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć parametru `AzContext` z [get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Dla podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` i są aliasami dla polecenia `Connect-AzAccount` . Możesz użyć tych polecenia cmdlet lub zaktualizować [moduły](../automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto usługi Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Otwórz edytor tekstów, klikając pozycję **Edytuj** MyFirstRunbook-PowerShell stronie.

2. Wiersz nie jest `Write-Output` już potrzebny. Wystarczy go usunąć.

3. Wpisz lub skopiuj i wklej następujący kod, który obsługuje uwierzytelnianie przy użyciu konta Uruchom jako usługi Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Kliknij **pozycję Okienko testowania,** aby przetestować ten runbook.

5. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny zostać wyświetlony dane wyjściowe podobne do następujących, z wyświetlonymi podstawowymi informacjami z konta. Te dane wyjściowe potwierdzają, że konto Uruchom jako jest prawidłowe.

   ![Uwierzytelnianie](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy twój runbook uwierzytelnia się w subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i na razie po prostu zakodować tę nazwę w ramach runbook.

1. Do skryptu runbook dodaj polecenie cmdlet [Start-AzVM,](/powershell/module/Az.Compute/Start-AzVM) aby uruchomić maszynę wirtualną. Jak pokazano poniżej, polecenie cmdlet uruchamia maszynę wirtualną o nazwie i `VMName` grupie zasobów o nazwie `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Zapisz go, a następnie kliknij okienko **Test,** aby go przetestować.

3. Kliknij **przycisk Start,** aby rozpocząć test. Po zakończeniu upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter"></a>Krok 7. Dodawanie parametru wejściowego

Twój runbook aktualnie uruchamia maszynę wirtualną, która została zakodowana w tym runbook. Ten runbook jest bardziej przydatny, jeśli określisz maszynę wirtualną podczas jego uruchamiania. Dodajmy parametry wejściowe do runbook, aby zapewnić tę funkcjonalność.

1. W edytorze tekstowym zmodyfikuj polecenie cmdlet , aby `Start-AzVM` używać zmiennych dla parametrów i `VMName` `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.

3. Zamknij okienko testowania.

4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.

5. Zatrzymaj maszynę wirtualną, która została wcześniej uruchomiona.

6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. 

7. Wpisz wartości **VMNAME** i **RESOURCEGROUPNAME** dla maszyny wirtualnej, która ma być uruchamiana, a następnie kliknij przycisk **OK.**

    ![Przekazywanie parametru](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Po zakończeniu pracy z programem Runbook upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym informacje o językach i modułach szkoleniowych, zobacz Dokumenty [programu PowerShell.](/powershell/scripting/overview)
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Tworzenie graficznego element runbook.](automation-tutorial-runbook-graphical.md)
* Aby rozpocząć pracę z runbook przepływu pracy programu PowerShell, zobacz [Tworzenie runbook przepływu pracy programu PowerShell.](automation-tutorial-runbook-textual.md)
* Aby dowiedzieć się więcej o typach elementów Runbook oraz ich zaletach i ograniczeniach, zobacz [Azure Automation runbook](../automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [Natywna obsługa skryptów programu PowerShell w Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
