---
title: Konfigurowanie reguły WAF ograniczeń adresów IP dla drzwi platformy Azure
description: Dowiedz się, jak skonfigurować regułę zapory aplikacji sieci Web, aby ograniczyć adresy IP dla istniejącego punktu końcowego drzwi platformy Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 60a4ef47bc30955c918983d54f613cbdb5cbed73
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746766"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurowanie reguły ograniczeń adresów IP za pomocą zapory aplikacji sieci Web dla drzwi frontonu platformy Azure

W tym artykule opisano sposób konfigurowania reguł ograniczeń adresów IP w zaporze aplikacji sieci Web (WAF) dla drzwi frontonu platformy Azure przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub szablonu Azure Resource Manager.

Reguła kontroli dostępu opartej na adresie IP to Niestandardowa reguła WAF, która umożliwia kontrolowanie dostępu do aplikacji sieci Web. W tym celu należy określić listę adresów IP lub zakresy adresów IP w formacie Inter-Domain routingu, bez klas. Istnieją dwa typy zmiennych dopasowania w adresie IP, **RemoteAddr** i **SocketAddr**. RemoteAddr to oryginalny adres IP klienta, który jest zazwyczaj wysyłany za pośrednictwem nagłówka żądania X-forwardd-for. SocketAddr to źródłowy adres IP WAF widzi. Jeśli użytkownik znajduje się za serwerem proxy, SocketAddr jest często adresem serwera proxy.

Domyślnie aplikacja sieci Web jest dostępna z Internetu. Jeśli chcesz ograniczyć dostęp do klientów z listy znanych adresów IP lub zakresów adresów IP, możesz utworzyć regułę dopasowania adresów IP, która zawiera listę adresów IP jako pasujące wartości i zestawy, do "not" (Negate jest true) i akcję do **zablokowania**. Po zastosowaniu reguły ograniczeń adresów IP żądania, które pochodzą z adresów spoza tej listy dozwolonych, odbierają niedostępną odpowiedź 403.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurowanie zasad WAFymi za pomocą Azure Portal

### <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil z przodu platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie zewnętrznych drzwi dla globalnej aplikacji sieci Web o wysokiej](../../frontdoor/quickstart-create-front-door.md)dostępności.

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

1. Na Azure Portal wybierz pozycję **Utwórz zasób**, wpisz  **Zapora aplikacji sieci Web** w polu wyszukiwania, a następnie wybierz opcję **Zapora aplikacji sieci Web (WAF)**.
2. Wybierz przycisk **Utwórz**.
3. Na stronie **Tworzenie zasad WAF** Użyj następujących wartości, aby ukończyć kartę **podstawowe** :
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Zasady dla     |Globalne WAF (tylne drzwi)|
   |Subskrypcja     |Wybierz swoją subskrypcję|
   |Grupa zasobów     |Wybierz grupę zasobów, w której znajduje się przód.|
   |Nazwa zasady     |Wpisz nazwę zasad|
   |Stan zasad     |Enabled (Włączony)|

   Wybierz pozycję **Dalej: ustawienia zasad**

1. Na karcie **Ustawienia zasad** wybierz pozycję **zapobieganie**. Dla **treści odpowiedzi blokowej** typ *został zablokowany* . Możesz zobaczyć, że reguła niestandardowa jest aktywna.
2. Wybierz kolejno pozycje **Dalej: reguły zarządzane**.
3. Wybierz pozycję **Dalej: Reguły niestandardowe**.
4. Wybierz pozycję **Dodaj regułę niestandardową**.
5. Na stronie **Dodaj regułę niestandardową** Użyj następujących wartości testowych, aby utworzyć regułę niestandardową:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Nazwa reguły niestandardowej     |FdWafCustRule|
   |Stan     |Enabled (Włączony)|
   |Typ reguły     |Dopasowanie|
   |Priorytet    |100|
   |Typ dopasowania     |Adres IP|
   |Match — zmienna|RemoteAddr|
   |Operacja|Nie zawiera|
   |Adres IP lub zakres|10.10.10.0/24|
   |Następnie|Odmów ruchu|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Reguła niestandardowa":::

   Wybierz pozycję **Dodaj**.
6. Wybierz pozycję **Dalej: skojarzenie**.
7. Wybierz pozycję **Dodaj hosta frontonu**.
8. W obszarze **host frontonu** wybierz hosta frontonu i wybierz pozycję **Dodaj**.
9. Wybierz pozycję **Przejrzyj i utwórz**.
10. Po zakończeniu walidacji zasad wybierz pozycję **Utwórz**.

### <a name="test-your-waf-policy"></a>Testowanie zasad WAF

1. Po zakończeniu wdrażania zasad WAF przejdź do nazwy hosta frontonu z przodu.
2. Powinien zostać wyświetlony niestandardowy komunikat dotyczący blokady.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test reguły WAF":::

   > [!NOTE]
   > Prywatny adres IP został celowo użyty w regule niestandardowej w celu zagwarantowania, że reguła zostanie wyzwolona. W rzeczywistym wdrożeniu Utwórz reguły *zezwalania* i *odmowy* przy użyciu adresów IP dla konkretnej sytuacji.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurowanie zasad WAFymi za pomocą interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP Skonfiguruj środowisko interfejsu wiersza polecenia i Utwórz profil dla drzwi platformy Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurowanie środowiska interfejsu wiersza polecenia platformy Azure
