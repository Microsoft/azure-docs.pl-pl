---
title: Sieci wirtualne
titleSuffix: Azure Cognitive Services
description: Skonfiguruj zabezpieczenia warstwowe sieci dla zasobów Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: eaffa535b51b786a53f1e6cc35233c55dd837233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99989071"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurowanie sieci wirtualnych usług Azure Cognitive Services

Usługa Azure Cognitive Services udostępnia model zabezpieczeń warstwowych. Ten model umożliwia zabezpieczanie kont usług Cognitive Services w określonym podzestawie sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta. Można ograniczyć dostęp do zasobów za pomocą filtrowania żądań. Zezwalanie tylko na żądania pochodzące z określonych adresów IP, zakresów adresów IP lub z listy podsieci w [sieciach wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md).

Aplikacja, która uzyskuje dostęp do zasobu Cognitive Services, gdy reguły sieciowe są stosowane, wymagają autoryzacji. Autoryzacja jest obsługiwana z poświadczeniami [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) lub z prawidłowym kluczem interfejsu API.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta Cognitive Services domyślnie blokuje przychodzące żądania dla danych. Aby można było zezwolić na żądania w programie, należy spełnić jeden z następujących warunków:

> * Żądanie powinno pochodzić z usługi działającej w ramach platformy Azure Virtual Network (VNet) na liście dozwolonych podsieci docelowego konta Cognitive Services. Punkt końcowy w żądaniach pochodzących z sieci wirtualnej musi być ustawiony jako [niestandardową poddomeną](cognitive-services-custom-subdomains.md) konta Cognitive Services.
> * Lub żądanie powinno pochodzić z listy dozwolonych adresów IP.
>
> Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć zasób Cognitive Services, należy najpierw skonfigurować regułę, aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji. Możesz również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych zakresów publicznych adresów IP w Internecie, włączając połączenia z określonych klientów internetowych lub lokalnych.

Reguły sieciowe są wymuszane dla wszystkich protokołów sieciowych na platformie Azure Cognitive Services, w tym REST i WebSocket. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak konsole testów platformy Azure, należy skonfigurować jawne reguły sieciowe. Reguły sieci można stosować do istniejących zasobów Cognitive Services lub podczas tworzenia nowych zasobów Cognitive Services. Po zastosowaniu reguł sieci są one wymuszane dla wszystkich żądań.

## <a name="supported-regions-and-service-offerings"></a>Obsługiwane regiony i oferty usług

