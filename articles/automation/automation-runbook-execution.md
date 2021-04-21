---
title: Wykonywanie elementu runbook w usłudze Azure Automation
description: Ten artykuł zawiera omówienie przetwarzania elementami Runbook w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0807b11adfc46b9c32a8f7bd36a2f7d4db519975
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830524"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonywanie elementu runbook w usłudze Azure Automation

Automatyzacja procesów w Azure Automation umożliwia tworzenie i zarządzanie programem PowerShell, przepływem pracy programu PowerShell i graficznymi elementami Runbook. Aby uzyskać szczegółowe informacje, [zobacz Azure Automation runbook.](automation-runbook-types.md) 

Automatyzacja wykonuje twoje runbook na podstawie logiki zdefiniowanej w nich. Jeśli runbook zostanie przerwany, zostanie uruchomiony ponownie na początku. To zachowanie wymaga napisania podręczników Runbook, które obsługują ponowne uruchamianie w przypadku wystąpienia przejściowych problemów.

Uruchomienie runbook w programie Azure Automation powoduje utworzenie zadania, które jest pojedynczym wystąpieniem wykonywania tego runbook. Każde zadanie uzyskuje dostęp do zasobów platformy Azure, nawiązaniu połączenia z subskrypcją platformy Azure. Zadanie może uzyskać dostęp do zasobów w centrum danych tylko wtedy, gdy są one dostępne z chmury publicznej.

Azure Automation proces roboczy do uruchamiania każdego zadania podczas wykonywania runbook. Chociaż pracownicy są współużytowani przez wiele kont platformy Azure, zadania z różnych kont usługi Automation są odizolowane od siebie. Nie można kontrolować, które usługi procesu roboczego są usługami żądań zadań.

Podczas wyświetlania listy elementów Runbook w Azure Portal jest przedstawiany stan każdego zadania, które zostało uruchomione dla każdego z nich. Azure Automation przechowuje dzienniki zadań przez maksymalnie 30 dni.