1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w następujących poleceń interfejsu wiersza polecenia, a następnie zaloguj się do konta platformy Azure w sesji Cloud Shell, która zostanie otwarta. Po rozpoczęciu sesji wprowadź, `az extension add --name front-door` Aby dodać rozszerzenie frontu drzwi platformy Azure.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie w bash, zaloguj się do platformy Azure przy użyciu `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu platformy Azure z przodu
Utwórz profil z przodu platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie zewnętrznych drzwi dla globalnej aplikacji sieci Web o wysokiej](../../frontdoor/quickstart-create-front-door.md)dostępności.

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

Utwórz zasady WAF przy użyciu polecenia [AZ Network Front-drzwiczke WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . W poniższym przykładzie Zastąp nazwę zasad *IPAllowPolicyExampleCLI* unikatową nazwą zasad.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Dodaj niestandardową regułę kontroli dostępu IP

Użyj polecenia [AZ Network Front-WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) , aby dodać niestandardową regułę kontroli dostępu IP dla właśnie utworzonych zasad WAF.

W następujących przykładach:
-  Zastąp *IPAllowPolicyExampleCLI* własnymi utworzonymi wcześniej zasadami.
-  Zastąp wartości *IP-Address-Range-1*, *IP-Address-Range-2* własnym zakresem.

Najpierw utwórz regułę zezwalania IP dla zasad utworzonych w poprzednim kroku. 
> [!NOTE]
> **--Odłóż** jest wymagane, ponieważ reguła musi zawierać warunek dopasowania, który ma zostać dodany w następnym kroku.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Następnie Dodaj warunek dopasowania do reguły:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Znajdź identyfikator zasad WAF 
Znajdź identyfikator zasad WAF przy użyciu polecenia [AZ Network Front-drzwiczke WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Zastąp *IPAllowPolicyExampleCLI* w poniższym przykładzie z unikatowymi zasadami, które zostały utworzone wcześniej.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem frontonu na platformie Azure

Ustaw identyfikator *WebApplicationFirewallPolicyLink* drzwi frontonu platformy Azure na identyfikator zasad, używając polecenia [AZ Network Front-drzwiczk Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Zastąp *IPAllowPolicyExampleCLI* własnymi utworzonymi wcześniej zasadami.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zasady WAF są stosowane do **FrontendEndpoints [0]**. Zasady WAF można połączyć z dowolnym frontonem.
> [!Note]
> Właściwość **WebApplicationFirewallPolicyLink** należy ustawić tylko raz, aby połączyć zasady WAF z frontonem drzwi platformy Azure. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurowanie zasad WAFymi za pomocą Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP Skonfiguruj środowisko programu PowerShell i Utwórz profil z przodu platformy Azure.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Azure PowerShell zawiera zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../../azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure.

Możesz zainstalować program [Azure PowerShell](/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się do programu PowerShell przy użyciu poświadczeń platformy Azure, a następnie zainstaluj moduł AZ module.

1. Połącz się z platformą Azure przy użyciu następującego polecenia, a następnie zaloguj się przy użyciu interaktywnego okna dialogowego.
    ```
    Connect-AzAccount
    ```
 2. Przed zainstalowaniem modułu front-drzwi platformy Azure upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom następujące polecenie, a następnie otwórz ponownie program PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Zainstaluj moduł AZ. Usługa frontdoor za pomocą następującego polecenia. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu platformy Azure z przodu
Utwórz profil z przodu platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie zewnętrznych drzwi dla globalnej aplikacji sieci Web o wysokiej](../../frontdoor/quickstart-create-front-door.md)dostępności.

### <a name="define-an-ip-match-condition"></a>Zdefiniuj warunek dopasowania adresu IP
Użyj polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) , aby zdefiniować warunek dopasowania adresów IP.
W poniższym przykładzie Zastąp wartość *IP-Address-Range-1*, *IP-Address-Range-2* własnym zakresem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Tworzenie niestandardowej reguły zezwalania na adresy IP

Użyj polecenia [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) , aby zdefiniować akcję i ustawić priorytet. W poniższym przykładzie żądania spoza adresów IP klientów pasujących do listy zostaną zablokowane.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurowanie zasad WAFymi
Znajdź nazwę grupy zasobów, która zawiera profil "drzwi frontonu Azure" przy użyciu programu `Get-AzResourceGroup` . Następnie skonfiguruj zasady WAF z regułą adresów IP przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem frontonu na platformie Azure

Połącz obiekt zasad WAF z istniejącym hostem frontonu i zaktualizuj właściwości drzwi frontonu platformy Azure. Najpierw Pobierz obiekt z przodu platformy Azure za pomocą polecenia [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnie ustaw właściwość **WebApplicationFirewallPolicyLink** na identyfikator zasobu *$IPAllowPolicyExamplePS*, który został utworzony w poprzednim kroku przy użyciu polecenia [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zasady WAF są stosowane do **FrontendEndpoints [0]**. Zasady WAF można połączyć z dowolnym frontonem. Właściwość **WebApplicationFirewallPolicyLink** należy ustawić tylko raz, aby połączyć zasady WAF z frontonem drzwi platformy Azure. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurowanie zasad WAFymi przy użyciu szablonu Menedżer zasobów
Aby wyświetlić szablon, który tworzy zasady dla drzwi platformy Azure i zasady WAF z użyciem niestandardowych reguł ograniczeń adresów IP, przejdź do witryny [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil dla drzwi platformy Azure](../../frontdoor/quickstart-create-front-door.md).
