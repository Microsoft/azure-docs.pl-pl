---
title: Tworzenie runbook przepływu pracy programu PowerShell w programie Azure Automation
description: W tym artykule nauczysz się tworzyć, testować i publikować prosty runbook przepływu pracy programu PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 45c7bb19e3f48fedf93fbb147a3014e09914a475
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831280"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Samouczek: tworzenie runbook przepływu pracy programu PowerShell

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Pliki Runbook przepływu pracy programu PowerShell są tekstami runbook opartymi na Windows PowerShell Workflow. Kod runbook można tworzyć i edytować przy użyciu edytora tekstów w Azure Portal. 

> [!div class="checklist"]
> * Tworzenie prostego runbook przepływu pracy programu PowerShell
> * Testowanie i publikowanie runbook
> * Uruchamianie i śledzenie stanu zadania runbook
> * Aktualizowanie runbook w celu uruchomienia maszyny wirtualnej platformy Azure przy użyciu parametrów runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być produkcyjna maszyna wirtualna.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook

Rozpocznij od utworzenia prostego runbook, który wyprowadza tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość tych zasobów to moduły automatycznie uwzględniane na nowym koncie usługi Automation. Z subskrypcją powinien być również skojarzony zasób Poświadczenie.
 
2. Wybierz **pozycję Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.

3. Utwórz nowy runbook, wybierając **pozycję Utwórz.**

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Workflow**.

