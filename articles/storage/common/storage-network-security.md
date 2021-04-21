---
title: Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage | Microsoft Docs
description: Skonfiguruj warstwowe zabezpieczenia sieciowe dla konta magazynu przy użyciu zapór usługi Azure Storage i usługi Azure Virtual Network.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5e8123c252d99b2999eeef42fecae189a05e382b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778125"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage

Usługa Azure Storage udostępnia warstwowy model zabezpieczeń. Ten model umożliwia zabezpieczanie i kontrolowanie poziomu dostępu do kont magazynu, których wymagają aplikacje i środowiska przedsiębiorstwa, na podstawie typu i podzestawu używanych sieci lub zasobów. Po skonfigurowaniu reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci lub za pośrednictwem określonego zestawu zasobów platformy Azure mogą uzyskać dostęp do konta magazynu. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP, podsieci w usłudze Azure Virtual Network (VNet) lub wystąpień zasobów niektórych usług platformy Azure.

Konta magazynu mają publiczny punkt końcowy, który jest dostępny za pośrednictwem Internetu. Możesz również utworzyć prywatne punkty końcowe dla konta [magazynu,](storage-private-endpoints.md)które przypisze prywatny adres IP z sieci wirtualnej do konta magazynu i zabezpieczy cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem łącza prywatnego. Zapora usługi Azure Storage zapewnia kontrolę dostępu do publicznego punktu końcowego konta magazynu. Możesz również użyć zapory, aby zablokować cały dostęp za pośrednictwem publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych. Konfiguracja zapory magazynu umożliwia również wybieranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy obowiązują reguły sieciowe, nadal wymaga odpowiedniej autoryzacji dla żądania. Autoryzacja jest obsługiwana Azure Active Directory (Azure AD) dla obiektów blob i kolejek, przy użyciu prawidłowego klucza dostępu do konta lub tokenu SAS.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w ramach usługi Azure Virtual Network (VNet) lub z dozwolonych publicznych adresów IP. Żądania, które są blokowane, obejmują te z innych usług platformy Azure, z Azure Portal, z usług rejestrowania i metryk i tak dalej.
>
> Możesz udzielić dostępu do usług platformy Azure, które działają z poziomu sieci wirtualnej, zezwalając na ruch z podsieci hostowania wystąpienia usługi. Można również włączyć ograniczoną liczbę scenariuszy za pomocą opisanego poniżej mechanizmu wyjątków. Aby uzyskać dostęp do danych z konta magazynu za pośrednictwem usługi Azure Portal, musisz być na maszynie w ramach ustawionej zaufanej granicy (adresu IP lub sieci wirtualnej).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć konto magazynu, należy najpierw skonfigurować regułę, aby domyślnie odmawiać dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) w publicznym punkcie końcowym. Następnie należy skonfigurować reguły, które przyznają dostęp do ruchu z określonych sieci wirtualnych. Można również skonfigurować reguły udzielania dostępu do ruchu z wybranych zakresów publicznych adresów IP w Internecie, umożliwiając połączenia z określonego Internetu lub klientów lokalnych. Ta konfiguracja umożliwia tworzenie bezpiecznych granic sieci dla aplikacji.

Można połączyć reguły zapory, które zezwalają na dostęp z określonych sieci wirtualnych i z zakresów publicznych adresów IP na tym samym koncie magazynu. Reguły zapory magazynu można stosować do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Reguły zapory magazynu mają zastosowanie do publicznego punktu końcowego konta magazynu. Nie potrzebujesz żadnych reguł dostępu zapory, aby zezwolić na ruch dla prywatnych punktów końcowych konta magazynu. Proces zatwierdzania tworzenia prywatnego punktu końcowego zapewnia niejawny dostęp do ruchu z podsieci, która hostuje prywatny punkt końcowy.

Reguły sieci są wymuszane we wszystkich protokołach sieciowych dla usługi Azure Storage, w tym REST i SMB. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak Azure Portal, Eksplorator usługi Storage i AZCopy, należy skonfigurować jawne reguły sieciowe.

