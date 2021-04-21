---
title: Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie można znaleźć informacje o sieciach wirtualnych oraz jak dodać, zmienić lub usunąć podsieć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 1e655b20d2f6295f0d6cfe8008fee7b360525611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774291"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie i usuwanie podsieci sieci wirtualnej

Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej. Wszystkie zasoby platformy Azure wdrożone do sieci wirtualnej są wdrażane w podsieci w ramach sieci wirtualnej. Jeśli nie masz jeszcze dostępu do sieci wirtualnych, możesz dowiedzieć się więcej na ich temat z tematu [Omówienie](virtual-networks-overview.md) sieci wirtualnej lub przewodnika [Szybki start.](quick-create-portal.md) Aby dowiedzieć się więcej na temat zarządzania siecią wirtualną, zobacz [Create, change, or delete a virtual network (Tworzenie, zmienianie lub usuwanie sieci wirtualnej).](manage-virtual-network.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w dowolnej sekcji tego artykułu: 

- **Użytkownicy portalu:** zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell:** uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell przeglądarki znajdź  listę rozwijaną Wybierz środowisko, a następnie wybierz program **PowerShell,** jeśli nie został jeszcze wybrany.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom również narzędzie `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure:** uruchamiaj polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiaj interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom również narzędzie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, na które się logujesz lub łączysz się [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) z platformą Azure, musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w te [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [tematu Permissions (Uprawnienia).](#permissions)

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. Przejdź do strony [Azure Portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz **pozycję Sieci wirtualne.**

2. Wybierz nazwę sieci wirtualnej, do której chcesz dodać podsieć.

3. W **menu Ustawienia** wybierz pozycję Podsieci **Podsieć.**  >  

4. W **oknie dialogowym** Dodawanie podsieci wprowadź wartości następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Nazwa** | Nazwa musi być unikatowa w obrębie sieci wirtualnej. Aby uzyskać maksymalną zgodność z innymi usługami platformy Azure, zalecamy użycie litery jako pierwszego znaku nazwy. Na przykład Azure Application Gateway nie będą wdrażane w podsieci o nazwie, która rozpoczyna się od liczby. |
    | **Zakres adresów** | <p>Zakres musi być unikatowy w obrębie przestrzeni adresowej sieci wirtualnej. Zakres nie może pokrywać się z innymi zakresami adresów podsieci w sieci wirtualnej. Przestrzeń adresowa musi być określona przy użyciu notacji Inter-Domain routingu (CIDR).</p><p>Na przykład w sieci wirtualnej z przestrzenią adresową *10.0.0.0/16* można zdefiniować przestrzeń adresową podsieci *10.0.0.0/22.* Najmniejszy zakres, który można określić, to */29,* który zapewnia osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci w celu zgodności z protokołem. Trzy dodatkowe adresy są zarezerwowane do użycia usługi platformy Azure. W związku z tym zdefiniowanie podsieci z zakresem *adresów /29* powoduje, że w podsieci będą używane trzy adresy IP.</p><p>Jeśli planujesz połączenie sieci wirtualnej z bramą sieci VPN, musisz utworzyć podsieć bramy. Dowiedz się więcej o konkretnych zagadnieniach dotyczących zakresu [adresów dla podsieci bramy.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) Zakres adresów można zmienić po dodaniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zmienianie ustawień podsieci](#change-subnet-settings).</p> |
    | **Sieciowa grupa zabezpieczeń** | Aby filtrować przychodzący i wychodzący ruch sieciowy dla podsieci, możesz skojarzyć istniejącą sieciową grupę zabezpieczeń z podsiecią. Sieciowa grupa zabezpieczeń musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej na [temat sieciowych grup zabezpieczeń](./network-security-groups-overview.md) i sposobu tworzenia [sieciowej grupy zabezpieczeń.](tutorial-filter-network-traffic.md) |
    | **Tabela tras** | Aby kontrolować routing ruchu sieciowego do innych sieci, możesz opcjonalnie skojarzyć istniejącą tabelę tras z podsiecią. Tabela tras musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej na [temat routingu na platformie Azure](virtual-networks-udr-overview.md) [i sposobu tworzenia tabeli tras.](tutorial-create-route-table-portal.md) |
    | **Punkty końcowe usługi** | <p>Podsieć może opcjonalnie mieć włączony co najmniej jeden punkt końcowy usługi. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, dla których chcesz włączyć punkty końcowe usługi, z **listy Usługi.** Platforma Azure automatycznie konfiguruje lokalizację punktu końcowego. Domyślnie platforma Azure konfiguruje punkty końcowe usługi dla regionu sieci wirtualnej. Aby obsługiwać regionalne scenariusze trybu failover, platforma Azure automatycznie konfiguruje punkty końcowe w sparowanych [regionach platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) dla usługi Azure Storage.</p><p>Aby usunąć punkt końcowy usługi, usuń zaznaczenie usługi, dla której chcesz usunąć punkt końcowy usługi. Aby dowiedzieć się więcej o punktach końcowych usługi i usługach, dla których można je włączyć, zobacz Virtual network service endpoints (Punkty [końcowe usługi dla sieci wirtualnej).](virtual-network-service-endpoints-overview.md) Po włączeniu punktu końcowego usługi dla usługi należy również włączyć dostęp sieciowy dla podsieci dla zasobu utworzonego za pomocą usługi. Jeśli na przykład włączysz punkt końcowy usługi **Microsoft.Storage,** musisz również włączyć dostęp sieciowy do wszystkich kont usługi Azure Storage, do których chcesz udzielić dostępu sieciowego. Aby włączyć dostęp sieciowy do podsieci, dla których włączono punkt końcowy usługi, zapoznaj się z dokumentacją dla poszczególnych usług, dla których włączono punkt końcowy usługi.</p><p>Aby sprawdzić, czy punkt końcowy usługi jest włączony dla podsieci, wyświetl efektywne [trasy](diagnose-network-routing-problem.md) dla dowolnego interfejsu sieciowego w podsieci. Podczas konfigurowania punktu końcowego  zobaczysz domyślną trasę z prefiksami adresów usługi i typem następnego przeskoku **VirtualNetworkServiceEndpoint**. Aby dowiedzieć się więcej na temat routingu, zobacz [Routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Podsieć może opcjonalnie mieć włączone co najmniej jedno delegowanie. Delegowanie podsieci daje usłudze jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. Aby delegować dla usługi, wybierz usługę, do której chcesz delegować, z **listy** Usługi. |

5. Aby dodać podsieć do wybranej sieci wirtualnej, wybierz przycisk **OK.**

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Zmienianie ustawień podsieci

1. Przejdź do strony [Azure Portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz pozycję **Sieci wirtualne.**

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz zmienić.

3. W **menu Ustawienia** wybierz pozycję **Podsieci.**

4. Na liście podsieci wybierz podsieć, dla której chcesz zmienić ustawienia.

5. Na stronie podsieci zmień dowolne z następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Zakres adresów** | Jeśli w podsieci nie wdrożono żadnych zasobów, można zmienić zakres adresów. Jeśli w podsieci istnieją jakiekolwiek zasoby, musisz najpierw przenieść zasoby do innej podsieci lub usunąć je z tej podsieci. Kroki przenoszenia lub usuwania zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak przenieść lub usunąć zasoby, które znajdują się w podsieciach, zapoznaj się z dokumentacją każdego z tych typów zasobów. Zobacz ograniczenia dotyczące zakresu adresów **w** kroku 4 [dodawania podsieci](#add-a-subnet). |
    | **Użytkownicy** | Dostęp do podsieci można kontrolować przy użyciu ról wbudowanych lub własnych ról niestandardowych. Aby dowiedzieć się więcej na temat przypisywania ról i użytkowników w celu uzyskania dostępu do podsieci, zobacz [Przypisywanie ról platformy Azure.](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
    | **Sieciowa grupa zabezpieczeń** i **Tabela tras** | Zobacz krok 4 [dodawania podsieci](#add-a-subnet). |
    | **Punkty końcowe usługi** | <p>Zobacz punkty końcowe usługi w kroku 4 [dodawania podsieci](#add-a-subnet). Podczas włączania punktu końcowego usługi dla istniejącej podsieci upewnij się, że żadne krytyczne zadania nie są uruchomione na żadnym zasobie w podsieci. Punkty końcowe usługi przełączą trasy na każdym interfejsie sieciowym w podsieci. Punkty końcowe usługi nie będą używać trasy domyślnej z prefiksem adresu *0.0.0.0/0* i typu następnego przeskoku *Internetu*, do używania nowej trasy z prefiksami adresów usługi i typu następnego przeskoku *VirtualNetworkServiceEndpoint*.</p><p>Podczas przełączania wszystkie otwarte połączenia TCP mogą zostać zakończone. Punkt końcowy usługi nie zostanie włączony, dopóki ruch do usługi dla wszystkich interfejsów sieciowych nie zostanie zaktualizowany przy użyciu nowej trasy. Aby dowiedzieć się więcej na temat routingu, zobacz [Routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Zobacz punkty końcowe usługi w kroku 4 [dodawania podsieci](#add-a-subnet). Delegowanie podsieci można zmodyfikować do zera lub włączyć dla niego wiele delegowania. Jeśli zasób dla usługi został już wdrożony w podsieci, delegowania podsieci nie można dodać ani usunąć, dopóki nie zostaną usunięte wszystkie zasoby dla usługi. Aby delegować dla innej usługi, wybierz usługę, do której chcesz delegować, z **listy Usługi.** |

6. Wybierz pozycję **Zapisz**.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Podsieć można usunąć tylko wtedy, gdy w tej podsieci nie ma żadnych zasobów. Jeśli zasoby znajdują się w podsieci, musisz usunąć te zasoby, zanim będzie można usunąć podsieć. Kroki usuwania zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które znajdują się w podsieciach, zapoznaj się z dokumentacją każdego z tych typów zasobów.

1. Przejdź do strony [Azure Portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz **pozycję Sieci wirtualne.**

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz usunąć.

3. W **menu Ustawienia** wybierz pozycję **Podsieci.**

4. Na liście podsieci wybierz podsieć, którą chcesz usunąć.

5. Wybierz **pozycję Usuń,** a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w podsieciach, twoje konto [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) musi być przypisane [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do roli Współautor sieci lub Roli niestandardowej, która ma przypisane odpowiednie akcje w poniższej tabeli:

|Akcja                                                                   |   Nazwa                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Odczytywanie podsieci sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Tworzenie lub aktualizowanie podsieci sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Usuwanie podsieci sieci wirtualnej            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołączanie do sieci wirtualnej                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Włączanie punktu końcowego usługi dla podsieci     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Uzyskiwanie maszyn wirtualnych w podsieci       |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej i podsieci przy użyciu [przykładowych skryptów programu PowerShell](powershell-samples.md) lub interfejsu wiersza polecenia platformy [Azure](cli-samples.md) albo szablonów usługi Azure [Resource Manager](template-samples.md)
- Tworzenie i [przypisywanie Azure Policy sieci](./policy-reference.md) wirtualnych