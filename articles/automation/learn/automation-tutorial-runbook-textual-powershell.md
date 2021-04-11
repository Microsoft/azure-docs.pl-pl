---
title: Tworzenie elementu Runbook programu PowerShell w Azure Automation
description: W tym artykule przedstawiono sposób tworzenia, testowania i publikowania prostego elementu Runbook programu PowerShell.
keywords: azure powershell, samouczek skryptu programu powershell, automatyzacja programu powershell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 0e5573634edf29698e7b118487fb2ec4e22e8eec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169440"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Samouczek: Tworzenie elementu Runbook programu PowerShell

W tym samouczku przedstawiono proces tworzenia [elementu Runbook programu PowerShell](../automation-runbook-types.md#powershell-runbooks) w usłudze Azure Automation. Elementy Runbook programu PowerShell są oparte na programie Windows PowerShell. Kod elementu Runbook można edytować bezpośrednio przy użyciu edytora tekstu w Azure Portal.

> [!div class="checklist"]
> * Tworzenie prostego elementu Runbook programu PowerShell
> * Testowanie i publikowanie elementu Runbook
> * Uruchamianie i śledzenie stanu zadania elementu Runbook
> * Aktualizowanie elementu Runbook w celu uruchomienia maszyny wirtualnej platformy Azure z parametrami elementu Runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-quickstart-create-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna ona być produkcyjną maszyną wirtualną.
* W razie potrzeby [zaimportuj moduły platformy Azure](../shared-resources/modules.md) lub [Aktualizuj moduły](../automation-update-azure-modules.md) na podstawie używanych poleceń cmdlet.

## <a name="differences-from-powershell-workflow-runbooks"></a>Różnice między elementami Runbook przepływu pracy programu PowerShell

Elementy Runbook programu PowerShell mają taki sam cykl życia, możliwości i zarządzanie jak elementy Runbook przepływu pracy programu PowerShell. Istnieją jednak pewne różnice i ograniczenia.

| Charakterystyka  | Elementy Runbook programu PowerShell | Elementy Runbook przepływu pracy programu PowerShell |
| ------ | ----- | ----- |
| Szybkość | Uruchamiaj szybko, ponieważ nie korzystają z kroku kompilacji. | Uruchamiaj wolniej. |
| Punkty kontrolne | Nie obsługują punktów kontrolnych. Element Runbook programu PowerShell może wznowić operację tylko od początku. | Użyj punktów kontrolnych, które umożliwiają skoroszytowi wznowienie operacji z dowolnego punktu. |
| Wykonywanie polecenia | Obsługa tylko wykonywania szeregowego. | Obsługa wykonywania zarówno sekwencyjnych, jak i równoległych.|
| Obszarze działania | Pojedynczy obszar działania uruchamia wszystko w skrypcie. | Do działania, polecenia lub bloku skryptu można użyć oddzielnego obszaru. |

Oprócz tych różnic elementy Runbook programu PowerShell mają pewne [różnice syntaktyczne](/previous-versions/technet-magazine/dn151046(v=msdn.10)) w elementach Runbook przepływu pracy programu PowerShell.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Zacznij od utworzenia prostego elementu Runbook, który wyświetla tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.

3. Utwórz nowy element Runbook, wybierając pozycję **Utwórz element Runbook**.

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-PowerShell**.

5. W tym przypadku utworzysz [element Runbook programu PowerShell](../automation-runbook-types.md#powershell-runbooks). Wybierz opcję **PowerShell** dla **typu elementu Runbook**.

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W tym samouczku nastąpi bezpośrednie wpisanie kodu do elementu Runbook.

1. Element Runbook jest obecnie pusty. Wpisz `Write-Output "Hello World"` Treść skryptu.

   ![Witaj, świecie](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu Runbook w celu udostępnienia go w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook uruchamia jego wersję roboczą i umożliwia interakcyjne wyświetlanie danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Należy pamiętać, że [zadanie elementu Runbook](../automation-runbook-execution.md) jest tworzone i jego stan jest wyświetlany w okienku.

   Zadanie jest uruchamiane jako kolejkowane, wskazując, że zadanie oczekuje na dostępność procesu roboczego elementu Runbook w chmurze. Stan zmieni się na rozpoczęty, gdy proces roboczy pozyskuje zadanie. Na koniec stan zostanie uruchomiony, gdy element Runbook faktycznie zacznie działać.

4. Po zakończeniu zadania elementu Runbook w okienku test zostaną wyświetlone dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

   ![Dane wyjściowe w okienku testowania](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony element Runbook jest nadal w trybie wersji roboczej. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić element Runbook na stronie elementy Runbook, i pamiętaj, że wartość **Stan tworzenia** jest ustawiona na **Publikowanie**.

3. Przewiń z powrotem w prawo, aby wyświetlić stronę programu **MyFirstRunbook — PowerShell**.
   
   Opcje w górnej części umożliwiają rozpoczęcie elementu Runbook, zaplanowanie przyszłego czasu rozpoczęcia lub utworzenie [elementu webhook](../automation-webhooks.md) , aby można było uruchomić element Runbook za pośrednictwem wywołania http.

4. Wybierz przycisk **Start** , a następnie pozycję **tak** po wyświetleniu monitu o uruchomienie elementu Runbook. 

5. Zostanie otwarte okienko zadań dla zadania elementu Runbook, które zostało utworzone. Mimo że można zamknąć to okienko, pozostaw je otwarte teraz, aby można było obserwować postęp zadania. Stan zadania jest pokazywany w **podsumowaniu zadania**, a możliwe stany są zgodne z opisem dla testowania elementu Runbook.

   ![Podsumowanie zadania](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Po zakończeniu wyświetlania stanu elementu Runbook kliknij pozycję **dane wyjściowe** , aby otworzyć stronę dane wyjściowe, gdzie są `Hello World` wyświetlane.

   ![Dane wyjściowe zadania](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Zamknij stronę wyjściową.

8. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być widoczny tylko `Hello World` w strumieniu danych wyjściowych.

    Należy pamiętać, że w okienku strumienie mogą być wyświetlane inne strumienie zadania elementu Runbook, takie jak pełne strumienie i błędy, jeśli element Runbook zapisze w nich.

   ![Wszystkie dzienniki](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Zamknij okienko strumieni i okienko zadania, aby wrócić do strony MyFirstRunbook-PowerShell.

10. W obszarze **szczegóły** kliknij pozycję **zadania** , aby otworzyć stronę zadania dla tego elementu Runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez element Runbook. Tylko jedno zadanie jest widoczne na liście, ponieważ zadanie jest uruchamiane tylko raz.

   ![Lista zadań](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Kliknij nazwę zadania, aby otworzyć okienko zadania, które zostało wyświetlone po uruchomieniu elementu Runbook. To okienko służy do wyświetlania szczegółów wszystkich zadań utworzonych dla elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. W tym celu element Runbook musi mieć możliwość uwierzytelniania przy użyciu konta Uruchom jako, które zostało utworzone automatycznie podczas tworzenia konta usługi Automation.

Jak pokazano w poniższym przykładzie, połączenie Uruchom jako jest nawiązywane przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć `AzContext` parametru z poleceniem [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](../automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.

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

1. Otwórz Edytor tekstów, klikając pozycję **Edytuj** na stronie MyFirstRunbook-PowerShell.

2. Wiersz nie jest `Write-Output` już potrzebny. Po prostu przejdź dalej i usuń go.

3. Wpisz lub skopiuj i wklej poniższy kod, który obsługuje uwierzytelnianie przy użyciu konta Uruchom jako usługi Automation.

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

4. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.

5. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinna zostać wyświetlona informacja wyjściowa podobna do poniższego, wyświetlając podstawowe informacje z Twojego konta. Dane wyjściowe potwierdzają, że konto Uruchom jako jest prawidłowe.

   ![Uwierzytelnianie](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy element Runbook jest uwierzytelniany w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i w tym momencie tylko kod w elemencie Runbook.

1. Do skryptu elementu Runbook Dodaj polecenie cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) , aby uruchomić maszynę wirtualną. Jak pokazano poniżej, polecenie cmdlet uruchamia maszynę wirtualną o nazwie `VMName` i z grupą zasobów o nazwie `ResourceGroupName` .

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

2. Zapisz element Runbook, a następnie kliknij pozycję **okienko testowania** , aby można było go przetestować.

3. Kliknij przycisk **Rozpocznij** , aby rozpocząć test. Po zakończeniu upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter"></a>Krok 7. Dodawanie parametru wejściowego

Element Runbook aktualnie uruchamia maszynę wirtualną, która jest zakodowana w elemencie Runbook. Element Runbook jest bardziej użyteczny, jeśli określisz maszynę wirtualną, gdy element Runbook zostanie uruchomiony. Dodajmy do elementu Runbook parametry wejściowe w celu zapewnienia tej funkcjonalności.

1. W edytorze tekstu zmodyfikuj `Start-AzVM` polecenie cmdlet, aby użyć zmiennych dla parametrów `VMName` i `ResourceGroupName` . 

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

7. Wpisz wartości dla **VMNAME** i **RESOURCEGROUPNAME** dla maszyny wirtualnej, która ma zostać uruchomiona, a następnie kliknij przycisk **OK**.

    ![Przekazywanie parametru](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Po zakończeniu działania elementu Runbook upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Tworzenie graficznego elementu Runbook](automation-tutorial-runbook-graphical.md).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz temat [Tworzenie elementu Runbook przepływu pracy programu PowerShell](automation-tutorial-runbook-textual.md).
* Aby dowiedzieć się więcej o typach elementów Runbook i ich zaletach i ograniczeniach, zobacz [Azure Automation typów elementów Runbook](../automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [natywną obsługę skryptów programu PowerShell w Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
