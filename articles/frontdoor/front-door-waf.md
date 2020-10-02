---
title: 'Samouczek: skalowanie i ochrona aplikacji sieci Web przy użyciu usług frontonu platformy Azure i zapory aplikacji sieci Web platformy Azure (WAF)'
description: W tym samouczku pokazano, jak używać zapory aplikacji sieci Web platformy Azure z usługą Azure front-drzwi.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 7c5e938f985296e0534ca6e2438cf3acedb0fb65
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626483"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Samouczek: szybka skalowanie i ochrona aplikacji sieci Web przy użyciu platformy Azure z przodu i zapory aplikacji sieci Web platformy Azure (WAF)

Wiele aplikacji sieci Web napotkało szybki wzrost ruchu w ostatnich tygodniach z powodu COVID-19. Te aplikacje sieci Web również wystąpiły przez gwałtowny wzrost ilości szkodliwego ruchu, w tym ataki typu "odmowa usługi". Istnieje skuteczny sposób skalowania aplikacji w celu przepięcia ruchu i ochrony przed atakami: Skonfiguruj drzwi platformy Azure z użyciem usługi Azure WAF jako akcelerację, buforowanie i warstwę zabezpieczeń przed aplikacją sieci Web. Ten artykuł zawiera wskazówki dotyczące uzyskiwania drzwi platformy Azure z systemem Azure WAF skonfigurowanych dla dowolnej aplikacji sieci Web, która działa w ramach platformy Azure lub poza nią. 

