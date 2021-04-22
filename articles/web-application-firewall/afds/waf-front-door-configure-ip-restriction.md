---
title: Konfigurowanie reguły WAF ograniczeń adresów IP dla Azure Front Door
description: Dowiedz się, jak skonfigurować regułę Web Application Firewall, aby ograniczyć adresy IP dla istniejącego Azure Front Door końcowego.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860855"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurowanie reguły ograniczeń adresów IP przy użyciu Web Application Firewall dla Azure Front Door

W tym artykule pokazano, jak skonfigurować reguły ograniczeń adresów IP w aplikacji Web Application Firewall (WAF) dla usługi Azure Front Door przy użyciu szablonu Azure Portal, interfejsu wiersza polecenia platformy Azure Azure PowerShell lub Azure Resource Manager szablonu.

Reguła kontroli dostępu oparta na adresach IP to niestandardowa reguła WAF, która umożliwia kontrolowanie dostępu do aplikacji internetowych. W tym celu należy określić listę adresów IP lub zakresów adresów IP w formacie Inter-Domain routingu Inter-Domain (CIDR). W przypadku dopasowania adresu IP istnieją dwa typy zmiennych dopasowania: **RemoteAddr** i **SocketAddr.** RemoteAddr to oryginalny adres IP klienta, który jest zwykle wysyłany za pośrednictwem nagłówka żądania X-Forwarded-For. SocketAddr to źródłowy adres IP, który widzi WAF. Jeśli użytkownik znajduje się za serwerem proxy, często jest to adres serwera proxy SocketAddr.

Domyślnie aplikacja internetowa jest dostępna z Internetu. Jeśli chcesz ograniczyć dostęp do klientów z listy znanych adresów IP lub zakresów adresów IP, możesz utworzyć regułę dopasowywania adresów IP, która zawiera listę adresów IP jako zgodne wartości i ustawia operator na wartość "Not" (negacja jest prawdziwa) i akcję Blokuj **.** Po zastosowaniu reguły ograniczeń adresów IP żądania pochodzące z adresów spoza tej listy dozwolonych otrzymają odpowiedź 403 Zabronione.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurowanie zasad WAF przy użyciu Azure Portal

### <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil Azure Front Door, zgodnie z instrukcjami opisanymi w przewodniku Szybki start: tworzenie Front Door dla [globalnej aplikacji internetowej o wysokiej dostępie.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

1. Na stronie Azure Portal utwórz zasób, wpisz **zaporę** aplikacji internetowej w polu wyszukiwania, **a** następnie wybierz pozycję **Web Application Firewall (WAF).**
2. Wybierz przycisk **Utwórz**.
3. Na stronie **Tworzenie zasad aplikacji internetowej** użyj następujących wartości, aby ukończyć **kartę Podstawy:**
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Zasady dla     |Globalnaaf (Front Door)|
   |Subskrypcja     |Wybierz swoją subskrypcję|
   |Grupa zasobów     |Wybierz grupę zasobów, w której znajduje Front Door zasobów.|
   |Nazwa zasady     |Wpisz nazwę zasad|
   |Stan zasad     |Enabled (Włączony)|

   Wybierz **pozycję Dalej: Ustawienia zasad**

1. Na karcie **Ustawienia zasad** wybierz pozycję **Zapobieganie**. W treści **blokuj odpowiedź** wpisz *You've been blocked! (Blokuj treść odpowiedzi) wpisz You've been blocked! (Zablokowano).* , aby zobaczyć, że reguła niestandardowa jest w mocy.
2. Wybierz **pozycję Dalej: Reguły zarządzane.**
3. Wybierz **pozycję Dalej: Reguły niestandardowe.**
4. Wybierz **pozycję Dodaj regułę niestandardową.**
5. Na stronie **Dodawanie reguły niestandardowej** użyj następujących wartości testowych, aby utworzyć regułę niestandardową:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Nazwa reguły niestandardowej     |FdWafCustRule|
   |Stan     |Enabled (Włączony)|
   |Typ reguły     |Dopasowanie|
   |Priorytet    |100|
   |Typ dopasowania     |Adres IP|
   |Dopasowanie zmiennej|RemoteAddr|
   |Operacja|Nie zawiera|
   |Adres IP lub zakres|10.10.10.0/24|
   |Następnie|Odrzucanie ruchu|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Reguła niestandardowa":::

   Wybierz pozycję **Dodaj**.