Po zastosowaniu reguł sieci są one wymuszane dla wszystkich żądań. Tokeny SAS, które przyznają dostęp do określonego adresu IP, ograniczają dostęp właściciela tokenu, ale nie przyznają nowego dostępu poza skonfigurowanymi regułami sieci.

Reguły sieci nie wpływają na ruch dyskowy maszyny wirtualnej (w tym operacje instalacji i odinstalowania oraz operacje we/wy dysku). Dostęp REST do stronicowych obiektów blob jest chroniony przez reguły sieciowe.

Klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

Na kontach magazynu można używać dysków niezaładowych z zastosowanymi regułami sieci w celu tworzenia kopii zapasowej i przywracania maszyn wirtualnych, tworząc wyjątek. Ten proces jest udokumentowany w sekcji [Zarządzanie wyjątkami](#manage-exceptions) w tym artykule. Wyjątki zapory nie mają zastosowania do dysków zarządzanych, ponieważ są już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie konta magazynu akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Zmiany reguł sieci mogą mieć wpływ na możliwość łączenia się aplikacji z usługą Azure Storage. Ustawienie domyślnej reguły sieciowej odmawiania **blokuje** cały dostęp  do danych, chyba że są również stosowane określone reguły sieciowe, które przyznają dostęp. Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Domyślnymi regułami dostępu do sieci dla kont magazynu można zarządzać za pośrednictwem Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz pozycję w menu ustawień o nazwie **Sieć.**

3. Aby domyślnie odmówić dostępu, wybierz opcję zezwalania na dostęp z **wybranych sieci.** Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

4. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

<a id="powershell"></a>

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj aplikację [Azure PowerShell](/powershell/azure/install-Az-ps) [i zaloguj się.](/powershell/azure/authenticate-azureps)

2. Wyświetla stan reguły domyślnej dla konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Ustaw domyślną regułę, aby domyślnie odmawiać dostępu do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj [się.](/cli/azure/authenticate-azure-cli)

2. Wyświetla stan reguły domyślnej dla konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Ustaw domyślną regułę, aby domyślnie odmawiać dostępu do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Ustaw domyślną regułę, aby domyślnie zezwalać na dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub do tych w innej subskrypcji, w tym do subskrypcji należących do innej Azure Active Directory dzierżawy.

Włączanie punktu [końcowego usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Storage w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Storage. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla konta magazynu, które zezwalają na żądania odbierane z określonych podsieci w sieci wirtualnej. Klienci, którzy mają dostęp za pośrednictwem tych reguł sieciowych, muszą nadal spełniać wymagania autoryzacji konta magazynu w celu uzyskania dostępu do danych.

Każde konto magazynu obsługuje maksymalnie 200 reguł sieci wirtualnej, które mogą być łączone z [regułami sieci IP.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Dostępne regiony sieci wirtualnej

Ogólnie rzecz biorąc, punkty końcowe usługi działają między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie świadczenia usługi Azure. W przypadku korzystania z punktów końcowych usługi w usłudze Azure Storage ten zakres zwiększa się tak, aby obejmował [sparowany region](../../best-practices-availability-paired-regions.md). Punkty końcowe usługi umożliwiają ciągłość działania podczas regionalnego trybu failover i dostęp do wystąpień magazynu geograficznie nadmiarowego tylko do odczytu (RA-GRS). Reguły sieci, które przyznają dostęp z sieci wirtualnej do konta magazynu, również przyznają dostęp do dowolnego wystąpienia magazynu RA-GRS.

Podczas planowania odzyskiwania po awarii podczas regionalnej awarii należy wcześniej utworzyć sieci wirtualne w sparowanych regionach. Włącz punkty końcowe usługi dla usługi Azure Storage, przy użyciu reguł sieciowych, które przyznają dostęp z tych alternatywnych sieci wirtualnych. Następnie zastosuj te reguły do kont magazynu geograficznie nadmiarowego.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza regionem sieci wirtualnej i pary wyznaczonych regionów. Reguły sieci, które przyznają dostęp z sieci wirtualnych, można stosować tylko do kont magazynu w regionie podstawowym konta magazynu lub w wyznaczonym sparowanych regionach.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia do dodawanych podsieci. Zastosowanie reguły może zostać wykonane przez [współautora](../../role-based-access-control/built-in-roles.md#storage-account-contributor) konta magazynu lub użytkownika, który otrzymał uprawnienia do operacji dostawcy zasobów platformy Azure za pośrednictwem `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) niestandardowej roli platformy Azure.

Konto magazynu i sieci wirtualne, do których udzielono dostępu, mogą być w różnych subskrypcjach, w tym subskrypcjach, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które przyznają dostęp do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy usługi Azure Active Directory, jest obecnie obsługiwana tylko za pośrednictwem programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pośrednictwem Azure Portal, chociaż mogą być one przeglądane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Regułami sieci wirtualnej dla kont magazynu można zarządzać za pośrednictwem Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz pozycję w menu ustawień o **nazwie Sieć.**

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci.**

4. Aby udzielić dostępu do sieci wirtualnej przy użyciu nowej reguły sieci,  w  obszarze Sieci wirtualne wybierz pozycję Dodaj istniejącą sieć wirtualną, wybierz opcje Sieci wirtualne i Podsieci, a następnie wybierz pozycję **Dodaj.** Aby utworzyć nową sieć wirtualną i przyznać jej dostęp, wybierz **pozycję Dodaj nową sieć wirtualną.** Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz.**

    > [!NOTE]
    > Jeśli punkt końcowy usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, można go skonfigurować w ramach tej operacji.
    >
    > Obecnie tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory są wyświetlane do wyboru podczas tworzenia reguły. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

5. Aby usunąć sieć wirtualną lub regułę podsieci, wybierz pozycję **...,** aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

6. Wybierz **pozycję Zapisz,** aby zastosować zmiany.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj aplikację [Azure PowerShell](/powershell/azure/install-Az-ps) [i zaloguj się.](/powershell/azure/authenticate-azureps)

2. Lista reguł sieci wirtualnej.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Włączanie punktu końcowego usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Aby dodać regułę sieci dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/nazwa-sieci/podsieci/nazwa podsieci".

5. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną tak,](#change-the-default-network-access-rule) **aby** odmawiała lub reguły sieciowe nie mają żadnego efektu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj [się.](/cli/azure/authenticate-azure-cli)

2. Lista reguł sieci wirtualnej.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Włączanie punktu końcowego usługi dla usługi Azure Storage w istniejącej sieci wirtualnej i podsieci.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/ \<subscription-ID\> /resourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Za pomocą parametru **subskrypcji** możesz pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

5. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną tak,](#change-the-default-network-access-rule) **aby** odmawiała lub reguły sieciowe nie mają żadnego efektu.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z zakresu internetowych adresów IP

Reguły sieci IP umożliwiają dostęp z określonych zakresów publicznych internetowych adresów IP, tworząc reguły sieci IP. Każde konto magazynu obsługuje maksymalnie 200 reguł. Te reguły przyznają dostęp do określonych usług internetowych i sieci lokalnych oraz blokują ogólny ruch internetowy.

Następujące ograniczenia dotyczą zakresów adresów IP.

- Reguły sieci IP są dozwolone tylko dla **publicznych internetowych adresów** IP. 

  Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) są niedozwolone w zasadach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się _od 10.*_, _172.16.*_  -  _172.31.*_ i _192.168.*_.

- Dozwolone zakresy adresów internetowych należy podać przy użyciu notacji [CIDR](https://tools.ietf.org/html/rfc4632) w postaci *16.17.18.0/24* lub jako indywidualne adresy IP, takie jak *16.17.18.19.* 

- Małe zakresy adresów używające prefiksów "/31" lub "/32" nie są obsługiwane. Te zakresy należy skonfigurować przy użyciu poszczególnych reguł adresów IP. 

- Tylko adresy IPV4 są obsługiwane w przypadku konfigurowania reguł zapory magazynu.

Reguł sieci IP nie można używać w następujących przypadkach:

- Aby ograniczyć dostęp do klientów w tym samym regionie świadczenia usługi Azure co konto magazynu.
  
  Reguły sieci ip nie mają wpływu na żądania pochodzące z tego samego regionu świadczenia usługi Azure co konto magazynu. Użyj [reguł sieci wirtualnej,](#grant-access-from-a-virtual-network) aby zezwolić na żądania w tym samym regionie. 

- Aby ograniczyć dostęp do klientów w [sparowanych regionach,](../../best-practices-availability-paired-regions.md) które znajdują się w sieci wirtualnej z punktem końcowym usługi.

- Aby ograniczyć dostęp do usług platformy Azure wdrożonych w tym samym regionie co konto magazynu.

  Usługi wdrożone w tym samym regionie co konto magazynu używają prywatnych adresów IP platformy Azure do komunikacji. W związku z tym nie można ograniczyć dostępu do określonych usług platformy Azure na podstawie ich zakresu publicznych wychodzących adresów IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do konta magazynu przy użyciu reguły sieci IP, należy zidentyfikować adresy IP używane przez sieć przez Internet. Skontaktuj się z administratorem sieci, aby uzyskać pomoc.

jeśli korzystasz z usługi [ExpressRoute](../../expressroute/expressroute-introduction.md) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP nat są dostarczane przez klienta lub udostępniane przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Regułami sieci adresów IP dla kont magazynu można zarządzać za pośrednictwem Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz pozycję w menu ustawień o **nazwie Sieć.**

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci.**

4. Aby udzielić dostępu do internetowego zakresu adresów IP, wprowadź adres IP lub zakres adresów (w formacie CIDR) w obszarze **Zakres**  >  **adresów zapory.**

5. Aby usunąć regułę sieci ip, wybierz ikonę kosza obok zakresu adresów.

6. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj aplikację [Azure PowerShell](/powershell/azure/install-Az-ps) [i zaloguj się.](/powershell/azure/authenticate-azureps)

2. Lista reguł sieci IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Dodaj regułę sieci dla pojedynczego adresu IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Dodaj regułę sieci dla zakresu adresów IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Usuń regułę sieci dla pojedynczego adresu IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Usuń regułę sieci dla zakresu adresów IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną odmawiania](#change-the-default-network-access-rule) **lub** reguły sieci nie mają żadnego efektu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj [się.](/cli/azure/authenticate-azure-cli)

1. Lista reguł sieci IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Dodaj regułę sieci dla pojedynczego adresu IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Dodaj regułę sieci dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Usuń regułę sieci dla pojedynczego adresu IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Usuń regułę sieci dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną odmawiania](#change-the-default-network-access-rule) **lub** reguły sieci nie mają żadnego efektu.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Udzielanie dostępu z wystąpień zasobów platformy Azure (wersja zapoznawcza)

W niektórych przypadkach aplikacja może zależeć od zasobów platformy Azure, których nie można odizolować za pośrednictwem sieci wirtualnej lub reguły adresu IP. Jednak nadal chcesz zabezpieczyć i ograniczyć dostęp do konta magazynu tylko do zasobów platformy Azure aplikacji. Konta magazynu można skonfigurować tak, aby zezwalały na dostęp do określonych wystąpień zasobów niektórych usług platformy Azure, tworząc regułę wystąpienia zasobu. 

Typy operacji, które wystąpienie zasobu może wykonywać na danych konta magazynu, są określane przez przypisania ról platformy [Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) dla wystąpienia zasobu. Wystąpienia zasobów muszą pochodzić z tej samej dzierżawy co konto magazynu, ale mogą należeć do dowolnej subskrypcji w dzierżawie.

> [!NOTE]
> Ta funkcja jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach chmury publicznej.

> [!NOTE]
> Reguły wystąpień zasobów są obecnie obsługiwane tylko w przypadku Azure Synapse. Obsługa innych usług platformy Azure wymienionych w sekcji [Zaufany](#trusted-access-system-assigned-managed-identity) dostęp oparty na tożsamości zarządzanej przypisanej przez system tego artykułu będzie dostępna w najbliższych tygodniach.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Reguły sieci zasobów można dodawać lub usuwać w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

2. Znajdź konto magazynu i wyświetl omówienie konta.

3. Wybierz **pozycję Sieć,** aby wyświetlić stronę konfiguracji sieci.

4. Z **listy rozwijanej** Typ zasobu wybierz typ zasobu wystąpienia zasobu. 

5. Z **listy rozwijanej** Nazwa wystąpienia wybierz wystąpienie zasobu. Możesz również uwzględnić wszystkie wystąpienia zasobów w aktywnej dzierżawie, subskrypcji lub grupie zasobów.

6. Aby zastosować zmiany, wybierz pozycję **Zapisz**. Wystąpienie zasobu zostanie wyświetlone w **sekcji Wystąpienia zasobów** na stronie ustawień sieciowych. 

Aby usunąć wystąpienie zasobu, wybierz ikonę usuwania ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) obok wystąpienia zasobu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Za pomocą poleceń programu PowerShell można dodawać lub usuwać reguły sieci zasobów.

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną odmawiania](#change-the-default-network-access-rule) **lub** reguły sieci nie mają żadnego efektu.

#### <a name="install-the-preview-module"></a>Instalowanie modułu w wersji zapoznawczej

Zainstaluj najnowszą wersję modułu PowershellGet. Następnie zamknij i otwórz ponownie konsolę programu PowerShell.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Zainstaluj **moduł Az. Storage (wersja** zapoznawcza).

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Udzielanie dostępu

Dodaj regułę sieci, która udziela dostępu z wystąpienia zasobu.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Określ wiele wystąpień zasobów jednocześnie, modyfikując zestaw reguł sieci.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Usuwanie dostępu

Usuń regułę sieci, która udziela dostępu z wystąpienia zasobu.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Usuń wszystkie reguły sieciowe, które przyznają dostęp z wystąpień zasobów.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Wyświetlanie listy dozwolonych wystąpień zasobów

Wyświetl pełną listę wystąpień zasobów, do których udzielono dostępu do konta magazynu.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Za pomocą poleceń interfejsu wiersza polecenia platformy Azure można dodawać lub usuwać reguły sieci zasobów.

#### <a name="install-the-preview-extension"></a>Instalowanie rozszerzenia w wersji zapoznawczej

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)polecenia lub jeśli interfejs [](/cli/azure/install-azure-cli) wiersza polecenia platformy Azure został zainstalowany lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Następnie sprawdź, czy zainstalowana wersja interfejsu wiersza polecenia platformy Azure jest lub nowsza, `2.13.0` używając następującego polecenia.

   ```azurecli
   az --version
   ```

   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.13.0` , zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

3. Wpisz następujące polecenie, aby zainstalować rozszerzenie w wersji zapoznawczej.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Udzielanie dostępu

Dodaj regułę sieci, która udziela dostępu z wystąpienia zasobu.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Usuwanie dostępu

Usuń regułę sieci, która udziela dostępu z wystąpienia zasobu.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Wyświetlanie listy dozwolonych wystąpień zasobów

Wyświetl pełną listę wystąpień zasobów, do których udzielono dostępu do konta magazynu.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Udzielanie dostępu do zaufanych usług platformy Azure 

Niektóre usługi platformy Azure działają z sieci, których nie można dodać do reguł sieci. Możesz udzielić podzestawowi takich zaufanych usług platformy Azure dostępu do konta magazynu przy zachowaniu reguł sieci dla innych aplikacji. Te zaufane usługi będą następnie używać silnego uwierzytelniania do bezpiecznego nawiązywania połączenia z kontem magazynu.

Możesz udzielić dostępu do zaufanych usług platformy Azure, tworząc wyjątek reguły sieci. Aby uzyskać szczegółowe wskazówki, zobacz [sekcję Zarządzanie wyjątkami](#manage-exceptions) w tym artykule.

Podczas udzielania dostępu do zaufanych usług platformy Azure udzielasz następujących typów dostępu:

- Zaufany dostęp do wybranych operacji do zasobów zarejestrowanych w subskrypcji.
- Zaufany dostęp do zasobów na podstawie tożsamości zarządzanej przypisanej przez system.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Zaufany dostęp do zasobów zarejestrowanych w subskrypcji

Zasoby niektórych usług, po **zarejestrowaniu** w ramach subskrypcji  , mogą uzyskać dostęp do konta magazynu w tej samej subskrypcji dla wybranych operacji, takich jak zapisywanie dzienników lub tworzenie kopii zapasowych.  W poniższej tabeli opisano każdą usługę i dozwolone operacje. 

| Usługa                  | Nazwa dostawcy zasobów     | Dozwolone operacje                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Uruchamianie tworzenia kopii zapasowych i przywracania dysków nieza jednymi dyskami na maszynach wirtualnych IAAS. (nie jest wymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Umożliwia importowanie danych na platformę Azure przy użyciu urządzenie Data Box. [Dowiedz się więcej](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Tworzenie obrazu niestandardowego i instalacja artefaktów. [Dowiedz się więcej](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Włącz Blob Storage publikowania zdarzeń i Event Grid publikowanie w kolejkach magazynu. Dowiedz się więcej [o zdarzeniach usługi Blob Storage](../../event-grid/overview.md#event-sources) i [publikowaniu w kolejkach.](../../event-grid/event-handlers.md) |
| Azure Event Hubs         | Microsoft.EventHub         | Archiwizowanie danych za pomocą Event Hubs Capture. [Dowiedz się więcej](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft.StorageSync      | Umożliwia przekształcenie serwera plików w pamięć podręczną udziałów plików platformy Azure. Umożliwia synchronizację wielu lokacji, szybkie odzyskiwanie po awarii i tworzenie kopii zapasowych po stronie chmury. [Dowiedz się więcej](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Aprowizowanie początkowej zawartości domyślnego systemu plików dla nowego klastra usługi HDInsight. [Dowiedz się więcej](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import Export      | Microsoft.ImportExport     | Umożliwia importowanie danych do usługi Azure Storage lub eksportowanie danych z usługi Azure Storage przy użyciu usługi Azure Storage Import/Export. [Dowiedz się więcej](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Umożliwia zapisywanie danych monitorowania na zabezpieczonym koncie magazynu, w tym dzienników zasobów, Azure Active Directory logowania i dzienników inspekcji oraz Microsoft Intune dzienników. [Dowiedz się więcej](../../azure-monitor/roles-permissions-security.md). |
| Azure Networking         | Microsoft.Network          | Przechowywanie i analizowanie dzienników ruchu sieciowego, w tym za pośrednictwem Network Watcher i Analiza ruchu usług. [Dowiedz się więcej](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Włącz replikację w celu odzyskiwania po awarii maszyn wirtualnych IaaS platformy Azure w przypadku korzystania z pamięci podręcznej, źródłowego lub docelowego konta magazynu z włączoną zaporą.  [Dowiedz się więcej](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Zaufany dostęp oparty na przypisanej przez system tożsamości zarządzanej

W poniższej tabeli wymieniono usługi, które mogą mieć dostęp do danych konta magazynu, jeśli wystąpienia zasobów tych usług mają odpowiednie uprawnienia. Aby udzielić uprawnień, musisz jawnie przypisać rolę [platformy Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) do przypisanej przez [system](../../active-directory/managed-identities-azure-resources/overview.md) tożsamości zarządzanej dla każdego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do tożsamości zarządzanej. 

> [!TIP]
> Zalecanym sposobem udzielania dostępu do określonych zasobów jest użycie reguł wystąpień zasobów. Aby udzielić dostępu do określonych wystąpień zasobów, zobacz sekcję Udzielanie dostępu z wystąpień zasobów platformy [Azure (wersja zapoznawcza)](#grant-access-specific-instances) tego artykułu.


| Usługa                        | Nazwa dostawcy zasobów                 | Przeznaczenie            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Usługa Azure API Management           | Microsoft.ApiManagement/service        | Umożliwia usłudze Api Management dostęp do kont magazynu za zaporą przy użyciu zasad. [Dowiedz się więcej](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Umożliwia Cognitive Search dostępu do kont magazynu w celu indeksowania, przetwarzania i wykonywania zapytań. |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Umożliwia Cognitive Services dostępu do kont magazynu. |
| Usługa Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | zadania usługi ACR może uzyskać dostęp do kont magazynu podczas tworzenia obrazów kontenerów. |
| Azure Data Factory             | Microsoft.DataFactory/fabryki        | Umożliwia dostęp do kont magazynu za pośrednictwem środowiska uruchomieniowego usługi ADF. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Umożliwia dostęp do kont magazynu za pośrednictwem Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Umożliwia dostęp do kont magazynu za pośrednictwem usługi DevTest Labs. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Umożliwia przechowywanie danych z centrum IoT w magazynie obiektów blob. [Dowiedz się więcej](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Umożliwia aplikacjom logiki dostęp do kont magazynu. [Dowiedz się więcej](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning Service | Microsoft.MachineLearningServices      | Autoryzowane Azure Machine Learning obszary robocze zapisują dane wyjściowe eksperymentu, modele i dzienniki do usługi Blob Storage oraz odczytują dane. [Dowiedz się więcej](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft.Media/mediaservices          | Umożliwia dostęp do kont magazynu za pośrednictwem Media Services. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Umożliwia dostęp do kont magazynu za pośrednictwem Azure Migrate. |
| Azure Purview                  | Microsoft.Purview/accounts             | Zezwala programowi Purview na dostęp do kont magazynu. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Umożliwia dostęp do kont magazynu za pośrednictwem Remote Rendering. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Umożliwia dostęp do kont magazynu za pośrednictwem Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Umożliwia [zapisywanie danych](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) inspekcji na kontach magazynu za zaporą. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Umożliwia importowanie i eksportowanie danych z określonych baz danych SQL przy użyciu instrukcji COPY lub technologii PolyBase (w dedykowanej puli) lub funkcji i tabel zewnętrznych w puli `openrowset` bez serwera. [Dowiedz się więcej](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Usługa Azure Stream Analytics         | Microsoft.StreamAnalytics              | Umożliwia przechowywanie danych z zadania przesyłania strumieniowego w magazynie obiektów blob. [Dowiedz się więcej](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Umożliwia dostęp do danych w usłudze Azure Storage z Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Udzielanie dostępu do analizy magazynu

W niektórych przypadkach dostęp do odczytu dzienników zasobów i metryk jest wymagany spoza granic sieci. Podczas konfigurowania dostępu zaufanych usług do konta magazynu można zezwolić na dostęp do odczytu dla plików dziennika, tabel metryk lub obu tych usług, tworząc wyjątek reguły sieci. Aby uzyskać szczegółowe wskazówki, zobacz sekcję **Zarządzanie wyjątkami** poniżej. Aby dowiedzieć się więcej na temat pracy z analizą magazynu, zobacz Używanie analizy [usługi Azure Storage do zbierania dzienników i danych metryk.](./storage-analytics.md) 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Zarządzanie wyjątkami

Wyjątkami reguł sieci można zarządzać za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure w wersji 2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz pozycję w menu ustawień o nazwie **Sieć.**

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci.**

4. W **obszarze** Wyjątki wybierz wyjątki, które chcesz przyznać.

5. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj aplikację [Azure PowerShell](/powershell/azure/install-Az-ps) [i zaloguj się.](/powershell/azure/authenticate-azureps)

2. Wyświetlanie wyjątków dla reguł sieciowych konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Skonfiguruj wyjątki od reguł sieciowych konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Usuń wyjątki od reguł sieciowych konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) **odmawiania** lub usuwanie wyjątków nie ma żadnego efektu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj [się.](/cli/azure/authenticate-azure-cli)

2. Wyświetlanie wyjątków dla reguł sieciowych konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Skonfiguruj wyjątki od reguł sieciowych konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Usuń wyjątki od reguł sieciowych konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) **odmawiania** lub usuwanie wyjątków nie ma żadnego efektu.

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o punktach końcowych usługi sieci platformy Azure w [tesłudze.](../../virtual-network/virtual-network-service-endpoints-overview.md)

Bardziej zagłębij się w zabezpieczenia usługi Azure Storage w [przewodniku po zabezpieczeniach usługi Azure Storage.](../blobs/security-recommendations.md)