Użyjemy interfejsu wiersza polecenia platformy Azure, aby skonfigurować WAF w tym samouczku. Tę samą czynność można wykonać przy użyciu Azure Portal, Azure PowerShell, Azure Resource Manager lub interfejsów API REST platformy Azure. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Utwórz drzwi tylne.
> - Utwórz zasady usługi Azure WAF.
> - Skonfiguruj zestawy reguł dla zasad WAFymi.
> - Skojarz zasady WAF z przednimi drzwiami.
> - Skonfiguruj domenę niestandardową.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Instrukcje podane w tym samouczku korzystają z interfejsu wiersza polecenia platformy Azure. [Obejrzyj ten przewodnik](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true) , aby rozpocząć pracę z interfejsem wiersza polecenia platformy Azure.

  > [!TIP] 
  > Łatwa i szybka metoda rozpoczynania pracy w interfejsie wiersza polecenia platformy Azure to [bash w Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

- Upewnij się, że `front-door` rozszerzenie zostało dodane do interfejsu wiersza polecenia platformy Azure:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Aby uzyskać więcej informacji na temat poleceń używanych w tym samouczku, zobacz [Dokumentacja interfejsu wiersza polecenia platformy Azure dla drzwi zewnętrznych](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest&preserve-view=true).

## <a name="create-an-azure-front-door-resource"></a>Tworzenie zasobu frontonu platformy Azure

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: W pełni kwalifikowana nazwa domeny (FQDN) aplikacji, która ma być chroniona. Na przykład `myapplication.contoso.com`.

`--accepted-protocols`: Określa protokoły, które mają być obsługiwane przez platformę Azure na potrzeby aplikacji sieci Web. Na przykład `--accepted-protocols Http Https`.

`--name`: Nazwa zasobu usługi Azure front-drzwi.

`--resource-group`: Grupa zasobów, w której ma zostać umieszczony ten zasób platformy Azure. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [Zarządzanie grupami zasobów na platformie Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal).

W odpowiedzi, którą uzyskasz po uruchomieniu tego polecenia, poszukaj klucza `hostName` . Ta wartość będzie potrzebna w późniejszym kroku. `hostName`Jest nazwą DNS utworzonego zasobu platformy Azure z góry.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Utwórz profil usługi Azure WAF, który ma być używany z zasobami usług Azure Front-drzwiczk

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Nazwa nowych zasad usługi Azure WAF.

`--resource-group`: Grupa zasobów, w której ma zostać umieszczony ten zasób WAF. 

Poprzedni kod interfejsu wiersza polecenia spowoduje utworzenie zasad WAF, które są włączone i które są w trybie zapobiegania. 

> [!NOTE] 
> Możesz chcieć utworzyć zasady WAF w trybie wykrywania i sprawdzić, jak wykrywane są i rejestruje złośliwe żądania (bez ich blokowania) przed podjęciem decyzji o użyciu trybu ochrony.

W odpowiedzi, którą uzyskasz po uruchomieniu tego polecenia, poszukaj klucza `ID` . Ta wartość będzie potrzebna w późniejszym kroku. 

`ID`Pole powinno mieć następujący format:

**Identyfikator subskrypcji**/subscriptions//ResourceGroups/**Nazwa grupy zasobów**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF Nazwa zasad**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Dodaj zarządzane zestawy reguł do zasad WAF

Do zasad WAF można dodawać zarządzane zestawy reguł. Zarządzanym zestawem reguł jest zestaw reguł wbudowanych i zarządzanych przez firmę Microsoft, który pomaga chronić użytkownika przed zagrożeniami. W tym przykładzie dodawane są dwa zestawy reguł:
- Domyślny zestaw reguł, który pomaga chronić użytkownika przed typowymi zagrożeniami internetowymi. 
- Zestaw reguł ochrony bot, który pomaga chronić użytkownika przed złośliwym botów.

Dodaj domyślny zestaw reguł:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Dodaj zestaw reguł ochrony bot:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Nazwa określona dla zasobu usługi Azure WAF.

`--resource-group`: Grupa zasobów, w której został umieszczony zasób WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Kojarzenie zasad WAF z zasobami frontonu platformy Azure

W tym kroku utworzymy zasady WAF utworzone przy użyciu zasobu platformy Azure front-drzwi, który znajduje się przed aplikacją sieci Web:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Nazwa określona dla zasobu usługi Azure front-drzwi.

`--resource-group`: Grupa zasobów, w której został umieszczony zasób z przodu platformy Azure.

`--set`: To miejsce, w którym aktualizujesz `WebApplicationFirewallPolicyLink` atrybut `frontendEndpoint` skojarzonego z zasobem drzwi platformy Azure z nowymi zasadami WAFymi. Należy mieć identyfikator zasad WAF z odpowiedzi, która została utworzona podczas tworzenia profilu WAF wcześniej w tym samouczku.

 > [!NOTE] 
> Powyższy przykład dotyczy sytuacji, gdy nie używasz domeny niestandardowej. Jeśli nie korzystasz z żadnych domen niestandardowych w celu uzyskania dostępu do aplikacji sieci Web, możesz pominąć następną sekcję. W takim przypadku otrzymasz klientów, którzy `hostName` otrzymali podczas tworzenia zasobu platformy Azure z przodu. Użyjemy tego `hostName` , aby przejść do aplikacji sieci Web.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Skonfiguruj domenę niestandardową dla aplikacji sieci Web

Niestandardowa nazwa domeny aplikacji sieci Web jest używana przez klientów do odwoływania się do Twojej aplikacji. Na przykład www.contoso.com. Początkowo Ta niestandardowa nazwa domeny wskazywała lokalizację, w której została uruchomiona przed wprowadzeniem zewnętrznych drzwi platformy Azure. Po dodaniu platformy Azure z przodu i WAF do aplikacji, wpis DNS, który odpowiada tej domenie niestandardowej, powinien wskazywać na zasób frontonu platformy Azure. Możesz wprowadzić tę zmianę, ponownie mapując wpis na serwerze DNS na moje drzwi platformy Azure `hostName` zanotowane podczas tworzenia zasobu platformy Azure z przodu.

Określone kroki aktualizowania rekordów DNS będą zależeć od dostawcy usługi DNS. Jeśli używasz Azure DNS do hostowania nazwy DNS, możesz zapoznać się z dokumentacją, aby uzyskać [instrukcje dotyczące aktualizacji rekordu DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) i wskazywać drzwiczki frontonu platformy Azure `hostName` . 

Należy pamiętać o tym, że klienci mogą uzyskać dostęp do witryny sieci Web przy użyciu wierzchołka strefy (na przykład contoso.com). W takim przypadku należy użyć Azure DNS i jego [typu rekordu aliasu](https://docs.microsoft.com/azure/dns/dns-alias) , aby hostować nazwę DNS. 

Należy również zaktualizować konfigurację drzwi platformy Azure, aby dodać do niej [domenę niestandardową](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , tak aby była ona świadoma tego mapowania.

Na koniec, jeśli używasz domeny niestandardowej do uzyskiwania dostępu do aplikacji sieci Web i chcesz włączyć protokół HTTPS. Musisz [skonfigurować certyfikaty dla niestandardowej domeny na platformie Azure](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>Zablokuj aplikację sieci Web

Zalecamy upewnienie się, że tylko krawędzie z przodu platformy Azure mogą komunikować się z Twoją aplikacją sieci Web. Dzięki temu nikt nie będzie mógł ominąć ochrony przed drzwiami platformy Azure i bezpośrednio uzyskiwać dostęp do aplikacji. Aby wykonać tę blokadę, zobacz [Jak mogę zablokować dostęp do mojego zaplecza tylko do platformy Azure — przód?](https://docs.microsoft.com/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby używane w tym samouczku nie są już potrzebne, użyj polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) , aby usunąć grupę zasobów, drzwiczki z przodu i zasady WAF:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Nazwa grupy zasobów dla wszystkich zasobów używanych w tym samouczku.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozwiązać problemy z drzwiami wstępnymi, zobacz przewodniki dotyczące rozwiązywania problemów:

> [!div class="nextstepaction"]
> [Rozwiązywanie typowych problemów z routingiem](front-door-troubleshoot-routing.md)
