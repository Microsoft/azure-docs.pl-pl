---
title: Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage | Microsoft Docs
description: Skonfiguruj zabezpieczenia warstwowe sieci dla konta magazynu za pomocą zapór usługi Azure Storage i platformy Azure Virtual Network.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3d71a7ad2507909dacf54e7f1c49b6e768033113
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600483"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage

Usługa Azure Storage udostępnia warstwowy model zabezpieczeń. Ten model pozwala zabezpieczyć i kontrolować poziom dostępu do kont magazynu używanych przez aplikacje i środowiska korporacyjne na podstawie typu i podzbioru sieci lub zasobów. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci lub przez określony zestaw zasobów platformy Azure mogą uzyskiwać dostęp do konta magazynu. Możesz ograniczyć dostęp do konta magazynu, aby żądania pochodzące z określonych adresów IP, zakresów adresów IP, podsieci w usłudze Azure Virtual Network (VNet) lub wystąpień zasobów niektórych usług platformy Azure.

Konta magazynu mają publiczny punkt końcowy, który jest dostępny za pomocą Internetu. Możesz również utworzyć [prywatne punkty końcowe dla konta magazynu](storage-private-endpoints.md), które przypisuje prywatny adres IP z sieci wirtualnej do konta magazynu, i zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem prywatnego linku. Zapora usługi Azure Storage zapewnia kontrolę dostępu dla publicznego punktu końcowego konta magazynu. Możesz również użyć zapory, aby zablokować dostęp za pośrednictwem publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych. Konfiguracja zapory magazynu umożliwia także wybranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy reguły sieciowe nadal obowiązują, wymagają odpowiedniej autoryzacji dla żądania. Autoryzacja jest obsługiwana przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) dla obiektów blob i kolejek z prawidłowym kluczem dostępu do konta lub z tokenem SAS.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w ramach platformy Azure Virtual Network (VNet) lub z dozwolonych publicznych adresów IP. Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej.
>
> Można udzielić dostępu do usług platformy Azure, które działają w sieci wirtualnej, zezwalając na ruch z podsieci obsługującej wystąpienie usługi. Można również włączyć ograniczoną liczbę scenariuszy za pomocą mechanizmu wyjątków opisanych poniżej. Aby uzyskać dostęp do danych z konta magazynu za pośrednictwem Azure Portal, musisz znajdować się na komputerze w ramach zaufanej granicy (adresu IP lub sieci wirtualnej), który został skonfigurowany.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć konto magazynu, należy najpierw skonfigurować regułę, aby domyślnie odmówić dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) w publicznym punkcie końcowym. Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Możesz również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych zakresów publicznych adresów IP, co umożliwia nawiązywanie połączeń z określonych klientów internetowych lub lokalnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji.

Można łączyć reguły zapory zezwalające na dostęp z określonych sieci wirtualnych i z zakresów publicznych adresów IP na tym samym koncie magazynu. Reguły zapory magazynu mogą być stosowane do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Reguły zapory magazynu dotyczą publicznego punktu końcowego konta magazynu. Nie są wymagane żadne reguły dostępu zapory, aby zezwalać na ruch dla prywatnych punktów końcowych konta magazynu. Proces zatwierdzania tworzenia prywatnego punktu końcowego przyznaje niejawny dostęp do ruchu z podsieci, która hostuje prywatny punkt końcowy.

Reguły sieciowe są wymuszane dla wszystkich protokołów sieciowych usługi Azure Storage, w tym REST i SMB. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak Azure Portal, Eksplorator usługi Storage i AZCopy, należy skonfigurować jawne reguły sieciowe.

Po zastosowaniu reguł sieci są one wymuszane dla wszystkich żądań. Tokeny SAS, które udzielają dostępu do określonego adresu IP w celu ograniczenia dostępu do posiadacza tokenu, ale nie zapewniają nowego dostępu poza skonfigurowanymi regułami sieciowymi.

Reguły sieciowe nie wpływają na ruch dysku maszyny wirtualnej (w tym operacje instalowania i odinstalowywania oraz we/wy dysku). Dostęp REST do stronicowych obiektów BLOB jest chroniony przez reguły sieciowe.

Klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

