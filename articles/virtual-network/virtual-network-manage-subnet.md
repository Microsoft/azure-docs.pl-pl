---
title: Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie znaleźć informacje o sieciach wirtualnych i sposobach dodawania, zmieniania lub usuwania podsieci sieci wirtualnej na platformie Azure.
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
ms.openlocfilehash: 54228ac0aa582d15509fbf967728364841e52453
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220579"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie i usuwanie podsieci sieci wirtualnej

Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej. Wszystkie zasoby platformy Azure wdrożone do sieci wirtualnej są wdrażane w podsieci w ramach sieci wirtualnej. Jeśli dopiero zaczynasz korzystanie z sieci wirtualnych, możesz dowiedzieć się więcej na ich temat w [sieci wirtualnej](virtual-networks-overview.md) lub przez ukończenie [przewodnika Szybki Start](quick-create-portal.md). Aby dowiedzieć się więcej o zarządzaniu siecią wirtualną, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w każdej sekcji tego artykułu: 

- **Użytkownicy portalu**: Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell**: Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub Uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell Browser Znajdź listę rozwijaną **Wybierz środowisko** , a następnie wybierz opcję **PowerShell** , jeśli nie została jeszcze wybrana.

    Jeśli używasz programu PowerShell lokalnie, użyj Azure PowerShell module w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom również `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI)**: uruchamiają polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiają interfejs CLI z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom również `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieci wirtualne. Wyszukaj i wybierz pozycję **sieci wirtualne**.

2. Wybierz nazwę sieci wirtualnej, do której chcesz dodać podsieć.

3. W obszarze **Ustawienia** wybierz **pozycję podsieci**  >  **podsieć**.

