---
title: Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation
description: Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: f3c9197faaae89e0ffb238f987ee66dafea8abdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579808"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę Automation do sieci wirtualnej. Ruch sieciowy między maszynami w sieci wirtualnej a kontem usługi Automation przechodzi przez sieć wirtualną oraz łącze prywatne w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Na przykład masz sieć wirtualną, w której wyłączono dostęp do Internetu. Jednak chcesz uzyskać dostęp do konta usługi Automation prywatnie i korzystać z funkcji automatyzacji, takich jak webhook, konfiguracja stanu i zadania elementu Runbook w hybrydowych procesach roboczych elementów Runbook. Ponadto chcesz, aby użytkownicy mieli dostęp do konta usługi Automation tylko za pomocą sieci wirtualnej.  Wdrożenie prywatnego punktu końcowego osiąga te cele.

W tym artykule omówiono, kiedy należy używać programu i jak skonfigurować prywatny punkt końcowy przy użyciu konta usługi Automation.

![Przegląd koncepcyjny prywatnego linku do Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Obsługa linków prywatnych z Azure Automation jest dostępna tylko w chmurach dla instytucji rządowych platformy Azure.

## <a name="advantages"></a>Zalety

Za pomocą linku prywatnego można:

- Połącz się prywatnie, aby Azure Automation bez otwierania dostępu do sieci publicznej.
- Połącz się prywatnie z Azure Monitor Log Analytics obszaru roboczego bez otwierania dostępu do sieci publicznej.

    >[!NOTE]
    >Jeśli konto usługi Automation jest połączone z obszarem roboczym Log Analytics do przesyłania danych o zadaniach, a także włączone funkcje, takie jak Update Management, Change Tracking i spisu, konfiguracja stanu lub Start/Stop VMs during off-hours, jest wymagane oddzielny prywatny punkt końcowy dla obszaru roboczego Log Analytics. Aby uzyskać więcej informacji na temat prywatnego linku do Azure Monitor, zobacz [bezpieczne łączenie sieci do Azure monitor za pomocą prywatnego linku platformy Azure](../../azure-monitor/logs/private-link-security.md).

- Upewnij się, że dane automatyzacji są dostępne tylko za poorednictwem autoryzowanych sieci prywatnych.
- Zapobiegaj eksfiltracji danych z sieci prywatnych przez definiowanie zasobów Azure Automation, które łączą się za pośrednictwem prywatnego punktu końcowego.
- Bezpiecznie łącz swoją prywatną sieć lokalną z Azure Automation przy użyciu linku ExpressRoute i prywatnego.
- Zachowaj cały ruch w sieci szkieletowej Microsoft Azure.

Aby uzyskać więcej informacji, zobacz  [najważniejsze zalety linku prywatnego](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Ograniczenia

- W bieżącej implementacji linku prywatnego konta usługi Automation zadania chmury nie mogą uzyskać dostępu do zasobów platformy Azure zabezpieczonych za pomocą prywatnego punktu końcowego. Na przykład Azure Key Vault, Azure SQL, konto usługi Azure Storage itd. Aby obejść ten sposób, zamiast tego użyj [hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md) .
- Musisz użyć najnowszej wersji [agenta log Analytics](../../azure-monitor/agents/log-analytics-agent.md) dla systemu Windows lub Linux.
- [Brama log Analytics](../../azure-monitor/agents/gateway.md) nie obsługuje prywatnego linku.

## <a name="how-it-works"></a>Jak to działa

Łącze prywatne Azure Automation łączy co najmniej jeden prywatny punkt końcowy (i w związku z tym sieci wirtualne, które znajdują się w nim) do zasobu konta usługi Automation. Te punkty końcowe to maszyny korzystające z elementów webhook do uruchamiania elementu Runbook, Maszyny obsługujące rolę hybrydowego procesu roboczego elementu Runbook oraz węzły konfiguracji żądanego stanu (DSC).

Po utworzeniu prywatnych punktów końcowych dla usługi Automation każdy publiczny adres URL automatyzacji jest mapowany na jeden prywatny punkt końcowy w sieci wirtualnej. Użytkownik lub komputer mogą bezpośrednio kontaktować się z adresami URL usługi Automation.

### <a name="webhook-scenario"></a>Scenariusz elementu webhook

Elementy Runbook można uruchamiać przy użyciu wpisu na adres URL elementu webhook. Na przykład adres URL wygląda następująco: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Scenariusz hybrydowego procesu roboczego elementu Runbook

Funkcja hybrydowego procesu roboczego elementu Runbook w programie Azure Automation umożliwia uruchamianie elementów Runbook bezpośrednio na platformie Azure lub na maszynach spoza platformy Azure, w tym na serwerach zarejestrowanych przy użyciu serwerów z obsługą usługi Azure Arc. Na komputerze lub serwerze, na którym znajduje się rola, można uruchamiać elementy Runbook bezpośrednio na nim i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi.

Punkt końcowy JRDS jest używany przez hybrydowy proces roboczy do uruchamiania/zatrzymywania elementów Runbook, pobierania elementów Runbook do procesu roboczego i wysyłania strumienia dziennika zadań z powrotem do usługi Automation.Po włączeniu punktu końcowego JRDS adres URL będzie wyglądać następująco: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Dzięki temu wykonywanie elementów Runbook w hybrydowym procesie roboczym połączonym z platformą Azure Virtual Network może wykonywać zadania bez konieczności otwierania połączenia wychodzącego z Internetem.  

> [!NOTE]
>W przypadku bieżącej implementacji linków prywatnych Azure Automation program obsługuje tylko Uruchamianie zadań w hybrydowym procesie roboczym elementu Runbook podłączonym do sieci wirtualnej platformy Azure i nie obsługuje zadań w chmurze.

## <a name="hybrid-worker-scenario-for-update-management"></a>Scenariusz hybrydowego procesu roboczego dla Update Management  

Hybrydowy proces roboczy elementu Runbook systemu obsługuje zestaw ukrytych elementów Runbook używanych przez funkcję Update Management, które są przeznaczone do instalowania aktualizacji określonych przez użytkownika na maszynach z systemem Windows i Linux. Po włączeniu Update Management Azure Automation, każda maszyna połączona z obszarem roboczym Log Analytics zostanie automatycznie skonfigurowana jako hybrydowy proces roboczy elementu Runbook systemu.

Aby zrozumieć & konfigurowania Update Management przegląd [Update Management](../update-management/overview.md). Funkcja Update Management ma zależność od obszaru roboczego Log Analytics, dlatego wymaga połączenia obszaru roboczego z kontem usługi Automation. W obszarze roboczym Log Analytics są przechowywane dane zbierane przez rozwiązanie oraz hostowanie wyszukiwania i widoków dzienników.

Jeśli chcesz, aby maszyny skonfigurowane do zarządzania aktualizacjami łączyły się z programem Automation & Log Analytics obszarze roboczym w bezpieczny sposób za pośrednictwem kanału linku prywatnego, musisz włączyć prywatny link do obszaru roboczego Log Analytics połączonego z kontem usługi Automation skonfigurowanym za pomocą linku prywatnego.

Można kontrolować sposób, w jaki można uzyskać dostęp do obszaru roboczego Log Analytics spoza zakresów linków prywatnych, wykonując kroki opisane w temacie [konfigurowanie log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics). Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej na potrzeby** pozyskiwania na **nie**, maszyny spoza połączonych zakresów nie mogą przekazywać danych do tego obszaru roboczego. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej dla zapytań** na wartość **nie**, wówczas maszyny spoza zakresów nie mogą uzyskać dostępu do danych w tym obszarze roboczym.

Użyj podzasobu **DSCAndHybridWorker** Target w celu włączenia prywatnego linku dla hybrydowych procesów roboczych użytkownika &.

> [!NOTE]
> Maszyny hostowane poza platformą Azure, które są zarządzane przez program Update Management i są połączone z siecią wirtualną platformy Azure za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i sieci wirtualne równorzędne przy użyciu prywatnych punktów końcowych obsługują link prywatny.

### <a name="state-configuration-agentsvc-scenario"></a>Scenariusz konfiguracja stanu (agentsvc)

Konfiguracja stanu udostępnia usługę Azure Configuration Management, która pozwala na pisanie i kompilowanie konfiguracji konfiguracji żądanego stanu (DSC) programu PowerShell dla węzłów w chmurze lub lokalnych centrach danych oraz zarządzanie nimi.

Agent na komputerze rejestruje usługę DSC, a następnie używa punktu końcowego usługi do ściągania konfiguracji DSC. Punkt końcowy usługi agenta wygląda następująco: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Adres URL publicznego punktu końcowego & prywatny będzie taki sam, ale zostałby zamapowany na prywatny adres IP, gdy łącze prywatne jest włączone.

## <a name="planning-based-on-your-network"></a>Planowanie w oparciu o sieć

Przed skonfigurowaniem zasobu konta usługi Automation należy wziąć pod uwagę wymagania dotyczące izolacji sieci. Oceń dostęp do sieci wirtualnych za pomocą publicznego Internetu, a także ograniczenia dostępu do konta usługi Automation (w tym konfigurowania zakresu grupy linków prywatnych do Azure Monitor dzienników, jeśli są zintegrowane z kontem usługi Automation). Należy również uwzględnić przegląd [rekordów DNS](./automation-region-dns-records.md) usługi Automation w ramach planu, aby zapewnić, że obsługiwane funkcje działają bez problemu.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Utwórz prywatny punkt końcowy, aby połączyć naszą sieć. Można go utworzyć w [Azure Portal centrum łączy prywatnych](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Po zastosowaniu zmian do publicNetworkAccess i linku prywatnego może upłynąć do 35 minut.

W tej sekcji utworzysz prywatny punkt końcowy dla konta usługi Automation.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > prywatnym centrum linków**.

2. W **centrum linków prywatnych — Omówienie** opcji **tworzenia połączenia prywatnego z usługą** wybierz pozycję **Rozpocznij**.

3. W obszarze **Tworzenie maszyny wirtualnej — podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź *PrivateEndpoint*. |
    | Region (Region) | Wybierz pozycję **YourRegion**. |
    |||

4. Wybierz pozycję **Dalej: zasób**.

5. W obszarze **Utwórz prywatny punkt końcowy zasobu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Automation/automationAccounts**. |
    | Zasób |Wybierz *myAutomationAccount*|
    |Podzasób docelowy |W zależności od scenariusza wybierz pozycję *webhook* lub *DSCAndHybridWorker* .|
    |||

6. Wybierz pozycję **Dalej: Konfiguracja**.

7. W obszarze **Tworzenie prywatnego punktu końcowego — konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**SIEĆ**| |
    | Sieć wirtualna| Wybierz pozycję *MyVirtualNetwork*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    |**INTEGRACJA PRYWATNEJ USŁUGI DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |SELECT *(New) privatelink. Azure-Automation.NET* |
    |||

8. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

9. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

W **centrum linku prywatnego** wybierz **prywatne punkty końcowe** , aby wyświetlić zasób linku prywatnego.

![Prywatny link do zasobu usługi Automation](./media/private-link-security/private-link-automation-resource.png)

Wybierz zasób, aby wyświetlić wszystkie szczegóły. Spowoduje to utworzenie nowego prywatnego punktu końcowego dla konta usługi Automation i przypisanie go do prywatnego adresu IP z sieci wirtualnej. **Stan połączenia** jest wyświetlany jako **zatwierdzone**.

Analogicznie, unikatowa w pełni kwalifikowana nazwa domeny (FQDN) jest tworzona dla konfiguracji stanu (agentsvc) oraz dla hybrydowego środowiska uruchomieniowego Runbook Worker (jrds). Każdy z nich ma przypisany oddzielny adres IP od sieci wirtualnej, a **stan połączenia** jest wyświetlany jako **zatwierdzony**.

Jeśli odbiorca usługi ma uprawnienia usługi Azure RBAC w ramach zasobu usługi Automation, może wybrać metodę automatycznego zatwierdzania. W takim przypadku, gdy żądanie osiągnie zasób dostawcy usługi Automation, nie jest wymagane żadne działanie od usługodawcy, a połączenie jest automatycznie zatwierdzane.

## <a name="set-public-network-access-flags"></a>Ustawianie flag dostępu do sieci publicznej

Można skonfigurować konto usługi Automation, aby odmówić całej konfiguracji publicznej i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci. Jeśli chcesz ograniczyć dostęp do konta usługi Automation tylko z poziomu sieci wirtualnej i nie zezwalać na dostęp z publicznego Internetu, możesz ustawić `publicNetworkAccess` Właściwość na `$false` .

Gdy ustawienie **dostępu do sieci publicznej** jest ustawione na `$false` , dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych, a wszystkie połączenia za pośrednictwem publicznych punktów końcowych są odrzucane z nieautoryzowanym komunikatem o błędzie i stanem HTTP 401.

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **publicznego dostępu do sieci** na poziomie konta usługi Automation:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Możesz również kontrolować właściwość dostępu do sieci publicznej z poziomu Azure Portal. Z poziomu konta usługi Automation wybierz pozycję **izolacja sieciowa** z okienka po lewej stronie w sekcji **Ustawienia konta** . Gdy ustawienie dostępu do sieci publicznej jest ustawione na wartość **nie**, dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych, a wszystkie połączenia za pośrednictwem publicznych punktów końcowych są odrzucane.

![Ustawienie dostępu do sieci publicznej](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

W przypadku nawiązywania połączenia z prywatnym zasobem linku przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia należy prawidłowo skonfigurować ustawienia DNS w celu rozpoznania przydzielony prywatny adres IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia przez publiczny punkt końcowy. Twoja konfiguracja DNS powinna zostać sprawdzona i zaktualizowana w celu nawiązania połączenia przy użyciu prywatnego punktu końcowego.

Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełen zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatnych adresów IP przyznanych dla danego zasobu linku prywatnego.

Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji:

* Użyj pliku hosta (zalecane tylko do testowania). Na maszynie wirtualnej można użyć pliku hosta do przesłonięcia najpierw usługi DNS w celu rozpoznawania nazw. Wpis DNS powinien wyglądać podobnie do poniższego przykładu: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Użyj [prywatnej strefy DNS](../../dns/private-dns-privatednszone.md). Za pomocą prywatnych stref DNS można zastąpić rozpoznawanie nazw DNS dla określonego prywatnego punktu końcowego. Prywatna strefa DNS może być połączona z siecią wirtualną w celu rozpoznania określonych domen. Aby włączyć agenta na maszynie wirtualnej w celu komunikowania się za pośrednictwem prywatnego punktu końcowego, Utwórz rekord Prywatna strefa DNS jako `privatelink.azure-automation.net` . Dodaj nowe mapowanie rekordu DNS *a* na adres IP prywatnego punktu końcowego.

* Użyj usługi przesyłania dalej DNS (opcjonalnie). Możesz użyć usługi przesyłania dalej DNS, aby zastąpić rozpoznawanie nazw DNS dla określonego zasobu linku prywatnego. Jeśli [serwer DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) jest hostowany w sieci wirtualnej, można utworzyć regułę przekazywania DNS w celu użycia prywatnej strefy DNS w celu uproszczenia konfiguracji wszystkich prywatnych zasobów łączy.

Aby uzyskać więcej informacji, zobacz [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](../../private-link/private-endpoint-overview.md).