---
title: Tworzenie elementu Runbook przepływu pracy programu PowerShell w programie Azure Automation
description: W tym artykule przedstawiono sposób tworzenia, testowania i publikowania prostego elementu Runbook przepływu pracy programu PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890818"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Samouczek: Tworzenie elementu Runbook przepływu pracy programu PowerShell

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Elementy Runbook przepływu pracy programu PowerShell są tekstowymi elementami Runbook opartymi na przepływie pracy programu Windows PowerShell. Kod elementu Runbook można utworzyć i edytować za pomocą edytora tekstu w Azure Portal. 

> [!div class="checklist"]
> * Tworzenie prostego elementu Runbook przepływu pracy programu PowerShell
> * Testowanie i publikowanie elementu Runbook
> * Uruchamianie i śledzenie stanu zadania elementu Runbook
> * Aktualizowanie elementu Runbook w celu uruchomienia maszyny wirtualnej platformy Azure z parametrami elementu Runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna ona być produkcyjną maszyną wirtualną.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook

Zacznij od utworzenia prostego elementu Runbook, który wyświetla tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z tych elementów zawartości to moduły automatycznie dołączone do nowego konta usługi Automation. Należy również mieć zasób poświadczeń skojarzony z subskrypcją.
 
2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.

3. Utwórz nowy element Runbook, wybierając pozycję **Utwórz element Runbook**.

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Workflow**.

5. W tym przypadku utworzysz [element Runbook przepływu pracy programu PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks). Wybierz **przepływ pracy programu PowerShell** dla **typu elementu Runbook**.

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i zasoby z kontrolki biblioteka i dodać je do elementu Runbook przy użyciu wszelkich powiązanych parametrów. W tym samouczku wpiszesz kod bezpośrednio w elemencie Runbook.

1. Element Runbook jest obecnie pusty tylko za pomocą `Workflow` słowa kluczowego Required, nazwy elementu Runbook i nawiasów klamrowych, które obejmować cały przepływ pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Wpisz `Write-Output "Hello World"` między nawiasami klamrowymi.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu Runbook w celu udostępnienia go w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook uruchamia jego wersję roboczą i umożliwia interakcyjne wyświetlanie danych wyjściowych.

1. Wybierz **okienko testowania** , aby otworzyć okienko testowania.

2. Kliknij przycisk **Rozpocznij** , aby rozpocząć test, z testowaniem tylko włączonej opcji.

3. Należy pamiętać, że [zadanie elementu Runbook](../automation-runbook-execution.md) jest tworzone i jego stan jest wyświetlany w okienku.

   Zadanie jest uruchamiane jako kolejkowane, wskazując, że zadanie oczekuje na dostępność procesu roboczego elementu Runbook w chmurze. Stan zmieni się na rozpoczęty, gdy proces roboczy pozyskuje zadanie. Na koniec stan zostanie uruchomiony, gdy element Runbook faktycznie zacznie działać.