Sieci wirtualne (sieci wirtualnych) są obsługiwane w [regionach, w których są dostępne Cognitive Services](https://azure.microsoft.com/global-infrastructure/services/). Cognitive Services obsługuje Tagi usług dla konfiguracji reguł sieci. Wymienione poniżej usługi znajdują się w tagu usługi **CognitiveServicesManagement** .

> [!div class="checklist"]
> * Narzędzie do wykrywania anomalii
> * Przetwarzanie obrazów
> * Content Moderator
> * Custom Vision
> * Rozpoznawanie twarzy
> * Rozpoznawanie formularzy
> * Czytnik immersyjny
> * Language Understanding (LUIS)
> * Personalizacja
> * Usługi mowy
> * Analiza tekstu
> * QnA Maker
> * Tłumaczenie tekstu w usłudze Translator


> [!NOTE]
> Jeśli używasz usługi LUIS lub mowy, tag **CognitiveServicesManagement** umożliwia tylko Korzystanie z usługi przy użyciu zestawu SDK lub interfejsu API REST. Aby uzyskać dostęp do portalu LUIS i/lub mowy programu Speech Studio z sieci wirtualnej, należy użyć następujących tagów:  
> * **Usługi azureactivedirectory**
> * **AzureFrontDoor. frontonu**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Zmienianie domyślnej reguły dostępu do sieci

Domyślnie zasoby Cognitive Services akceptują połączenia od klientów w dowolnej sieci. Aby zawęzić dostęp do grupy wybranych sieci, należy najpierw zmienić akcję domyślną.

> [!WARNING]
> Wprowadzanie zmian w regułach sieci może mieć wpływ na możliwość łączenia się aplikacji z platformą Azure Cognitive Services. Ustawienie domyślnej reguły sieci na **Odmów** uniemożliwia dostęp do danych, chyba że stosowane są również określone reguły **sieciowe zezwalające na dostęp.** Należy pamiętać o udzieleniu dostępu do wszystkich dozwolonych sieci przy użyciu reguł sieci, zanim reguła domyślna zostanie zmieniona na odmawianie dostępu. Jeśli zezwolisz na wyświetlanie listy adresów IP dla sieci lokalnej, pamiętaj, aby dodać wszystkie możliwe wychodzące publiczne adresy IP z sieci lokalnej.

### <a name="managing-default-network-access-rules"></a>Zarządzanie domyślnymi regułami dostępu sieciowego

Można zarządzać domyślnymi regułami dostępu do sieci dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

   ![Opcja Sieć wirtualna](media/vnet/virtual-network-blade.png)

1. Aby domyślnie zablokować dostęp, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. Z ustawieniem **wybrane sieci** , niezależnie od skonfigurowanych **sieci wirtualnych** lub **zakresów adresów** — wszystkie dostępy są skutecznie odrzucane. W przypadku odmowy dostępu, żądania próbujące korzystać z zasobu Cognitive Services nie są dozwolone. Azure Portal, Azure PowerShell lub interfejs wiersza polecenia platformy Azure nadal może służyć do konfigurowania zasobu Cognitive Services.
1. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwalającą na dostęp ze **wszystkich sieci**.

   ![Odmowa sieci wirtualnych](media/vnet/virtual-network-deny.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Wyświetl stan domyślnej reguły dla zasobu Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Wyświetl stan domyślnej reguły dla zasobu Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ustaw regułę domyślną, aby domyślnie odrzucać dostęp do sieci.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ustaw domyślną regułę zezwalającą na dostęp do sieci.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Można skonfigurować zasoby Cognitive Services w taki sposób, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w innej subskrypcji, łącznie z subskrypcjami należącymi do innej dzierżawy Azure Active Directory.

Włącz [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Cognitive Services w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Cognitive Services. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla zasobu Cognitive Services, które zezwalają na odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pomocą tych reguł sieci, muszą nadal spełnić wymagania dotyczące autoryzacji Cognitive Services zasobu, aby uzyskać dostęp do danych.

Każdy zasób Cognitive Services obsługuje do 100 reguł sieci wirtualnej, które mogą być połączone z [regułami sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do zasobu Cognitive Services, użytkownik musi mieć odpowiednie uprawnienia do dodawanych podsieci. Wymagane uprawnienie jest domyślną rolą *współautor* lub *Cognitive Services rolą współautor* . Wymagane uprawnienia można także dodać do niestandardowych definicji ról.

Zasób Cognitive Services i sieci wirtualne z udzielonym dostępem mogą znajdować się w różnych subskrypcjach, w tym w przypadku subskrypcji należących do innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy Azure Active Directory, są obecnie obsługiwane tylko za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pomocą Azure Portal, ale mogą one być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Można zarządzać regułami sieci wirtualnej dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do sieci wirtualnej przy użyciu istniejącej reguły sieci, w obszarze **sieci wirtualne** wybierz pozycję **Dodaj istniejącą sieć wirtualną**.

   ![Dodaj istniejącą sieć wirtualną](media/vnet/virtual-network-add-existing.png)

1. Wybierz opcje **sieci wirtualne** i **podsieci** , a następnie wybierz pozycję **Włącz**.

   ![Dodaj szczegóły istniejącej sieci wirtualnej](media/vnet/virtual-network-add-existing-details.png)

1. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, wybierz pozycję **Dodaj nową sieć wirtualną**.

   ![Dodaj nową sieć wirtualną](media/vnet/virtual-network-add-new.png)

1. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usługi Azure Cognitive Services nie był wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować w ramach tej operacji.
    >
    > Obecnie do wyboru podczas tworzenia reguły są wyświetlane tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz pozycję **...** , aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   ![Usuń sieć wirtualną](media/vnet/virtual-network-remove.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Aby dodać regułę sieciową dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-name".

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Wyświetl listę reguł sieci wirtualnej.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Możesz użyć parametru **subskrypcji** , aby pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

1. Usuń regułę sieci dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z zakresu internetowych adresów IP

Można skonfigurować zasoby Cognitive Services, aby zezwolić na dostęp z określonych publicznych zakresów adresów IP. Ta konfiguracja zapewnia dostęp do określonych usług i sieci lokalnych, co skutecznie blokuje ogólny ruch internetowy.

Podaj dozwolone zakresy adresów internetowych przy użyciu [notacji CIDR](https://tools.ietf.org/html/rfc4632) w postaci `16.17.18.0/24` lub jako indywidualne adresy IP, takie jak `16.17.18.19` .

   > [!Tip]
   > Małe zakresy adresów przy użyciu prefiksów "/31" lub "/32" nie są obsługiwane. Te zakresy należy skonfigurować przy użyciu poszczególnych reguł adresów IP.

Reguły sieci IP są dozwolone tylko dla **publicznych** adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od `10.*` , `172.16.*`  -  `172.31.*` i `192.168.*` .

W tej chwili są obsługiwane tylko adresy IPV4. Każdy zasób Cognitive Services obsługuje do 100 reguł sieci adresów IP, które mogą być połączone z [regułami sieci wirtualnej](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu z sieci lokalnych

Aby udzielić dostępu z sieci lokalnych do zasobu Cognitive Services przy użyciu reguły sieci adresu IP, należy zidentyfikować adresy IP, które są używane przez sieć. Skontaktuj się z administratorem sieci, aby uzyskać pomoc.

Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md) lokalnie do publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, musisz zidentyfikować adresy IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych. Każda z nich jest stosowana do ruchu usługi platformy Azure, gdy ruch przechodzi do Microsoft Azure sieci szkieletowej. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Można zarządzać regułami sieci IP dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do zakresu internetowego adresu IP, wprowadź adres IP lub zakres adresów (w [formacie CIDR](https://tools.ietf.org/html/rfc4632)) w obszarze   >  **zakres adresów** zapory. Akceptowane są tylko prawidłowe publiczne adresy IP (niezarezerwowane).

   ![Dodaj zakres adresów IP](media/vnet/virtual-network-add-ip-range.png)

1. Aby usunąć regułę sieci IP, wybierz <span class="docon docon-delete x-hidden-focus"></span> ikonę kosza obok zakresu adresów.

   ![Usuń zakres adresów IP](media/vnet/virtual-network-delete-ip-range.png)

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Wyświetl listę reguł sieci adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Wyświetl listę reguł sieci adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Dodawanie reguły sieci dla indywidualnego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciową dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieciową dla indywidualnego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieciową dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="use-private-endpoints"></a>Używanie prywatnych punktów końcowych

Możesz użyć [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla zasobów Cognitive Services, aby umożliwić klientom w sieci wirtualnej (VNET) bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla zasobu Cognitive Services. Ruch sieciowy między klientami w sieci wirtualnej a zasobem przechodzącym przez sieć wirtualną i prywatnym łączem w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Prywatne punkty końcowe dla zasobów Cognitive Services pozwalają:

* Zabezpiecz zasób Cognitive Services, konfigurując zaporę do blokowania wszystkich połączeń w publicznym punkcie końcowym usługi Cognitive Services.
* Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając zablokowanie eksfiltracji danych z sieci wirtualnej.
* Bezpiecznie łącz się z zasobami Cognitive Services z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

### <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla zasobu platformy Azure w sieci [wirtualnej](../virtual-network/virtual-networks-overview.md). Utworzenie prywatnego punktu końcowego dla zasobu Cognitive Services zapewnia bezpieczną łączność między klientami w sieci wirtualnej a zasobem. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Cognitive Services używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą za pośrednictwem prywatnego punktu końcowego, używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób. Wyjątkiem są usługi mowy, które wymagają oddzielnego punktu końcowego. Zapoznaj się z sekcją dotyczącą [prywatnych punktów końcowych za pomocą usługi Speech Services](#private-endpoints-with-the-speech-services). Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez zasób Cognitive Services, w tym REST.

Prywatne punkty końcowe można utworzyć w podsieciach, które korzystają z [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md). Klienci w podsieci mogą łączyć się z jednym zasobem Cognitive Services przy użyciu prywatnego punktu końcowego, podczas gdy używają punktów końcowych usługi do uzyskiwania dostępu do innych.

Gdy utworzysz prywatny punkt końcowy dla zasobu Cognitive Services w sieci wirtualnej, żądanie zgody jest wysyłane do zatwierdzenia dla Cognitive Services właściciela zasobu. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem zasobu, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele zasobów Cognitive Services mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty "*prywatne punkty końcowe*" dla zasobu Cognitive Services w [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Podczas tworzenia prywatnego punktu końcowego należy określić zasób Cognitive Services, z którym jest nawiązywane połączenie. Aby uzyskać więcej informacji na temat tworzenia prywatnego punktu końcowego, zobacz:

* [Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal](../private-link/create-private-endpoint-portal.md)
* [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md)
* [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi

Klienci w sieci wirtualnej korzystającej z prywatnego punktu końcowego powinni używać tych samych parametrów połączenia dla zasobu Cognitive Services, co w przypadku klientów nawiązujących połączenie z publicznym punktem końcowym. Wyjątkiem są usługi mowy, które wymagają oddzielnego punktu końcowego. Zapoznaj się z sekcją dotyczącą [prywatnych punktów końcowych za pomocą usługi Speech Services](#private-endpoints-with-the-speech-services). Firma Microsoft korzysta z rozpoznawania nazw DNS, aby automatycznie kierować połączenia z sieci wirtualnej do zasobu Cognitive Services za pośrednictwem prywatnego linku. 

Utworzymy [prywatną strefę DNS](../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z domyślnymi aktualizacjami dla prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS. W sekcji dotyczącej [zmian w systemie DNS](#dns-changes-for-private-endpoints) poniżej opisano aktualizacje wymagane dla prywatnych punktów końcowych.

### <a name="private-endpoints-with-the-speech-services"></a>Prywatne punkty końcowe z usługami mowy

Zobacz [Korzystanie z usług mowy z prywatnymi punktami końcowymi udostępnionymi przez link prywatny platformy Azure](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla zasobu Cognitive Services zostanie zaktualizowany do aliasu w poddomenie z prefiksem "*privatelink*". Domyślnie tworzymy również [prywatną strefę DNS](../dns/private-dns-overview.md), odpowiadającą poddomeną "*privatelink*", z rekordem zasobów DNS dla prywatnych punktów końcowych.

W przypadku rozpoznania adresu URL punktu końcowego spoza sieci wirtualnej przy użyciu prywatnego punktu końcowego jest on rozpoznawany jako publiczny punkt końcowy zasobu Cognitive Services. Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, adres URL punktu końcowego jest rozpoznawany jako adres IP prywatnego punktu końcowego.

Takie podejście umożliwia dostęp do Cognitive Services zasobu przy użyciu tych samych parametrów połączenia dla klientów w sieci wirtualnej, w których znajdują się prywatne punkty końcowe i klienci spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą być w stanie rozpoznać w pełni kwalifikowaną nazwę domeny (FQDN) dla punktu końcowego zasobu Cognitive Services na adres IP prywatnego punktu końcowego. Skonfiguruj serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer DNS, aby rozpoznawał Cognitive Services nazwę zasobu w poddomenie "privatelink" do prywatnego adresu IP punktu końcowego. Można to zrobić przez delegowanie poddomeny "privatelink" do prywatnej strefy DNS sieci wirtualnej lub skonfigurowanie strefy DNS na serwerze DNS oraz dodanie rekordu A usługi DNS.

Aby uzyskać więcej informacji na temat konfigurowania własnego serwera DNS do obsługi prywatnych punktów końcowych, zapoznaj się z następującymi artykułami:

* [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [Konfiguracja DNS dla prywatnych punktów końcowych](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Ceny

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Następne kroki

* Poznaj różne [Cognitive Services platformy Azure](./what-are-cognitive-services.md)
* Dowiedz się więcej o [punktach końcowych usługi Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)
