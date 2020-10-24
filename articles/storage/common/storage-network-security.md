---
title: Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage | Microsoft Docs
description: Skonfiguruj zabezpieczenia warstwowe sieci dla konta magazynu za pomocą zapór usługi Azure Storage i platformy Azure Virtual Network.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 96e06e31ae3c963459a0f6b4772147197913b52a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488592"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage

Usługa Azure Storage zapewnia warstwowy model zabezpieczeń. Ten model umożliwia zabezpieczenie i kontrolowanie poziomu dostępu do kont magazynu żądanych przez aplikację i przedsiębiorstwo na podstawie typu i podsieci używanych sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta magazynu. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w usłudze Azure Virtual Network (VNet).

Konta magazynu mają publiczny punkt końcowy, który jest dostępny za pomocą Internetu. Możesz również utworzyć [prywatne punkty końcowe dla konta magazynu](storage-private-endpoints.md), które przypisuje prywatny adres IP z sieci wirtualnej do konta magazynu, i zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem prywatnego linku. Zapora usługi Azure Storage zapewnia dostęp do kontroli dostępu do publicznego punktu końcowego konta magazynu. Możesz również użyć zapory, aby zablokować dostęp za pośrednictwem publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych. Konfiguracja zapory magazynu umożliwia także wybranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu.

