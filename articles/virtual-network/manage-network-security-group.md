---
title: Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie znaleźć informacje o regułach zabezpieczeń i sposobach tworzenia, zmieniania lub usuwania sieciowej grupy zabezpieczeń.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 5de909d0d57ae212fa562eb31551e2271d307d47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694261"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń

Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Omówienie grup zabezpieczeń sieci](./network-security-groups-overview.md). Następnie Ukończ samouczek [Filtruj ruch sieciowy](tutorial-filter-network-traffic.md) , aby uzyskać pewne doświadczenie z sieciowymi grupami zabezpieczeń.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Wykonaj jedno z tych zadań przed rozpoczęciem pozostałej części tego artykułu:

- **Użytkownicy portalu**: Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell**: Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub Uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell Browser Znajdź listę rozwijaną **Wybierz środowisko** , a następnie wybierz opcję **PowerShell** , jeśli nie została jeszcze wybrana.

    Jeśli używasz programu PowerShell lokalnie, użyj Azure PowerShell module w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI)**: uruchamiają polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiają interfejs CLI z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do [roli współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można tworzyć, [wyświetlać wszystkie](#view-all-network-security-groups), [wyświetlać szczegóły](#view-details-of-a-network-security-group), [zmieniać](#change-a-network-security-group)i [usuwać](#delete-a-network-security-group) sieciową grupę zabezpieczeń. Można także [skojarzyć lub usunąć skojarzenie](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) sieciowej grupy zabezpieczeń z interfejsu sieciowego lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje ograniczenie dotyczące liczby grup zabezpieczeń sieci, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **sieciowa Grupa zabezpieczeń**.

3. Na stronie **Tworzenie grupy zabezpieczeń sieci** na karcie **podstawowe** Ustaw wartości dla następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz żądaną lokalizację. |

4. Wybierz pozycję **Przejrzyj i utwórz**.

5. Po wyświetleniu komunikatu o **przekazaniu walidacji** wybierz pozycję **Utwórz**.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Wyświetl wszystkie sieciowe grupy zabezpieczeń

Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**. Zostanie wyświetlona lista sieciowych grup zabezpieczeń dla Twojej subskrypcji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [Lista AZ Network sieciowej grupy zabezpieczeń list](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Wyświetlanie szczegółów sieciowej grupy zabezpieczeń

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń.

Na pasku menu sieciowej grupy zabezpieczeń w obszarze **Ustawienia** można wyświetlić **reguły zabezpieczeń ruchu przychodzącego**, **reguły zabezpieczeń ruchu wychodzącego**, **interfejsy sieciowe** i **podsieci** , z którymi jest skojarzona Grupa zabezpieczeń sieci.

W obszarze **monitorowanie** można włączyć lub wyłączyć **Ustawienia diagnostyczne**. W obszarze **Pomoc techniczna i rozwiązywanie problemów** można wyświetlić **obowiązujące reguły zabezpieczeń**. Aby dowiedzieć się więcej, zobacz [rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń](virtual-network-nsg-manage-log.md) i [diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md).

Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące artykuły:

- [Dziennik aktywności](../azure-monitor/essentials/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network sieciowej grupy zabezpieczeń show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Zmiana sieciowej grupy zabezpieczeń

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz zmienić.

Najczęstszymi zmianami jest [dodanie reguły zabezpieczeń](#create-a-security-rule), [usunięcie reguły](#delete-a-security-rule)i [skojarzenie lub skojarzenie sieciowej grupy zabezpieczeń z lub z podsiecią lub interfejsem sieciowym](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network sieciowej grupy zabezpieczeń Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Kojarzenie lub usuwanie skojarzenia sieciowej grupy zabezpieczeń z lub z podsiecią lub interfejsem sieciowym

Aby skojarzyć sieciową grupę zabezpieczeń z interfejsem sieciowym lub usunąć skojarzenie sieciowej grupy zabezpieczeń, zobacz [kojarzenie sieciowej grupy zabezpieczeń lub usuń skojarzenie sieciowej grupy zabezpieczeń z interfejsu sieciowego](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Aby skojarzyć grupę zabezpieczeń sieci z lub usunąć skojarzenie sieciowej grupy zabezpieczeń z podsiecią, zobacz [Zmiana ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuń sieciową grupę zabezpieczeń

Jeśli grupa zabezpieczeń sieci jest skojarzona z dowolnymi podsieciami lub interfejsami sieciowymi, nie można jej usunąć. Przed podjęciem próby usunięcia Usuń skojarzenie sieciowej grupy zabezpieczeń ze wszystkimi podsieciami i interfejsami sieciowymi.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz usunąć.

3. Na pasku narzędzi sieciowej grupy zabezpieczeń wybierz pozycję **Usuń**. Następnie wybierz pozycję **tak** w oknie dialogowym potwierdzenia.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network sieciowej grupy zabezpieczeń Delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Korzystanie z reguł zabezpieczeń

Sieciowa Grupa zabezpieczeń zawiera co najmniej zero reguł zabezpieczeń. Możesz tworzyć, [wyświetlać wszystkie](#view-all-security-rules), [wyświetlać szczegóły](#view-details-of-a-security-rule), [zmieniać](#change-a-security-rule)i [usuwać](#delete-a-security-rule) regułę zabezpieczeń.

### <a name="create-a-security-rule"></a>Tworzenie reguły zabezpieczeń

Istnieje ograniczenie liczby reguł na grupę zabezpieczeń sieci, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, do której chcesz dodać regułę zabezpieczeń.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń ruchu wychodzącego**.

    Wymienione są kilka istniejących reguł, w tym niektóre, które nie zostały dodane. Podczas tworzenia sieciowej grupy zabezpieczeń są w niej tworzone kilka domyślnych reguł zabezpieczeń. Aby dowiedzieć się więcej, zobacz [domyślne reguły zabezpieczeń](./network-security-groups-overview.md#default-security-rules).  Nie można usunąć domyślnych reguł zabezpieczeń, ale można je zastąpić przy użyciu reguł mających wyższy priorytet.

4. <a name="security-rule-settings"></a>Wybierz pozycję **Dodaj**. Wybierz lub Dodaj wartości dla następujących ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie | Wartość | Szczegóły |
    | ------- | ----- | ------- |
    | **Element źródłowy** | Jeden z:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń ruchu przychodzącego) lub **VirtualNetwork** (reguła zabezpieczeń wychodzących)</li><li>**&nbsp;Grupa zabezpieczeń &nbsp; aplikacji**</li></ul> | <p>W przypadku wybrania **adresów IP** należy również określić **źródłowe adresy IP/zakresy CIDR**.</p><p>Jeśli wybierzesz pozycję **tag usługi**, możesz również wybrać **tag usługi źródłowej**.</p><p>W przypadku wybrania **grupy zabezpieczeń aplikacji** należy również wybrać istniejącą grupę zabezpieczeń aplikacji. W przypadku wybrania **grupy zabezpieczeń aplikacji** zarówno **źródłowej** , jak i **docelowej** interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą znajdować się w tej samej sieci wirtualnej.</p> |
    | **Źródłowe adresy IP/zakresy CIDR** | Rozdzielana przecinkami lista adresów IP i zakresów routingu między domenami (CIDR) | <p>To ustawienie pojawia się, jeśli zmienisz **Źródło** na **adresy IP**. Należy określić pojedynczą wartość lub rozdzieloną przecinkami listę wielu wartości. Przykładem wielu wartości jest `10.0.0.0/16, 192.188.1.1` . Istnieją limity liczby wartości, które można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, określ jego prywatny adres IP, a nie publiczny adres IP. Platforma Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, ale przed przekazaniem prywatnego adresu IP na publiczny adres IP dla reguł ruchu wychodzącego. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](./public-ip-addresses.md).</p> |
    | **Tag usługi źródłowej** | Tag usługi z listy rozwijanej | To ustawienie opcjonalne pojawia się, jeśli ustawisz **tag** **Source** to Service dla reguły zabezpieczeń dla ruchu przychodzącego. Tag usługi jest wstępnie zdefiniowanym identyfikatorem dla kategorii adresów IP. Aby dowiedzieć się więcej na temat dostępnych tagów usługi i informacje o tym, co reprezentuje każdy tag, zobacz [Tagi usług](./network-security-groups-overview.md#service-tags). |
    | **Grupa zabezpieczeń aplikacji źródłowej** | Istniejąca Grupa zabezpieczeń aplikacji | To ustawienie pojawia się, jeśli ustawisz **Źródło** na **grupę zabezpieczeń aplikacji**. Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). |
    | **Zakresy portów źródłowych** | Jeden z:<ul><li>Pojedynczy port, taki jak `80`</li><li>Zakres portów, taki jak `1024-65535`</li><li>Rozdzielana przecinkami lista pojedynczych portów i/lub zakresów portów, takich jak `80, 1024-65535`</li><li>Gwiazdka ( `*` ), aby zezwolić na ruch na dowolnym porcie</li></ul> | To ustawienie określa porty, na których reguła zezwala na ruch lub odmówi go. Istnieją limity liczby portów, które można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Element docelowy** | Jeden z:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń wychodzących) lub **VirtualNetwork** (reguła zabezpieczeń dla ruchu przychodzącego)</li><li>**&nbsp;Grupa zabezpieczeń &nbsp; aplikacji**</li></ul> | <p>W przypadku wybrania opcji **adresy IP** Określ także **docelowe adresy IP/zakresy CIDR**.</p><p>W przypadku wybrania opcji **VirtualNetwork** ruch będzie dozwolony dla wszystkich adresów IP w przestrzeni adresowej sieci wirtualnej. **VirtualNetwork** to tag usługi.</p><p>W przypadku wybrania **grupy zabezpieczeń aplikacji** należy wybrać istniejącą grupę zabezpieczeń aplikacji. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group).</p> |
    | **Docelowe adresy IP/zakresy CIDR** | Rozdzielana przecinkami lista adresów IP i zakresów CIDR | <p>To ustawienie pojawia się, jeśli zmienisz **miejsce docelowe** na **adresy IP**. Podobnie jak **źródłowe** i **źródłowe adresy IP/zakresy CIDR**, można określić jeden lub wiele adresów lub zakresów. Istnieją limity dotyczące liczby, którą można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że określisz jego prywatny adres IP, a nie jego publicznych adresów IP. Usługa Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, ale przed przekazaniem prywatnego adresu IP do publicznego adresu IP dla reguł ruchu wychodzącego przez platformę Azure. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](./public-ip-addresses.md).</p> |
    | **Docelowy tag usługi** | Tag usługi z listy rozwijanej | To ustawienie opcjonalne pojawia się, jeśli zmienisz **miejsce docelowe** na **tag usługi** dla reguły zabezpieczeń dla ruchu wychodzącego. Tag usługi jest wstępnie zdefiniowanym identyfikatorem dla kategorii adresów IP. Aby dowiedzieć się więcej na temat dostępnych tagów usługi i informacje o tym, co reprezentuje każdy tag, zobacz [Tagi usług](./network-security-groups-overview.md#service-tags). |
    | **Grupa zabezpieczeń aplikacji docelowej** | Istniejąca Grupa zabezpieczeń aplikacji | To ustawienie jest wyświetlane, jeśli ustawisz **miejsce docelowe** dla **grupy zabezpieczeń aplikacji**. Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). |
    | **Zakresy portów docelowych** | Jeden z:<ul><li>Pojedynczy port, taki jak `80`</li><li>Zakres portów, taki jak `1024-65535`</li><li>Rozdzielana przecinkami lista pojedynczych portów i/lub zakresów portów, takich jak `80, 1024-65535`</li><li>Gwiazdka ( `*` ), aby zezwolić na ruch na dowolnym porcie</li></ul> | Podobnie jak w przypadku **zakresów portów źródłowych**, można określić jeden lub wiele portów i zakresów. Istnieją limity dotyczące liczby, którą można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokół** | **Dowolny**, **TCP**, **UDP** lub **ICMP** | Możesz ograniczyć regułę do Transmission Control Protocol (TCP), User Datagram Protocol (UDP) lub protokołu komunikacyjnego sterowania Internetem (ICMP). Wartość domyślna to reguła, która ma być stosowana do wszystkich protokołów. |
    | **Akcja** | **Zezwalaj** lub **Odmów** | To ustawienie określa, czy ta reguła zezwala na dostęp do podanej konfiguracji źródłowej i docelowej. |
    | **Priority** | Wartość z zakresu od 100 do 4096, która jest unikatowa dla wszystkich reguł zabezpieczeń w sieciowej grupie zabezpieczeń | Platforma Azure przetwarza reguły zabezpieczeń w kolejności priorytetów. Im niższa wartość, tym wyższy priorytet. Zaleca się pozostawienie przerwy między numerami priorytetu podczas tworzenia reguł, takich jak 100, 200 i 300. Pozostawienie przerw w ułatwia dodawanie reguł w przyszłości, dzięki czemu można przyznać im wyższy lub niższy priorytet niż istniejące reguły. |
    | **Nazwa** | Unikatowa nazwa reguły w sieciowej grupie zabezpieczeń | Nazwa może składać się z maksymalnie 80 znaków. Musi zaczynać się literą lub cyfrą i musi kończyć się literą, cyfrą lub podkreśleniem. Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. |
    | **Opis** | Opis tekstowy | Opcjonalnie można określić opis tekstowy dla reguły zabezpieczeń. Długość opisu nie może przekraczać 140 znaków. |

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Wyświetl wszystkie reguły zabezpieczeń

Sieciowa Grupa zabezpieczeń zawiera zero lub więcej reguł. Aby dowiedzieć się więcej na temat informacji wyświetlanych podczas wyświetlania reguł, zobacz [Omówienie grup zabezpieczeń sieci](./network-security-groups-overview.md).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń ruchu wychodzącego**.

Lista zawiera wszystkie utworzone reguły oraz [domyślne reguły zabezpieczeń](./network-security-groups-overview.md#default-security-rules)sieciowej grupy zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz **sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić szczegóły reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń ruchu wychodzącego**.

4. Wybierz regułę, dla której chcesz wyświetlić szczegóły. Aby uzyskać informacje na temat wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura ma zastosowanie tylko do niestandardowej reguły zabezpieczeń. Nie działa, jeśli wybierzesz domyślną regułę zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network sieciowej grupy zabezpieczeń Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Zmiana reguły zabezpieczeń

1. Wykonaj kroki opisane w temacie [Wyświetlanie szczegółów reguły zabezpieczeń](#view-details-of-a-security-rule).

2. Zmień ustawienia zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz**. Aby uzyskać informacje na temat wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura ma zastosowanie tylko do niestandardowej reguły zabezpieczeń. Nie można zmienić domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Usuwanie reguły zabezpieczeń

1. Wykonaj kroki opisane w temacie [Wyświetlanie szczegółów reguły zabezpieczeń](#view-details-of-a-security-rule).

2. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**.

    > [!NOTE]
    > Ta procedura ma zastosowanie tylko do niestandardowej reguły zabezpieczeń. Nie można usunąć domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network sieciowej grupy zabezpieczeń Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji zawiera co najmniej jeden interfejs sieciowy. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji](./network-security-groups-overview.md#application-security-groups). Wszystkie interfejsy sieciowe w grupie zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Aby dowiedzieć się, jak dodać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie interfejsu sieciowego do grupy zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź *grupę zabezpieczeń aplikacji*.

3. Na stronie **Grupa zabezpieczeń aplikacji** wybierz pozycję **Utwórz**.

4. Na stronie **Tworzenie grupy zabezpieczeń aplikacji** na karcie **podstawowe** Ustaw wartości dla następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz żądaną lokalizację. |

5. Wybierz pozycję **Przejrzyj i utwórz**.

6. Na karcie **Recenzja i tworzenie** kliknij przycisk **Utwórz**, gdy zobaczysz komunikat o **przekazaniu walidacji** .

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Wyświetl wszystkie grupy zabezpieczeń aplikacji

Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić grupy zabezpieczeń aplikacji. Wyszukaj i wybierz **grupy zabezpieczeń aplikacji**. Azure Portal wyświetla listę grup zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [Lista AZ Network ASG list](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetl szczegóły określonej grupy zabezpieczeń aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, dla której chcesz wyświetlić szczegóły.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Zmień grupę zabezpieczeń aplikacji

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz zmienić.

3. Wybierz pozycję **Zmień** obok ustawienia, które chcesz zmodyfikować. Można na przykład dodawać lub usuwać **Tagi** lub zmieniać **grupę zasobów** lub **subskrypcję**.

    > [!NOTE]
    > Nie można zmienić lokalizacji.

    Na pasku menu można również wybrać pozycję **Kontrola dostępu (IAM)**. Na stronie **Kontrola dostępu (IAM)** można przypisywać lub usuwać uprawnienia do grupy zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Brak polecenia cmdlet programu PowerShell |

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli zawiera ona interfejsy sieciowe. Aby usunąć wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji, należy zmienić ustawienia interfejsu sieciowego lub usunąć interfejsy sieciowe. Aby dowiedzieć się więcej, zobacz [Dodawanie do lub usuwanie z grup zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub [Usuwanie interfejsu sieciowego](virtual-network-network-interface.md#delete-a-network-interface).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać grupami zabezpieczeń aplikacji. Wyszukaj i wybierz **grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz usunąć.

3. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** , aby usunąć grupę zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące sieciowych grup zabezpieczeń, reguł zabezpieczeń i grup zabezpieczeń aplikacji, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie uprawnienia, zgodnie z opisem w poniższych tabelach:

### <a name="network-security-group"></a>Sieciowa grupa zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/odczyt                  |   Pobierz sieciową grupę zabezpieczeń                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Utwórz lub Zaktualizuj grupę zabezpieczeń sieci                             |
| Microsoft. Network/networkSecurityGroups/Delete                |   Usuń sieciową grupę zabezpieczeń                                       |
| Microsoft. Network/networkSecurityGroups/Join/Action           |   Kojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym 


>[!NOTE]
> Aby wykonać `write` operacje w sieciowej grupie zabezpieczeń, konto subskrypcji musi mieć co najmniej `read` uprawnienia do grupy zasobów wraz z `Microsoft.Network/networkSecurityGroups/write` uprawnieniem.


### <a name="network-security-group-rule"></a>Reguła sieciowej grupy zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/securityRules/odczyt            |   Pobierz regułę                                                            |
| Microsoft. Network/networkSecurityGroups/securityRules/Write           |   Utwórz lub zaktualizuj regułę                                               |
| Microsoft. Network/networkSecurityGroups/securityRules/Delete          |   Usuwanie reguły                                                         |

### <a name="application-security-group"></a>Grupa zabezpieczeń aplikacji

| Akcja                                                                     | Nazwa                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/akcja     | Przyłączanie konfiguracji adresu IP do grupy zabezpieczeń aplikacji|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/akcja | Przyłączanie reguły zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft. Network/applicationSecurityGroups/odczyt                           | Pobierz grupę zabezpieczeń aplikacji                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Utwórz lub Zaktualizuj grupę zabezpieczeń aplikacji           |
| Microsoft. Network/applicationSecurityGroups/Delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Następne kroki

- Tworzenie grupy zabezpieczeń sieci lub aplikacji przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy](cli-samples.md) Azure lub szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i przypisywanie [definicji Azure Policy](./policy-reference.md) dla sieci wirtualnych