4. W oknie dialogowym **Dodawanie podsieci** wprowadź wartości następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Nazwa** | Nazwa musi być unikatowa w obrębie sieci wirtualnej. Aby zapewnić maksymalną zgodność z innymi usługami platformy Azure, zalecamy użycie litery jako pierwszego znaku nazwy. Na przykład platforma Azure Application Gateway nie zostanie wdrożona w podsieci o nazwie rozpoczynającej się od cyfry. |
    | **Zakres adresów** | <p>Zakres musi być unikatowy w obrębie przestrzeni adresowej sieci wirtualnej. Zakres nie może nakładać się na inne zakresy adresów podsieci w ramach sieci wirtualnej. Przestrzeń adresową należy określić przy użyciu notacji CIDR (Classless Inter-Domain Routing).</p><p>Na przykład w sieci wirtualnej z przestrzenią adresową *10.0.0.0/16* można zdefiniować przestrzeń adresową podsieci *10.0.0.0/22*. Najmniejszy zakres, który można określić, to */29*, który zapewnia osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci w celu zapewnienia zgodności z protokołem. Trzy dodatkowe adresy są zastrzeżone dla użycia usługi platformy Azure. W związku z tym Definiowanie podsieci z zakresem adresów */29* powoduje trzy użyteczne adresy IP w podsieci.</p><p>Jeśli planujesz połączenie sieci wirtualnej z bramą sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej [na temat konkretnych zagadnień dotyczących zakresów adresów dla podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Można zmienić zakres adresów po dodaniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zmiana ustawień podsieci](#change-subnet-settings).</p> |
    | **Sieciowa grupa zabezpieczeń** | Aby odfiltrować ruch sieciowy ruchu przychodzącego i wychodzącego dla podsieci, możesz skojarzyć istniejącą sieciową grupę zabezpieczeń z podsiecią. Grupa zabezpieczeń sieci musi znajdować się w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [grupach zabezpieczeń sieci](./network-security-groups-overview.md) i [sposobach tworzenia sieciowej grupy zabezpieczeń](tutorial-filter-network-traffic.md). |
    | **Tabela tras** | Aby kontrolować Routing ruchu sieciowego do innych sieci, można opcjonalnie skojarzyć istniejącą tabelę tras z podsiecią. Tabela tras musi znajdować się w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej na temat [routingu platformy Azure](virtual-networks-udr-overview.md) i [sposobu tworzenia tabeli tras](tutorial-create-route-table-portal.md). |
    | **Punkty końcowe usługi** | <p>Dla podsieci może być opcjonalnie włączony co najmniej jeden punkt końcowy usługi. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, dla których chcesz włączyć punkty końcowe usługi z listy **usług** . Platforma Azure automatycznie skonfiguruje lokalizację dla punktu końcowego. Domyślnie platforma Azure konfiguruje punkty końcowe usługi dla regionu sieci wirtualnej. Aby można było obsługiwać regionalne scenariusze trybu failover, platforma Azure automatycznie konfiguruje punkty końcowe w [sparowanych regionach platformy](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) Azure dla usługi Azure Storage.</p><p>Aby usunąć punkt końcowy usługi, usuń zaznaczenie usługi, dla której chcesz usunąć punkt końcowy usługi. Aby dowiedzieć się więcej na temat punktów końcowych usługi i usług, dla których można je włączyć, zobacz [punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md). Po włączeniu punktu końcowego usługi dla usługi należy również włączyć dostęp do sieci dla podsieci dla zasobu utworzonego za pomocą usługi. Jeśli na przykład zostanie włączony punkt końcowy usługi **Microsoft. Storage**, należy również włączyć dostęp do sieci do wszystkich kont usługi Azure Storage, do których chcesz udzielić dostępu do sieci. Aby włączyć dostęp do sieci w podsieciach, dla których włączono punkt końcowy usługi, zapoznaj się z dokumentacją dla poszczególnych usług, dla których włączono punkt końcowy usługi.</p><p>Aby sprawdzić, czy punkt końcowy usługi jest włączony dla podsieci, należy wyświetlić [obowiązujące trasy](diagnose-network-routing-problem.md) dla dowolnego interfejsu sieciowego w podsieci. Po skonfigurowaniu punktu końcowego zostanie wyświetlona trasa *Domyślna* z prefiksami adresów usługi oraz typem następnego przeskoku **VirtualNetworkServiceEndpoint**. Aby dowiedzieć się więcej na temat routingu, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Dla podsieci może być opcjonalnie włączono co najmniej jedno delegowanie. Delegowanie podsieci daje jawne uprawnienia do usługi w celu tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. Aby delegować do usługi, wybierz usługę, z której chcesz delegować listę **usług** . |

5. Aby dodać podsieć do wybranej sieci wirtualnej, wybierz **przycisk OK**.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Zmień ustawienia podsieci

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieci wirtualne. Wyszukaj i wybierz pozycję **sieci wirtualne**.

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz zmienić.

3. W obszarze **Ustawienia** wybierz pozycję **podsieci**.

4. Na liście podsieci wybierz podsieć, dla której chcesz zmienić ustawienia.

5. Na stronie podsieć zmień dowolne z następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Zakres adresów** | Jeśli w podsieci nie wdrożono żadnych zasobów, można zmienić zakres adresów. Jeśli jakieś zasoby istnieją w podsieci, należy przenieść zasoby do innej podsieci lub najpierw usunąć je z podsieci. Kroki wykonywane w celu przeniesienia lub usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak przenosić lub usuwać zasoby znajdujące się w podsieciach, zapoznaj się z dokumentacją dla każdego z tych typów zasobów. Zobacz ograniczenia dotyczące **zakresu adresów** w kroku 4 [Dodaj podsieć](#add-a-subnet). |
    | **Użytkownicy** | Dostęp do podsieci można kontrolować za pomocą wbudowanych ról lub własnych ról niestandardowych. Aby dowiedzieć się więcej na temat przypisywania ról i użytkowników w celu uzyskania dostępu do podsieci, zobacz [Dodawanie przypisania roli](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Sieciowa grupa zabezpieczeń** i **Tabela tras** | Zobacz Krok 4 [dodawania podsieci](#add-a-subnet). |
    | **Punkty końcowe usługi** | <p>Zobacz punkty końcowe usługi w kroku 4, [Dodaj podsieć](#add-a-subnet). Podczas włączania punktu końcowego usługi dla istniejącej podsieci upewnij się, że żadne krytyczne zadania nie są uruchomione w żadnym z zasobów podsieci. Punkty końcowe usługi przełączają trasy na każdy interfejs sieciowy w podsieci. Punkty końcowe usługi przechodzą z używania trasy domyślnej z prefiksem adresu *0.0.0.0/0* i typu następnego przeskoku w *Internecie*, aby używać nowej trasy z prefiksami adresów usługi i typem następnego przeskoku *VirtualNetworkServiceEndpoint*.</p><p>W trakcie przełączania można zakończyć wszystkie otwarte połączenia TCP. Punkt końcowy usługi nie jest włączony, dopóki nie zostaną zaktualizowane przepływy ruchu do usługi dla wszystkich interfejsów sieciowych przy użyciu nowej trasy. Aby dowiedzieć się więcej na temat routingu, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Zobacz punkty końcowe usługi w kroku 4, [Dodaj podsieć](#add-a-subnet). Delegowanie podsieci można zmodyfikować na zero lub dla niego włączono wiele delegowania. Jeśli zasób dla usługi został już wdrożony w podsieci, delegowanie podsieci nie może zostać dodane lub usunięte, dopóki nie zostaną usunięte wszystkie zasoby usługi. Aby delegować dla innej usługi, wybierz usługę, z którą chcesz delegować z listy **usług** . |

6. Wybierz pozycję **Zapisz**.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Podsieć można usunąć tylko wtedy, gdy nie ma żadnych zasobów w podsieci. Jeśli zasoby znajdują się w podsieci, przed usunięciem podsieci należy usunąć te zasoby. Kroki wykonywane w celu usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby znajdujące się w podsieciach, zapoznaj się z dokumentacją dla każdego z tych typów zasobów.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby wyświetlić sieci wirtualne. Wyszukaj i wybierz pozycję **sieci wirtualne**.

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz usunąć.

3. W obszarze **Ustawienia** wybierz pozycję **podsieci**.

4. Z listy podsieci wybierz podsieć, którą chcesz usunąć.

5. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** w oknie dialogowym potwierdzenia.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network VNET Subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania w podsieciach, Twoje konto musi być przypisane do [roli współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania w poniższej tabeli:

|Akcja                                                                   |   Nazwa                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Odczytaj podsieć sieci wirtualnej              |
|Microsoft. Network/virtualNetworks/podsieci/zapis                          |   Utwórz lub zaktualizuj podsieć sieci wirtualnej  |
|Microsoft. Network/virtualNetworks/podsieci/usuwanie                         |   Usuń podsieć sieci wirtualnej            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołącz do sieci wirtualnej                     |
|Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action  |   Włączanie punktu końcowego usługi dla podsieci     |
|Microsoft. Network/virtualNetworks/Subnets/virtualMachines/Read           |   Pobieranie maszyn wirtualnych w podsieci       |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej i podsieci przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i przypisywanie [definicji Azure Policy](./policy-reference.md) dla sieci wirtualnych