Na poniższym diagramie przedstawiono cykl życia zadania elementów Runbook dla elementów Runbook programu [PowerShell,](automation-runbook-types.md#powershell-runbooks)elementów Runbook przepływu pracy programu [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)i [graficznych elementów Runbook.](automation-runbook-types.md#graphical-runbooks)

![Stany zadania — przepływ pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Środowisko wykonawcze runbook

Element runbook w Azure Automation można uruchamiać w piaskownicy platformy Azure lub [hybrydowym procesu roboczego runbook.](automation-hybrid-runbook-worker.md) 

Gdy element runbook jest przeznaczony do uwierzytelniania i uruchamiania względem zasobów na platformie Azure, są one uruchamiane w piaskownicy platformy Azure, która jest środowiskiem udostępnionym, z którego można korzystać w wielu zadaniach. Zadania korzystające z tej samej piaskownicy są powiązane ograniczeniami zasobów piaskownicy. Środowisko piaskownicy platformy Azure nie obsługuje operacji interaktywnych. Uniemożliwia dostęp do wszystkich poza procesem serwerów COM i nie obsługuje wywołań [usługi WMI](/windows/win32/wmisdk/wmi-architecture) do dostawcy Win32 w twoim runbook.  Te scenariusze są obsługiwane tylko przez uruchomienie runbook w hybrydowym procesie roboczy runbook systemu Windows.


Za pomocą hybrydowego procesu roboczego runbook można również uruchamiać podręczniki [runbook](automation-hybrid-runbook-worker.md) bezpośrednio na komputerze, który hostuje rolę, oraz względem zasobów lokalnych w środowisku. Azure Automation przechowuje i zarządza nimi, a następnie dostarcza je do jednego lub większej liczby przypisanych komputerów.

>[!NOTE]
>Aby można było uruchomić w hybrydowym procesie roboczy runbook systemu Linux, skrypty muszą być podpisane i odpowiednio skonfigurowany proces roboczy. Alternatywnie [walidację podpisu należy wyłączyć.](automation-linux-hrw-install.md#turn-off-signature-validation)

W poniższej tabeli wymieniono niektóre zadania wykonywania runbook z zalecanym środowiskiem wykonywania wymienionymi dla każdego z nich.

|Zadanie|Zalecenie|Uwagi|
|---|---|---|
|Integracja z zasobami platformy Azure|Piaskownica platformy Azure|Uwierzytelnianie hostowane na platformie Azure jest prostsze. Jeśli używasz hybrydowego procesu roboczego runbook na maszynie wirtualnej platformy Azure, możesz użyć uwierzytelniania element [runbook z tożsamościami zarządzanymi.](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)|
|Uzyskiwanie optymalnej wydajności w celu zarządzania zasobami platformy Azure|Piaskownica platformy Azure|Skrypt jest uruchamiany w tym samym środowisku, co ma mniejsze opóźnienie.|
|Minimalizowanie kosztów operacyjnych|Piaskownica platformy Azure|Nie ma narzutu obliczeniowego i nie ma potrzeby tworzenia maszyny wirtualnej.|
|Wykonywanie długotrwałego skryptu|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure mają [limity zasobów.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interakcja z usługami lokalnymi|Hybrydowy proces roboczy elementu Runbook|Bezpośredni dostęp do maszyny hosta lub zasobów w innych środowiskach chmury lub środowisku lokalnym. |
|Wymaganie oprogramowania i plików wykonywalnych innych firm|Hybrydowy proces roboczy elementu Runbook|Użytkownik zarządza systemem operacyjnym i może instalować oprogramowanie.|
|Monitorowanie pliku lub folderu za pomocą programu Runbook|Hybrydowy proces roboczy elementu Runbook|Użyj zadania [watcher w hybrydowym](automation-watchers-tutorial.md) procesu roboczego runbook.|
|Uruchamianie skryptu intensywnie obciążania zasobów|Hybrydowy proces roboczy elementu Runbook| Piaskownice platformy Azure mają [limity zasobów.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Używanie modułów z określonymi wymaganiami| Hybrydowy proces roboczy elementu Runbook|Przykłady to:</br> WinSCP — zależność od winscp.exe </br> Administrowanie usługami IIS — zależność od włączania usług IIS lub zarządzania nimi|
|Instalowanie modułu za pomocą instalatora|Hybrydowy proces roboczy elementu Runbook|Moduły piaskownicy muszą obsługiwać kopiowanie.|
|Korzystanie z modułów lub modułów, które .NET Framework w wersji innej niż 4.7.2|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure .NET Framework 4.7.2, a uaktualnianie do innej wersji nie jest obsługiwane.|
|Uruchamianie skryptów, które wymagają podniesienia uprawnień|Hybrydowy proces roboczy elementu Runbook|Piaskownice nie zezwalają na podniesienie uprawnień. Hybrydowy proces roboczy runbook umożliwia wyłączenie funkcji UAC i użycie polecenia [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) podczas uruchamiania polecenia wymagającego podniesienia uprawnień.|
|Uruchamianie skryptów wymagających dostępu do Instrumentacja zarządzania Windows (WMI)|Hybrydowy proces roboczy elementu Runbook|Zadania uruchomione w piaskownicach w chmurze nie mogą uzyskać dostępu do dostawcy WMI. |

## <a name="temporary-storage-in-a-sandbox"></a>Magazyn tymczasowy w piaskownicy

Jeśli musisz utworzyć pliki tymczasowe w ramach logiki runbook, możesz użyć folderu tymczasowego (czyli ) w piaskownicy platformy Azure dla elementów Runbook uruchomionych `$env:TEMP` na platformie Azure. Jedynym ograniczeniem jest to, że nie można użyć więcej niż 1 GB miejsca na dysku, czyli limitu przydziału dla każdej piaskownicy. Podczas pracy z przepływami pracy programu PowerShell ten scenariusz może spowodować problem, ponieważ przepływy pracy programu PowerShell używają punktów kontrolnych, a skrypt może zostać ponoszony w innej piaskownicy.

W piaskownicy hybrydowej można używać funkcji na podstawie dostępności magazynu w hybrydowym `C:\temp` procesie roboczym runbook. Jednak na podstawie zaleceń dotyczących maszyn [](../virtual-machines/managed-disks-overview.md#temporary-disk) wirtualnych platformy Azure nie należy używać dysku tymczasowego w systemie Windows lub Linux dla danych, które muszą zostać utrwalone.

## <a name="resources"></a>Zasoby

Twoje zasoby runbook muszą zawierać logikę do obsługi [zasobów](/rest/api/resources/resources), na przykład maszyn wirtualnych, sieci i zasobów w sieci. Zasoby są powiązane z subskrypcją platformy Azure, a element Runbook wymaga odpowiednich poświadczeń w celu uzyskania dostępu do dowolnego zasobu. Aby uzyskać przykład obsługi zasobów w elementach Runbook, zobacz [Handle resources (Obsługa zasobów).](manage-runbooks.md#handle-resources)

## <a name="security"></a>Zabezpieczenia

Azure Automation używa usługi [Azure Security Center (ASC)](../security-center/security-center-introduction.md) w celu zapewnienia bezpieczeństwa zasobów i wykrywania naruszyć bezpieczeństwa w systemach Linux. Zabezpieczenia są udostępniane między obciążeniami, niezależnie od tego, czy zasoby znajdują się na platformie Azure, czy nie. Zobacz [Wprowadzenie do uwierzytelniania w Azure Automation](automation-security-overview.md).

Usługi ASC umieszczają ograniczenia na użytkownikach, którzy mogą uruchamiać dowolne skrypty (podpisane lub niepodpisane) na maszynie wirtualnej. Jeśli jesteś użytkownikiem z dostępem głównym do maszyny wirtualnej, musisz jawnie skonfigurować maszynę przy użyciu podpisu cyfrowego lub wyłączyć ją. W przeciwnym razie można uruchomić skrypt w celu zastosowania aktualizacji systemu operacyjnego tylko po utworzeniu konta usługi Automation i włączeniu odpowiedniej funkcji.

## <a name="subscriptions"></a>Subskrypcje

Subskrypcja [platformy](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) Azure to umowa z firmą Microsoft na korzystanie z co najmniej jednej usługi opartej na chmurze, za którą naliczane są opłaty. Na Azure Automation każda subskrypcja jest połączona z kontem usługi Azure Automation i można utworzyć wiele subskrypcji [na](manage-runbooks.md#work-with-multiple-subscriptions) koncie.

## <a name="credentials"></a>Poświadczenia

Aby uzyskać dostęp do [dowolnego](shared-resources/credentials.md) zasobu, zarówno dla platformy Azure, jak i systemów innych firm, należy zapewnić odpowiednie poświadczenia. Te poświadczenia są przechowywane w Azure Automation, Key Vault itp.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation korzysta z [Azure Monitor](../azure-monitor/overview.md) do monitorowania operacji na maszynach. Operacje wymagają obszaru roboczego usługi Log Analytics i agenta [usługi Log Analytics.](../azure-monitor/agents/log-analytics-agent.md)

### <a name="log-analytics-agent-for-windows"></a>Agent usługi Log Analytics dla systemu Windows

Agent [usługi Log Analytics dla systemu Windows współpracuje](../azure-monitor/agents/agent-windows.md) z Azure Monitor do zarządzania maszynami wirtualnych z systemem Windows i komputerami fizycznymi. Maszyny mogą działać na platformie Azure lub w środowisku spoza platformy Azure, takim jak lokalne centrum danych.

>[!NOTE]
>Agent usługi Log Analytics dla systemu Windows był wcześniej znany jako Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agent usługi Log Analytics dla systemu Linux

Agent [usługi Log Analytics dla systemu Linux](../azure-monitor/agents/agent-linux.md) działa podobnie do agenta dla systemu Windows, ale łączy komputery z systemem Linux z Azure Monitor. Agent jest instalowany z kontem **użytkownika nxautomation,** które umożliwia wykonywanie poleceń wymagających uprawnień administratora, na przykład w hybrydowym procesie roboczy runbook. Konto **nxautomation** jest kontem systemowym, które nie wymaga hasła.

Konto **nxautomation** z odpowiednimi uprawnieniami sudo musi być obecne podczas instalacji hybrydowego procesu [roboczego runbook systemu Linux.](automation-linux-hrw-install.md) Jeśli spróbujesz zainstalować proces roboczy, a konto nie istnieje lub nie ma odpowiednich uprawnień, instalacja zakończy się niepowodzeniem.

Nie należy zmieniać uprawnień `sudoers.d` folderu ani jego własności. Uprawnienie Sudo jest wymagane dla **konta nxautomation** i nie należy usuwać uprawnień. Ograniczenie tego do niektórych folderów lub poleceń może spowodować zmianę przerywania.

Dzienniki dostępne dla agenta usługi Log Analytics i konta **nxautomation** to:

* /var/opt/microsoft/omsagent/log/omsagent.log — dziennik agenta usługi Log Analytics
* /var/opt/microsoft/omsagent/run/automationworker/worker.log — dziennik procesu roboczego usługi Automation

>[!NOTE]
>Użytkownik **nxautomation** włączony w ramach Update Management wykonuje tylko podpisane element runbook.

## <a name="runbook-permissions"></a>Uprawnienia elementu Runbook

Aby można było uwierzytelniać się na platformie Azure za pomocą poświadczeń, należy mieć uprawnienia do tego celu. Zobacz [Azure Automation omówienie uwierzytelniania.](automation-security-overview.md)

## <a name="modules"></a>Moduły

Azure Automation obsługuje szereg domyślnych modułów, w tym niektóre moduły AzureRM (AzureRM.Automation) i moduł zawierający kilka wewnętrznych cmdlet. Obsługiwane są również moduły, które można instalować, w tym moduły Az (Az.Automation), obecnie używane w preferencji do modułów AzureRM. Aby uzyskać szczegółowe informacje o modułach dostępnych dla twoich modułów Runbook i konfiguracji DSC, zobacz Zarządzanie modułami w [programie Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certyfikaty

Azure Automation używa [certyfikatów do uwierzytelniania](shared-resources/certificates.md) na platformie Azure lub dodaje je do zasobów platformy Azure lub innych firm. Certyfikaty są bezpiecznie przechowywane w celu uzyskania dostępu przez podręczniki Runbook i konfiguracje DSC.

Twoje podręczniki Runbook mogą używać certyfikatów z podpisem własnym, które nie są podpisane przez urząd certyfikacji. Zobacz [Tworzenie nowego certyfikatu.](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Stanowiska

Azure Automation obsługuje środowisko do uruchamiania zadań z tego samego konta usługi Automation. Pojedynczy podręcznik Runbook może mieć wiele zadań uruchomionych jednocześnie. Im więcej zadań jest uruchamianych w tym samym czasie, tym częściej można je wysyłać do tej samej piaskownicy. 

Zadania uruchomione w tym samym procesie piaskownicy mogą mieć na siebie wpływ. Jednym z przykładów jest uruchomienie polecenia cmdlet [Disconnect-AzAccount.](/powershell/module/az.accounts/disconnect-azaccount) Wykonanie tego polecenia cmdlet rozłącza każde zadanie runbook w udostępnionym procesie piaskownicy. Aby uzyskać przykład pracy z tym scenariuszem, zobacz Prevent concurrent jobs (Zapobieganie [współbieżnych zadań).](manage-runbooks.md#prevent-concurrent-jobs)

>[!NOTE]
>Zadania programu PowerShell uruchomione z elementem Runbook uruchomionym w piaskownicy platformy Azure mogą nie być uruchamiane w trybie pełnego [języka programu PowerShell.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="job-statuses"></a>Stany zadania

W poniższej tabeli opisano stany możliwe dla zadania. Możesz wyświetlić podsumowanie stanu dla wszystkich zadań runbook lub przejść do szczegółów określonego zadania runbook w Azure Portal. Możesz również skonfigurować integrację z obszarem roboczym usługi Log Analytics w celu przekazywania stanu zadania runbook i strumieni zadań. Aby uzyskać więcej informacji na temat integracji z dziennikami Azure Monitor, zobacz Przekazywanie stanu zadania i strumieni zadań z usługi [Automation do Azure Monitor dzienników.](automation-manage-send-joblogs-log-analytics.md) Zobacz też [temat Uzyskiwanie stanów zadań,](manage-runbooks.md#obtain-job-statuses) aby uzyskać przykład pracy ze stanami w elementach Runbook.

| Stan | Opis |
|:--- |:--- |
| Aktywowanie |Zadanie jest aktywowane. |
| Ukończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Nie można skompilować graficznego lub przepływu pracy programu PowerShell. Nie można uruchomić element runbook programu PowerShell lub zadanie miało wyjątek. Zobacz [Azure Automation typów elementów Runbook.](automation-runbook-types.md)|
| Niepowodzenie, oczekiwanie na zasoby |Zadanie nie powiodło się, [](#fair-share) ponieważ trzykrotnie osiągnęło limit sprawiedliwego udostępniania i za każdym razem uruchomiło się z tego samego punktu kontrolnego lub od początku tego samego runbook. |
| W kolejce |Zadanie oczekuje, aż zasoby w procesach roboczych usługi Automation staną się dostępne, aby można je było rozpocząć. |
| Wznawianie |System wznawia zadanie po jego wstrzymaniu. |
| Uruchomienie |Zadanie jest uruchomione. |
| Uruchamianie, oczekiwanie na zasoby |Zadanie zostało zwolniony, ponieważ osiągnęło limit sprawiedliwego udostępniania. Zostanie wznowione wkrótce od ostatniego punktu kontrolnego. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego i system uruchamia je. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika przed jego ukończeniem. |
| Zatrzymywanie |System zatrzymuje zadanie. |
| Suspended |Dotyczy tylko [elementów Runbook graficznych i elementów Runbook przepływu pracy programu PowerShell.](automation-runbook-types.md) Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie Runbook. Jeśli runbook nie ma punktu kontrolnego, rozpoczyna się od początku. Jeśli ma punkt kontrolny, można go uruchomić ponownie i wznowić od ostatniego punktu kontrolnego. System wstrzymuje element Runbook tylko wtedy, gdy wystąpi wyjątek. Domyślnie zmienna `ErrorActionPreference` jest ustawiona na wartość Kontynuuj, co oznacza, że zadanie nadal działa w przypadku błędu. Jeśli zmienna preferencji jest ustawiona na Zatrzymaj, zadanie zawiesza się po błędzie.  |
| Zawieszanie |Dotyczy tylko [elementów Runbook graficznych i elementów Runbook przepływu pracy programu PowerShell.](automation-runbook-types.md) System próbuje wstrzymać zadanie na żądanie użytkownika. Przez zawieszeniem element Runbook musi dotrzeć do swojego następnego punktu kontrolnego. Jeśli ostatni punkt kontrolny został już przekazany, zostanie on ukończony, zanim będzie można go zawiesić. |

## <a name="activity-logging"></a>Dziennik aktywności

Wykonywanie elementami Runbook w Azure Automation zapisuje szczegóły w dzienniku aktywności dla konta usługi Automation. Aby uzyskać szczegółowe informacje na temat korzystania z dziennika, zobacz [Pobieranie szczegółów z dziennika aktywności](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Wyjątki

W tej sekcji opisano niektóre sposoby obsługi wyjątków lub sporadyczny problem w twoich runbookach. Przykładem jest wyjątek WebSocket. Prawidłowa obsługa wyjątków zapobiega przejściowym awariom sieci powodującym awarie twoich runbook.

### <a name="erroractionpreference"></a>ErrorActionPreference

Zmienna [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) określa, jak program PowerShell reaguje na błąd niepowodujący zakończenia. Błędy kończące zawsze są przerywane i nie mają na nie wpływu `ErrorActionPreference` .

Gdy w programie Runbook jest używany błąd , zwykle niepowodujący zakończenia, taki jak polecenie `ErrorActionPreference` `PathNotFound` cmdlet [Get-ChildItem,](/powershell/module/microsoft.powershell.management/get-childitem) zatrzymuje ukończenie działania. W poniższym przykładzie pokazano użycie funkcji `ErrorActionPreference` . Końcowe polecenie [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) nigdy nie jest wykonywane, ponieważ skrypt zatrzymuje się.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Wypróbuj catch na końcu

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) jest używany w skryptach programu PowerShell do obsługi błędów kończących. Skrypt może używać tego mechanizmu do przechwytowania określonych wyjątków lub wyjątków ogólnych. Instrukcja `catch` powinna być używana do śledzenia lub próby obsługi błędów. Poniższy przykład próbuje pobrać plik, który nie istnieje. `System.Net.WebException`Przechwytuje wyjątek i zwraca ostatnią wartość dla każdego innego wyjątku.

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) może służyć do generowania błędu zakończenia. Ten mechanizm może być przydatny podczas definiowania własnej logiki w ramach runbook. Jeśli skrypt spełnia kryterium, które powinno go zatrzymać, może użyć `throw` instrukcji , aby zatrzymać. W poniższym przykładzie użyto tej instrukcji do pokazania wymaganego parametru funkcji.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>błędy

Twoje podręczniki Runbook muszą obsługiwać błędy. Azure Automation obsługuje dwa typy błędów programu PowerShell: błędy przerywania i niepowiązywujące zakończenia. 

Błędy przerywania przerywają wykonywanie runbook, gdy wystąpią. W przypadku tego podręcznika runbook stan zadania to Niepowodzenie.

Błędy niepowiązyujące się z błędami umożliwiają kontynuowanie skryptu nawet po ich zakończeniu. Przykładem błędu bez zakończenia jest błąd, który występuje, gdy element runbook używa `Get-ChildItem` polecenia cmdlet ze ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje pracę do następnego folderu. Błąd w tym przypadku nie ustawia stanu zadania runbook na Niepowodzenie, a zadanie może nawet zostać zakończone. Aby wymusić zatrzymanie runbook po niepowiązywających się błędach, możesz użyć polecenia `ErrorAction Stop` w poleceniach cmdlet .

## <a name="calling-processes"></a>Wywoływanie procesów

Aplikacje Runbook uruchamiane w piaskownicach platformy Azure nie obsługują wywoływania procesów, takich jak pliki wykonywalne **(pliki exe)** lub podprocesy. Przyczyną jest to, że piaskownica platformy Azure jest udostępnionym procesem uruchamianym w kontenerze, który może nie mieć dostępu do wszystkich bazowych interfejsów API. W przypadku scenariuszy wymagających oprogramowania innych firm lub wywołań do podprocesu należy wykonać runbook w hybrydowym procesu [roboczego runbook.](automation-hybrid-runbook-worker.md)

## <a name="device-and-application-characteristics"></a>Charakterystyka urządzenia i aplikacji

Zadania runbook w piaskownicach platformy Azure nie mogą uzyskać dostępu do żadnych cech urządzenia ani aplikacji. Najczęściej używanym interfejsem API do wykonywania zapytań dotyczących metryk wydajności w systemie Windows jest usługa WMI, a niektóre typowe metryki to użycie pamięci i procesora CPU. Nie ma jednak znaczenia, jaki interfejs API jest używany, ponieważ zadania uruchomione w chmurze nie mogą uzyskać dostępu do implementacji rozwiązania Web-Based Enterprise Management (WBEM) firmy Microsoft. Ta platforma jest zbudowana na model wspólnych informacji (CIM), zapewniając standardy branżowe definiujące charakterystykę urządzeń i aplikacji.

## <a name="webhooks"></a>Elementy webhook

Usługi zewnętrzne, na przykład Azure DevOps Services i GitHub, mogą uruchamiać w programie Azure Automation. Aby wykonać ten typ uruchamiania, usługa używa [webhook](automation-webhooks.md) za pośrednictwem pojedynczego żądania HTTP. Użycie elementu webhook umożliwia uruchamianie elementu runbook bez implementacji pełnej Azure Automation funkcji.

## <a name="shared-resources"></a><a name="fair-share"></a>Zasoby udostępnione

Aby udostępniać zasoby wszystkim zasobom Runbook w chmurze, platforma Azure używa koncepcji nazywanej uczciwym udostępnianiem. Korzystając z funkcji sprawiedliwego udostępniania, platforma Azure tymczasowo zwalnia lub zatrzymuje wszystkie zadania uruchomione przez ponad trzy godziny. Zadania dla [modułów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [python runbook](automation-runbook-types.md#python-runbooks) są zatrzymywane i nie są ponownie uruchamiane, a stan zadania to Zatrzymane.

W przypadku długotrwałych Azure Automation zadań zaleca się użycie hybrydowego procesu roboczego runbook. Hybrydowe środowiska pracy runbook nie są ograniczone przez sprawiedliwe udostępnianie i nie mają ograniczenia czasu wykonywania tego typu. Pozostałe limity [zadań dotyczą](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zarówno piaskownic platformy Azure, jak i hybrydowych pracowników runbook. Hybrydowe środowiska pracy runbook nie są ograniczone limitem 3-godzinnego sprawiedliwego udostępniania, jednak należy opracowywać je w taki sposób, aby działały w środowiskach workers, które obsługują ponowne uruchamianie z nieoczekiwanych problemów z infrastrukturą lokalną.

Inną opcją jest optymalizacja podręcznika Runbook przy użyciu podrzędnych książek Runbook. Na przykład twój runbook może przechodzić w pętli przez tę samą funkcję w kilku zasobach, na przykład przy użyciu operacji bazy danych na kilku bazach danych. Możesz przenieść tę funkcję do podrzędnego podręcznika [Runbook](automation-child-runbooks.md) i wywołać go za pomocą [start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook) Podrzędne podręczniki Runbook są wykonywane równolegle w oddzielnych procesach.

Użycie podrzędnego elementu Runbook zmniejsza łączny czas ukończenia nadrzędnego elementu Runbook. Za pomocą polecenia cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) można sprawdzić stan zadania podrzędnego podręcznika Runbook, jeśli po zakończeniu działania podrzędnego nadal będzie wykonywanych więcej operacji.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z programem Runbook programu PowerShell, zobacz [Samouczek: tworzenie runbook programu PowerShell.](learn/automation-tutorial-runbook-textual-powershell.md)
* Aby pracować z podręcznikami Runbook, zobacz [Manage runbook in Azure Automation (Zarządzanie Azure Automation Runbook).](manage-runbooks.md)
* Aby uzyskać szczegółowe informacje o programie PowerShell, zobacz [PowerShell Docs](/powershell/scripting/overview).
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation#automation).
