---
title: Omówienie Update Management Azure Automation
description: Ten artykuł zawiera omówienie funkcji Update Management, która implementuje aktualizacje dla maszyn z systemami Windows i Linux.
services: automation
ms.subservice: update-management
ms.date: 11/30/2020
ms.topic: conceptual
ms.openlocfilehash: 37ab05ce7e963ab7fdc4d2b02e254adaa205446c
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327495"
---
# <a name="update-management-overview"></a>Omówienie rozwiązania Update Management

Update Management w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemami Windows i Linux na platformie Azure, w środowiskach lokalnych i w innych środowiskach w chmurze. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów.

> [!NOTE]
> Nie można użyć komputera skonfigurowanego przy użyciu Update Management do uruchamiania skryptów niestandardowych z Azure Automation. Na tym komputerze można uruchomić tylko skrypt aktualizacji podpisany przez firmę Microsoft.

> [!NOTE]
> W tej chwili Włączanie Update Management bezpośrednio z serwera z włączonymi Łukiemmi nie jest obsługiwane. Zapoznaj się z tematem [włączanie Update Management na koncie usługi Automation](../../automation/update-management/enable-from-automation-account.md) , aby poznać wymagania i jak włączyć serwer.

Aby automatycznie pobierać i instalować dostępne poprawki *krytyczne* i *zabezpieczenia* na maszynie wirtualnej platformy Azure, przejrzyj [automatyczną poprawkę gościa maszyny wirtualnej](../../virtual-machines/windows/automatic-vm-guest-patching.md) dla maszyn wirtualnych z systemem Windows.

Przed wdrożeniem Update Management i włączeniem maszyn w celu zarządzania należy zapoznać się z informacjami w poniższych sekcjach.  

## <a name="about-update-management"></a>Informacje o Update Management

Maszyny zarządzane przez Update Management polegają na następujących kwestiach w celu przeprowadzenia oceny i wdrożenia aktualizacji:

* [Agent log Analytics](../../azure-monitor/platform/log-analytics-agent.md) dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu Runbook usługi Automation (automatycznie instalowany po włączeniu Update Management na maszynie)
* Microsoft Update lub [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) dla maszyn z systemem Windows
* Prywatne lub publiczne repozytorium aktualizacji dla maszyn z systemem Linux

Na poniższym diagramie przedstawiono, w jaki sposób Update Management oceniać i stosować aktualizacje zabezpieczeń dla wszystkich podłączonych serwerów z systemem Windows Server i Linux w obszarze roboczym:

![Przepływ pracy Update Management](./media/overview/update-mgmt-updateworkflow.png)

Update Management może służyć do natywnego wdrażania na maszynach w wielu subskrypcjach w ramach tej samej dzierżawy.

Po wydaniu pakietu trwa od 2 do 3 godzin, aby poprawka była wyświetlana dla maszyn z systemem Linux na potrzeby oceny. W przypadku maszyn z systemem Windows trwa od 12 do 15 godzin, aby poprawka była wyświetlana na potrzeby oceny po jej udostępnieniu. Gdy maszyna ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekazuje informacje zbiorczo do Azure Monitor dzienników. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin. W przypadku maszyny z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent Log Analytics zostanie uruchomiony ponownie, skanowanie zgodności zostanie rozpoczęte w ciągu 15 minut.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest uruchamiane w ciągu 15 minut od ponownego uruchomienia agenta Log Analytics, przed zainstalowaniem aktualizacji i po zainstalowaniu aktualizacji.

Update Management raportuje, jak to jest aktualność maszyny, na podstawie źródła, z którym jest skonfigurowana synchronizacja. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania do [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), w zależności od czasu ostatniej synchronizacji programu WSUS z Microsoft Update, wyniki mogą się różnić od tego, co Microsoft Update. Takie zachowanie jest takie samo dla maszyn z systemem Linux, które są skonfigurowane do raportowania do lokalnego repozytorium, a nie do repozytorium publicznego.

