---
title: Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation
description: Użyj prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a4985784a17f2e0350a7b2c7a4f62f574862d50c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714351"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>Skorzystaj z prywatnego linku platformy Azure, aby bezpiecznie połączyć sieci z Azure Automation (wersja zapoznawcza)

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę Automation do sieci wirtualnej. Ruch sieciowy między maszynami w sieci wirtualnej a kontem usługi Automation przechodzi przez sieć wirtualną oraz łącze prywatne w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Na przykład masz sieć wirtualną, w której wyłączono dostęp do Internetu. Jednak chcesz uzyskać dostęp do konta usługi Automation prywatnie i korzystać z funkcji automatyzacji, takich jak webhook, konfiguracja stanu i zadania elementu Runbook w hybrydowych procesach roboczych elementów Runbook. Ponadto chcesz, aby użytkownicy mieli dostęp do konta usługi Automation tylko za pomocą sieci wirtualnej.  Wdrożenie prywatnego punktu końcowego osiąga te cele.

W tym artykule omówiono, kiedy należy używać i jak skonfigurować prywatny punkt końcowy przy użyciu konta usługi Automation (wersja zapoznawcza).

![Przegląd koncepcyjny prywatnego linku do Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Obsługa linków prywatnych z Azure Automation (wersja zapoznawcza) jest dostępna tylko w chmurach dla instytucji rządowych platformy Azure.

## <a name="advantages"></a>Zalety

Za pomocą linku prywatnego można:

- Połącz się prywatnie, aby Azure Automation bez otwierania dostępu do sieci publicznej.
- Połącz się prywatnie z Azure Monitor Log Analytics obszaru roboczego bez otwierania dostępu do sieci publicznej.

    >[!NOTE]
    >Jest to wymagane, jeśli konto usługi Automation jest połączone z obszarem roboczym Log Analytics, aby przekazywać dane dotyczące zadań, a w przypadku włączenia funkcji, takich jak Update Management, Change Tracking i spis, konfiguracja stanu lub Start/Stop VMs during off-hours. Aby uzyskać więcej informacji na temat prywatnego linku do Azure Monitor, zobacz [bezpieczne łączenie sieci do Azure monitor za pomocą prywatnego linku platformy Azure](../../azure-monitor/platform/private-link-security.md).

- Upewnij się, że dane automatyzacji są dostępne tylko za poorednictwem autoryzowanych sieci prywatnych.
- Zapobiegaj eksfiltracji danych z sieci prywatnych przez definiowanie zasobów Azure Automation, które łączą się za pośrednictwem prywatnego punktu końcowego.
- Bezpiecznie łącz swoją prywatną sieć lokalną z Azure Automation przy użyciu linku ExpressRoute i prywatnego.
- Zachowaj cały ruch w sieci szkieletowej Microsoft Azure.

Aby uzyskać więcej informacji, zobacz  [najważniejsze zalety linku prywatnego](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to działa

Łącze prywatne Azure Automation łączy co najmniej jeden prywatny punkt końcowy (i w związku z tym sieci wirtualne, które znajdują się w nim) do zasobu konta usługi Automation. Te punkty końcowe to maszyny korzystające z elementów webhook do uruchamiania elementu Runbook, Maszyny obsługujące rolę hybrydowego procesu roboczego elementu Runbook i węzły DSC.

Po utworzeniu prywatnych punktów końcowych dla usługi Automation każdy z publicznych adresów URL usługi Automation, które użytkownik lub komputer może bezpośrednio się skontaktować, jest mapowany do jednego prywatnego punktu końcowego w sieci wirtualnej.

W ramach wersji zapoznawczej konto usługi Automation nie może uzyskać dostępu do zasobów platformy Azure zabezpieczonych za pomocą prywatnego punktu końcowego. Na przykład Azure Key Vault, Azure SQL, konto usługi Azure Storage itd.

### <a name="webhook-scenario"></a>Scenariusz elementu webhook

Elementy Runbook można uruchamiać przy użyciu wpisu na adres URL elementu webhook. Na przykład adres URL wygląda następująco: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Scenariusz konfiguracja stanu (agentsvc)

Konfiguracja stanu udostępnia usługę Azure Configuration Management, która pozwala na pisanie i kompilowanie konfiguracji konfiguracji żądanego stanu (DSC) programu PowerShell dla węzłów w chmurze lub lokalnych centrach danych oraz zarządzanie nimi.

Agent na komputerze rejestruje usługę DSC, a następnie używa punktu końcowego usługi do ściągania konfiguracji DSC. Punkt końcowy usługi agenta wygląda następująco: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Adres URL publicznego punktu końcowego & prywatny będzie taki sam, ale zostałby zamapowany na prywatny adres IP, gdy łącze prywatne jest włączone.

## <a name="planning-based-on-your-network"></a>Planowanie w oparciu o sieć

Przed skonfigurowaniem zasobu konta usługi Automation należy wziąć pod uwagę wymagania dotyczące izolacji sieci. Oceń dostęp do sieci wirtualnych za pomocą publicznego Internetu, a także ograniczenia dostępu do konta usługi Automation (w tym konfigurowania zakresu grupy linków prywatnych do Azure Monitor dzienników, jeśli są zintegrowane z kontem usługi Automation). Należy również uwzględnić przegląd [rekordów DNS](./automation-region-dns-records.md) usługi Automation w ramach planu, aby zapewnić, że obsługiwane funkcje działają bez problemu.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Utwórz prywatny punkt końcowy, aby połączyć naszą sieć. Można go utworzyć w [Azure Portal centrum łączy prywatnych](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Po zastosowaniu zmian do publicNetworkAccess i linku prywatnego może upłynąć do 35 minut.

W tej sekcji utworzysz prywatny punkt końcowy dla konta usługi Automation.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > prywatnym centrum linków (wersja zapoznawcza)**.

2. W **centrum linków prywatnych — Omówienie**opcji **tworzenia połączenia prywatnego z usługą**wybierz pozycję **Rozpocznij**.

3. W obszarze **Tworzenie maszyny wirtualnej — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź *PrivateEndpoint*. |
    | Region | Wybierz pozycję **YourRegion**. |
    |||

4. Wybierz pozycję **Dalej: zasób**.

5. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Automation/automationAccounts**. |
    | Zasób |Wybierz *myAutomationAccount*|
    |Podzasób docelowy |W zależności od scenariusza wybierz pozycję *webhook* lub *DSCAndHybridWorker* .|
    |||

6. Wybierz pozycję **Dalej: Konfiguracja**.

7. W obszarze **Tworzenie prywatnego punktu końcowego — konfiguracja**wprowadź lub wybierz następujące informacje:

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

W **centrum linku prywatnego (wersja zapoznawcza)** wybierz pozycję **prywatne punkty końcowe** , aby wyświetlić zasób łącza prywatnego.

![Prywatny link do zasobu usługi Automation](./media/private-link-security/private-link-automation-resource.png)

Wybierz zasób, aby wyświetlić wszystkie szczegóły. Spowoduje to utworzenie nowego prywatnego punktu końcowego dla konta usługi Automation i przypisanie go do prywatnego adresu IP z sieci wirtualnej. **Stan połączenia** jest wyświetlany jako **zatwierdzone**.

Analogicznie, unikatowa w pełni kwalifikowana nazwa domeny (FQDN) jest tworzona dla konfiguracji stanu (agentsvc) oraz dla hybrydowego środowiska uruchomieniowego Runbook Worker (jrds). Każdy z nich ma przypisany oddzielny adres IP od sieci wirtualnej, a **stan połączenia** jest wyświetlany jako **zatwierdzony**.

Jeśli odbiorca usługi ma uprawnienia usługi Azure RBAC w ramach zasobu usługi Automation, może wybrać metodę automatycznego zatwierdzania. W takim przypadku, gdy żądanie osiągnie zasób dostawcy usługi Automation, nie jest wymagane żadne działanie od usługodawcy, a połączenie jest automatycznie zatwierdzane.

## <a name="set-public-network-access-flags"></a>Ustawianie flag dostępu do sieci publicznej

Można skonfigurować konto usługi Automation, aby odmówić całej konfiguracji publicznej i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci. Jeśli chcesz ograniczyć dostęp do konta usługi Automation tylko z poziomu sieci wirtualnej i nie zezwalać na dostęp z publicznego Internetu, możesz ustawić `publicNetworkAccess` Właściwość na `$false` .

Gdy ustawienie **dostępu do sieci publicznej** jest ustawione na `$false` , dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych, a wszystkie połączenia za pośrednictwem publicznych punktów końcowych są odrzucane z komunikatem o błędzie unathorized i stanem HTTP 401. 

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **publicznego dostępu do sieci** na poziomie konta usługi Automation:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

W przypadku nawiązywania połączenia z prywatnym zasobem linku przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia należy prawidłowo skonfigurować ustawienia DNS w celu rozpoznania przydzielony prywatny adres IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia przez publiczny punkt końcowy. Twoja konfiguracja DNS powinna zostać sprawdzona i zaktualizowana w celu nawiązania połączenia przy użyciu prywatnego punktu końcowego.

Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełen zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatnych adresów IP przyznanych dla danego zasobu linku prywatnego.

Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji:

* Użyj pliku hosta (zalecane tylko do testowania). Na maszynie wirtualnej można użyć pliku hosta do przesłonięcia najpierw usługi DNS w celu rozpoznawania nazw.

* Użyj [prywatnej strefy DNS](../../dns/private-dns-privatednszone.md). Za pomocą prywatnych stref DNS można zastąpić rozpoznawanie nazw DNS dla określonego prywatnego punktu końcowego. Prywatna strefa DNS może być połączona z siecią wirtualną w celu rozpoznania określonych domen. Aby włączyć agenta na maszynie wirtualnej w celu komunikowania się za pośrednictwem prywatnego punktu końcowego, Utwórz rekord Prywatna strefa DNS jako `privatelink.azure-automation.net` . Dodaj nowe mapowanie rekordu DNS *a* na adres IP prywatnego punktu końcowego.

* Użyj usługi przesyłania dalej DNS (opcjonalnie). Możesz użyć usługi przesyłania dalej DNS, aby zastąpić rozpoznawanie nazw DNS dla określonego zasobu linku prywatnego. Jeśli [serwer DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) jest hostowany w sieci wirtualnej, można utworzyć regułę przekazywania DNS w celu użycia prywatnej strefy DNS w celu uproszczenia konfiguracji wszystkich prywatnych zasobów łączy.

Aby uzyskać więcej informacji, zobacz [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](../../private-link/private-endpoint-overview.md).