Aplikacja, która uzyskuje dostęp do konta magazynu, gdy reguły sieciowe nadal obowiązują, wymagają odpowiedniej autoryzacji dla żądania. Autoryzacja jest obsługiwana przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) dla obiektów blob i kolejek z prawidłowym kluczem dostępu do konta lub z tokenem SAS.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w ramach platformy Azure Virtual Network (VNet) lub z dozwolonych publicznych adresów IP. Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej.
>
> Można udzielić dostępu do usług platformy Azure, które działają w sieci wirtualnej, zezwalając na ruch z podsieci obsługującej wystąpienie usługi. Można również włączyć ograniczoną liczbę scenariuszy za pomocą mechanizmu [wyjątków](#exceptions) opisanych poniżej. Aby uzyskać dostęp do danych z konta magazynu za pośrednictwem Azure Portal, musisz znajdować się na komputerze w ramach zaufanej granicy (adresu IP lub sieci wirtualnej), który został skonfigurowany.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć konto magazynu, należy najpierw skonfigurować regułę, aby domyślnie odmówić dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) w publicznym punkcie końcowym. Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Możesz również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych zakresów publicznych adresów IP w Internecie, włączając połączenia z określonych klientów internetowych lub lokalnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji.

Można łączyć reguły zapory zezwalające na dostęp z określonych sieci wirtualnych i z zakresów publicznych adresów IP na tym samym koncie magazynu. Reguły zapory magazynu mogą być stosowane do istniejących kont magazynu lub podczas tworzenia nowych kont magazynu.

Reguły zapory magazynu dotyczą publicznego punktu końcowego konta magazynu. Nie są wymagane żadne reguły dostępu zapory, aby zezwalać na ruch dla prywatnych punktów końcowych konta magazynu. Proces zatwierdzania tworzenia prywatnego punktu końcowego przyznaje niejawny dostęp do ruchu z podsieci, która hostuje prywatny punkt końcowy.

Reguły sieciowe są wymuszane dla wszystkich protokołów sieciowych w usłudze Azure Storage, w tym REST i SMB. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak Azure Portal, Eksplorator usługi Storage i AZCopy, należy skonfigurować jawne reguły sieciowe.

Po zastosowaniu reguł sieci są one wymuszane dla wszystkich żądań. Tokeny SAS, które udzielają dostępu do określonego adresu IP w celu ograniczenia dostępu do posiadacza tokenu, ale nie zapewniają nowego dostępu poza skonfigurowanymi regułami sieciowymi.

Reguły sieciowe nie wpływają na ruch dysku maszyny wirtualnej (w tym operacje instalowania i odinstalowywania oraz we/wy dysku). Dostęp REST do stronicowych obiektów BLOB jest chroniony przez reguły sieciowe.

Klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

Dysków niezarządzanych można używać na kontach magazynu z regułami sieci stosowanymi do tworzenia kopii zapasowych i przywracania maszyn wirtualnych przez utworzenie wyjątku. Ten proces jest udokumentowany w sekcji [wyjątki](#exceptions) tego artykułu. Wyjątki zapory nie mają zastosowania w przypadku dysków zarządzanych, ponieważ są one już zarządzane przez platformę Azure.

## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie konta magazynu akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Zmiany reguł sieci mogą mieć wpływ na możliwość łączenia się aplikacji z usługą Azure Storage. Ustawienie domyślnej reguły sieci na **Odmów** uniemożliwia dostęp do danych, chyba że stosowane są również określone reguły **sieciowe zezwalające na dostęp.** Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Można zarządzać domyślnymi regułami dostępu do sieci dla kont magazynu za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Aby domyślnie zablokować dostęp, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl stan domyślnej reguły dla konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl stan domyślnej reguły dla konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w ramach innej subskrypcji, w tym subskrypcji należących do innej dzierżawy Azure Active Directory.

Włącz [punkt końcowy usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) dla magazynu Azure w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Storage. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla konta magazynu, które zezwalają na odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pomocą tych reguł sieci, muszą nadal spełnić wymagania dotyczące autoryzacji konta magazynu, aby uzyskać dostęp do danych.

Każde konto magazynu obsługuje do 200 reguł sieci wirtualnej, które mogą być połączone z [regułami sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostępne regiony sieci wirtualnej

Ogólnie rzecz biorąc, punkty końcowe usługi działają między sieciami wirtualnymi i wystąpieniami usług w tym samym regionie świadczenia usługi Azure. W przypadku korzystania z punktów końcowych usługi w usłudze Azure Storage ten zakres rośnie, aby uwzględnić [sparowany region](/azure/best-practices-availability-paired-regions). Punkty końcowe usługi umożliwiają ciągłość podczas regionalnego przełączania do trybu failover oraz dostęp do wystąpień magazynu geograficznie nadmiarowego (RA-GRS) tylko do odczytu. Reguły sieciowe, które przyznają dostęp z sieci wirtualnej do konta magazynu, również zapewniają dostęp do dowolnego wystąpienia RA-GRS.

Podczas planowania odzyskiwania po awarii w trakcie regionalnej awarii należy utworzyć sieci wirtualnych w sparowanym regionie z wyprzedzeniem. Włączanie punktów końcowych usługi Azure Storage przy użyciu reguł sieci udzielających dostępu z tych alternatywnych sieci wirtualnych. Następnie Zastosuj te reguły do konta magazynu geograficznie nadmiarowego.

> [!NOTE]
> Punkty końcowe usługi nie mają zastosowania do ruchu poza regionem sieci wirtualnej i wyznaczono pary regionów. Reguły sieci udzielające dostępu z sieci wirtualnych można stosować tylko do kont magazynu w regionie podstawowym konta magazynu lub w wyznaczeniu w określonym regionie.

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do konta magazynu, użytkownik musi mieć odpowiednie uprawnienia do dodawanych podsieci. Konieczne uprawnienie jest *przyłączanie usługi do podsieci* i jest uwzględniona w wbudowanej roli *współautor konta magazynu* . Można go również dodać do niestandardowych definicji ról.

Konto magazynu i dostępne sieci wirtualne mogą znajdować się w różnych subskrypcjach, w tym subskrypcje, które są częścią innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy Azure Active Directory, są obecnie obsługiwane tylko za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pomocą Azure Portal, ale mogą one być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Zasadami sieci wirtualnej dla kont magazynu można zarządzać za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do sieci wirtualnej przy użyciu nowej reguły sieci, w obszarze **sieci wirtualne**kliknij przycisk **Dodaj istniejącą sieć wirtualną**, wybierz opcję **sieci wirtualne** i **podsieci** , a następnie kliknij przycisk **Dodaj**. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, kliknij przycisk **Dodaj nową sieć wirtualną**. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Jeśli punkt końcowy usługi Azure Storage nie został wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować w ramach tej operacji.
    >
    > Obecnie do wyboru podczas tworzenia reguły są wyświetlane tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

1. Aby usunąć regułę sieci wirtualnej lub podsieci, kliknij przycisk **...** , aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie kliknij przycisk **Usuń**.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl listę reguł sieci wirtualnej.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla magazynu Azure w istniejącej sieci wirtualnej i podsieci.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Aby dodać regułę sieciową dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-name".

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla magazynu Azure w istniejącej sieci wirtualnej i podsieci.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> ".
    >
    > Możesz użyć parametru **subskrypcji** , aby pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z zakresu internetowych adresów IP

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp z określonych publicznych zakresów adresów IP. Ta konfiguracja zapewnia dostęp do określonych usług internetowych i sieci lokalnych oraz blokuje ogólny ruch internetowy.

Podaj dozwolone zakresy adresów internetowych przy użyciu [notacji CIDR](https://tools.ietf.org/html/rfc4632) w postaci *16.17.18.0/24* lub jako indywidualne adresy IP, takie jak *16.17.18.19*.

   > [!NOTE]
   > Małe zakresy adresów przy użyciu prefiksów "/31" lub "/32" nie są obsługiwane. Te zakresy należy skonfigurować przy użyciu poszczególnych reguł adresów IP.

Reguły sieci IP są dozwolone tylko dla **publicznych** adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od _10. *_, _172,16. *_  -  _172,31. *_ i _192,168. *_.

   > [!NOTE]
   > Reguły sieci IP nie mają wpływu na żądania pochodzące z tego samego regionu świadczenia usługi Azure, w którym znajduje się konto magazynu. Użyj [reguł sieci wirtualnej](#grant-access-from-a-virtual-network) , aby zezwolić na żądania tego samego regionu.

  > [!NOTE]
  > Usługi wdrożone w tym samym regionie co konto magazynu używają prywatnych adresów IP platformy Azure do komunikacji. W ten sposób nie można ograniczyć dostępu do określonych usług platformy Azure w oparciu o ich publiczny zakres adresów IP.

Dla konfiguracji reguł zapory magazynu są obsługiwane tylko adresy IPV4.

Każde konto magazynu obsługuje do 200 reguł sieci adresów IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do konta magazynu z regułą sieci adresu IP, należy zidentyfikować adresy IP, które są używane przez sieć. Skontaktuj się z administratorem sieci, aby uzyskać pomoc.

jeśli korzystasz z usługi [ExpressRoute](/azure/expressroute/expressroute-introduction) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Regułami sieci IP dla kont magazynu można zarządzać za pomocą Azure Portal, PowerShell lub CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do zakresu internetowego adresu IP, wprowadź adres IP lub zakres adresów (w formacie CIDR) w obszarze **Firewall**  >  **zakres adresów**zapory.

1. Aby usunąć regułę sieci IP, kliknij ikonę kosza obok zakresu adresów.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl listę reguł sieci adresów IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl listę reguł sieci adresów IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="exceptions"></a>Wyjątki

Reguły sieciowe pomagają w tworzeniu bezpiecznego środowiska dla połączeń między aplikacjami i danymi w większości scenariuszy. Niektóre aplikacje są jednak zależne od usług platformy Azure, których nie można jednoznacznie odizolować za pomocą sieci wirtualnej lub reguł adresów IP. Jednak te usługi muszą zostać przyznane do magazynu, aby umożliwić pełne działanie aplikacji. W takich sytuacjach można użyć **_Zezwalaj na zaufane usługi firmy Microsoft..._* _ ustawienie, aby umożliwić tym usługom dostęp do danych, dzienników lub analizy.

### <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft

Niektóre usługi firmy Microsoft działają z sieci, które nie mogą być uwzględnione w regułach sieci. Można przyznać podzbiór takich zaufanych usług firmy Microsoft dostęp do konta magazynu przy zachowaniu reguł sieci dla innych aplikacji. Te zaufane usługi będą następnie używać silnego uwierzytelniania do bezpiecznego łączenia się z kontem magazynu. Włączono dwa tryby zaufanego dostępu dla usług firmy Microsoft.

- Zasoby niektórych usług, _ * po zarejestrowaniu w subskrypcji * *, mogą uzyskiwać dostęp do konta magazynu **w ramach tej samej subskrypcji** dla operacji wybierania, takich jak zapisywanie dzienników lub kopia zapasowa.
- Do zasobów niektórych usług można uzyskać jawny dostęp do konta magazynu, **przypisując rolę platformy Azure** do zarządzanej tożsamości przypisanej do systemu.


Po włączeniu ustawienia **Zezwalaj na zaufane usługi firmy Microsoft..** . dla zasobów następujących usług, które są zarejestrowane w ramach tej samej subskrypcji, co konto magazynu, zostanie udzielony dostęp do ograniczonego zestawu operacji zgodnie z opisem:

| Usługa                  | Nazwa dostawcy zasobów     | Dozwolone operacje                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Uruchamianie kopii zapasowych i przywracanie dysków niezarządzanych w IAAS maszynach wirtualnych. (niewymagane w przypadku dysków zarządzanych). [Dowiedz się więcej](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft. DataBox          | Umożliwia importowanie danych na platformę Azure przy użyciu urządzenie Data Box. [Dowiedz się więcej](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft. wspólny       | Tworzenie obrazu niestandardowego i instalacja artefaktu. [Dowiedz się więcej](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Włącz Blob Storage publikowanie zdarzeń i zezwól Event Grid na publikowanie w kolejkach magazynu. Informacje o [zdarzeniach magazynu obiektów BLOB](/azure/event-grid/event-sources) i [publikowaniu w kolejkach](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft. EventHub         | Archiwizuj dane przy użyciu funkcji przechwytywania Event Hubs. [Dowiedz się więcej](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft. StorageSync      | Umożliwia przekształcenie serwera plików Premium na pamięć podręczną dla udziałów plików platformy Azure. Umożliwienie synchronizacji obejmujących wiele witryn, szybkie odzyskiwanie po awarii i tworzenie kopii zapasowej po stronie chmury. [Dowiedz się więcej](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Zainicjuj wstępną zawartość domyślnego systemu plików dla nowego klastra usługi HDInsight. [Dowiedz się więcej](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Eksportowanie importu platformy Azure      | Microsoft. ImportExport     | Umożliwia importowanie danych do usługi Azure Storage lub eksportowanie danych z usługi Azure Storage w usłudze Azure Storage Import/Export. [Dowiedz się więcej](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft. Insights         | Umożliwia zapisywanie danych monitorowania na zabezpieczonym koncie magazynu, w tym dzienników zasobów, Azure Active Directory logowania i dzienników inspekcji oraz dzienników Microsoft Intune. [Dowiedz się więcej](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Sieć platformy Azure         | Microsoft.Network          | Przechowywanie i analizowanie dzienników ruchu sieciowego, w tym za pomocą usług Network Watcher i Analiza ruchu. [Dowiedz się więcej](/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Włącz replikację na potrzeby odzyskiwania po awarii maszyn wirtualnych platformy Azure IaaS w przypadku korzystania z pamięci podręcznej z włączoną zaporą, źródła lub docelowego konta magazynu.  [Dowiedz się więcej](/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Ustawienie **Zezwalaj na zaufane usługi firmy Microsoft...** umożliwia również określonym wystąpieniu poniższych usług uzyskanie dostępu do konta magazynu, jeśli użytkownik jawnie [przypisze rolę platformy Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) do [zarządzanej tożsamości przypisanej do systemu](../../active-directory/managed-identities-azure-resources/overview.md) dla tego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do zarządzanej tożsamości.

| Usługa                        | Nazwa dostawcy zasobów                 | Przeznaczenie            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Umożliwia usłudze API Management dostęp do kont magazynu za zaporą przy użyciu zasad. [Dowiedz się więcej](/azure/api-management/api-management-authentication-policies#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. Search/searchServices        | Umożliwia Wyszukiwanie poznawcze usługom dostęp do kont magazynu na potrzeby indeksowania, przetwarzania i wykonywania zapytań. |
| Usługa Azure Container Registry Tasks | Microsoft. ContainerRegistry/rejestry | Zadania ACR mogą uzyskać dostęp do kont magazynu podczas kompilowania obrazów kontenerów. |
| Azure Data Factory             | Microsoft. DataFactory/fabryki        | Zezwala na dostęp do kont magazynu za pomocą środowiska uruchomieniowego ADF. |
| Azure Data Share               | Microsoft. dataudział/konta           | Zezwala na dostęp do kont magazynu za poorednictwem udziału danych. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Umożliwia zapisanie danych z usługi IoT Hub w usłudze BLOB Storage. [Dowiedz się więcej](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/przepływy pracy              | Umożliwia aplikacjom logiki dostęp do kont magazynu. [Dowiedz się więcej](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning Service | Microsoft.MachineLearningServices      | Autoryzowane Azure Machine Learning obszary robocze zapisują dane wyjściowe eksperymentu, modele i dzienniki w usłudze BLOB Storage i odczytywane danych. [Dowiedz się więcej](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)       | Microsoft.Sql                          | Umożliwia importowanie i eksportowanie danych z określonych baz danych SQL przy użyciu instrukcji COPY lub Base. [Dowiedz się więcej](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure SQL Database       | Microsoft.Sql                          | Umożliwia [Importowanie](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) danych z kont magazynu i [Zapisywanie](/azure/azure-sql/database/audit-write-storage-account-behind-vnet-firewall) danych inspekcji na kontach magazynu za zaporą. |
| Usługa Azure Stream Analytics         | Microsoft. StreamAnalytics             | Umożliwia zapisanie danych z zadania przesyłania strumieniowego w usłudze BLOB Storage. [Dowiedz się więcej](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. Synapse/obszary robocze          | Umożliwia dostęp do danych w usłudze Azure Storage z Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Dostęp do danych w usłudze Storage Analytics

W niektórych przypadkach dostęp do odczytu dzienników zasobów i metryk jest wymagany spoza granicy sieci. Podczas konfigurowania dostępu do zaufanych usług do konta magazynu można zezwolić na dostęp do odczytu dla plików dziennika, metryk tabel lub obu. [Dowiedz się więcej na temat pracy z usługą analiza magazynu.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Zarządzanie wyjątkami

Wyjątkami reguł sieci można zarządzać za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure w wersji 2.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do konta magazynu, które chcesz zabezpieczyć.

1. Kliknij menu Ustawienia o nazwie **zapory i sieci wirtualne**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. W obszarze **wyjątki**Wybierz wyjątki, które chcesz udzielić.

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

#### <a name="powershell"></a>PowerShell

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-Az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps).

1. Wyświetl wyjątki dla reguł sieci konta magazynu.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Skonfiguruj wyjątki dla reguł sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Usuń wyjątki dla reguł sieci konta magazynu.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**lub usunięcie wyjątków nie ma żadnego wpływu.

#### <a name="cliv2"></a>CLIv2

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

1. Wyświetl wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Skonfiguruj wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Usuń wyjątki dla reguł sieci konta magazynu.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**lub usunięcie wyjątków nie ma żadnego wpływu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o punktach końcowych usługi sieciowej Azure w [punktach końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dig bardziej szczegółowe zabezpieczenia usługi Azure Storage w [przewodniku po zabezpieczeniach usługi Azure Storage](../blobs/security-recommendations.md).