4. Po zakończeniu zadania elementu Runbook w okienku test zostaną wyświetlone dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

   ![Witaj, świecie](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony element Runbook nadal działa w trybie roboczym. Należy opublikować ją przed uruchomieniem jej w środowisku produkcyjnym. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić element Runbook na stronie **elementów Runbook** , a następnie Zwróć uwagę, że pole **Stan tworzenia** ma wartość **opublikowany**.

3. Przewiń z powrotem w prawo, aby wyświetlić stronę **MyFirstRunbook-Workflow**.

   Opcje w górnej części umożliwiają rozpoczęcie elementu Runbook, zaplanowanie przyszłego czasu rozpoczęcia lub utworzenie [elementu webhook](../automation-webhooks.md) , aby można było uruchomić element Runbook za pośrednictwem wywołania http.

4. Wybierz przycisk **Start** , a następnie pozycję **tak** po wyświetleniu monitu o uruchomienie elementu Runbook.

   ![Uruchamianie elementu Runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Zostanie otwarte okienko zadań dla zadania elementu Runbook, które zostało utworzone. W takim przypadku Pozostaw otwarte okienko, aby można było obserwować postęp zadania.

6. Należy pamiętać, że stan zadania jest pokazywany w **podsumowaniu zadania**. Ten stan jest zgodny z stanami, które zostały podane podczas testowania elementu Runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Zrzut ekranu przedstawiający okienko zadania elementu Runbook z wyróżnioną sekcją podsumowania zadań.":::

7. Po zakończeniu wyświetlania stanu elementu Runbook kliknij pozycję **dane wyjściowe**. Zostanie otwarta strona wyjście, na której będzie widoczny `Hello World` komunikat.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Zrzut ekranu przedstawiający okienko zadania elementu Runbook z wyróżnionym przyciskiem wyjście.":::

8. Zamknij stronę wyjściową.

9. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być widoczny tylko `Hello World` w strumieniu danych wyjściowych. Należy pamiętać, że w okienku strumienie mogą być wyświetlane inne strumienie zadania elementu Runbook, takie jak pełne strumienie i błędy, jeśli element Runbook zapisze w nich.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Zrzut ekranu przedstawiający okienko zadania elementu Runbook z wyróżnionym przyciskiem wszystkie dzienniki.":::

10. Zamknij okienko strumieni i okienko zadania, aby powrócić do strony MyFirstRunbook.

11. Kliknij pozycję **zadania** w obszarze **zasoby** , aby otworzyć stronę zadania dla tego elementu Runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez element Runbook. Tylko jedno zadanie jest widoczne na liście, ponieważ uruchomiono zadanie tylko raz.

   ![Stanowiska](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kliknij nazwę zadania, aby otworzyć okienko zadania, które zostało wyświetlone po uruchomieniu elementu Runbook. To okienko służy do wyświetlania szczegółów wszystkich zadań utworzonych dla elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie można tego zrobić, chyba że zostanie on uwierzytelniony przy użyciu poświadczeń dla subskrypcji. Uwierzytelnianie używa polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

>[!NOTE]
>Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](../automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.

1. Przejdź do strony MyFirstRunbook-Workflow i Otwórz Edytor tekstów, klikając pozycję **Edytuj**.

2. Usuń `Write-Output` wiersz.

3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.

4. Wpisz lub skopiuj i wklej poniższy kod, który obsługuje uwierzytelnianie przy użyciu konta Uruchom jako usługi Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.

6. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinna zostać wyświetlona informacja wyjściowa podobna do poniższego, wyświetlając podstawowe informacje z Twojego konta. Ta akcja potwierdza, że poświadczenie jest prawidłowe.

   ![Uwierzytelnianie](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy element Runbook jest uwierzytelniany w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz zakodowana tę nazwę w elemencie Runbook. Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć `AzContext` parametru z poleceniem cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Podaj nazwę i nazwę grupy zasobów maszyny wirtualnej do uruchomienia, wprowadzając wywołanie polecenia cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) , jak pokazano poniżej. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Zapisz element Runbook, a następnie kliknij pozycję **okienko testowania** , aby można było go przetestować.

3. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu Sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook

Element Runbook aktualnie uruchamia maszynę wirtualną, która została stałe w elemencie Runbook. Będzie bardziej użyteczna, jeśli możesz określić maszynę wirtualną, gdy element Runbook zostanie uruchomiony. Dodajmy do elementu Runbook parametry wejściowe w celu zapewnienia tej funkcjonalności.

1. Dodaj zmienne dla `VMName` parametrów i `ResourceGroupName` do elementu Runbook, a następnie użyj zmiennych z `Start-AzVM` poleceniem cmdlet, jak pokazano poniżej.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które są w teście.

3. Zamknij okienko testowania.

4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.

5. Zatrzymaj uruchomioną maszynę wirtualną.

6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. 

7. Wpisz wartości dla **VMNAME** i **RESOURCEGROUPNAME** dla maszyny wirtualnej, która ma zostać uruchomiona.

   ![Uruchamianie elementu Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Po zakończeniu działania elementu Runbook Sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Tworzenie graficznego elementu Runbook](automation-tutorial-runbook-graphical.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz [Tworzenie elementu Runbook programu PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Aby dowiedzieć się więcej o typach elementów Runbook i ich zaletach i ograniczeniach, zobacz [Azure Automation typów elementów Runbook](../automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [natywną obsługę skryptów programu PowerShell w Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