Można używać dysków niezarządzanych na kontach magazynu z regułami sieci stosowanymi do tworzenia kopii zapasowych i przywracania maszyn wirtualnych przez utworzenie wyjątku. Ten proces jest udokumentowany w sekcji [Zarządzanie wyjątkami](#manage-exceptions) tego artykułu. Wyjątki zapory nie mają zastosowania w przypadku dysków zarządzanych, ponieważ są one już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie konta magazynu akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Zmiany reguł sieci mogą mieć wpływ na możliwość łączenia się aplikacji z usługą Azure Storage. Ustawienie domyślnej reguły sieci na **Odmów** uniemożliwia dostęp do danych, chyba że stosowane są również określone reguły **sieciowe zezwalające na dostęp.** Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Można zarządzać domyślnymi regułami dostępu do sieci dla kont magazynu za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz opcję w menu Ustawienia o nazwie **Sieć**.

3. Aby domyślnie zablokować dostęp, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

4. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

<a id="powershell"></a>

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

2. Wyświetl stan domyślnej reguły dla konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

2. Wyświetl stan domyślnej reguły dla konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w ramach innej subskrypcji, w tym subskrypcji należących do innej dzierżawy Azure Active Directory.

Włącz [punkt końcowy usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) dla magazynu Azure w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Storage. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla konta magazynu, które zezwalają na odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pomocą tych reguł sieci, muszą nadal spełnić wymagania dotyczące autoryzacji konta magazynu, aby uzyskać dostęp do danych.

Każde konto magazynu obsługuje do 200 reguł sieci wirtualnej, które mogą być połączone z [regułami sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostępne regiony sieci wirtualnej

Ogólnie rzecz biorąc, punkty końcowe usługi działają między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie świadczenia usługi Azure. W przypadku korzystania z punktów końcowych usługi w usłudze Azure Storage ten zakres rośnie, aby uwzględnić [sparowany region](../../best-practices-availability-paired-regions.md). Punkty końcowe usługi umożliwiają ciągłość podczas regionalnego przełączania do trybu failover oraz dostęp do wystąpień magazynu geograficznie nadmiarowego (RA-GRS) tylko do odczytu. Reguły sieciowe, które przyznają dostęp z sieci wirtualnej do konta magazynu, również zapewniają dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii w trakcie regionalnej awarii należy utworzyć sieci wirtualnych w sparowanym regionie z wyprzedzeniem. Włączanie punktów końcowych usługi Azure Storage przy użyciu reguł sieci udzielających dostępu z tych alternatywnych sieci wirtualnych. Następnie Zastosuj te reguły do konta magazynu geograficznie nadmiarowego.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza regionem sieci wirtualnej i wyznaczono pary regionów. Reguły sieci udzielające dostępu z sieci wirtualnych można stosować tylko do kont magazynu w regionie podstawowym konta magazynu lub w wyznaczeniu w określonym regionie.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia do dodawanych podsieci. Zastosowanie reguły może wykonać [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor) lub użytkownik, który otrzymał uprawnienia do `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) za pośrednictwem niestandardowej roli platformy Azure.

Konto magazynu i dostępne sieci wirtualne mogą znajdować się w różnych subskrypcjach, w tym subskrypcje, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy Azure Active Directory, są obecnie obsługiwane tylko za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pomocą Azure Portal, ale mogą one być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Zasadami sieci wirtualnej dla kont magazynu można zarządzać za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz opcję w menu Ustawienia o nazwie **Sieć**.

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

4. Aby udzielić dostępu do sieci wirtualnej przy użyciu nowej reguły sieci, w obszarze **sieci wirtualne** wybierz opcję **Dodaj istniejącą sieć wirtualną**, wybierz opcję **sieci wirtualne** i **podsieci** , a następnie wybierz pozycję **Dodaj**. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, wybierz pozycję **Dodaj nową sieć wirtualną**. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**.

    > [!NOTE]
    > Jeśli punkt końcowy usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować w ramach tej operacji.
    >
    > Obecnie do wyboru podczas tworzenia reguły są wyświetlane tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

5. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz pozycję **...** , aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

6. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

2. Wyświetl listę reguł sieci wirtualnej.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Włącz punkt końcowy usługi dla magazynu Azure w istniejącej sieci wirtualnej i podsieci.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Aby dodać regułę sieciową dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-name".

5. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

2. Wyświetl listę reguł sieci wirtualnej.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Włącz punkt końcowy usługi dla magazynu Azure w istniejącej sieci wirtualnej i podsieci.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> ".
    >
    > Możesz użyć parametru **subskrypcji** , aby pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

5. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z zakresu internetowych adresów IP

Reguł sieci IP można użyć, aby zezwolić na dostęp z określonych publicznych zakresów adresów IP przez tworzenie reguł sieci adresów IP. Każde konto magazynu obsługuje do 200 reguł. Te reguły zapewniają dostęp do określonych usług internetowych i sieci lokalnych i blokują ogólny ruch internetowy.

Do zakresów adresów IP mają zastosowanie następujące ograniczenia.

- Reguły sieci IP są dozwolone tylko dla **publicznych** adresów IP. 

  Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od _10. *_, _172,16. *_  -  _172,31. *_ i _192,168. *_.

- Należy podać dozwolone zakresy adresów internetowych przy użyciu [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formie *16.17.18.0/24* lub jako pojedyncze adresy IP, takie jak *16.17.18.19*. 

- Małe zakresy adresów przy użyciu prefiksów "/31" lub "/32" nie są obsługiwane. Te zakresy należy skonfigurować przy użyciu poszczególnych reguł adresów IP. 

- Dla konfiguracji reguł zapory magazynu są obsługiwane tylko adresy IPV4.

Reguły sieci IP nie mogą być używane w następujących przypadkach:

- Aby ograniczyć dostęp do klientów w tym samym regionie świadczenia usługi Azure co konto magazynu.
  
  Reguły sieci IP nie mają wpływu na żądania pochodzące z tego samego regionu świadczenia usługi Azure, w którym znajduje się konto magazynu. Użyj [reguł sieci wirtualnej](#grant-access-from-a-virtual-network) , aby zezwolić na żądania tego samego regionu. 

- Aby ograniczyć dostęp do klientów w [sparowanym regionie](../../best-practices-availability-paired-regions.md) , które znajdują się w sieci wirtualnej, która ma punkt końcowy usługi.

- Aby ograniczyć dostęp do usług platformy Azure wdrożonych w tym samym regionie co konto magazynu.

  Usługi wdrożone w tym samym regionie co konto magazynu używają prywatnych adresów IP platformy Azure do komunikacji. W tym celu nie można ograniczyć dostępu do określonych usług platformy Azure w oparciu o ich publiczny zakres adresów IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do konta magazynu z regułą sieci adresu IP, należy zidentyfikować adresy IP, które są używane przez sieć. Skontaktuj się z administratorem sieci, aby uzyskać pomoc.

jeśli korzystasz z usługi [ExpressRoute](../../expressroute/expressroute-introduction.md) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Regułami sieci IP dla kont magazynu można zarządzać za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz opcję w menu Ustawienia o nazwie **Sieć**.

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

4. Aby udzielić dostępu do zakresu internetowego adresu IP, wprowadź adres IP lub zakres adresów (w formacie CIDR) w obszarze   >  **zakres adresów** zapory.

5. Aby usunąć regułę sieci IP, wybierz ikonę kosza obok zakresu adresów.

6. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

2. Wyświetl listę reguł sieci adresów IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Dodaj regułę sieciową dla zakresu adresów IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Usuń regułę sieciową dla zakresu adresów IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl listę reguł sieci adresów IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Udzielanie dostępu z wystąpień zasobów platformy Azure (wersja zapoznawcza)

W niektórych przypadkach aplikacja może zależeć od zasobów platformy Azure, które nie mogą być izolowane za pomocą sieci wirtualnej lub reguły adresów IP. Jednak nadal chcesz zabezpieczyć i ograniczyć dostęp do konta magazynu tylko do zasobów platformy Azure Twojej aplikacji. Można skonfigurować konta magazynu, aby zezwolić na dostęp do określonych wystąpień zasobów niektórych usług platformy Azure przez utworzenie reguły wystąpienia zasobu. 

Typy operacji, które może wykonywać wystąpienie zasobu na danych konta magazynu, są określane przez [przypisania ról platformy Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) wystąpienia zasobu. Wystąpienia zasobów muszą znajdować się w tej samej dzierżawie co konto magazynu, ale mogą należeć do dowolnej subskrypcji w dzierżawie.

> [!NOTE]
> Ta funkcja jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach chmury publicznej.

> [!NOTE]
> Reguły wystąpień zasobów są obecnie obsługiwane tylko w przypadku usługi Azure Synapse. Obsługa innych usług platformy Azure wymienionych w polu [zaufany dostęp opartych na zarządzanej tożsamości przypisanej do systemu](#trusted-access-system-assigned-managed-identity) w tym artykule będzie dostępna w najbliższych tygodniach.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Reguły sieci zasobów można dodawać i usuwać w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

2. Znajdź konto magazynu i wyświetl omówienie konta.

3. Wybierz pozycję **Sieć** , aby wyświetlić stronę konfiguracji sieci.

4. Z listy rozwijanej **Typ zasobu** wybierz typ zasobu wystąpienia zasobu. 

5. Z listy rozwijanej **Nazwa wystąpienia** wybierz wystąpienie zasobu. Możesz również uwzględnić wszystkie wystąpienia zasobów w aktywnej dzierżawie, subskrypcji lub grupie zasobów.

6. Aby zastosować zmiany, wybierz pozycję **Zapisz**. Wystąpienie zasobu jest wyświetlane w sekcji **wystąpienia zasobów** na stronie Ustawienia sieci. 

Aby usunąć wystąpienie zasobu, wybierz ikonę usuwania ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) obok wystąpienia zasobu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby dodać lub usunąć reguły sieci zasobów, można użyć poleceń programu PowerShell.

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

#### <a name="install-the-preview-module"></a>Instalowanie modułu podglądu

Zainstaluj najnowszą wersję modułu PowershellGet. Następnie zamknij i ponownie otwórz konsolę programu PowerShell.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Zainstaluj **AZ. Storage** Preview module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Udzielanie dostępu

Dodaj regułę sieci, która przyznaje dostęp z wystąpienia zasobu.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Jednocześnie należy określić wiele wystąpień zasobów, modyfikując zestaw reguł sieci.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Usuwanie dostępu

Usuń regułę sieci, która przyznaje dostęp z wystąpienia zasobu.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Usuń wszystkie reguły sieci, które przyznają dostęp z wystąpień zasobów.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Wyświetlanie listy dozwolonych wystąpień zasobów

Wyświetl pełną listę wystąpień zasobów, którym udzielono dostępu do konta magazynu.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby dodać lub usunąć reguły sieci zasobów, można użyć poleceń interfejsu wiersza polecenia platformy Azure.

#### <a name="install-the-preview-extension"></a>Zainstaluj rozszerzenie podglądu

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Następnie sprawdź, czy zainstalowana wersja interfejsu wiersza polecenia platformy Azure jest `2.13.0` nowsza, przy użyciu następującego polecenia.

   ```azurecli
   az --version
   ```

   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.13.0` , zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

3. Wpisz następujące polecenie, aby zainstalować rozszerzenie wersji zapoznawczej.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Udzielanie dostępu

Dodaj regułę sieci, która przyznaje dostęp z wystąpienia zasobu.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Usuwanie dostępu

Usuń regułę sieci, która przyznaje dostęp z wystąpienia zasobu.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Wyświetlanie listy dozwolonych wystąpień zasobów

Wyświetl pełną listę wystąpień zasobów, którym udzielono dostępu do konta magazynu.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Udzielanie dostępu zaufanym usługom platformy Azure 

Niektóre usługi platformy Azure działają z sieci, które nie mogą być uwzględnione w regułach sieci. Można udzielić podzestawu takich zaufanych usług platformy Azure do konta magazynu, zachowując reguły sieciowe dla innych aplikacji. Te zaufane usługi będą następnie używały silnego uwierzytelniania do bezpiecznego łączenia się z kontem magazynu.

Można udzielić dostępu do zaufanych usług platformy Azure przez utworzenie wyjątku reguły sieci. Instrukcje krok po kroku znajdują się w sekcji [Zarządzanie wyjątkami](#manage-exceptions) w tym artykule.

Po udzieleniu dostępu do zaufanych usług platformy Azure przyznaje się następujące typy dostępu:

- Zaufany dostęp do wybranych operacji dla zasobów, które są zarejestrowane w ramach subskrypcji.
- Zaufany dostęp do zasobów na podstawie tożsamości zarządzanej przypisanej do systemu.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Zaufany dostęp do zasobów zarejestrowanych w ramach subskrypcji

Zasoby niektórych usług, które są **rejestrowane w ramach subskrypcji**, mogą uzyskiwać dostęp do konta magazynu **w ramach tej samej subskrypcji** dla operacji wybierania, takich jak zapisywanie dzienników lub kopia zapasowa.  W poniższej tabeli opisano każdą usługę i dozwolone operacje. 

| Usługa                  | Nazwa dostawcy zasobów     | Dozwolone operacje                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Uruchamianie kopii zapasowych i przywracanie dysków niezarządzanych w IAAS maszynach wirtualnych. (niewymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft. DataBox          | Umożliwia importowanie danych na platformę Azure przy użyciu urządzenie Data Box. [Dowiedz się więcej](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft. wspólny       | Tworzenie obrazu niestandardowego i instalacja artefaktu. [Dowiedz się więcej](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Włącz Blob Storage publikowanie zdarzeń i zezwól Event Grid na publikowanie w kolejkach magazynu. Informacje o [zdarzeniach magazynu obiektów BLOB](../../event-grid/overview.md#event-sources) i [publikowaniu w kolejkach](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft. EventHub         | Archiwizuj dane przy użyciu funkcji przechwytywania Event Hubs. [Dowiedz się więcej](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft. StorageSync      | Umożliwia przekształcenie serwera plików Premium na pamięć podręczną dla udziałów plików platformy Azure. Umożliwienie synchronizacji obejmujących wiele witryn, szybkie odzyskiwanie po awarii i tworzenie kopii zapasowej po stronie chmury. [Dowiedz się więcej](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Zainicjuj wstępną zawartość domyślnego systemu plików dla nowego klastra usługi HDInsight. [Dowiedz się więcej](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Eksportowanie importu platformy Azure      | Microsoft. ImportExport     | Umożliwia importowanie danych do usługi Azure Storage lub eksportowanie danych z usługi Azure Storage w usłudze Azure Storage Import/Export. [Dowiedz się więcej](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft. Insights         | Umożliwia zapisywanie danych monitorowania na zabezpieczonym koncie magazynu, w tym dzienników zasobów, Azure Active Directory logowania i dzienników inspekcji oraz dzienników Microsoft Intune. [Dowiedz się więcej](../../azure-monitor/roles-permissions-security.md). |
| Sieć platformy Azure         | Microsoft.Network          | Przechowywanie i analizowanie dzienników ruchu sieciowego, w tym za pomocą usług Network Watcher i Analiza ruchu. [Dowiedz się więcej](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Włącz replikację na potrzeby odzyskiwania po awarii maszyn wirtualnych platformy Azure IaaS w przypadku korzystania z pamięci podręcznej z włączoną zaporą, źródła lub docelowego konta magazynu.  [Dowiedz się więcej](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Zaufany dostęp oparty na zarządzanej tożsamości przypisanej do systemu

Poniższa tabela zawiera listę usług, które mogą mieć dostęp do danych konta magazynu, jeśli wystąpienia zasobów tych usług mają odpowiednie uprawnienia. Aby udzielić uprawnienia, musisz jawnie [przypisać rolę platformy Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) do [zarządzanej tożsamości przypisanej do systemu](../../active-directory/managed-identities-azure-resources/overview.md) dla każdego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do zarządzanej tożsamości. 

> [!TIP]
> Zalecanym sposobem udzielenia dostępu do określonych zasobów jest użycie reguł wystąpień zasobów. Aby udzielić dostępu do określonych wystąpień zasobów, zobacz sekcję [udzielanie dostępu z wystąpień zasobów platformy Azure (wersja zapoznawcza)](#grant-access-specific-instances) w tym artykule.


| Usługa                        | Nazwa dostawcy zasobów                 | Przeznaczenie            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Usługa Azure API Management           | Microsoft.ApiManagement/service        | Umożliwia usłudze API Management dostęp do kont magazynu za zaporą przy użyciu zasad. [Dowiedz się więcej](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. Search/searchServices        | Umożliwia Wyszukiwanie poznawcze usługom dostęp do kont magazynu na potrzeby indeksowania, przetwarzania i wykonywania zapytań. |
| Azure Cognitive Services       | Microsoft. CognitiveService/konta    | Umożliwia Cognitive Services dostępu do kont magazynu. |
| Usługa Azure Container Registry Tasks | Microsoft. ContainerRegistry/rejestry | Zadania ACR mogą uzyskać dostęp do kont magazynu podczas kompilowania obrazów kontenerów. |
| Azure Data Factory             | Microsoft. DataFactory/fabryki        | Zezwala na dostęp do kont magazynu za pomocą środowiska uruchomieniowego ADF. |
| Azure Data Share               | Microsoft. dataudział/konta           | Zezwala na dostęp do kont magazynu za poorednictwem udziału danych. |
| Azure DevTest Labs             | Microsoft. wspólny/laboratoria              | Zezwala na dostęp do kont magazynu za poorednictwem DevTest Labs. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Umożliwia zapisanie danych z usługi IoT Hub w usłudze BLOB Storage. [Dowiedz się więcej](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/przepływy pracy              | Umożliwia aplikacjom logiki dostęp do kont magazynu. [Dowiedz się więcej](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning Service | Microsoft.MachineLearningServices      | Autoryzowane Azure Machine Learning obszary robocze zapisują dane wyjściowe eksperymentu, modele i dzienniki w usłudze BLOB Storage i odczytywane danych. [Dowiedz się więcej](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft. Media/MediaServices          | Umożliwia dostęp do kont magazynu za poorednictwem Media Services. |
| Azure Migrate                  | Microsoft. migrowana/migrateprojects      | Umożliwia dostęp do kont magazynu za poorednictwem Azure Migrate. |
| Azure Purview                  | Microsoft. kontrolą/konta             | Zezwala usłudze kontrolą na dostęp do kont magazynu. |
| Azure Remote Rendering         | Microsoft. MixedReality/remoteRenderingAccounts | Umożliwia dostęp do kont magazynu za poorednictwem renderowania zdalnego. |
| Azure Site Recovery            | Microsoft. RecoveryServices/magazyny      | Umożliwia dostęp do kont magazynu za poorednictwem Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Umożliwia [Zapisywanie](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) danych inspekcji na kontach magazynu za zaporą. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Umożliwia importowanie i eksportowanie danych z określonych baz danych SQL przy użyciu instrukcji COPY lub Base (w puli dedykowanej) lub `openrowset` funkcji i tabel zewnętrznych w puli bezserwerowej. [Dowiedz się więcej](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Usługa Azure Stream Analytics         | Microsoft. StreamAnalytics              | Umożliwia zapisanie danych z zadania przesyłania strumieniowego w usłudze BLOB Storage. [Dowiedz się więcej](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft. Synapse/obszary robocze           | Umożliwia dostęp do danych w usłudze Azure Storage z usługi Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Udzielanie dostępu do analizy magazynu

W niektórych przypadkach dostęp do odczytu dzienników zasobów i metryk jest wymagany spoza granicy sieci. Podczas konfigurowania dostępu do zaufanych usług do konta magazynu można zezwolić na dostęp do odczytu dla plików dziennika, tabel metryk lub obu, tworząc wyjątek reguły sieci. Aby uzyskać wskazówki krok po kroku, zobacz sekcję **Manage Exceptions** poniżej. Aby dowiedzieć się więcej o pracy z analizą magazynu, zobacz [Korzystanie z usługi Azure Storage Analytics do zbierania danych dzienników i metryk](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Zarządzanie wyjątkami

Wyjątkami reguł sieci można zarządzać za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure w wersji 2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

2. Wybierz opcję w menu Ustawienia o nazwie **Sieć**.

3. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

4. W obszarze **wyjątki** Wybierz wyjątki, które chcesz udzielić.

5. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

2. Wyświetl wyjątki dla reguł sieci konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Skonfiguruj wyjątki dla reguł sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Usuń wyjątki dla reguł sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie** lub usunięcie wyjątków nie ma żadnego wpływu.

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

2. Wyświetl wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Skonfiguruj wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Usuń wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie** lub usunięcie wyjątków nie ma żadnego wpływu.

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o punktach końcowych usługi sieciowej Azure w [punktach końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md).

Dig bardziej szczegółowe zabezpieczenia usługi Azure Storage w [przewodniku po zabezpieczeniach usługi Azure Storage](../blobs/security-recommendations.md).