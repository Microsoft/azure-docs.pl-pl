---
title: Rozwiązanie do zarządzania aktualizacjami na platformie Azure
description: W tym artykule opisano, jak używać rozwiązania usługi Azure Update Management do zarządzania aktualizacjami dla komputerów z systemem Windows i Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617399"
---
# <a name="update-management-solution-in-azure"></a>Rozwiązanie do zarządzania aktualizacjami na platformie Azure

Za pomocą **rozwiązania Zarządzania aktualizacjami** w usłudze Azure Automation można zarządzać aktualizacjami systemu operacyjnego dla komputerów z systemem Windows i Linux na platformie Azure, w środowiskach lokalnych i w innych środowiskach w chmurze. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agenta i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów.

Zarządzanie aktualizacjami dla maszyn wirtualnych (VM) można włączyć przy użyciu następujących metod:

- Z [konta usługi Azure Automation](automation-onboard-solutions-from-automation-account.md) dla jednej lub więcej maszyn platformy Azure i ręcznie dla maszyn innych niż Azure.

- Dla pojedynczej maszyny wirtualnej platformy Azure ze strony maszyny wirtualnej w witrynie Azure portal. Ten scenariusz jest dostępny dla maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) i [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Dla [wielu maszyn wirtualnych platformy Azure,](manage-update-multi.md) wybierając je ze strony maszyny wirtualne w witrynie Azure portal. 

> [!NOTE]
> **Rozwiązanie do zarządzania aktualizacjami** wymaga połączenia obszaru roboczego usługi Log Analytics z kontem automatyzacji. Aby uzyskać ostateczną listę obsługiwanych regionów, zobacz [Mapowania obszaru roboczego platformy Azure](./how-to/region-mappings.md). Mapowania regionu nie wpływają na możliwość zarządzania maszynami wirtualnymi w osobnym regionie z konta automatyzacji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Szablon [usługi](automation-update-management-deploy-template.md) Azure Resource Manager jest dostępny, aby ułatwić wdrażanie **rozwiązania zarządzania aktualizacjami** na nowym lub istniejącym koncie automatyzacji i obszarze roboczym usługi Log Analytics w ramach subskrypcji.

## <a name="solution-overview"></a>Omówienie rozwiązania

Maszyny zarządzane przez usługę Zarządzanie aktualizacjami używają następujących konfiguracji do przeprowadzania oceny i aktualizowania wdrożeń:

* Agent analizy dzienników dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Usługi Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Na poniższym diagramie przedstawiono, jak rozwiązanie ocenia i stosuje aktualizacje zabezpieczeń do wszystkich podłączonych komputerów z systemem Windows Server i Linux w obszarze roboczym:

![Przepływ procesu zarządzania aktualizacją](./media/automation-update-management/update-mgmt-updateworkflow.png)

Rozwiązanie Update Management pozwala natywnie dołączać maszyny z różnych subskrypcji do jednej dzierżawy.

Po wydaniu pakietu trwa od 2 do 3 godzin, aby poprawka pojawiła się na komputerach z systemem Linux do oceny. W przypadku komputerów z systemem Windows łatka po wydaniu aktualizacji trwa od 12 do 15 godzin.

Po zakończeniu skanowania w celu zapewnienia zgodności z aktualizacjami agent przesyła zbiorczo informacje do dzienników usługi Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie w celu zapewnienia zgodności aktualizacji jest inicjowane w ciągu 15 minut od ponownego uruchomienia agenta usługi Log Analytics, przed instalacją aktualizacji i po instalacji aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest domyślnie wykonywane co godzinę. Jeśli agent usługi Log Analytics zostanie ponownie uruchomiony, skanowanie zgodności jest inicjowane w ciągu 15 minut.

Rozwiązanie raportuje, jak aktualne urządzenie jest oparte na źródle, z którym chcesz być skonfigurowany do synchronizacji. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania do programu WSUS, w zależności od tego, kiedy program WSUS ostatnio zsynchronizowany z usługą Microsoft Update, wyniki mogą różnić się od wyników programu Microsoft Update. To zachowanie jest takie samo dla komputerów z systemem Linux, które są skonfigurowane do raportowania do lokalnego repozytorium zamiast do publicznego repozytorium.

> [!NOTE]
> Aby poprawnie zgłosić się do usługi, zarządzanie aktualizacjami wymaga włączenia niektórych adresów URL i portów. Aby dowiedzieć się więcej o tych wymaganiach, zobacz [Planowanie sieci dla pracowników hybrydowych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrażać i instalować na komputerach, które wymagają aktualizacji, tworząc zaplanowane wdrożenie. Aktualizacje *sklasyfikowane* jako opcjonalne nie są uwzględniane w zakresie wdrażania dla komputerów z systemem Windows. Tylko wymagane aktualizacje są zawarte w zakresie wdrażania.

Zaplanowane wdrożenie określa, które maszyny docelowe otrzymują odpowiednie aktualizacje. Robi to albo jawnie określając niektóre maszyny lub wybierając [grupę komputerów,](../azure-monitor/platform/computer-groups.md) która jest oparta na wyszukiwania dziennika określonego zestawu maszyn (lub na [kwerendę platformy Azure,](automation-update-management-query-logs.md) która dynamicznie wybiera maszyny wirtualne platformy Azure na podstawie określonych kryteriów). Grupy te różnią się od [konfiguracji zakresu](../azure-monitor/insights/solution-targeting.md), który jest używany tylko do określenia, które maszyny otrzymują pakiety administracyjne, które umożliwiają rozwiązanie.

Należy również określić harmonogram zatwierdzania i ustawić okres, w którym można instalować aktualizacje. Okres ten jest nazywany okno konserwacji. 20-minutowy zakres okna konserwacji jest zarezerwowany dla ponownych rozruchów, przy założeniu, że jest potrzebny i wybrano odpowiednią opcję ponownego uruchomienia. Jeśli poprawki trwa dłużej niż oczekiwano i jest mniej niż 20 minut w oknie konserwacji, ponowne uruchomienie nie nastąpi.

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić tych śmiób ekwol umiań i nie wymagają one żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji tworzy harmonogram, który uruchamia element runbook aktualizacji wzorca w określonym czasie dla uwzględnionych maszyn. Główny projekt runbook uruchamia podrzędny system runbook na każdym agencie, aby zainstalować wymagane aktualizacje.

W dniu i godzinie określonej we wdrożeniu aktualizacji maszyny docelowe wykonują wdrożenie równolegle. Przed instalacją zostanie uruchomione skanowanie w celu sprawdzenia, czy aktualizacje są nadal wymagane. W przypadku komputerów klienckich programu WSUS jeśli aktualizacje nie są zatwierdzone w usłudze WSUS, wdrożenie aktualizacji kończy się niepowodzeniem.

Komputer zarejestrowany do zarządzania aktualizacjami w więcej niż jednym obszarze roboczym usługi Log Analytics (nazywany również wielohomingiem) nie jest obsługiwany.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Obsługiwane typy klientów

W poniższej tabeli wymieniono obsługiwane systemy operacyjne do oceny aktualizacji. Łatanie wymaga hybrydowego procesu roboczego uruchomieniu. 100. Aby uzyskać informacje na temat wymagań procesu roboczego hybrydowego systemu Runbook, zobacz przewodniki instalacji dotyczące instalowania [procesu roboczego hybrydowego systemu Windows](automation-windows-hrw-install.md) i procesu [roboczego hybrydowego systemu Linux.](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2019 (centrum danych/centrum danych rdzeń/standard)<br><br>Windows Server 2016 (centrum danych/centrum danych rdzeń/standard)<br><br>Windows Server 2012 R2(Centrum danych/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM i SP1 Standard)| Zarządzanie aktualizacjami obsługuje tylko wykonywanie ocen dla tego systemu operacyjnego, łatanie nie jest obsługiwane, ponieważ [hybrydowy proces roboczy żyjącego](automation-windows-hrw-install.md) nie jest obsługiwany dla systemu Windows Server 2008 R2. |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji. Poprawki oparte na `yum` klasyfikacji wymagają zwrócenia danych zabezpieczeń, których centos nie ma w swoich wersjach RTM. Aby uzyskać więcej informacji na temat poprawek opartych na klasyfikacji w systemie CentOS, zobacz [Aktualizowanie klasyfikacji w systemie Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS, 16.04 LTS i 18.04 (x86/x64)      |Agenci systemu Linux wymagają dostępu do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyny wirtualnej platformy Azure można zarządzać za pomocą usługi Zarządzanie aktualizacjami. Zarządzanie aktualizacjami działa na samych wystąpieniach, a nie na obrazie podstawowym. Należy zaplanować aktualizacje w sposób przyrostowy, tak aby nie wszystkie wystąpienia maszyny Wirtualnej są aktualizowane na raz.
> Można dodać węzły dla zestawów skalowania maszyny wirtualnej, wykonując kroki opisane w obszarze [Wbudowana maszyna nienawiązysowa](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)platformy Azure.

### <a name="unsupported-client-types"></a>Nieobsługiwały typy klientów

W poniższej tabeli wymieniono nieobsługiwały systemy operacyjne:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Klienckie systemy operacyjne (takie jak Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server Nano Server 2016     | Bez pomocy technicznej.       |
|Węzły usługi Azure Kubernetes | Bez pomocy technicznej. Użyj procesu wprowadzania poprawek opisanego w [sekcji Stosowanie aktualizacji zabezpieczeń i jąder do węzłów systemu Linux w usłudze Azure Kubernetes (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Wymagania dotyczące klientów

W poniższych informacjach opisano wymagania klienta specyficzne dla systemu operacyjnego. Aby uzyskać dodatkowe wskazówki, zobacz [Planowanie sieci](#ports).

#### <a name="windows"></a>Windows

Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem WSUS lub wymagają dostępu do witryny Microsoft Update.

Można użyć usługi Zarządzanie aktualizacjami w programie Configuration Manager. Aby dowiedzieć się więcej o scenariuszach integracji, zobacz [Integrowanie programu Configuration Manager z zarządzaniem aktualizacjami](oms-solution-updatemgmt-sccmintegration.md#configuration). Wymagany jest [agent systemu Windows usługi Log Analytics.](../azure-monitor/platform/agent-windows.md) Agent jest instalowany automatycznie, jeśli dołączasz maszynę wirtualną platformy Azure.

Domyślnie maszyny wirtualne systemu Windows, które są wdrażane z portalu Azure Marketplace są ustawione do odbierania automatycznych aktualizacji z usługi Windows Update. To zachowanie nie zmienia się po dodaniu tego rozwiązania lub dodaniu maszyn wirtualnych systemu Windows do obszaru roboczego. Jeśli nie będziesz aktywnie zarządzać aktualizacjami przy użyciu tego rozwiązania, stosuje się domyślne zachowanie (do automatycznego stosowania aktualizacji).

> [!NOTE]
> Użytkownik może modyfikować zasady grupy, dzięki czemu ponowne uruchomienie komputera może być wykonywane tylko przez użytkownika, a nie przez system. Zarządzane maszyny mogą utknąć, jeśli zarządzanie aktualizacjami nie ma praw do ponownego uruchomienia komputera bez ręcznej interakcji użytkownika.
>
> Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zasad grupy dla aktualizacji automatycznych](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

W przypadku systemu Linux komputer wymaga dostępu do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatne lub publiczne. TLS 1.1 lub TLS 1.2 jest wymagane do interakcji z Zarządzanie aktualizacjami. Agent analizy dzienników dla systemu Linux, który jest skonfigurowany do raportowania do więcej niż jednego obszaru roboczego usługi Log Analytics, nie jest obsługiwany w tym rozwiązaniu. Urządzenie musi mieć również zainstalowany Python 2.x.

Aby uzyskać informacje na temat instalowania agenta usługi Log Analytics dla systemu Linux i pobierania najnowszej wersji, zobacz [Agent analizy dzienników dla systemu Linux](../azure-monitor/platform/agent-linux.md). Aby uzyskać informacje dotyczące instalowania agenta usługi Log Analytics dla systemu Windows, zobacz [Łączenie komputerów z systemem Windows z monitorem Azure](../log-analytics/log-analytics-windows-agent.md).

Maszyny wirtualne, które zostały utworzone na żądanie Red Hat Enterprise Linux (RHEL) obrazy, które są dostępne w portalu Azure Marketplace są rejestrowane w celu uzyskania dostępu do [red hat update infrastructure (RHUI),](../virtual-machines/workloads/redhat/redhat-rhui.md) który jest wdrażany na platformie Azure. Każda inna dystrybucja Linuksa musi być aktualizowana z internetowego repozytorium plików dystrybucji przy użyciu obsługiwanych metod dystrybucji.

## <a name="permissions"></a>Uprawnienia

Aby utworzyć wdrożenia aktualizacji i zarządzać nimi, potrzebujesz określonych uprawnień. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [Dostęp oparty na rolach — Zarządzanie aktualizacjami](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Składniki rozwiązania

Rozwiązanie składa się z następujących zasobów. Te zasoby są automatycznie dodawane do konta automatyzacji po włączeniu rozwiązania. 

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania każdy komputer z systemem Windows, który jest bezpośrednio połączony z obszarem roboczym usługi Log Analytics jest automatycznie konfigurowany jako hybrydowy proces roboczy systemu runbook do obsługi żyłastek, które są zawarte w tym rozwiązaniu.

Każdy komputer z systemem Windows, który jest zarządzany przez rozwiązanie jest wymieniony w hybrydowym grup roboczych grup okienka jako grupa hybrydowych procesów roboczych systemu dla konta automatyzacji. Rozwiązania używają konwencji `Hostname FQDN_GUID` nazewnictwa. Nie możesz kierować reklam do tych grup z plikami runbook na koncie. Jeśli spróbujesz, próba zakończy się niepowodzeniem. Te grupy są przeznaczone do obsługi tylko tego rozwiązania do zarządzania.

Komputer z systemem Windows można dodać do grupy hybrydowego pracownika żyła roboczej na koncie automatyzacji, aby obsługiwać elementy runbook automatyzacji, jeśli używasz tego samego konta zarówno dla rozwiązania, jak i członkostwa w grupie hybrydowego systemu runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego uruchomieniu. .

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania programu System Center Operations Manager jest [połączona z obszarem roboczym usługi Log Analytics,](../azure-monitor/platform/om-agents.md)w programie Operations Manager są zainstalowane następujące pakiety administracyjne. Te pakiety administracyjne są również instalowane na bezpośrednio podłączonych komputerach z systemem Windows po dodaniu rozwiązania. Pakietów administracyjnych nie trzeba konfigurować ani zarządzać nimi.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> Jeśli masz grupę zarządzania programu Operations Manager 1807 lub 2019 połączoną z obszarem roboczym usługi Log `IsAutoRegistrationEnabled` Analytics z agentami skonfigurowanymi w grupie zarządzania do zbierania danych dziennika, musisz zastąpić ten parametr i ustawić go na True w regule **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Aby uzyskać więcej informacji na temat aktualizowanania pakietów administracyjnych rozwiązań, zobacz [Łączenie dzienników programu Operations Manager z usługą Azure Monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> W przypadku komputerów z agentem menedżer operacji, które mają być w pełni zarządzane przez zarządzanie aktualizacjami, agent musi zostać zaktualizowany do agenta usługi Log Analytics dla systemu Windows lub agenta analizy dzienników dla systemu Linux. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [Jak uaktualnić agenta programu Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). W środowiskach korzystających z programu Operations Manager należy uruchomić program System Center Operations Manager 2012 R2 UR 14 lub nowszą.

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu od agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu od agentów systemu Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/><br/>Bezpośrednie połączenie z agentem programu Operations Manager do dzienników usługi Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest wykonywane dwa razy dziennie dla każdego zarządzanego komputera z systemem Windows. Co 15 minut interfejs API systemu Windows jest wywoływany do wykonywania zapytań o ostatni czas aktualizacji w celu ustalenia, czy stan uległ zmianie. Jeśli stan uległ zmianie, zostanie zainicjowane skanowanie zgodności.

Skanowanie jest wykonywane co godzinę dla każdego zarządzanego komputera z systemem Linux.

Może upłynąć od 30 minut do 6 godzin, aby pulpit nawigacyjny wyświetlił zaktualizowane dane z zarządzanych komputerów.

Średnie użycie danych przez dzienniki usługi Azure Monitor dla komputera przy użyciu usługi Zarządzanie aktualizacjami wynosi około 25 megabajtów (MB) miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie, w zależności od środowiska. Zaleca się monitorowanie środowiska, aby śledzić dokładne użycie. Aby uzyskać więcej informacji do analizy użycia danych, zobacz [Zarządzanie użyciem i kosztem](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planowanie sieci

Następujące adresy są wymagane specjalnie dla zarządzania aktualizacjami. Komunikacja z tymi adresami odbywa się za porcie 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

W przypadku komputerów z systemem Windows należy również zezwolić na ruch do wszystkich punktów końcowych wymaganych przez usługę Windows Update. Zaktualizowaną listę wymaganych punktów końcowych można znaleźć w [części Problemy związane z protokołem HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Jeśli masz lokalny [serwer Windows Update,](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)musisz również zezwolić na ruch na serwerze określonym w [kluczu programu WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

W przypadku komputerów z systemem Linux Red Hat zobacz [punkty IP dla serwerów dostarczania zawartości RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) dla wymaganych punktów końcowych. W przypadku innych dystrybucji systemu Linux zobacz dokumentację dostawcy.

Aby uzyskać więcej informacji na temat portów wymaganych dla hybrydowego procesu roboczego uruchomieniu, zobacz [Porty roli proces roboczy hybrydowy](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Zaleca się używanie adresów wymienionych podczas definiowania wyjątków. W przypadku adresów IP można pobrać [zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowany co tydzień i odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP.

Postępuj zgodnie z instrukcjami w [connect komputerów bez dostępu do Internetu,](../azure-monitor/platform/gateway.md) aby skonfigurować maszyny, które nie mają dostępu do Internetu.

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższych tabelach przedstawiono klasyfikacje aktualizacji w zarządzania aktualizacjami z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacja określonego problemu, który rozwiązuje krytyczny błąd niezwiązany z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacja problemu związanego z zabezpieczeniami specyficznym dla produktu.        |
|Pakiety zbiorcze aktualizacji     | Skumulowany zestaw poprawek, które są pakowane razem w celu łatwego wdrożenia.        |
|Pakiety funkcji     | Nowe funkcje produktu, które są dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Skumulowany zestaw poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacja do wirusów lub innych plików definicji.        |
|narzędzia     | Narzędzie lub funkcja, która pomaga wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który jest aktualnie zainstalowany.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące określonego problemu lub problemu związanego z zabezpieczeniami specyficznego dla produktu.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, które nie mają charakteru krytycznego lub nie są aktualizacjami zabezpieczeń.        |

W systemie Linux zarządzanie aktualizacjami może rozróżniać aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze podczas wyświetlania danych oceny z powodu wzbogacania danych w chmurze. W przypadku poprawek zarządzanie aktualizacjami opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma tych informacji dostępnych w wersji RTM. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla następującego polecenia, zarządzanie aktualizacjami może łatać się na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie ma żadnej obsługiwanej metody, aby włączyć dostępność danych natywnych klasyfikacji w CentOS. W tej chwili klientom, którzy mogli włączyć tę funkcję, jest dostępna tylko najlepsza pomoc techniczna. 

Aby sklasyfikować aktualizacje w red hat enterprise w wersji 6, musisz zainstalować wtyczkę yum-security. Na Red Hat Enterprise Linux 7 wtyczka jest już częścią samego mniam, nie ma potrzeby instalowania czegokolwiek. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)Red Hat .

## <a name="integrate-with-configuration-manager"></a>Integracja z programem Configuration Manager

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager w zakresie zarządzania komputerami, serwerami i urządzeniami przenośnymi, również polegają na sile i dojrzałości programu Configuration Manager, aby pomóc im w zarządzaniu aktualizacjami oprogramowania. Program Configuration Manager jest częścią cyklu zarządzania aktualizacjami oprogramowania (SUM).

Aby dowiedzieć się, jak zintegrować rozwiązanie do zarządzania z programem Configuration Manager, zobacz [Integrowanie programu Menedżer konfiguracji z zarządzaniem aktualizacjami](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-updates-on-windows"></a>Aktualizacje innych firm w systemie Windows

Usługa Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu aktualizacji obsługiwanych systemów Windows. Jest to usługa WSUS lub Windows Update. Narzędzia, takie jak [Program System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher), umożliwiają importowanie i publikowanie aktualizacje niestandardowych w programie WSUS. W tym scenariuszu usługa Zarządzanie aktualizacjami umożliwia aktualizowanie maszyn korzystających z programu Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [Instalowanie programu Publisher aktualizacji](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Włącz zarządzanie aktualizacjami

Aby rozpocząć aktualizowanie systemów, należy włączyć rozwiązanie do zarządzania aktualizacjami. Poniżej przedstawiono zalecane i obsługiwane metody dołączania rozwiązania:

- [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
- [Od przeglądania wielu maszyn](automation-onboard-solutions-from-browse.md)
- [Z konta Automation](automation-onboard-solutions-from-automation-account.md)
- [Z elementem uruchamianym usługi Azure Automation](automation-onboard-solutions.md)
- [Z szablonem usługi Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [często zadawanymi pytaniami](automation-faq.md) dotyczącymi usługi Azure Automation, aby przejrzeć typowe pytania dotyczące tego rozwiązania.
