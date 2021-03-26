---
title: Wykonywanie elementu runbook w usłudze Azure Automation
description: Ten artykuł zawiera omówienie przetwarzania elementów Runbook w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.openlocfilehash: 165c9ea721bec7fc7a1657f5dde5c19d9e254e20
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954347"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonywanie elementu runbook w usłudze Azure Automation

Automatyzacja procesów w Azure Automation umożliwia tworzenie programu PowerShell, przepływu pracy programu PowerShell i graficznych elementów Runbook oraz zarządzanie nimi. Aby uzyskać szczegółowe informacje, zobacz [Azure Automation elementów Runbook](automation-runbook-types.md). 

Automatyzacja wykonuje elementy Runbook na podstawie logiki zdefiniowanej wewnątrz nich. Jeśli element Runbook zostanie przerwany, zostanie ponownie uruchomiony na początku. Takie zachowanie wymaga zapisania elementów Runbook, które obsługują ponownie uruchamianie w przypadku wystąpienia problemów przejściowych.

Uruchomienie elementu Runbook w Azure Automation powoduje utworzenie zadania, które jest pojedynczym wystąpieniem wykonywania elementu Runbook. Każde zadanie uzyskuje dostęp do zasobów platformy Azure przez nawiązanie połączenia z subskrypcją platformy Azure. Zadanie może uzyskać dostęp tylko do zasobów w centrum danych, jeśli te zasoby są dostępne z chmury publicznej.

Azure Automation przypisuje proces roboczy do uruchamiania każdego zadania podczas wykonywania elementu Runbook. Chociaż procesy robocze są współużytkowane przez wiele kont platformy Azure, zadania z różnych kont usługi Automation są od siebie odizolowane. Nie można kontrolować, które usługi procesu roboczego żądają zadań.

Po wyświetleniu listy elementów Runbook w Azure Portal zostanie wyświetlony stan każdego zadania uruchomionego dla każdego elementu Runbook. Azure Automation przechowuje dzienniki zadań przez maksymalnie 30 dni.

Na poniższym diagramie przedstawiono cykl życia zadania elementu Runbook dla [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)i [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks).