6. Wybierz pozycję **Dalej: Skojarzenie**.
7. Wybierz **pozycję Dodaj hosta frontonia.**
8. W **przypadku opcji Host frontendu** wybierz hosta frontonia, a następnie wybierz pozycję **Dodaj**.
9. Wybierz pozycję **Przejrzyj i utwórz**.
10. Po zakończeniu walidacji zasad wybierz pozycję **Utwórz**.

### <a name="test-your-waf-policy"></a>Testowanie zasad aplikacji sieci Szkieletowej

1. Po zakończeniu wdrażania zasad aplikacji sieci Web przejdź do nazwy Front Door frontonie.
2. Powinien zostać wyświetlony niestandardowy komunikat o blokadzie.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test reguły WAF":::

   > [!NOTE]
   > Prywatny adres IP został celowo użyty w regułę niestandardową w celu zagwarantowania, że reguła zostanie wyzwolona. W rzeczywistym wdrożeniu utwórz reguły *zezwalania i* *blokowania* przy użyciu adresów IP w konkretnej sytuacji.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurowanie zasad aplikacji sieci Szkieletowej przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP skonfiguruj środowisko interfejsu wiersza polecenia i utwórz profil Azure Front Door IP.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurowanie środowiska interfejsu wiersza polecenia platformy Azure
1. Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj w** poleceniach interfejsu wiersza polecenia, a następnie zaloguj się do konta platformy Azure w Cloud Shell, która zostanie otwarta. Po zakończeniu sesji wprowadź , `az extension add --name front-door` aby dodać Azure Front Door rozszerzenia.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie w powłoce Bash, zaloguj się do platformy Azure przy użyciu polecenia `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu Azure Front Door aplikacji
Utwórz profil Azure Front Door, zgodnie z instrukcjami opisanymi w przewodniku Szybki start: tworzenie Front Door dla [globalnej aplikacji internetowej o wysokiej dostępie.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

Utwórz zasady WAF za pomocą [polecenia az network front-door waf-policy create.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) W poniższym przykładzie zastąp nazwę zasad *IPAllowPolicyExampleCLI* unikatową nazwą zasad.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Dodawanie niestandardowej reguły kontroli dostępu do adresu IP

Użyj polecenia [az network front-door waf-policy custom-rule create,](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) aby dodać niestandardową regułę kontroli dostępu do adresu IP dla właśnie utworzonych zasad WAF.

W poniższych przykładach:
-  Zastąp *wartości IPAllowPolicyExampleCLI* utworzonymi wcześniej unikatowymi zasadami.
-  Zastąp *wartości ip-address-range-1*, *ip-address-range-2* własnym zakresem.

Najpierw utwórz regułę zezwalania na adresy IP dla zasad utworzonych w poprzednim kroku. 
> [!NOTE]
> **--odrocz** jest wymagany, ponieważ reguła musi mieć warunek dopasowania, który ma zostać dodany w następnym kroku.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Następnie dodaj warunek dopasowania do reguły:

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
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Znajdowanie identyfikatora zasad WAF 
Znajdź identyfikator zasad WAF za pomocą polecenia [az network front-door waf-policy show.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) Zastąp *wartości IPAllowPolicyExampleCLI* w poniższym przykładzie unikatowymi zasadami utworzonymi wcześniej.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem Azure Front Door frontonie

Ustaw identyfikator Azure Front Door *WebApplicationFirewallPolicyLink* na identyfikator zasad za pomocą polecenia [az network front-door update.](/cli/azure/network/front-door#az_network_front_door_update) Zastąp *wartości IPAllowPolicyExampleCLI* utworzonymi wcześniej unikatowymi zasadami.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zasady WAF są stosowane do **punktów frontendendu[0].** Zasady WAF można połączyć z dowolnym frontonie.
> [!Note]
> Należy ustawić właściwość **WebApplicationFirewallPolicyLink** tylko raz, aby połączyć zasady aplikacji internetowej z Azure Front Door frontonie. Kolejne aktualizacje zasad są automatycznie stosowane do frontonie.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurowanie zasad aplikacji sieci Szkieletowej przy użyciu Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP skonfiguruj środowisko programu PowerShell i utwórz profil Azure Front Door IP.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Azure PowerShell udostępnia zestaw polecenia cmdlet, które używają [modelu Azure Resource Manager](../../azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure.

Możesz zainstalować program [Azure PowerShell](/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się do programu PowerShell przy użyciu poświadczeń platformy Azure, a następnie zainstaluj moduł Az.

1. Połącz się z platformą Azure za pomocą następującego polecenia, a następnie użyj interaktywnego okna dialogowego, aby się zalogować.
    ```
    Connect-AzAccount
    ```
 2. Przed zainstalowaniem modułu Azure Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom następujące polecenie, a następnie otwórz ponownie program PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Zainstaluj moduł Az.FrontDoor przy użyciu następującego polecenia. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu Azure Front Door aplikacji
Utwórz profil Azure Front Door, zgodnie z instrukcjami opisanymi w przewodniku Szybki start: tworzenie Front Door dla globalnej aplikacji [internetowej o wysokiej dostępie.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definiowanie warunku dopasowania adresu IP
Użyj polecenia [New-AzFrontDoorWafMatchConditionObject,](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) aby zdefiniować warunek dopasowania adresu IP.
W poniższym przykładzie zastąp *wartości ip-address-range-1*, *ip-address-range-2* własnym zakresem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Tworzenie niestandardowej reguły zezwalania na adresy IP

Użyj polecenia [New-AzFrontDoorWafCustomRuleObject,](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) aby zdefiniować akcję i ustawić priorytet. W poniższym przykładzie żądania nie z adresów IP klientów, które pasują do listy, zostaną zablokowane.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurowanie zasad WAF
Znajdź nazwę grupy zasobów, która zawiera profil Azure Front Door przy `Get-AzResourceGroup` użyciu . Następnie skonfiguruj zasady WAF z regułą adresu IP przy użyciu [new-AzFrontDoorWafPolicy.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem Azure Front Door frontonie

Połącz obiekt zasad WAF z istniejącym hostem frontonia i zaktualizuj Azure Front Door właściwości. Najpierw pobierz obiekt Azure Front Door za pomocą [get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnie ustaw właściwość **WebApplicationFirewallPolicyLink** na identyfikator zasobu *$IPAllowPolicyExamplePS* utworzonego w poprzednim kroku za pomocą polecenia [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zasady WAF są stosowane do **punktów frontendendu[0].** Zasady WAF można połączyć z dowolnym frontonie. Właściwość **WebApplicationFirewallPolicyLink** należy ustawić tylko raz, aby połączyć zasady WAF z Azure Front Door frontonie. Kolejne aktualizacje zasad są automatycznie stosowane do frontonia.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurowanie zasad aplikacji sieci Web przy użyciu szablonu Resource Manager aplikacji
Aby wyświetlić szablon, który tworzy zasady aplikacji Azure Front Door i zasady aplikacji sieci Web z niestandardowymi regułami ograniczeń adresów IP, przejdź do [witryny GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak utworzyć Azure Front Door profil.](../../frontdoor/quickstart-create-front-door.md)
