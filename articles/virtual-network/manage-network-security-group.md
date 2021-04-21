---
title: Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie można znaleźć informacje o zasadach zabezpieczeń oraz tworzyć, zmieniać lub usuwać sieciową grupę zabezpieczeń.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783507"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń

Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci i interfejsów sieciowych sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń.](./network-security-groups-overview.md) Następnie wykonaj kroki [samouczka Filtrowanie ruchu sieciowego,](tutorial-filter-network-traffic.md) aby uzyskać pewne doświadczenie w pracy z sieciowymi grupami zabezpieczeń.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Przed rozpoczęciem dalszej części tego artykułu wykonaj jedno z tych zadań:

- **Użytkownicy portalu:** zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell:** uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell przeglądarki znajdź  listę rozwijaną Wybierz środowisko, a następnie wybierz program **PowerShell,** jeśli nie został jeszcze wybrany.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure:** uruchamiaj polecenia w Azure Cloud Shell [lub](https://shell.azure.com/bash)uruchamiaj interfejs wiersza polecenia na komputerze. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

Konto, na które się logujesz lub łączysz [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) się z platformą [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure, musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w [tece Uprawnienia.](#permissions)

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można tworzyć, [wyświetlać wszystkie,](#view-all-network-security-groups) [wyświetlać szczegóły](#view-details-of-a-network-security-group), [zmieniać](#change-a-network-security-group)i [usuwać](#delete-a-network-security-group) sieciową grupę zabezpieczeń. Można również [skojarzyć lub skojarzyć](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) sieciową grupę zabezpieczeń z interfejsu sieciowego lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje limit liczby sieciowych grup zabezpieczeń, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)usług i subskrypcji platformy Azure).

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz **pozycję Sieć,** a następnie **wybierz pozycję Sieciowa grupa zabezpieczeń.**

3. Na stronie **Tworzenie sieciowej grupy zabezpieczeń** na karcie **Podstawowe** ustaw wartości dla następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz lokalizację. |

4. Wybierz pozycję **Przejrzyj i utwórz**.

5. Po wyświetleniu komunikatu **Weryfikacja pomyślnie wybierz** pozycję **Utwórz**.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Wyświetl wszystko sieciowych grup zabezpieczeń

Przejdź do [strony Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.** Zostanie wyświetlona lista sieciowych grup zabezpieczeń dla subskrypcji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Wyświetlanie szczegółów sieciowej grupy zabezpieczeń

1. Przejdź do [strony Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń.

Na pasku menu sieciowej grupy zabezpieczeń w obszarze Ustawienia można wyświetlić reguły zabezpieczeń dla ruchu przychodzącego, reguły zabezpieczeń ruchu wychodzącego, interfejsy sieciowe i podsieci, z które jest skojarzona sieciowa grupa zabezpieczeń. 

W **obszarze** Monitorowanie możesz włączyć lub wyłączyć **ustawienia diagnostyczne.** W **obszarze Pomoc techniczna i rozwiązywanie problemów** można wyświetlić obowiązujące reguły **zabezpieczeń.** Aby dowiedzieć się więcej, zobacz Rejestrowanie diagnostyczne dla [sieciowej grupy](virtual-network-nsg-manage-log.md) zabezpieczeń i Diagnozowanie problemu z filtrowaniem [ruchu sieciowego maszyny wirtualnej.](diagnose-network-traffic-filter-problem.md)

Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure wymienionych na liście, zobacz następujące artykuły:

- [Dziennik aktywności](../azure-monitor/essentials/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Zmienianie sieciowej grupy zabezpieczeń

1. Przejdź do [strony Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz zmienić.

Najbardziej typowe zmiany to dodanie reguły [](#delete-a-security-rule) [zabezpieczeń,](#create-a-security-rule)usunięcie reguły oraz skojarzenie lub usunięcie skojarzenia sieciowej grupy zabezpieczeń z podsiecią lub [interfejsem sieciowym.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym lub ich skojarzenie z podsiecią lub interfejsem sieciowym

Aby skojarzyć sieciową grupę zabezpieczeń z interfejsem sieciowym lub ją skojarzyć z interfejsem sieciowym, zobacz Kojarzenie sieciowej grupy zabezpieczeń z sieciową grupą zabezpieczeń lub jej skojarzenie z interfejsem [sieciowym.](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) Aby skojarzyć sieciową grupę zabezpieczeń z podsiecią lub ją skojarzyć z podsiecią, zobacz [Zmienianie ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuwanie sieciowej grupy zabezpieczeń

Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsieciami lub interfejsami sieciowym, nie można jej usunąć. Usuń skojarzenie sieciowej grupy zabezpieczeń ze wszystkimi podsieciami i interfejsami sieciowym przed podjęciem próby jej usunięcia.

1. Przejdź do [strony Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz usunąć.

3. Na pasku narzędzi sieciowej grupy zabezpieczeń wybierz pozycję **Usuń.** Następnie wybierz **pozycję Tak** w oknie dialogowym potwierdzenia.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Praca z regułami zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub więcej reguł zabezpieczeń. Można tworzyć, [wyświetlać wszystkie,](#view-all-security-rules) [wyświetlać szczegóły](#view-details-of-a-security-rule)reguł zabezpieczeń, [zmieniać](#change-a-security-rule) [je](#delete-a-security-rule) i usuwać.

### <a name="create-a-security-rule"></a>Tworzenie reguły zabezpieczeń

Istnieje ograniczenie liczby reguł na sieciową grupę zabezpieczeń, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)usług i subskrypcji platformy Azure).

1. Przejdź do [strony Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń, do której chcesz dodać regułę zabezpieczeń.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń** dla ruchu przychodzącego lub Reguły zabezpieczeń dla **ruchu wychodzącego.**

    Zostanie wymienionych kilka istniejących reguł, w tym niektóre, które nie zostały dodane. Podczas tworzenia sieciowej grupy zabezpieczeń jest tworzona kilka domyślnych reguł zabezpieczeń. Aby dowiedzieć się więcej, zobacz [domyślne reguły zabezpieczeń.](./network-security-groups-overview.md#default-security-rules)  Nie można usunąć domyślnych reguł zabezpieczeń, ale można je zastąpić regułami o wyższym priorytecie.

4. <a name="security-rule-settings"></a>Wybierz **pozycję Dodaj**. Wybierz lub dodaj wartości dla następujących ustawień, a następnie wybierz przycisk **OK:**

    | Ustawienie | Wartość | Szczegóły |
    | ------- | ----- | ------- |
    | **Element źródłowy** | Jeden z nich:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń dla ruchu przychodzącego) lub **VirtualNetwork** (reguła zabezpieczeń ruchu wychodzącego)</li><li>**Grupa &nbsp; zabezpieczeń &nbsp; aplikacji**</li></ul> | <p>W przypadku wybrania **opcji Adresy IP** należy również określić źródłowe adresy **IP/zakresy CIDR.**</p><p>W przypadku wybrania **tagu usługi** możesz również wybrać tag Usługi **źródłowej**.</p><p>W przypadku **wybrania grupy zabezpieczeń aplikacji** należy również wybrać istniejącą grupę zabezpieczeń aplikacji. W przypadku wybrania grupy  zabezpieczeń  **aplikacji** dla opcji Źródło i Miejsce docelowe interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą znajdować się w tej samej sieci wirtualnej.</p> |
    | **Źródłowe adresy IP/zakresy CIDR** | Rozdzielana przecinkami lista adresów IP i zakresów routingu międzydomenowego bez klas (CIDR, Classless Interdomain Routing) | <p>To ustawienie jest wyświetlane, jeśli zmienisz **ustawienie Źródło** na **Adresy IP.** Należy określić pojedynczą wartość lub rozdzielaną przecinkami listę wielu wartości. Przykładem wielu wartości jest `10.0.0.0/16, 192.188.1.1` . Istnieją limity liczby wartości, które można określić. Aby uzyskać więcej informacji, zobacz [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, określ jej prywatny adres IP, a nie publiczny adres IP. Platforma Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, ale zanim przetłumaczy prywatny adres IP na publiczny adres IP dla reguł ruchu wychodzącego. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, [zobacz Typy adresów IP](./public-ip-addresses.md).</p> |
    | **Tag usługi źródłowej** | Tag usługi z listy rozwijanej | To opcjonalne ustawienie jest wyświetlane, jeśli dla ustawienia **Źródło** ustawiono **tag usługi** dla reguły zabezpieczeń dla ruchu przychodzącego. Tag usługi to wstępnie zdefiniowany identyfikator kategorii adresów IP. Aby dowiedzieć się więcej na temat dostępnych tagów usługi i tego, co reprezentuje każdy tag, zobacz [Tagi usługi](./network-security-groups-overview.md#service-tags). |
    | **Źródłową grupę zabezpieczeń aplikacji** | Istniejąca grupa zabezpieczeń aplikacji | To ustawienie jest wyświetlane, jeśli źródło **ma ustawioną** **grupę zabezpieczeń Aplikacja.** Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, [jak utworzyć grupę zabezpieczeń aplikacji.](#create-an-application-security-group) |
    | **Zakresy portów źródłowych** | Jeden z nich:<ul><li>Pojedynczy port, taki jak `80`</li><li>Zakres portów, na przykład `1024-65535`</li><li>Rozdzielana przecinkami lista pojedynczych portów i/lub zakresów portów, na przykład `80, 1024-65535`</li><li>Gwiazdka ( `*` ), aby zezwolić na ruch na dowolnym porcie</li></ul> | To ustawienie określa porty, na których reguła zezwala lub nie zezwala na ruch. Istnieją ograniczenia liczby portów, które można określić. Aby uzyskać więcej informacji, zobacz [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Element docelowy** | Jeden z nich:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń dla ruchu wychodzącego) lub **VirtualNetwork** (reguła zabezpieczeń dla ruchu przychodzącego)</li><li>**Grupa &nbsp; zabezpieczeń &nbsp; aplikacji**</li></ul> | <p>W przypadku wybrania **adresów IP** określ również docelowe **adresy IP/zakresy CIDR.**</p><p>W przypadku wybrania **opcji VirtualNetwork** dozwolony jest ruch do wszystkich adresów IP w przestrzeni adresowej sieci wirtualnej. **VirtualNetwork** to tag usługi.</p><p>W przypadku wybrania **grupy zabezpieczeń aplikacji** należy wybrać istniejącą grupę zabezpieczeń aplikacji. Dowiedz się, [jak utworzyć grupę zabezpieczeń aplikacji.](#create-an-application-security-group)</p> |
    | **Docelowe adresy IP/zakresy CIDR** | Rozdzielana przecinkami lista adresów IP i zakresów CIDR | <p>To ustawienie jest wyświetlane, jeśli zmienisz **ustawienie Miejsce** docelowe na **Adresy IP.** Podobnie jak **w przypadku źródłowych** i źródłowych **adresów IP/zakresów CIDR,** można określić jeden lub wiele adresów bądź zakresów. Istnieją limity liczby, które można określić. Aby uzyskać więcej informacji, zobacz [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że określono jej prywatny adres IP, a nie publiczny adres IP. Platforma Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, ale zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP dla reguł ruchu wychodzącego. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, [zobacz Typy adresów IP](./public-ip-addresses.md).</p> |
    | **Docelowy tag usługi** | Tag usługi z listy rozwijanej | To opcjonalne ustawienie jest wyświetlane w przypadku zmiany **ustawienia Miejsce** docelowe na **Tag usługi** dla reguły zabezpieczeń dla ruchu wychodzącego. Tag usługi jest wstępnie zdefiniowanym identyfikatorem kategorii adresów IP. Aby dowiedzieć się więcej na temat dostępnych tagów usługi i tego, co reprezentuje każdy tag, zobacz [Tagi usługi](./network-security-groups-overview.md#service-tags). |
    | **Docelowa grupa zabezpieczeń aplikacji** | Istniejąca grupa zabezpieczeń aplikacji | To ustawienie jest wyświetlane, jeśli dla **ustawienia Miejsce** docelowe **ustawiono wartość Grupa zabezpieczeń aplikacji.** Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, [jak utworzyć grupę zabezpieczeń aplikacji.](#create-an-application-security-group) |
    | **Zakresy portów docelowych** | Jeden z nich:<ul><li>Pojedynczy port, taki jak `80`</li><li>Zakres portów, na przykład `1024-65535`</li><li>Rozdzielana przecinkami lista pojedynczych portów i/lub zakresów portów, na przykład `80, 1024-65535`</li><li>Gwiazdka ( `*` ), aby zezwolić na ruch na dowolnym porcie</li></ul> | Podobnie jak **w przypadku zakresów portów źródłowych,** można określić jeden lub wiele portów i zakresów. Istnieją limity liczby, które można określić. Aby uzyskać więcej informacji, zobacz [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protokół** | **Dowolny,** **TCP,** **UDP** **lub ICMP** | Regułę można ograniczyć do protokołu Transmission Control Protocol (TCP), UDP (User Datagram Protocol) lub ICMP (Internet Control Message Protocol). Ustawieniem domyślnym reguły jest stosowanie do wszystkich protokołów. |
    | **Akcja** | **Zezwalaj** lub **Odmów** | To ustawienie określa, czy ta reguła zezwala na dostęp do podanej konfiguracji źródłowej i docelowej, czy nie. |
    | **Priority** | Wartość z zakresów od 100 do 4096, która jest unikatowa dla wszystkich reguł zabezpieczeń w sieciowej grupie zabezpieczeń | Platforma Azure przetwarza reguły zabezpieczeń w kolejności priorytetów. Im mniejsza liczba, tym wyższy priorytet. Zalecamy pozostawienie luki między numerami priorytetów podczas tworzenia reguł, takich jak 100, 200 i 300. Pozostawienie luk ułatwia dodawanie reguł w przyszłości, dzięki czemu można nadać im wyższy lub niższy priorytet niż istniejące reguły. |
    | **Nazwa** | Unikatowa nazwa reguły w sieciowej grupie zabezpieczeń | Nazwa może mieć maksymalnie 80 znaków. Musi zaczynać się literą lub liczbą i musi kończyć się literą, liczbą lub podkreśleniem. Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. |
    | **Opis** | Opis tekstowy | Opcjonalnie możesz określić opis tekstowy reguły zabezpieczeń. Opis nie może być dłuższy niż 140 znaków. |

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Wyświetl wszystko zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub więcej reguł. Aby dowiedzieć się więcej na temat informacji wymienionych podczas wyświetlania reguł, zobacz [Omówienie sieciowych grup zabezpieczeń.](./network-security-groups-overview.md)

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń** dla ruchu przychodzącego lub Reguły zabezpieczeń dla **ruchu wychodzącego.**

Lista zawiera wszystkie utworzone reguły i domyślne reguły zabezpieczeń [sieciowej grupy zabezpieczeń.](./network-security-groups-overview.md#default-security-rules)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz pozycję **Sieciowe grupy zabezpieczeń.**

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić szczegóły reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń** dla ruchu przychodzącego lub Reguły zabezpieczeń dla **ruchu wychodzącego.**

4. Wybierz regułę, dla której chcesz wyświetlić szczegóły. Aby uzyskać objaśnienie wszystkich ustawień, zobacz [Ustawienia reguły zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. To nie zadziała, jeśli wybierzesz domyślną regułę zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Zmienianie reguły zabezpieczeń

1. Wykonaj kroki opisane w [te tematu View details of a security rule (Wyświetlanie szczegółów reguły zabezpieczeń).](#view-details-of-a-security-rule)

2. Zmień ustawienia zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz.** Aby uzyskać objaśnienie wszystkich ustawień, zobacz [Ustawienia reguły zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. Nie możesz zmienić domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Usuwanie reguły zabezpieczeń

1. Wykonaj kroki opisane w [te tematu View details of a security rule (Wyświetlanie szczegółów reguły zabezpieczeń).](#view-details-of-a-security-rule)

2. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**.

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. Nie możesz usunąć domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji zawiera zero lub więcej interfejsów sieciowych. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji.](./network-security-groups-overview.md#application-security-groups) Wszystkie interfejsy sieciowe w grupie zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Aby dowiedzieć się, jak dodać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz Dodawanie interfejsu sieciowego [do grupy zabezpieczeń aplikacji.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź *wartość Grupa zabezpieczeń aplikacji.*

3. Na stronie **Grupa zabezpieczeń aplikacji** wybierz pozycję **Utwórz**.

4. Na stronie **Tworzenie grupy zabezpieczeń aplikacji** na karcie **Podstawowe** ustaw wartości następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz lokalizację. |

5. Wybierz pozycję **Przejrzyj i utwórz**.

6. Na karcie **Przeglądanie + tworzenie** po wyświetleniu komunikatu **Weryfikacja** przekazana wybierz pozycję **Utwórz**.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Wyświetl wszystko zabezpieczeń aplikacji

Przejdź do strony [Azure Portal,](https://portal.azure.com) aby wyświetlić grupy zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji.** W Azure Portal zostanie wyświetlona lista grup zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetlanie szczegółów określonej grupy zabezpieczeń aplikacji

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji.**

2. Wybierz nazwę grupy zabezpieczeń aplikacji, dla której chcesz wyświetlić szczegóły.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Zmienianie grupy zabezpieczeń aplikacji

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji.**

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz zmienić.

3. Wybierz **pozycję** Zmień obok ustawienia, które chcesz zmodyfikować. Możesz na przykład dodać lub usunąć pozycję **Tagi** albo zmienić grupę zasobów **lub** **subskrypcję**.

    > [!NOTE]
    > Nie można zmienić lokalizacji.

    Na pasku menu możesz również wybrać pozycję **Kontrola dostępu (IAM)**. Na stronie **Kontrola dostępu (IAM)** możesz przypisać lub usunąć uprawnienia do grupy zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Brak polecenia cmdlet programu PowerShell |

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli zawiera ona jakiekolwiek interfejsy sieciowe. Aby usunąć wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji, zmień ustawienia interfejsu sieciowego lub usuń interfejsy sieciowe. Aby dowiedzieć się więcej, zobacz Dodawanie do [lub usuwanie z grup zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub Usuwanie [interfejsu sieciowego.](virtual-network-network-interface.md#delete-a-network-interface)

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać grupami zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji.**

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz usunąć.

3. Wybierz **pozycję Usuń,** a następnie wybierz pozycję **Tak,** aby usunąć grupę zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania dotyczące sieciowych grup zabezpieczeń, reguł zabezpieczeń i grup [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) zabezpieczeń aplikacji, Twoje [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konto musi być przypisane do roli Współautor sieci lub roli niestandardowej, która ma przypisane odpowiednie uprawnienia wymienione w poniższych tabelach:

### <a name="network-security-group"></a>Sieciowa grupa zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Uzyskiwanie sieciowej grupy zabezpieczeń                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Tworzenie lub aktualizowanie sieciowej grupy zabezpieczeń                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Usuwanie sieciowej grupy zabezpieczeń                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Kojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym 


>[!NOTE]
> Aby wykonać operacje na sieciowej grupie zabezpieczeń, konto subskrypcji musi mieć co najmniej uprawnienia do grupy zasobów `write` `read` wraz z `Microsoft.Network/networkSecurityGroups/write` uprawnieniami.


### <a name="network-security-group-rule"></a>Reguła sieciowej grupy zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Pobierz regułę                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Tworzenie lub aktualizowanie reguły                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Usuwanie reguły                                                         |

### <a name="application-security-group"></a>Grupa zabezpieczeń aplikacji

| Akcja                                                                     | Nazwa                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Dołączanie konfiguracji adresu IP do grupy zabezpieczeń aplikacji|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołączanie reguły zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft.Network/applicationSecurityGroups/read                           | Uzyskiwanie grupy zabezpieczeń aplikacji                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Tworzenie lub aktualizowanie grupy zabezpieczeń aplikacji           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci lub grupy zabezpieczeń aplikacji przy użyciu [przykładowych skryptów programu PowerShell](powershell-samples.md) lub interfejsu wiersza polecenia platformy [Azure](cli-samples.md) albo szablonów usługi Azure [Resource Manager azure](template-samples.md)
- Tworzenie i [przypisywanie Azure Policy sieci](./policy-reference.md) wirtualnych