> [!NOTE]
> Aby poprawnie zgłosić usługę, Update Management wymaga włączenia określonych adresów URL i portów. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [Konfiguracja sieci](../automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które wymagają aktualizacji przez utworzenie zaplanowanego wdrożenia. Aktualizacje sklasyfikowane jako opcjonalne nie są uwzględnione w zakresie wdrożenia dla maszyn z systemem Windows. Zakres wdrożenia obejmuje tylko wymagane aktualizacje.

Zaplanowane wdrożenie definiuje, które maszyny docelowe otrzymują odpowiednie aktualizacje. Robi to przez jawne określenie pewnych maszyn lub wybranie [grupy komputerów](../../azure-monitor/platform/computer-groups.md) , która jest oparta na przeszukiwaniu dzienników określonego zestawu maszyn (lub w [zapytaniu platformy Azure](query-logs.md) , które dynamicznie wybiera maszyny wirtualne platformy Azure na podstawie określonych kryteriów). Te grupy różnią się od [konfiguracji zakresu](../../azure-monitor/insights/solution-targeting.md), która jest używana do sterowania kierowaniem maszyn, które odbierają konfigurację w celu włączenia Update Management. Zapobiega to wykonywaniu i raportowaniu zgodności aktualizacji oraz instalowaniu zatwierdzonych wymaganych aktualizacji.

Podczas definiowania wdrożenia należy również określić harmonogram zatwierdzania i ustawiania przedziału czasu, w którym można zainstalować aktualizacje. Ten okres jest nazywany oknem obsługi. 20-minutowy zakres okna obsługi jest zarezerwowany dla ponownych uruchomień, przy założeniu, że jest to wymagane i wybrano odpowiednią opcję ponownego uruchomienia. Jeśli stosowanie poprawek trwa dłużej niż oczekiwano, a w oknie obsługi jest mniej niż 20 minut, ponowne uruchomienie nie zostanie przeprowadzone.

Aktualizacje są instalowane za pomocą elementów runbook w usłudze Azure Automation. Nie można wyświetlić tych elementów Runbook i nie wymagają one żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji tworzy harmonogram, który uruchamia główny element Runbook aktualizacji w określonym czasie dla dołączonych maszyn. Główny element Runbook uruchamia podrzędny element Runbook na każdym agencie, aby zainstalować wymagane aktualizacje.

Zgodnie z datą i godziną określoną we wdrożeniu aktualizacji maszyny docelowe wykonują wdrożenie równolegle. Przed rozpoczęciem instalacji zostanie uruchomione skanowanie w celu sprawdzenia, czy aktualizacje są nadal wymagane. W przypadku komputerów klienckich programu WSUS, jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji nie powiedzie się.

Posiadanie maszyny zarejestrowanej dla Update Management w więcej niż jednym Log Analytics obszarze roboczym (nazywanym również wieloadresowości) nie jest obsługiwane.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Obsługiwane typy klientów

W poniższej tabeli wymieniono systemy operacyjne obsługiwane w przypadku ocen aktualizacji i poprawek. Stosowanie poprawek wymaga hybrydowego procesu roboczego elementu Runbook, który jest instalowany automatycznie po włączeniu maszyny wirtualnej lub serwera do zarządzania przez Update Management. Aby uzyskać informacje o wymaganiach dotyczących systemu hybrydowego procesu roboczego elementu Runbook, zobacz [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) i [wdrażanie hybrydowego procesu roboczego elementu Runbook programu Linux](../automation-linux-hrw-install.md).

> [!NOTE]
> Ocena aktualizacji maszyn z systemem Linux jest obsługiwana tylko w niektórych regionach, jak wymieniono w [tabeli mapowania](../how-to/region-mappings.md#supported-mappings)obszaru roboczego konta usługi Automation i log Analytics.

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2 (wersja RTM i SP1 standard)| Update Management obsługuje oceny i poprawki dla tego systemu operacyjnego. [Hybrydowy proces roboczy elementu Runbook](../automation-windows-hrw-install.md) jest obsługiwany w przypadku systemu Windows Server 2008 R2. |
|CentOS 6 i 7 (x64)      | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji. Stosowanie poprawek opartych na klasyfikacji wymaga `yum` zwrócenia danych zabezpieczeń, które nie są dostępne w wersjach RTM CentOS. Aby uzyskać więcej informacji na temat stosowania poprawek opartych na klasyfikacji na CentOS, zobacz [Aktualizacja klasyfikacji w systemie Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 i 7 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 12 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 LTS (x64)      |Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyn wirtualnych platformy Azure mogą być zarządzane za pomocą Update Management. Update Management działa na samych wystąpieniach, a nie na obrazie podstawowym. Należy zaplanować aktualizacje w sposób przyrostowy, aby nie wszystkie wystąpienia maszyn wirtualnych były aktualizowane jednocześnie. Węzły dla zestawów skalowania maszyn wirtualnych można dodać, wykonując czynności opisane w sekcji [Dodawanie maszyny spoza platformy Azure do Change Tracking i spisu](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-client-types"></a>Nieobsługiwane typy klientów

W poniższej tabeli wymieniono nieobsługiwane systemy operacyjne:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Systemy operacyjne klienta (takie jak Windows 7 i Windows 10) nie są obsługiwane.<br> W przypadku usługi Azure Windows Virtual Desktop (WVD) zalecana metoda<br> do zarządzania aktualizacjami służy [Configuration Manager Microsoft Endpoint Protection](../../virtual-desktop/configure-automatic-updates.md) for Windows 10 Client Patch Management. |
|Windows Server Nano Server 2016     | Nieobsługiwane.       |
|Węzły usługi Azure Kubernetes | Nieobsługiwane. Użyj procesu poprawek opisanego w temacie [stosowanie aktualizacji zabezpieczeń i jądra do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Wymagania dotyczące klientów

Poniższe informacje opisują wymagania klienta specyficzne dla systemu operacyjnego. Aby uzyskać dodatkowe wskazówki, zobacz [Planowanie sieci](#ports). Aby zrozumieć wymagania klienta dotyczące protokołu TLS 1,2, zobacz [Wymuszanie protokołu tls 1,2 dla Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem WSUS lub muszą mieć dostęp do Microsoft Update. W przypadku maszyn hybrydowych zaleca się zainstalowanie agenta Log Analytics dla systemu Windows, najpierw łącząc maszynę z [włączonymi serwerami usługi Azure Arc](../../azure-arc/servers/overview.md), a następnie używając Azure Policy do przypisywania [agenta wdrożenia log Analytics do zasad wbudowanych dla maszyn usługi Windows Azure](../../governance/policy/samples/built-in-policies.md#monitoring) . Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Możesz użyć Update Management z usługą Microsoft Endpoint Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integrowanie Update Management z Configuration Manager punktu końcowego systemu Windows](mecmintegration.md). [Agent log Analytics dla systemu Windows](../../azure-monitor/platform/agent-windows.md) jest wymagany dla serwerów z systemem Windows zarządzanych przez lokacje w środowisku Configuration Manager. 

Domyślnie maszyny wirtualne z systemem Windows wdrożone w witrynie Azure Marketplace są ustawione tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. Takie zachowanie nie zmienia się po dodaniu maszyn wirtualnych z systemem Windows do obszaru roboczego. Jeśli aktualizacje nie są aktywnie zarządzane przy użyciu Update Management, mają zastosowanie domyślne zachowanie (aby automatycznie zastosować aktualizacje).

> [!NOTE]
> Zasady grupy można modyfikować, aby można było wykonać ponowny rozruch maszyny tylko przez użytkownika, a nie przez system. Maszyny zarządzane mogą zostać zablokowane, jeśli Update Management nie ma uprawnień do ponownego uruchomienia maszyny bez ręcznej interakcji z użytkownikiem. Aby uzyskać więcej informacji, zobacz [Configure zasady grupy Settings for Automatic Updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

W przypadku systemu Linux maszyna wymaga dostępu do repozytorium aktualizacji — prywatne lub publiczne. Do współdziałania z Update Managementami wymagany jest protokół TLS 1,1 lub TLS 1,2. Update Management nie obsługuje agenta Log Analytics dla systemu Linux, który został skonfigurowany do raportowania do więcej niż jednego obszaru roboczego Log Analytics. Na komputerze musi być zainstalowany język Python 2. x.

> [!NOTE]
> Ocena aktualizacji maszyn z systemem Linux jest obsługiwana tylko w określonych regionach. Zobacz [tabelę mapowania](../how-to/region-mappings.md#supported-mappings)obszaru roboczego konta usługi Automation i log Analytics.

W przypadku maszyn hybrydowych zaleca się zainstalowanie agenta Log Analytics dla systemu Linux, najpierw łącząc maszynę z [włączonymi serwerami usługi Azure Arc](../../azure-arc/servers/overview.md), a następnie używając Azure Policy do przypisywania programu [Deploy log Analytics Agent do systemu Linux Azure Arc Policy Machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Maszyny wirtualne utworzone na podstawie obrazów na żądanie Red Hat Enterprise Linux (RHEL), które są dostępne w portalu Azure Marketplace, są zarejestrowane w celu uzyskania dostępu do [infrastruktury aktualizacji firmy Red Hat (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) wdrożonej na platformie Azure. Wszystkie inne dystrybucje systemu Linux należy zaktualizować z repozytorium plików online dystrybucji przy użyciu metod obsługiwanych przez dystrybucję.

## <a name="permissions"></a>Uprawnienia

Do tworzenia wdrożeń aktualizacji i zarządzania nimi wymagane są określone uprawnienia. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [dostęp oparty na rolach — Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Składniki Update Management

Update Management używa zasobów opisanych w tej sekcji. Te zasoby są automatycznie dodawane do konta usługi Automation po włączeniu Update Management.

### <a name="hybrid-runbook-worker-groups"></a>Grupy hybrydowych procesów roboczych elementu Runbook

Po włączeniu Update Management każda maszyna z systemem Windows, która jest bezpośrednio połączona z obszarem roboczym Log Analytics, zostanie automatycznie skonfigurowana jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook, które obsługują Update Management.

Każda maszyna z systemem Windows zarządzana przez Update Management jest wymieniona w okienku grupy hybrydowych procesów roboczych jako Grupa hybrydowych procesów roboczych systemu dla konta usługi Automation. Grupy używają `Hostname FQDN_GUID` konwencji nazewnictwa. Nie można kierować tymi grupami do elementów Runbook na Twoim koncie. Jeśli spróbujesz, próba zakończy się niepowodzeniem. Te grupy są przeznaczone do obsługi tylko Update Management. Aby dowiedzieć się więcej na temat wyświetlania listy maszyn systemu Windows skonfigurowanych jako hybrydowy proces roboczy elementu Runbook, zobacz temat [Wyświetlanie hybrydowych procesów roboczych elementów Runbook](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Możesz dodać maszynę z systemem Windows do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, jeśli używasz tego samego konta dla Update Management i członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania Operations Manager jest [połączona z obszarem roboczym log Analytics](../../azure-monitor/platform/om-agents.md), następujące pakiety administracyjne są instalowane w Operations Manager. Te pakiety administracyjne są również zainstalowane dla Update Management na urządzeniach z systemem Windows podłączonych bezpośrednio. Pakietów administracyjnych nie trzeba konfigurować ani zarządzać nimi.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> Jeśli grupa zarządzania programu Operations Manager 1807 lub 2019 jest połączona z obszarem roboczym Log Analytics z agentami skonfigurowanymi w grupie zarządzania w celu zbierania danych dziennika, należy zastąpić parametr `IsAutoRegistrationEnabled` i ustawić dla niego wartość true w regule **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** .

Aby uzyskać więcej informacji o aktualizacjach pakietów administracyjnych, zobacz [Connect Operations Manager to Azure monitor Logs](../../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Aby uzyskać Update Management w pełni zarządzać maszynami przy użyciu agenta Log Analytics, należy zaktualizować agenta Log Analytics dla systemu Windows lub agenta Log Analytics w systemie Linux. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [jak uaktualnić agenta Operations Manager](/system-center/scom/deploy-upgrade-agents). W środowiskach, w których jest używana Operations Manager, musi być uruchomiony program System Center Operations Manager 2012 R2 UR 14 lub nowszy.

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-sources"></a>Obsługiwane źródła

W poniższej tabeli opisano połączone źródła obsługiwane przez Update Management:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie uruchamia instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie uruchamia instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Update Management zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/><br/>Bezpośrednie połączenie z agentem Operations Manager do Azure Monitor dzienników nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Update Management skanuje zarządzane maszyny pod kątem danych przy użyciu następujących reguł. Wyświetlenie zaktualizowanych danych z zarządzanych maszyn na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

* Każdy komputer z systemem Windows — Update Management skanuje dwa razy dziennie dla każdej maszyny.

* Każda maszyna z systemem Linux Update Management skanuje co godzinę.

Średnie użycie danych przez Azure Monitor dzienników dla maszyny używającej Update Management wynosi około 25 MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie, w zależności od środowiska. Zalecamy monitorowanie środowiska, aby śledzić dokładne użycie. Aby uzyskać więcej informacji na temat analizowania danych dzienników Azure Monitor, zobacz [Zarządzanie użyciem i kosztem](../../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planowanie sieci

Poniższe adresy są wymagane dla Update Management. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

Podczas tworzenia reguł zabezpieczeń grupy sieciowej lub konfigurowania zapory platformy Azure, aby zezwalać na ruch do usługi Automation i obszaru roboczego Log Analytics, należy użyć [znacznika usługi](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** i **AzureMonitor**. Upraszcza to ciągłe zarządzanie regułami zabezpieczeń sieci. Aby bezpiecznie i prywatnie połączyć się z usługą Automation z maszyn wirtualnych platformy Azure, zapoznaj się z tematem [Korzystanie z prywatnego linku platformy Azure](../how-to/private-link-security.md). Aby uzyskać bieżący tag usługi i informacje o zakresie do uwzględnienia w ramach konfiguracji lokalnych zapór, zobacz [pliki JSON do pobrania](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

W przypadku maszyn z systemem Windows należy również zezwolić na ruch do wszystkich punktów końcowych wymaganych przez Windows Update. Zaktualizowaną listę wymaganych punktów końcowych można znaleźć w przypadku [problemów związanych z protokołem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Jeśli masz lokalny [serwer Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musisz również zezwolić na ruch do serwera określonego w [kluczu WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

W przypadku maszyn z systemem Red Hat Linux zapoznaj [się z tematem adresy IP dla serwerów RHUI Content Delivery](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) for Required Endpoints. W przypadku innych dystrybucji systemu Linux zapoznaj się z dokumentacją dostawcy.

Aby uzyskać więcej informacji na temat portów wymaganych dla hybrydowego procesu roboczego elementu Runbook, zobacz [adresy Update Management dla hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](../../azure-monitor/platform/gateway.md) , a następnie skonfigurować maszynę do nawiązywania połączeń za pomocą bramy w celu Azure Automation i Azure monitor.

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższej tabeli zdefiniowano klasyfikacje, które Update Management obsługiwane w przypadku aktualizacji systemu Windows.

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacja dla konkretnego problemu, która stanowi odpowiedź na krytyczną usterkę niepowiązaną z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacja problemu dotyczącego określonego produktu, związanego z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zbiorczy zestaw poprawek, które są pakowane razem w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowe funkcje produktu dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Zbiorczy zestaw poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacja dla wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzie lub funkcja, która pomaga wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który jest aktualnie zainstalowany.        |

W następnej tabeli zdefiniowano obsługiwane klasyfikacje aktualizacji systemu Linux.

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia ani aktualizacje zabezpieczeń.        |

>[!NOTE]
>Klasyfikacja aktualizacji dla maszyn z systemem Linux jest dostępna tylko w przypadku, gdy są używane w obsługiwanych regionach chmury publicznej platformy Azure. W przypadku korzystania z Update Management w następujących regionach w chmurze krajowej:
>* Wersja platformy Azure dla administracji USA
>* 21Vianet w Chinach
>
> nie ma klasyfikacji aktualizacji systemu Linux i są one zgłaszane w kategorii **inne aktualizacje** . Update Management używa danych opublikowanych przez obsługiwane dystrybucje, w tym w oddzielnym pliku. [OVAL](https://oval.mitre.org/) Ponieważ dostęp do Internetu jest ograniczony z tych chmur narodowych, Update Management nie może uzyskać dostępu do tych plików i korzystać z nich.

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze, a następnie wyświetlać dane oceny z powodu wzbogacania danych w chmurze. W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma informacji dostępnych w wersji RTM. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla poniższego polecenia, Update Management może zostać poprawione na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w systemie CentOS. W tej chwili ograniczona pomoc techniczna jest świadczona klientom, którzy mogą korzystać z tej funkcji samodzielnie.

Aby sklasyfikować aktualizacje w systemie Red Hat Enterprise w wersji 6, należy zainstalować wtyczkę yum-Security. W Red Hat Enterprise Linux 7 wtyczka jest już częścią yum i nie trzeba instalować żadnych elementów. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)firmy Red Hat.

## <a name="integrate-update-management-with-configuration-manager"></a>Integracja Update Management z usługą Configuration Manager

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager na potrzeby zarządzania komputerami, serwerami i urządzeniami przenośnymi, również opierają się na sile i dojrzałości Configuration Manager, aby ułatwić zarządzanie aktualizacjami oprogramowania. Aby dowiedzieć się, jak zintegrować Update Management z Configuration Manager, zobacz [integrowanie Update Management z Configuration Manager punktu końcowego systemu Windows](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Aktualizacje innych firm w systemie Windows

Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji, aby zaktualizować obsługiwane systemy Windows, WSUS lub Windows Update. Narzędzia, takie jak [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) , umożliwiają importowanie i publikowanie aktualizacji niestandardowych za pomocą programu WSUS. Ten scenariusz umożliwia Update Management aktualizowania maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [Install Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Poniżej przedstawiono sposoby włączania Update Management i wybierania maszyn, które mają być zarządzane:

- Użycie [szablonu Menedżer zasobów](enable-from-template.md) platformy Azure w celu wdrożenia Update Management na nowym lub istniejącym koncie usługi Automation i Azure Monitor Log Analytics obszaru roboczego w ramach subskrypcji. Nie konfiguruje zakresu maszyn, które powinny być zarządzane, jest to wykonywane w osobnym kroku po użyciu szablonu.

- Z poziomu [konta usługi Automation](enable-from-automation-account.md) dla jednej lub wielu maszyn platformy Azure i spoza platformy Azure, w tym serwerów z włączonym łukiem.

- Za pomocą metody [elementu Runbook](enable-from-runbook.md) **enable-AutomationSolution** .

- Dla [wybranej maszyny wirtualnej platformy Azure](enable-from-vm.md) na stronie **maszyny wirtualne** w Azure Portal. Ten scenariusz jest dostępny dla maszyn wirtualnych z systemami Linux i Windows.

- Dla [wielu maszyn wirtualnych platformy Azure](enable-from-portal.md) , wybierając je na stronie **maszyny wirtualne** w Azure Portal.

> [!NOTE]
> Update Management wymaga połączenia obszaru roboczego Log Analytics z kontem usługi Automation. Aby uzyskać ostateczną listę obsługiwanych regionów, zobacz [mapowania obszaru roboczego platformy Azure](../how-to/region-mappings.md). Mapowania regionów nie mają wpływu na możliwość zarządzania maszynami wirtualnymi w innym regionie niż konto usługi Automation.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z Update Management, zobacz [Zarządzanie aktualizacjami dla maszyn wirtualnych](manage-updates-for-vm.md).

* Zapoznaj się z często zadawanymi pytaniami dotyczącymi Update Management w [Azure Automation często zadawanych pytań](../automation-faq.md).
