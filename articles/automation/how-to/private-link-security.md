---
title: Użyj Azure Private Link, aby bezpiecznie połączyć sieci z Azure Automation
description: Użyj Azure Private Link, aby bezpiecznie połączyć sieci z Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831460"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Użyj Azure Private Link, aby bezpiecznie połączyć sieci z Azure Automation

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę Automation do sieci wirtualnej. Ruch sieciowy między maszynami w sieci wirtualnej i kontem usługi Automation przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu.

Na przykład masz sieć wirtualną, w której wyłączono wychodzący dostęp do Internetu. Chcesz jednak prywatnie uzyskać dostęp do konta usługi Automation i korzystać z funkcji automatyzacji, takich jak elementy webhook, State Configuration i zadania elementów Runbook, w hybrydowych procesach procesów pracy elementów Runbook. Ponadto chcesz, aby użytkownicy mieli dostęp do konta usługi Automation tylko za pośrednictwem sieci wirtualnej.  Wdrożenie prywatnego punktu końcowego pozwala osiągnąć te cele.

W tym artykule opisano, kiedy używać i jak skonfigurować prywatny punkt końcowy przy użyciu konta usługi Automation.

![Omówienie pojęć Private Link for Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Private Link z usługą Azure Automation jest dostępna tylko w chmurach Azure Commercial i Azure US Government.

## <a name="advantages"></a>Zalety

Za Private Link można:

- Nawiązywanie prywatnego połączenia z Azure Automation bez otwierania dostępu do sieci publicznej.
- Nawiązywanie prywatnego połączenia z Azure Monitor usługi Log Analytics bez otwierania dostępu do sieci publicznej.

    >[!NOTE]
    >Oddzielny prywatny punkt końcowy dla obszaru roboczego usługi Log Analytics jest wymagany, jeśli konto usługi Automation jest połączone z obszarem roboczym usługi Log Analytics w celu przekazywania danych zadania oraz gdy masz włączone funkcje, takie jak Update Management, Śledzenie zmian i spis, State Configuration lub Start/Stop VMs during off-hours. Aby uzyskać więcej informacji o Private Link dla Azure Monitor, zobacz Używanie Azure Private Link do bezpiecznego łączenia sieci [z Azure Monitor](../../azure-monitor/logs/private-link-security.md).

- Upewnij się, że dostęp do danych usługi Automation jest uzyskiwany tylko za pośrednictwem autoryzowanych sieci prywatnych.
- Zapobiegaj eksfiltracji danych z sieci prywatnych, definiując zasób Azure Automation, który nawiązuje połączenie za pośrednictwem prywatnego punktu końcowego.
- Bezpiecznie połącz prywatną sieć lokalną z usługą Azure Automation usługi ExpressRoute i Private Link.
- Cały ruch wewnątrz sieci szkieletowej Microsoft Azure sieci szkieletowej.

Aby uzyskać więcej informacji,  [zobacz Najważniejsze zalety Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Ograniczenia

- W bieżącej implementacji usługi Private Link w chmurze konta usługi Automation nie mogą uzyskać dostępu do zasobów platformy Azure zabezpieczonych przy użyciu prywatnego punktu końcowego. Na przykład Azure Key Vault, Azure SQL, konto usługi Azure Storage itp. Aby obejść ten problem, użyj [hybrydowego procesu roboczego runbook.](../automation-hybrid-runbook-worker.md)
- Musisz użyć najnowszej wersji agenta usługi [Log Analytics dla](../../azure-monitor/agents/log-analytics-agent.md) systemu Windows lub Linux.
- Brama [usługi Log Analytics](../../azure-monitor/agents/gateway.md) nie obsługuje Private Link.

## <a name="how-it-works"></a>Jak to działa

Azure Automation Private Link łączy co najmniej jeden prywatny punkt końcowy (i w związku z tym sieci wirtualne, w których się znajdują) z zasobem konta usługi Automation. Te punkty końcowe to maszyny używające webhook do uruchamiania runbook, maszyny hostujących rolę hybrydowego procesu roboczego runbook i Desired State Configuration (DSC).

Po utworzeniu prywatnych punktów końcowych dla usługi Automation każdy publiczny adres URL usługi Automation jest mapowany na jeden prywatny punkt końcowy w sieci wirtualnej. Użytkownik lub maszyna mogą bezpośrednio kontaktować się z adresami URL usługi Automation.

### <a name="webhook-scenario"></a>Scenariusz z webhook

Możesz uruchomić podręczniki Runbook, wykonując wpis POST w adresie URL webhook. Na przykład adres URL wygląda następująco: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Scenariusz hybrydowego procesu roboczego runbook

Funkcja hybrydowego procesu roboczego elementu Runbook użytkownika w programie Azure Automation umożliwia uruchamianie elementu Runbook bezpośrednio na maszynie platformy Azure lub spoza platformy Azure, w tym na serwerach zarejestrowanych Azure Arc serwerach z włączoną obsługą. Na maszynie lub serwerze hostującym rolę można uruchamiać na nim bezpośrednio i względem zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi.

Punkt końcowy środowiska JRDS jest używany przez hybrydowy proces roboczy do uruchamiania/zatrzymywania zadań Runbook, pobierania do procesu roboczego i wysyłania strumienia dziennika zadań z powrotem do usługi Automation.Po włączeniu punktu końcowego środowiska JRDS adres URL będzie wyglądać tak: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Dzięki temu wykonanie runbook w hybrydowym procesu roboczego połączonym z usługą Azure Virtual Network będzie w stanie wykonywać zadania bez konieczności otwierania połączenia wychodzącego z Internetem.  

> [!NOTE]
>Bieżąca implementacja linków prywatnych dla usługi Azure Automation obsługuje tylko uruchamianie zadań w hybrydowym procesu roboczego runbook połączonym z siecią wirtualną platformy Azure i nie obsługuje zadań w chmurze.

## <a name="hybrid-worker-scenario-for-update-management"></a>Scenariusz hybrydowego procesu roboczego dla Update Management  

Hybrydowy proces roboczy elementu Runbook systemu obsługuje zestaw ukrytych elementach Runbook używanych przez funkcję Update Management, które są przeznaczone do instalowania aktualizacji określonych przez użytkownika na maszynach z systemami Windows i Linux. Gdy Azure Automation Update Management jest włączona, każda maszyna połączona z obszarem roboczym usługi Log Analytics jest automatycznie konfigurowana jako systemowy hybrydowy proces roboczy runbook.

Aby zrozumieć &, Update Management temat [Informacje o Update Management](../update-management/overview.md). Ta Update Management jest zależna od obszaru roboczego usługi Log Analytics i dlatego wymaga połączenia obszaru roboczego z kontem usługi Automation. Obszar roboczy usługi Log Analytics przechowuje dane zebrane przez rozwiązanie oraz hostuje wyszukiwania i widoki w dziennikach.

Jeśli chcesz, aby maszyny skonfigurowane do zarządzania aktualizacjami łączyły się z obszarem roboczym usługi Automation & Log Analytics w bezpieczny sposób za pośrednictwem kanału Private Link, musisz włączyć usługę Private Link dla obszaru roboczego usługi Log Analytics połączonego z kontem usługi Automation skonfigurowanym przy użyciu usługi Private Link.

Możesz kontrolować sposób, w jaki można uzyskać do obszaru roboczego usługi Log Analytics spoza zakresów Private Link, zgodnie z instrukcjami opisanymi w tece [Konfigurowanie usługi Log Analytics.](../../azure-monitor/logs/private-link-security.md#configure-log-analytics) Jeśli ustawisz ustawienie **Zezwalaj na dostęp** do sieci publicznej na potrzeby pozyskiwania na wartość **Nie,** maszyny spoza połączonych zakresów nie będą w stanie przekazać danych do tego obszaru roboczego. Jeśli ustawisz **ustawienie Zezwalaj na dostęp do** sieci publicznej dla zapytań na wartość **Nie,** maszyny spoza zakresów nie będą mieć dostępu do danych w tym obszarze roboczym.

Docelowy **zasób podrzędny DSCAndHybridWorker** umożliwia użytkownikom Private Link użytkowników & hybrydowych ręcznych.

> [!NOTE]
> Maszyny hostowane poza platformą Azure, które są zarządzane przez usługę Update Management i są połączone z siecią wirtualną platformy Azure za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych korzystających z prywatnych punktów końcowych obsługują Private Link.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc)

State Configuration udostępnia usługę zarządzania konfiguracją platformy Azure, która umożliwia pisanie i kompilowanie konfiguracji programu PowerShell Desired State Configuration (DSC) oraz zarządzanie nimi dla węzłów w dowolnym chmurze lub lokalnym centrum danych.

Agent na maszynie rejestruje się w usłudze DSC, a następnie używa punktu końcowego usługi do ściągania konfiguracji DSC. Punkt końcowy usługi agenta wygląda następująco: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Adres URL dla publicznego & prywatnego punktu końcowego będzie taki sam, jednak po włączeniu linku prywatnego zostanie on zamapowany na prywatny adres IP.

## <a name="planning-based-on-your-network"></a>Planowanie na podstawie sieci

Przed skonfigurowaniem zasobu konta usługi Automation należy wziąć pod uwagę wymagania dotyczące izolacji sieci. Oceń dostęp sieci wirtualnych do publicznego Internetu oraz ograniczenia dostępu do konta usługi Automation (w tym konfigurowanie zakresu grupy usługi Private Link do dzienników usługi Azure Monitor, jeśli są zintegrowane z kontem usługi Automation). Uwzględnij również przegląd rekordów [DNS](./automation-region-dns-records.md) usługi Automation w ramach planu, aby upewnić się, że obsługiwane funkcje działają bez problemów.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Utwórz prywatny punkt końcowy, aby połączyć naszą sieć. Możesz go utworzyć w [centrum Azure Portal Private Link aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Zastosowanie zmian w funkcji publicNetworkAccess i Private Link może potrwać do 35 minut.

W tej sekcji utworzysz prywatny punkt końcowy dla konta usługi Automation.

1. W lewym górnym rogu ekranu wybierz pozycję Utwórz **zasób** przy > sieci > Centrum usługi Private Link .

2. W **Centrum usługi Private Link — Omówienie** dla opcji Tworzenie połączenia prywatnego z **usługą** wybierz pozycję **Uruchom.**

3. W **części Tworzenie maszyny wirtualnej — podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono je w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź wartość *w wartości PrivateEndpoint.* |
    | Region (Region) | Wybierz **pozycję TwójRegion**. |
    |||

4. Wybierz **pozycję Dalej: Zasób.**

5. W **grupę Tworzenie prywatnego punktu końcowego — Zasób** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz **pozycję Microsoft.Automation/automationAccounts.** |
    | Zasób |Wybierz *pozycję myAutomationAccount*|
    |Docelowy podźródło |Wybierz *pozycję Webhook* lub *DSCAndHybridWorker* w zależności od scenariusza.|
    |||

6. Wybierz **pozycję Dalej: Konfiguracja.**

7. W **jęz. Tworzenie prywatnego punktu końcowego — konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**SIEĆ**| |
    | Sieć wirtualna| Wybierz *pozycję MyVirtualNetwork.* |
    | Podsieć | Wybierz *pozycję mySubnet.* |
    |**INTEGRACJA PRYWATNEJ USŁUGI DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |Wybierz *pozycję (Nowy)privatelink.azure-automation.net* |
    |||

8. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

9. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

W **Centrum usługi Private Link** wybierz pozycję **Prywatne punkty końcowe,** aby wyświetlić zasób linku prywatnego.

![Link prywatny zasobu usługi Automation](./media/private-link-security/private-link-automation-resource.png)

Wybierz zasób, aby wyświetlić wszystkie szczegóły. Powoduje to utworzenie nowego prywatnego punktu końcowego dla konta usługi Automation i przypisanie mu prywatnego adresu IP z sieci wirtualnej. Stan **połączenia jest** następujący: **zatwierdzone.**

Podobnie tworzona jest unikatowa w pełni kwalifikowana nazwa domeny (FQDN) dla środowiska uruchomieniowego State Configuration (agentsvc) i środowiska uruchomieniowego zadania hybrydowego procesu roboczego runbook (jrds). Każdy z nich ma przypisany osobny adres IP od sieci wirtualnej, a **stan** połączenia jest przedstawiany jako **zatwierdzony.**

Jeśli użytkownik usługi ma uprawnienia RBAC platformy Azure do zasobu usługi Automation, może wybrać metodę automatycznego zatwierdzania. W takim przypadku, gdy żądanie dociera do zasobu dostawcy usługi Automation, nie jest wymagana żadna akcja od dostawcy usług, a połączenie jest automatycznie zatwierdzane.

## <a name="set-public-network-access-flags"></a>Ustawianie flag dostępu do sieci publicznej

Możesz skonfigurować konto usługi Automation tak, aby odrzucało wszystkie konfiguracje publiczne i zezwalało tylko na połączenia za pośrednictwem prywatnych punktów końcowych w celu dalszego zwiększenia bezpieczeństwa sieci. Jeśli chcesz ograniczyć dostęp do konta usługi Automation tylko z poziomu sieci wirtualnej i nie zezwolić na dostęp z publicznego Internetu, możesz ustawić właściwość `publicNetworkAccess` na `$false` .

Gdy ustawienie **Dostęp** do sieci publicznej jest ustawione na wartość , dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych, a wszystkie połączenia za pośrednictwem publicznych punktów końcowych są niedozwolone z nieautoryzowanym komunikatem o błędzie i stanem `$false` HTTP 401.

Poniższy skrypt programu PowerShell pokazuje, jak to zrobić, i właściwość Dostęp do `Get` sieci publicznej na poziomie konta usługi `Set` Automation: 

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Możesz również kontrolować właściwość dostępu do sieci publicznej z Azure Portal. Na koncie usługi Automation wybierz pozycję **Izolacja sieci** w okienku po lewej stronie w **sekcji Ustawienia** konta. Jeśli ustawienie Dostęp do sieci publicznej ma wartość **Nie,** dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych i wszystkie połączenia za pośrednictwem publicznych punktów końcowych są niedozwolone.

![Ustawienie Dostęp do sieci publicznej](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

Podczas nawiązywania połączenia z zasobem linku prywatnego przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia ważne jest prawidłowe skonfigurowanie ustawień DNS w celu rozpoznania przydzielonego prywatnego adresu IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia za pośrednictwem publicznego punktu końcowego. Konfiguracja DNS powinna być przeglądana i aktualizowana w celu nawiązania połączenia przy użyciu prywatnego punktu końcowego.

Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełny zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatne adresy IP przydzielone do danego zasobu łącza prywatnego.

Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji:

* Użyj pliku hosta (zalecane tylko do testowania). Można użyć pliku hosta na maszynie wirtualnej, aby przesłonić przy użyciu systemu DNS do rozpoznawania nazw. Wpis DNS powinien wyglądać podobnie do następującego przykładu: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Użyj prywatnej [strefy DNS.](../../dns/private-dns-privatednszone.md) Za pomocą prywatnych stref DNS można zastąpić rozpoznawanie nazw DNS dla określonego prywatnego punktu końcowego. Prywatną strefę DNS można połączyć z siecią wirtualną w celu rozpoznania określonych domen. Aby umożliwić agentowi na maszynie wirtualnej komunikację za pośrednictwem prywatnego punktu końcowego, utwórz rekord Prywatna strefa DNS jako `privatelink.azure-automation.net` . Dodaj nowe mapowanie *rekordu DNS A* do adresu IP prywatnego punktu końcowego.

* Użyj usługi przesyłania dalej DNS (opcjonalnie). Za pomocą usługi przesyłania dalej DNS można zastąpić rozpoznawanie nazw DNS dla określonego zasobu łącza prywatnego. Jeśli serwer [DNS jest](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostowany w sieci wirtualnej, można utworzyć regułę przekazywania DNS, aby użyć prywatnej strefy DNS, aby uprościć konfigurację wszystkich zasobów łącza prywatnego.

Aby uzyskać więcej informacji, zobacz [Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat prywatnego punktu końcowego, zobacz [Co to jest prywatny punkt końcowy platformy Azure?](../../private-link/private-endpoint-overview.md).