5. W tym przypadku utworzysz runbook przepływu pracy programu [PowerShell.](../automation-runbook-types.md#powershell-workflow-runbooks) Wybierz **pozycję Przepływ pracy programu PowerShell** dla typu **runbook**.

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w programie Runbook lub wybrać polecenia cmdlet, runbook i zasoby z kontrolki Biblioteka i dodać je do tego obiektu Runbook z dowolnymi powiązanymi parametrami. W tym samouczku wpiszemy kod bezpośrednio do runbook.

1. Twój podręcznik Runbook jest obecnie pusty i ma tylko wymagane słowo kluczowe, nazwę i nawiasy klamrowe, w których znajduje się `Workflow` cały przepływ pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Wpisz `Write-Output "Hello World"` między nawiasami klamrowych.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem podręcznika Runbook w celu jego udostępnić w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementów Runbook uruchamia wersję roboczą i umożliwia interaktywne wyświetlanie danych wyjściowych.

1. Wybierz **pozycję Okienko testowania,** aby otworzyć okienko testowania.

2. Kliknij **przycisk Start,** aby rozpocząć test, testując jedyną włączoną opcję.

3. Należy pamiętać, [że zadanie runbook](../automation-runbook-execution.md) zostanie utworzone, a jego stan zostanie wyświetlony w okienku.

   Zadanie ma stan W kolejce, co oznacza, że zadanie oczekuje na dostęp do procesu roboczego runbook w chmurze. Stan zmienia się na Uruchamianie, gdy proces roboczy oświadczenia zadania. Na koniec stanem będzie Uruchomiony, gdy faktycznie zacznie działać ten runbook.

4. Po zakończeniu zadania runbook w okienku Test zostaną wyświetlone jego dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

   ![Witaj, świecie](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony przez Ciebie runbook jest nadal w trybie wersji roboczej. Należy opublikować go przed uruchomieniem go w środowisku produkcyjnym. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić ten runbook na stronie **Runbook** i zwróć uwagę, że pole **Stan** tworzenia ma wartość **Opublikowano.**

3. Przewiń w prawo, aby wyświetlić stronę **myFirstRunbook-Workflow.**

   Opcje w górnej części pozwalają uruchomić element Runbook teraz, zaplanować przyszły czas rozpoczęcia lub utworzyć element [webhook,](../automation-webhooks.md) aby element Runbook można było uruchomić za pośrednictwem wywołania HTTP.

4. Po **wyświetleniu** **monitu** o uruchomienie runbook wybierz pozycję Uruchom, a następnie wybierz pozycję Tak.

   ![Uruchamianie elementu Runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Zostanie otwarte okienko Zadania dla utworzonego zadania runbook. W takim przypadku pozostaw okienko otwarte, aby można było obserwować postęp zadania.

6. Zwróć uwagę, że stan zadania jest wyświetlany w **podsumowaniu zadania.** Ten stan odpowiada stanom, które widzieliśmy podczas testowania runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Zrzut ekranu przedstawiający okienko zadania runbook z wyróżniona sekcją Podsumowanie zadania.":::

7. Gdy stan runbook będzie się wyświetlać Ukończono, kliknij pozycję **Output (Dane wyjściowe).** Zostanie otwarta strona Dane wyjściowe, na której można zobaczyć `Hello World` komunikat.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Zrzut ekranu przedstawiający okienko zadania runbook z wyróżnionym przyciskiem Dane wyjściowe.":::

8. Zamknij stronę Dane wyjściowe.

9. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinny być one dostępne `Hello World` tylko w strumieniu wyjściowym. Należy pamiętać, że w okienku Strumienie mogą być wyświetlane inne strumienie zadania runbook, takie jak pełne strumienie i błędy, jeśli są w nich zapisywane.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Zrzut ekranu przedstawiający okienko zadania runbook z wyróżnionym przyciskiem Wszystkie dzienniki.":::

10. Zamknij okienko Strumienie i okienko Zadania, aby wrócić do strony MyFirstRunbook.

11. Kliknij **pozycję Zadania** w obszarze **Zasoby,** aby otworzyć stronę Zadania dla tego runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez twój runbook. Na liście powinno być wyświetlane tylko jedno zadanie, ponieważ zadanie jest uruchamiane tylko raz.

   ![Stanowiska](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które było przeglądane podczas uruchamiania tego runbook. Użyj tego okienka, aby wyświetlić szczegóły dowolnego zadania utworzonego dla tego runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie można tego zrobić, chyba że uwierzytelnia się przy użyciu poświadczeń dla subskrypcji. Uwierzytelnianie używa polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

>[!NOTE]
>W przypadku podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Możesz użyć tych polecenia cmdlet lub [zaktualizować moduły](../automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto usługi Automation.

1. Przejdź do strony MyFirstRunbook-Workflow i otwórz edytor tekstów, klikając pozycję **Edytuj**.

2. Usuń `Write-Output` wiersz.

3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.

4. Wpisz lub skopiuj i wklej następujący kod, który obsługuje uwierzytelnianie przy użyciu konta Uruchom jako usługi Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Kliknij **pozycję Okienko testowania,** aby przetestować ten runbook.

6. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny zostać wyświetlony dane wyjściowe podobne do następujących, z wyświetlonymi podstawowymi informacjami z konta. Ta akcja potwierdza, że poświadczenie jest prawidłowe.

   ![Uwierzytelnianie](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy twój runbook uwierzytelnia się w subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz natłokusz tę nazwę w ramach runbook. Jeśli zarządzasz zasobami w wielu subskrypcjach, musisz użyć parametru z poleceniem `AzContext` cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Podaj nazwę i nazwę grupy zasobów maszyny wirtualnej, aby rozpocząć od wprowadzenia wywołania polecenia cmdlet [Start-AzVM,](/powershell/module/Az.Compute/Start-AzVM) jak pokazano poniżej. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Zapisz pozycję Runbook, a następnie kliknij **pozycję Okienko testowania,** aby można było go przetestować.

3. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook

Twój runbook aktualnie uruchamia maszynę wirtualną, która jest w tym wykodowana na dysku. Będzie to bardziej przydatne, jeśli można określić maszynę wirtualną po uruchomieniu tego runbook. Dodajmy parametry wejściowe do runbook, aby zapewnić tę funkcjonalność.

1. Dodaj zmienne dla parametrów i do runbook i użyj zmiennych z poleceniem cmdlet , jak `VMName` `ResourceGroupName` `Start-AzVM` pokazano poniżej.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które są w teście.

3. Zamknij okienko testowania.

4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.

5. Zatrzymaj uruchominą maszynę wirtualną.

6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. 

7. Wpisz wartości **VMNAME i** **RESOURCEGROUPNAME** dla maszyny wirtualnej, która ma być uruchamiana.

   ![Uruchamianie elementu Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Po zakończeniu pracy z programem Runbook sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym informacje o języku i moduły szkoleniowe, zapoznaj się z tematem [PowerShell Docs](/powershell/scripting/overview).
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [Tworzenie graficznego element runbook.](automation-tutorial-runbook-graphical.md)
* Aby rozpocząć pracę z książkami Runbook programu PowerShell, zobacz Create a PowerShell runbook (Tworzenie [runbook programu PowerShell).](automation-tutorial-runbook-textual-powershell.md)
* Aby dowiedzieć się więcej o typach elementów Runbook oraz ich zaletach i ograniczeniach, zobacz [Azure Automation typy elementów Runbook.](../automation-runbook-types.md)
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [Natywna obsługa skryptów programu PowerShell w Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
