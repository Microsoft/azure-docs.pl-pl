---
title: Mój pierwszy system runbook przepływu pracy programu PowerShell w usłudze Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego tekstowego elementu Runbook przy użyciu przepływu programu PowerShell.
keywords: przepływ pracy programu powershell, przykłady przepływu pracy programu powershell, program powershell przepływu pracy
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 8b103437ab30c05ddab88b7a8a723cd2b4b1d5f6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405888"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mój pierwszy element Runbook przepływu pracy programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Zacznij od prostego śmiętu, który testujesz i publikujesz, ucząc się, jak śledzić stan zadania likmy. Następnie zmodyfikuj element runbook, aby faktycznie zarządzać zasobami platformy Azure, co ilustruje uruchamianie maszyny wirtualnej platformy Azure. Na koniec uatywniesz system runbook, dodając parametry uruchomieniu. ŚL.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być maszyna wirtualna produkcyjna.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook

Zacznij od utworzenia prostego podstawowego `Hello World`podstawowego podstawowego tekstu .

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z tych zasobów to moduły automatycznie uwzględnione w nowym koncie automatyzacji. Powinieneś również mieć zasób poświadczeń skojarzony z subskrypcją.
 
1. Wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmięty.
1. Utwórz nowy projekt runbook, wybierając **pozycję Utwórz projekt runbook**.
1. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Workflow**.
1. W takim przypadku zamierzasz utworzyć [projekt runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Wybierz **przepływ pracy programu PowerShell** dla typu **uruchomieniu**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Można wpisać kod bezpośrednio do życiornika lub wybrać polecenia cmdlet, runbook i zasoby z kontrolki Biblioteka i dodać je do wiązki kreślić z dowolnymi powiązanymi parametrami. W tym samouczku wpisz kod bezpośrednio w bieśbiu.

1. Obiekt runbook jest obecnie pusty tylko `Workflow` z wymaganym słowem kluczowym, nazwą obiektu runbook i nawiasami klamrowymi, które otaczają cały przepływ pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Wpisz `Write-Output "Hello World"` między nawiasami klamrowymi.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem likemi, aby udostępnić go w produkcji, należy przetestować go, aby upewnić się, że działa poprawnie. Testowanie uruchomieniu uruchamia jego wersji roboczej i umożliwia interaktywne wyświetlanie jego danych wyjściowych.

1. Wybierz **okienko testu,** aby otworzyć okienko testowe.
1. Kliknij **przycisk Start,** aby rozpocząć test, z testowaniem jedynej włączonej opcji.
1. Należy zauważyć, że [zadanie runbook](automation-runbook-execution.md) jest tworzony i jego stan jest wyświetlany w okienku.

   Stan zadania rozpoczyna się jako w kolejce, co oznacza, że zadanie oczekuje na udostępnienie pracownika przewodniczącego w chmurze. Stan zmienia się na Rozpoczęcie, gdy pracownik żąda zadania. Na koniec stan staje się uruchomiony, gdy runbook faktycznie zaczyna działać.

1. Po zakończeniu zadania runbook okienko Test wyświetla jego dane wyjściowe. W takim przypadku `Hello World`widzisz .

   ![Witaj, świecie](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony projekt runbook jest nadal w trybie roboczym. Należy go opublikować, zanim będzie można uruchomić go w produkcji. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.
1. Przewiń w lewo, aby wyświetlić runbook na stronie **Runbooks** i pamiętać, że pole **Stan autora** jest ustawione na **Opublikowane**.
1. Przewiń z powrotem w prawo, aby wyświetlić stronę **myfirstRunbook-Workflow**.

   Opcje u góry umożliwiają teraz uruchomienie elementu runbook, zaplanowanie przyszłej godziny rozpoczęcia lub utworzenie [elementu webhook,](automation-webhooks.md) aby można było uruchomić element runbook za pośrednictwem wywołania HTTP.

1. Wybierz **przycisk Start,** a następnie **przycisk Tak** po wyświetleniu monitu o uruchomienie śmięty.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Okienko Zadania jest otwierane dla zadania uruchomieniu, które zostało utworzone. W takim przypadku pozostaw otwarte okienko, aby można było obserwować postęp zadania.

1. Należy zauważyć, że stan zadania jest wyświetlany w **podsumowaniu zadania**. Ten stan jest zgodny ze stanami, które zostały ujrzane podczas testowania śmiętu runbooka.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Gdy stan liksu eksletu zostanie wyświetlony, kliknij pozycję **Wyjście**. Zostanie otwarta strona Wyjście, na `Hello World` której można wyświetlić wiadomość.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Zamknij stronę Dane wyjściowe.

1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być `Hello World` widoczny tylko w strumieniu wyjściowym. Należy zauważyć, że okienko Strumienie można wyświetlić inne strumienie dla zadania przewodniczącego, takich jak pełne i strumienie błędów, jeśli runbook zapisuje do nich.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Zamknij okienko Strumienie i okienko zadania, aby powrócić do strony MyFirstRunbook.
1. Kliknij **pozycję Zadania** w obszarze **Zasoby,** aby otworzyć stronę Zadania dla tego liku. Ta strona zawiera listę wszystkich zadań utworzonych przez system runbook. Na liście powinna być wyświetlana tylko jedno zadanie, ponieważ zadanie zostało uruchomione tylko raz.

   ![Stanowiska](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które zostało wyświetlone podczas uruchamiania likwidu. To okienko służy do wyświetlania szczegółów każdego zadania utworzonego dla egonatu.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure. Nie można tego zrobić, chyba że uwierzytelnia się przy użyciu poświadczeń dla subskrypcji. Uwierzytelnianie używa polecenia cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)

>[!NOTE]
>Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.

1. Przejdź do strony MyFirstRunbook-Workflow i otwórz edytor tekstu, klikając przycisk **Edytuj**.
2. Usuń `Write-Output` wiersz.
3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.
4. Wpisz lub skopiuj i wklej następujący kod, który obsługuje uwierzytelnianie za pomocą konta Automation Run As.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Kliknij **okienko testu,** aby można było przetestować projekt runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po jego zakończeniu powinieneś zobaczyć dane wyjściowe podobne do następujących, wyświetlające podstawowe informacje z konta. Ta akcja potwierdza, że poświadczenie jest prawidłowe.

   ![Uwierzytelnianie](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy twój identyfikator runbook uwierzytelnia się w ramach subskrypcji platformy Azure, możesz zarządzać zasobami. Dodajmy polecenie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i na razie dysk koduj tę nazwę w rundzie runbook. Jeśli zarządzasz zasobami w wielu subskrypcjach, `AzContext` musisz użyć parametru z poleceniem cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Podaj nazwę nazwy i nazwy grupy zasobów maszyny Wirtualnej, aby rozpocząć od wprowadzenia wywołania polecenia cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) jak pokazano poniżej. 

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

1. Zapisz runbook, a następnie kliknij **okienko testu,** aby można było go przetestować.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po jej zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook

Twój program runbook aktualnie uruchamia maszynę wirtualną, która została zakodowana na stałe w bieśmicieli. Będzie to bardziej przydatne, jeśli można określić maszynę wirtualną po uruchomieniu śmiwoja. Dodajmy parametry wejściowe do zestawu runbook, aby zapewnić tę funkcjonalność.

1. Dodaj zmienne `VMName` i `ResourceGroupName` parametry do kreślenia i użyj `Start-AzVM` zmiennych z poleceniem cmdlet, jak pokazano poniżej.

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

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz można podać wartości dla dwóch zmiennych wejściowych, które są w teście.
3. Zamknij okienko testowania.
4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
5. Zatrzymaj rozpoczętą maszynę wirtualną.
6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. 
7. Wpisz wartości dla **VMNAME** i **RESOURCEGROUPNAME** dla maszyny Wirtualnej, które mają się rozpocząć.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Po zakończeniu uruchomieniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](automation-first-runbook-graphical.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby dowiedzieć się więcej o typach elementów runbook oraz ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [Natywna obsługa skryptów programu PowerShell w usłudze Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).