![Stany zadań — przepływ pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Środowisko wykonywania elementu Runbook

Elementy Runbook w Azure Automation mogą być uruchamiane w piaskownicy platformy Azure lub [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md). 

Gdy elementy Runbook są przeznaczone do uwierzytelniania i uruchamiania względem zasobów na platformie Azure, są uruchamiane w piaskownicy platformy Azure, która jest środowiskiem udostępnionym, które może być używane przez wiele zadań. Zadania korzystające z tej samej piaskownicy są powiązane z ograniczeniami zasobów piaskownicy. Środowisko piaskownicy platformy Azure nie obsługuje operacji interaktywnych. Uniemożliwia ona dostęp do wszystkich pozaprocesowych serwerów COM i nie obsługuje wykonywania [wywołań WMI](/windows/win32/wmisdk/wmi-architecture) dla dostawcy Win32 w elemencie Runbook.  Te scenariusze są obsługiwane tylko przez uruchomienie elementu Runbook w hybrydowym procesie roboczym elementu Runbook systemu Windows.


Można również użyć [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md) do uruchamiania elementów Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów lokalnych w środowisku. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednego przypisanego komputera.

>[!NOTE]
>Aby można było uruchomić program w ramach hybrydowego procesu roboczego elementu Runbook systemu Linux, skrypty muszą być podpisane, a proces roboczy jest odpowiednio skonfigurowany. Alternatywnie [Walidacja podpisu musi być](automation-linux-hrw-install.md#turn-off-signature-validation)wyłączona.

W poniższej tabeli wymieniono niektóre zadania wykonywania elementów Runbook z zalecanym środowiskiem wykonywania wymienionym dla każdego z nich.

|Zadanie|Zalecenie|Uwagi|
|---|---|---|
|Integracja z zasobami platformy Azure|Piaskownica systemu Azure|Uwierzytelnianie jest obsługiwane na platformie Azure. Jeśli używasz hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej platformy Azure, możesz [użyć uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Uzyskaj optymalną wydajność zarządzania zasobami platformy Azure|Piaskownica systemu Azure|Skrypt jest uruchamiany w tym samym środowisku, w którym jest mniej opóźnienia.|
|Minimalizuj koszty operacyjne|Piaskownica systemu Azure|Nie ma obciążeń obliczeniowych i nie ma potrzeby dla maszyny wirtualnej.|
|Wykonywanie długotrwałego skryptu|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure mają [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Korzystanie z usług lokalnych|Hybrydowy proces roboczy elementu Runbook|Bezpośredni dostęp do komputera hosta lub zasobów w innych środowiskach w chmurze lub w środowisku lokalnym. |
|Wymagaj oprogramowania i plików wykonywalnych innych firm|Hybrydowy proces roboczy elementu Runbook|Zarządzanie systemem operacyjnym i instalowanie oprogramowania może być możliwe.|
|Monitorowanie pliku lub folderu za pomocą elementu Runbook|Hybrydowy proces roboczy elementu Runbook|Użyj [zadania obserwatora](automation-watchers-tutorial.md) w hybrydowym procesie roboczym elementu Runbook.|
|Uruchamianie skryptu intensywnie korzystającego z zasobów|Hybrydowy proces roboczy elementu Runbook| Piaskownice platformy Azure mają [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Używanie modułów z określonymi wymaganiami| Hybrydowy proces roboczy elementu Runbook|Przykłady to:</br> WinSCP — zależność od winscp.exe </br> Administrowanie usługami IIS na potrzeby włączania usług IIS i zarządzania nimi|
|Instalowanie modułu przy użyciu Instalatora|Hybrydowy proces roboczy elementu Runbook|Moduły dla piaskownicy muszą obsługiwać kopiowanie.|
|Użyj elementów Runbook lub modułów, które wymagają .NET Framework wersji innej niż 4.7.2|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure obsługują .NET Framework 4.7.2, a uaktualnienie do innej wersji nie jest obsługiwane.|
|Uruchamianie skryptów wymagających podniesienia uprawnień|Hybrydowy proces roboczy elementu Runbook|Piaskownice nie umożliwiają podniesienia uprawnień. Przy użyciu hybrydowego procesu roboczego elementu Runbook można wyłączyć funkcję Kontrola konta użytkownika i użyć [polecenia Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) przy uruchamianiu polecenia, które wymaga podniesienia uprawnień.|
|Uruchamianie skryptów, które wymagają dostępu do Instrumentacja zarządzania Windows (WMI)|Hybrydowy proces roboczy elementu Runbook|Zadania uruchomione w piaskownicach w chmurze nie mogą uzyskać dostępu do dostawcy WMI. |

## <a name="temporary-storage-in-a-sandbox"></a>Magazyn tymczasowy w piaskownicy

Jeśli konieczne jest utworzenie plików tymczasowych jako części logiki elementu Runbook, można użyć folderu temp (czyli `$env:TEMP` ) w piaskownicy platformy Azure dla elementów Runbook uruchomionych na platformie Azure. Jedynym ograniczeniem jest nie można użyć więcej niż 1 GB miejsca na dysku, który jest przydziałem dla każdej piaskownicy. W przypadku pracy z przepływami pracy programu PowerShell ten scenariusz może spowodować wystąpienie problemu, ponieważ przepływy pracy programu PowerShell używają punktów kontrolnych, a skrypt może zostać ponowiony w innej piaskownicy.

Za pomocą piaskownicy hybrydowej można użyć na `C:\temp` podstawie dostępności magazynu w hybrydowym procesie roboczym elementu Runbook. Jednak na potrzeby zaleceń dotyczących maszyn wirtualnych platformy Azure nie należy używać [dysku tymczasowego](../virtual-machines/managed-disks-overview.md#temporary-disk) w systemie Windows lub Linux dla danych, które muszą zostać utrwalone.

## <a name="resources"></a>Zasoby

Elementy Runbook muszą zawierać logikę do obsługi [zasobów](/rest/api/resources/resources), np. maszyn wirtualnych, sieci i zasobów w sieci. Zasoby są powiązane z subskrypcją platformy Azure, a elementy Runbook wymagają odpowiednich poświadczeń w celu uzyskania dostępu do dowolnego zasobu. Aby zapoznać się z przykładem obsługi zasobów w elemencie Runbook, zobacz temat [Obsługa zasobów](manage-runbooks.md#handle-resources).

## <a name="security"></a>Zabezpieczenia

Azure Automation używa [Azure Security Center (ASC)](../security-center/security-center-introduction.md) w celu zapewnienia bezpieczeństwa zasobów i wykrywania kompromisów w systemach Linux. Zabezpieczenia są udostępniane w ramach obciążeń, niezależnie od tego, czy zasoby znajdują się na platformie Azure, czy nie. Zapoznaj [się z artykułem wprowadzenie do uwierzytelniania w Azure Automation](automation-security-overview.md).

Wartość ASC umieszcza ograniczenia dla użytkowników, którzy mogą uruchamiać dowolne skrypty, podpisane lub niepodpisane na maszynie wirtualnej. Jeśli jesteś użytkownikiem z dostępem głównym do maszyny wirtualnej, musisz jawnie skonfigurować maszynę z podpisem cyfrowym lub wyłączyć ją. W przeciwnym razie można uruchomić skrypt, aby zastosować aktualizacje systemu operacyjnego po utworzeniu konta usługi Automation i włączeniu odpowiedniej funkcji.

## <a name="subscriptions"></a>Subskrypcje

[Subskrypcja](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) platformy Azure to umowa z firmą Microsoft umożliwiająca korzystanie z jednej lub kilku usług w chmurze, dla których opłata jest naliczana. W przypadku Azure Automation Każda subskrypcja jest połączona z kontem Azure Automation i można [utworzyć wiele subskrypcji](manage-runbooks.md#work-with-multiple-subscriptions) na koncie.

## <a name="credentials"></a>Poświadczenia

Element Runbook wymaga odpowiednich [poświadczeń](shared-resources/credentials.md) w celu uzyskania dostępu do dowolnego zasobu, zarówno w przypadku platformy Azure, jak i systemów innych firm. Te poświadczenia są przechowywane w Azure Automation, Key Vault itd.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation używa [Azure monitor](../azure-monitor/overview.md) do monitorowania operacji maszyny. Operacje wymagają Log Analytics obszaru roboczego i [agenta log Analytics](../azure-monitor/agents/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Agent Log Analytics dla systemu Windows

[Agent log Analytics dla systemu Windows](../azure-monitor/agents/agent-windows.md) współpracuje z usługą Azure monitor do zarządzania maszynami wirtualnymi z systemem Windows i komputerami fizycznymi. Maszyny mogą działać na platformie Azure lub w środowisku spoza platformy Azure, na przykład w lokalnym centrum danych.

>[!NOTE]
>Agent Log Analytics dla systemu Windows był wcześniej znany jako Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agent Log Analytics dla systemu Linux

[Agent log Analytics dla systemu Linux](../azure-monitor/agents/agent-linux.md) działa podobnie jak Agent programu Windows, ale łączy komputery z systemem Linux, aby Azure monitor. Agent jest instalowany przy użyciu konta użytkownika **nxautomation** , które umożliwia wykonywanie poleceń wymagających uprawnień głównych, na przykład w hybrydowym procesie roboczym elementu Runbook. Konto **nxautomation** jest kontem systemowym, które nie wymaga hasła.

Konto **nxautomation** z odpowiednimi uprawnieniami sudo musi być obecne podczas [instalacji hybrydowego procesu roboczego elementu Runbook systemu Linux](automation-linux-hrw-install.md). Jeśli spróbujesz zainstalować proces roboczy, a konto nie jest obecne lub nie ma odpowiednich uprawnień, instalacja nie powiedzie się.

Nie należy zmieniać uprawnień do `sudoers.d` folderu ani jego własności. Uprawnienie sudo jest wymagane dla konta **nxautomation** i nie należy usuwać uprawnień. Ograniczenie tego do określonych folderów lub poleceń może spowodować powstanie istotnej zmiany.

Dzienniki dostępne dla agenta Log Analytics i konta **nxautomation** są następujące:

* /var/opt/Microsoft/omsagent/log/omsagent.log — dziennik agenta Log Analytics
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log — dziennik procesu roboczego usługi Automation

>[!NOTE]
>Użytkownik **nxautomation** włączony jako część Update Management wykonuje tylko podpisane elementy Runbook.

## <a name="runbook-permissions"></a>Uprawnienia elementu Runbook

Element Runbook wymaga uprawnień do uwierzytelniania na platformie Azure za pomocą poświadczeń. Zobacz [Omówienie uwierzytelniania Azure Automation](automation-security-overview.md).

## <a name="modules"></a>Moduły

Azure Automation obsługuje wiele modułów domyślnych, w tym niektóre moduły AzureRM (AzureRM. Automation) i moduł zawierający kilka wewnętrznych poleceń cmdlet. Obsługiwane są również moduły instalowalne, w tym AZ modules (AZ. Automation), które są obecnie używane w preferencjach do modułów AzureRM. Aby uzyskać szczegółowe informacje dotyczące modułów, które są dostępne dla elementów Runbook i konfiguracji DSC, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certyfikaty

Azure Automation używa [certyfikatów](shared-resources/certificates.md) do uwierzytelniania na platformie Azure lub dodaje je do platformy Azure lub zasobów innych firm. Certyfikaty są bezpiecznie przechowywane na potrzeby dostępu przez elementy Runbook i konfiguracje DSC.

Elementy Runbook mogą korzystać z certyfikatów z podpisem własnym, które nie są podpisane przez urząd certyfikacji. Zobacz [Tworzenie nowego certyfikatu](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Stanowiska

Azure Automation obsługuje środowisko do uruchamiania zadań z tego samego konta usługi Automation. Pojedynczy element Runbook może mieć wiele zadań uruchomionych jednocześnie. Im więcej zadań jest wykonywanych w tym samym czasie, tym częściej można je wysłać do tej samej piaskownicy. 

Zadania uruchomione w tym samym procesie piaskownicy mogą mieć wpływ na siebie nawzajem. Przykładem jest uruchomienie polecenia cmdlet [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) . Wykonanie tego polecenia cmdlet rozłącza każde zadanie elementu Runbook w udostępnionym procesie piaskownicy. Przykład pracy z tym scenariuszem można znaleźć w temacie [zapobieganie współbieżnym zadaniami](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Zadania programu PowerShell uruchomione z elementu Runbook, który działa w piaskownicy platformy Azure, mogą nie działać w [trybie pełnego języka programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Stany zadań

W poniższej tabeli opisano Stany, które są możliwe dla danego zadania. Można wyświetlić podsumowanie stanu dla wszystkich zadań elementu Runbook lub przejść do szczegółów dotyczących określonego zadania elementu Runbook w Azure Portal. Możesz również skonfigurować integrację z obszarem roboczym Log Analytics, aby przesyłać strumieniowo stan zadań elementu Runbook i zadania. Więcej informacji na temat integrowania z dziennikami Azure Monitor można znaleźć [w temacie przekazywanie stanu zadań i strumieni zadań z automatyzacji do Azure monitor dzienników](automation-manage-send-joblogs-log-analytics.md). Zobacz również artykuł [Uzyskiwanie stanów zadań](manage-runbooks.md#obtain-job-statuses) na przykład pracy z stanami w elemencie Runbook.

| Stan | Opis |
|:--- |:--- |
| Zdezaktywowan |Zadanie jest uaktywniane. |
| Ukończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Nie można skompilować elementu Runbook graficznego lub przepływu pracy programu PowerShell. Nie można uruchomić elementu Runbook programu PowerShell lub zadanie miało wyjątek. Zobacz [Azure Automation typów elementów Runbook](automation-runbook-types.md).|
| Niepowodzenie, oczekiwanie na zasoby |Zadanie nie powiodło się, ponieważ osiągnął limit [godziwego udziału](#fair-share) trzy razy i został uruchomiony z tego samego punktu kontrolnego lub od początku elementu Runbook za każdym razem. |
| W kolejce |Zadanie oczekuje na udostępnienie zasobów w procesie roboczym usługi Automation, aby można było je uruchomić. |
| Wznawianie |System wznawia zadanie po jego wstrzymaniu. |
| Uruchomienie |Zadanie jest uruchomione. |
| Uruchamianie, oczekiwanie na zasoby |Zadanie zostało zwolnione, ponieważ osiągnęło odpowiedni limit udostępniania. Wkrótce zostanie wznowione od ostatniego punktu kontrolnego. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego, a system go uruchamia. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika przed jego ukończeniem. |
| Zatrzymywanie |System zatrzymuje zadanie. |
| Suspended |Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie Runbook. Jeśli element Runbook nie ma punktu kontrolnego, rozpocznie się od początku. Jeśli ma punkt kontrolny, można go uruchomić ponownie i wznowić od ostatniego punktu kontrolnego. System zawiesza element Runbook tylko wtedy, gdy wystąpi wyjątek. Domyślnie `ErrorActionPreference` zmienna jest ustawiona na Kontynuuj, co oznacza, że zadanie działa w przypadku błędu. Jeśli zmienna preferencji jest ustawiona na Zatrzymaj, zadanie zawiesza się po błędzie.  |
| Zawieszanie |Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . System próbuje zawiesić zadanie na żądanie użytkownika. Przez zawieszeniem element Runbook musi dotrzeć do swojego następnego punktu kontrolnego. Jeśli przeszedł już ostatni punkt kontrolny, kończy się przed jego wstrzymaniem. |

## <a name="activity-logging"></a>Dziennik aktywności

Wykonywanie elementów Runbook w programie Azure Automation zapisuje szczegóły w dzienniku aktywności dla konta usługi Automation. Aby uzyskać szczegółowe informacje dotyczące korzystania z dziennika, zobacz [pobieranie szczegółów z dziennika aktywności](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Wyjątki

W tej sekcji opisano niektóre sposoby obsługi wyjątków lub sporadycznych problemów w elementach Runbook. Przykładem jest wyjątek protokołu WebSocket. Poprawna obsługa wyjątków uniemożliwia przejściowym awariom sieci spowodowanie niepowodzenia elementów Runbook.

### <a name="erroractionpreference"></a>ErrorActionPreference

Zmienna [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) określa, jak program PowerShell reaguje na błąd niepowodujący zakończenia. Przerywanie błędów zawsze kończy się i nie dotyczy `ErrorActionPreference` .

Gdy element Runbook używa `ErrorActionPreference` , zazwyczaj błąd niepowodujący zakończenia, taki jak `PathNotFound` polecenie cmdlet [GET-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) , uniemożliwia ukończenie elementu Runbook. W poniższym przykładzie pokazano użycie `ErrorActionPreference` . Końcowe polecenie [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) nigdy nie jest wykonywane, gdy skrypt zostaje zatrzymany.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Wypróbuj teraz

Aby obsłużyć błędy powodujące przerwanie, w skryptach programu PowerShell należy użyć instrukcji [catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) . Skrypt może używać tego mechanizmu do przechwytywania określonych wyjątków lub ogólnych wyjątków. `catch`Instrukcja powinna służyć do śledzenia lub próby obsługi błędów. Poniższy przykład próbuje pobrać plik, który nie istnieje. Przechwytuje `System.Net.WebException` wyjątek i zwraca ostatnią wartość dla każdego innego wyjątku.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) można użyć do wygenerowania błędu kończącego. Ten mechanizm może być przydatny podczas definiowania własnej logiki w elemencie Runbook. Jeśli skrypt spełnia kryterium, które powinno zostać zatrzymane, może użyć `throw` instrukcji, aby zatrzymać. Poniższy przykład używa tej instrukcji, aby wyświetlić wymagany parametr funkcji.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>błędy

Elementy Runbook muszą obsługiwać błędy. Azure Automation obsługuje dwa typy błędów programu PowerShell, kończenie i niekończenie. 

Przerywanie błędów Zatrzymaj wykonywanie elementu Runbook, gdy wystąpią. Element Runbook zostaje zatrzymany ze stanem zadania zakończonego niepowodzeniem.

Błędy niepowodujące zakończenia umożliwiają wykonywanie skryptu nawet po wystąpieniu. Przykładem błędu niekończącego jest taki, który występuje, gdy element Runbook używa `Get-ChildItem` polecenia cmdlet z ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje do następnego folderu. W tym przypadku błąd nie ustawił stanu zadania elementu Runbook na niepowodzenie, a zadanie może być nawet ukończone. Aby wymusić zatrzymanie elementu Runbook w przypadku błędu niekończącego, możesz użyć `ErrorAction Stop` polecenia cmdlet.

## <a name="calling-processes"></a>Procesy wywołujące

Elementy Runbook, które działają w piaskownicach platformy Azure, nie obsługują wywoływania procesów, takich jak pliki wykonywalne (**. exe** ) lub podprocesy. Przyczyną tego jest to, że piaskownica systemu Azure jest uruchomionym procesem w kontenerze, który może nie być w stanie uzyskać dostępu do wszystkich podstawowych interfejsów API. W przypadku scenariuszy wymagających oprogramowania innych firm lub wywołań podprocesów należy wykonać element Runbook w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Charakterystyka urządzenia i aplikacji

Zadania elementu Runbook w piaskownicach platformy Azure nie mogą uzyskać dostępu do żadnych właściwości urządzenia lub aplikacji. Najpopularniejszym interfejsem API używanym do wykonywania zapytań o metryki wydajności w systemie Windows jest usługa WMI z niektórymi typowymi metrykami używanymi do wykorzystania pamięci i procesora CPU. Jednak nie ma znaczenia, który interfejs API jest używany, ponieważ zadania uruchomione w chmurze nie mogą uzyskać dostępu do implementacji firmy Microsoft Web-Based Enterprise Management (WBEM). Ta platforma jest oparta na model wspólnych informacji (CIM), co zapewnia standardy branżowe do definiowania cech urządzeń i aplikacji.

## <a name="webhooks"></a>Elementy webhook

Usługi zewnętrzne, na przykład Azure DevOps Services i GitHub, mogą uruchomić element Runbook w programie Azure Automation. Aby wykonać ten typ uruchamiania, usługa używa [elementu webhook](automation-webhooks.md) za pośrednictwem pojedynczego żądania HTTP. Użycie elementu webhook umożliwia uruchamianie elementów Runbook bez implementacji pełnej Azure Automation funkcji.

## <a name="shared-resources"></a><a name="fair-share"></a>Udostępnione zasoby

Aby udostępnić zasoby między wszystkimi elementami Runbook w chmurze, platforma Azure korzysta z koncepcji o nazwie "uczciwe udziały". Przy użyciu funkcji uczciwego udostępniania platforma Azure tymczasowo zwalnia lub kończy każde zadanie, które zostało uruchomione przez ponad trzy godziny. Zadania dla [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementów Runbook języka Python](automation-runbook-types.md#python-runbooks) są zatrzymane i nie są ponownie uruchamiane, a stan zadania zostanie zatrzymany.

W przypadku długotrwałych Azure Automation zadań zaleca się użycie hybrydowego procesu roboczego elementu Runbook. Hybrydowe procesy robocze elementów Runbook nie są ograniczone przez sprawiedliwy udział i nie mają ograniczenia czasu wykonywania elementu Runbook. Pozostałe [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zadań dotyczą zarówno piaskownic systemu Azure, jak i hybrydowych procesów roboczych elementów Runbook. Gdy hybrydowe procesy robocze elementów Runbook nie są ograniczone przez trzy godziny, należy opracować elementy Runbook do działania w ramach procesów roboczych, które obsługują ponowne uruchomienia z nieoczekiwanych problemów z infrastrukturą lokalną.

Kolejną opcją jest optymalizacja elementu Runbook przy użyciu podrzędnych elementów Runbook. Na przykład element Runbook może przechodzić przez tę samą funkcję do kilku zasobów, na przykład z operacją bazy danych na kilku bazach danych. Tę funkcję można przenieść do [podrzędnego elementu Runbook](automation-child-runbooks.md) i mieć do niej wywołanie elementu Runbook za pomocą polecenia [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Podrzędne elementy Runbook są wykonywane równolegle w oddzielnych procesach.

Użycie podrzędnych elementów Runbook zmniejsza łączny czas pracy nadrzędnego elementu Runbook. Element Runbook może użyć polecenia cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) , aby sprawdzić stan zadania podrzędnego elementu Runbook, jeśli nadal ma więcej operacji po zakończeniu działania podrzędnego.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementem Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby korzystać z elementów Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation#automation).
