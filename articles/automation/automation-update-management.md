---
title: Update Management rozwiązanie na platformie Azure
description: W tym artykule opisano, jak używać rozwiązania Update Management platformy Azure do zarządzania aktualizacjami dla maszyn z systemami Windows i Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617399"
---
# <a name="update-management-solution-in-azure"></a>Update Management rozwiązanie na platformie Azure

**Update Management rozwiązanie** w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn z systemami Windows i Linux na platformie Azure, w środowiskach lokalnych i w innych środowiskach w chmurze. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów.

Update Management dla maszyn wirtualnych można włączyć przy użyciu następujących metod:

- Z [konta usługi Azure Automation](automation-onboard-solutions-from-automation-account.md) dla jednej lub wielu maszyn platformy Azure i ręcznie dla maszyn nienależących do platformy Azure.

- Dla jednej maszyny wirtualnej platformy Azure na stronie maszyny wirtualnej w Azure Portal. Ten scenariusz jest dostępny dla maszyn wirtualnych z systemami [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Dla [wielu maszyn wirtualnych platformy Azure](manage-update-multi.md) , wybierając je na stronie maszyny wirtualne w Azure Portal. 

> [!NOTE]
> **Rozwiązanie Update Management** wymaga połączenia obszaru roboczego log Analytics z kontem usługi Automation. Aby uzyskać ostateczną listę obsługiwanych regionów, zobacz [mapowania obszaru roboczego platformy Azure](./how-to/region-mappings.md). Mapowania regionów nie mają wpływu na możliwość zarządzania maszynami wirtualnymi w innym regionie niż konto usługi Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Dostępny jest [szablon Menedżer zasobów](automation-update-management-deploy-template.md) platformy Azure, który pomaga wdrożyć **rozwiązanie Update Management** na nowym lub istniejącym koncie usługi Automation i log Analytics obszarze roboczym w ramach subskrypcji.

## <a name="solution-overview"></a>Omówienie rozwiązania

Maszyny zarządzane przez Update Management używają następujących konfiguracji do przeprowadzania oceny i aktualizacji wdrożeń:

* Agent Log Analytics dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Microsoft Update lub Windows Server Update Services (WSUS) dla maszyn z systemem Windows

Na poniższym diagramie przedstawiono sposób, w jaki rozwiązanie ocenia i stosuje aktualizacje zabezpieczeń do wszystkich połączonych maszyn z systemem Windows Server i Linux w obszarze roboczym:

![Przepływ procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Rozwiązanie Update Management pozwala natywnie dołączać maszyny z różnych subskrypcji do jednej dzierżawy.

Po wydaniu pakietu trwa od 2 do 3 godzin, aby poprawka była wyświetlana dla maszyn z systemem Linux na potrzeby oceny. W przypadku maszyn z systemem Windows trwa od 12 do 15 godzin, aby poprawka była wyświetlana na potrzeby oceny po jej udostępnieniu.

Gdy maszyna ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekazuje informacje zbiorczo do Azure Monitor dzienników. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od ponownego uruchomienia agenta Log Analytics, przed zainstalowaniem aktualizacji i po zainstalowaniu aktualizacji.

W przypadku maszyny z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent Log Analytics zostanie uruchomiony ponownie, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Rozwiązanie raportuje, jak to jest aktualność maszyny, na podstawie źródła, z którym użytkownik jest skonfigurowany do synchronizacji. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania do programu WSUS, w zależności od tego, kiedy program WSUS został ostatnio zsynchronizowany z Microsoft Update, wyniki mogą się różnić od tego, co Microsoft Update. Takie zachowanie jest takie samo dla maszyn z systemem Linux, które są skonfigurowane do raportowania do lokalnego repozytorium, a nie do repozytorium publicznego.

> [!NOTE]
> Aby poprawnie zgłosić usługę, Update Management wymaga włączenia określonych adresów URL i portów. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które wymagają aktualizacji przez utworzenie zaplanowanego wdrożenia. Aktualizacje sklasyfikowane jako *opcjonalne* nie są uwzględnione w zakresie wdrożenia dla maszyn z systemem Windows. Zakres wdrożenia obejmuje tylko wymagane aktualizacje.

Zaplanowane wdrożenie definiuje, które maszyny docelowe otrzymują odpowiednie aktualizacje. Robi to przez jawne określenie pewnych maszyn lub wybranie [grupy komputerów](../azure-monitor/platform/computer-groups.md) , która jest oparta na przeszukiwaniu dzienników określonego zestawu maszyn (lub w [zapytaniu platformy Azure](automation-update-management-query-logs.md) , które dynamicznie wybiera maszyny wirtualne platformy Azure na podstawie określonych kryteriów). Te grupy różnią się od [konfiguracji zakresu](../azure-monitor/insights/solution-targeting.md), która jest używana tylko do określania, które maszyny otrzymują pakiety administracyjne, które umożliwiają rozwiązanie.

Należy również określić harmonogram zatwierdzania i ustawiania okresu, w którym można zainstalować aktualizacje. Ten okres jest nazywany oknem obsługi. 20-minutowy zakres okna obsługi jest zarezerwowany dla ponownych uruchomień, przy założeniu, że jest to wymagane i wybrano odpowiednią opcję ponownego uruchomienia. Jeśli stosowanie poprawek trwa dłużej niż oczekiwano, a w oknie obsługi jest mniej niż 20 minut, ponowne uruchomienie nie zostanie przeprowadzone.

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić tych elementów Runbook i nie wymagają one żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji tworzy harmonogram, który uruchamia główny element Runbook aktualizacji w określonym czasie dla dołączonych maszyn. Główny element Runbook uruchamia podrzędny element Runbook na każdym agencie, aby zainstalować wymagane aktualizacje.

Zgodnie z datą i godziną określoną we wdrożeniu aktualizacji maszyny docelowe wykonują wdrożenie równolegle. Przed rozpoczęciem instalacji zostanie uruchomione skanowanie w celu sprawdzenia, czy aktualizacje są nadal wymagane. W przypadku komputerów klienckich programu WSUS, jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji nie powiedzie się.

Posiadanie maszyny zarejestrowanej dla Update Management w więcej niż jednym Log Analytics obszarze roboczym (nazywanym również wieloadresowości) nie jest obsługiwane.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Obsługiwane typy klientów

W poniższej tabeli wymieniono systemy operacyjne obsługiwane w przypadku ocen aktualizacji. Stosowanie poprawek wymaga hybrydowego procesu roboczego elementu Runbook. Aby uzyskać informacje na temat wymagań hybrydowych procesów roboczych elementu Runbook, zobacz Przewodniki instalacji dotyczące instalowania [hybrydowego procesu roboczego elementu Runbook systemu Windows](automation-windows-hrw-install.md) i [hybrydowego procesu roboczego elementu Runbook](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)w systemie Linux

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (wersja RTM i SP1 standard)| Update Management obsługuje tylko wykonywanie ocen dla tego systemu operacyjnego, stosowanie poprawek nie jest obsługiwane, ponieważ [hybrydowy proces roboczy elementu Runbook](automation-windows-hrw-install.md) nie jest obsługiwany w przypadku systemu Windows Server 2008 R2. |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji. Stosowanie poprawek opartych na klasyfikacji `yum` wymaga zwrócenia danych zabezpieczeń, które nie są dostępne w wersjach RTM CentOS. Aby uzyskać więcej informacji na temat stosowania poprawek opartych na klasyfikacji na CentOS, zobacz [Aktualizacja klasyfikacji w systemie Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 (x86/x64)      |Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyn wirtualnych platformy Azure mogą być zarządzane za pomocą Update Management. Update Management działa na samych wystąpieniach, a nie na obrazie podstawowym. Należy zaplanować aktualizacje w sposób przyrostowy, aby nie wszystkie wystąpienia maszyn wirtualnych były aktualizowane jednocześnie.
> Węzły dla zestawów skalowania maszyn wirtualnych można dodać, wykonując czynności opisane w sekcji [dołączanie maszyny spoza platformy Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Nieobsługiwane typy klientów

W poniższej tabeli wymieniono nieobsługiwane systemy operacyjne:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Systemy operacyjne klienta (takie jak Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server Nano Server 2016     | Nieobsługiwane.       |
|Węzły usługi Azure Kubernetes | Nieobsługiwane. Użyj procesu poprawek opisanego w temacie [stosowanie aktualizacji zabezpieczeń i jądra do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Wymagania dotyczące klientów

Poniższe informacje opisują wymagania klienta specyficzne dla systemu operacyjnego. Aby uzyskać dodatkowe wskazówki, zobacz [Planowanie sieci](#ports).

#### <a name="windows"></a>Windows

Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem WSUS lub muszą mieć dostęp do Microsoft Update.

Możesz użyć Update Management z Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integracja Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). Wymagany jest [log Analytics Agent systemu Windows](../azure-monitor/platform/agent-windows.md) . Agent zostanie zainstalowany automatycznie, jeśli dołączysz maszynę wirtualną platformy Azure.

Domyślnie maszyny wirtualne z systemem Windows wdrożone z portalu Azure Marketplace są ustawione tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To zachowanie nie zmienia się po dodaniu tego rozwiązania lub dodaniu maszyn wirtualnych z systemem Windows do obszaru roboczego. Jeśli aktualizacje nie są aktywnie zarządzane przy użyciu tego rozwiązania, mają zastosowanie domyślne zachowanie (aby automatycznie zastosować aktualizacje).

> [!NOTE]
> Użytkownik może modyfikować zasady grupy, tak aby rozruch maszynowy mógł zostać wykonany tylko przez użytkownika, a nie przez system. Maszyny zarządzane mogą zostać zablokowane, jeśli Update Management nie ma uprawnień do ponownego uruchomienia maszyny bez ręcznej interakcji z użytkownikiem.
>
> Aby uzyskać więcej informacji, zobacz [Configure zasady grupy Settings for Automatic Updates](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

W przypadku systemu Linux maszyna wymaga dostępu do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatne lub publiczne. Do współdziałania z Update Managementami wymagany jest protokół TLS 1,1 lub TLS 1,2. Agent Log Analytics dla systemu Linux skonfigurowany do raportowania do więcej niż jednego obszaru roboczego Log Analytics nie jest obsługiwany w tym rozwiązaniu. Na komputerze musi być zainstalowany język Python 2. x.

Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Linux i pobierania najnowszej wersji, zobacz [log Analytics Agent dla systemu Linux](../azure-monitor/platform/agent-linux.md). Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Windows, zobacz [łączenie komputerów z systemem Windows w Azure monitor](../log-analytics/log-analytics-windows-agent.md).

Maszyny wirtualne, które zostały utworzone na podstawie obrazów na żądanie Red Hat Enterprise Linux (RHEL), które są dostępne w portalu Azure Marketplace, są zarejestrowane w celu uzyskania dostępu do [infrastruktury aktualizacji firmy Red Hat (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) wdrożonej na platformie Azure. Wszystkie inne dystrybucje systemu Linux muszą zostać zaktualizowane z repozytorium plików online dystrybucji przy użyciu obsługiwanych metod dystrybucji.

## <a name="permissions"></a>Uprawnienia

Do tworzenia wdrożeń aktualizacji i zarządzania nimi wymagane są określone uprawnienia. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [dostęp oparty na rolach — Update Management](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Składniki rozwiązania

Rozwiązanie składa się z następujących zasobów. Te zasoby są automatycznie dodawane do konta usługi Automation po włączeniu tego rozwiązania. 

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania każda maszyna z systemem Windows, która jest bezpośrednio połączona z obszarem roboczym Log Analytics, zostanie automatycznie skonfigurowana jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook uwzględnionych w tym rozwiązaniu.

Każda maszyna z systemem Windows zarządzana przez rozwiązanie znajduje się w okienku grupy hybrydowych procesów roboczych jako Grupa hybrydowych procesów roboczych systemu dla konta usługi Automation. Rozwiązania używają Konwencji `Hostname FQDN_GUID` nazewnictwa. Nie można kierować tymi grupami do elementów Runbook na Twoim koncie. Jeśli spróbujesz, próba zakończy się niepowodzeniem. Te grupy są przeznaczone do obsługi tylko tego rozwiązania do zarządzania.

Możesz dodać maszynę z systemem Windows do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, jeśli używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania System Center Operations Manager jest [połączona z obszarem roboczym log Analytics](../azure-monitor/platform/om-agents.md), następujące pakiety administracyjne są instalowane w Operations Manager. Te pakiety administracyjne są również instalowane na maszynach z systemem Windows podłączonych bezpośrednio po dodaniu rozwiązania. Pakietów administracyjnych nie trzeba konfigurować ani zarządzać nimi.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> Jeśli grupa zarządzania programu Operations Manager 1807 lub 2019 jest połączona z obszarem roboczym Log Analytics z agentami skonfigurowanymi w grupie zarządzania w celu zbierania danych dziennika, należy zastąpić `IsAutoRegistrationEnabled` parametr i ustawić go na wartość true w regule **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Aby uzyskać więcej informacji na temat aktualizacji pakietów zarządzania rozwiązaniami, zobacz [Connect Operations Manager to Azure monitor Logs](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> W przypadku maszyn z agentem programu Operations Manager, które mają być w pełni zarządzane przez Update Management, Agent musi zostać zaktualizowany do agenta Log Analytics dla systemu Windows lub agenta Log Analytics for Linux. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [jak uaktualnić agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). W środowiskach, w których jest używana Operations Manager, musi być uruchomiony program System Center Operations Manager 2012 R2 UR 14 lub nowszy.

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/><br/>Bezpośrednie połączenie z agentem Operations Manager do Azure Monitor dzienników nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest wykonywane dwa razy dziennie dla każdej zarządzanej maszyny z systemem Windows. Co 15 minut interfejs API systemu Windows jest wywoływany, aby wykonać zapytanie o czas ostatniej aktualizacji w celu ustalenia, czy stan został zmieniony. W przypadku zmiany stanu zostanie zainicjowane skanowanie zgodności.

Skanowanie jest wykonywane co godzinę dla każdej zarządzanej maszyny z systemem Linux.

Wyświetlenie zaktualizowanych danych z zarządzanych maszyn na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

Średnie użycie danych przez Azure Monitor dzienników dla maszyny używającej Update Management wynosi około 25 megabajtów (MB) miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie, w zależności od środowiska. Zalecamy monitorowanie środowiska, aby śledzić dokładne użycie. Aby uzyskać więcej informacji na temat analizowania użycia danych, zobacz [Zarządzanie użyciem i kosztem](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planowanie sieci

Poniższe adresy są wymagane dla Update Management. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

W przypadku maszyn z systemem Windows należy również zezwolić na ruch do wszystkich punktów końcowych wymaganych przez Windows Update. Zaktualizowaną listę wymaganych punktów końcowych można znaleźć w przypadku [problemów związanych z protokołem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Jeśli masz lokalny [serwer Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musisz również zezwolić na ruch do serwera określonego w [kluczu WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

W przypadku maszyn z systemem Red Hat Linux zapoznaj [się z tematem adresy IP dla serwerów RHUI Content Delivery](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) for Required Endpoints. W przypadku innych dystrybucji systemu Linux zapoznaj się z dokumentacją dostawcy.

Aby uzyskać więcej informacji na temat portów wymaganych dla hybrydowego procesu roboczego elementu Runbook, zobacz [porty ról hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Zalecamy korzystanie z adresów wymienionych podczas definiowania wyjątków. W przypadku adresów IP można pobrać [Microsoft Azure zakresy adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Ten plik jest aktualizowany co tydzień i odzwierciedla aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

Postępuj zgodnie z instrukcjami w temacie [Connect Computers bez dostępu do Internetu](../azure-monitor/platform/gateway.md) , aby skonfigurować maszyny, które nie mają dostępu do Internetu.

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższej tabeli wymieniono klasyfikacje aktualizacji w Update Management z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia ani aktualizacje zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze, a następnie wyświetlać dane oceny z powodu wzbogacania danych w chmurze. W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma informacji dostępnych w wersji RTM. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla poniższego polecenia, Update Management może zostać poprawione na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w systemie CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy mogli ją samodzielnie włączyć. 

Aby sklasyfikować aktualizacje w systemie Red Hat Enterprise w wersji 6, należy zainstalować wtyczkę yum-Security. W Red Hat Enterprise Linux 7 wtyczka jest już częścią yum, nie ma potrzeby instalowania żadnych elementów. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)firmy Red Hat.

## <a name="integrate-with-configuration-manager"></a>Integracja z programem Configuration Manager

Klienci, którzy zainwestowali w Configuration Manager Microsoft Endpoint Protection w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również opierają się na sile i dojrzałości Configuration Manager, aby ułatwić im zarządzanie aktualizacjami oprogramowania. Configuration Manager jest częścią cyklu zarządzania aktualizacjami oprogramowania (SUM).

Aby dowiedzieć się, jak zintegrować rozwiązanie do zarządzania z Configuration Manager, zobacz [integrowanie Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-updates-on-windows"></a>Aktualizacje innych firm w systemie Windows

Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji, aby zaktualizować obsługiwane systemy Windows. Jest to program WSUS lub Windows Update. Narzędzia, takie jak [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (program Updates Publisher), umożliwiają importowanie i publikowanie aktualizacji niestandardowych za pomocą programu WSUS. Ten scenariusz umożliwia Update Management aktualizowania maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [Install Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Włącz Update Management

Aby rozpocząć aktualizowanie systemów, należy włączyć rozwiązanie Update Management. Poniżej przedstawiono zalecane i obsługiwane metody dołączenia rozwiązania:

- [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
- [Przeglądanie wielu maszyn](automation-onboard-solutions-from-browse.md)
- [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
- [Z elementem Runbook Azure Automation](automation-onboard-solutions.md)
- [Z szablonem Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z Azure Automation [często zadawanych](automation-faq.md) pytań, aby przejrzeć typowe pytania dotyczące tego rozwiązania.
