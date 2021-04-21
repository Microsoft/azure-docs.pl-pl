---
title: 'Samouczek: skalowanie i ochrona aplikacji internetowej przy użyciu Azure Front Door i Azure Web Application Firewall (WAF)'
description: Ten samouczek pokazuje, jak używać usługi Azure Web Application Firewall z Azure Front Door service.
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
ms.openlocfilehash: d315fa5b588c6e5f2e4643ca18626e400e6ca01b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785653"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Samouczek: szybkie skalowanie i ochrona aplikacji internetowej przy użyciu Azure Front Door i Azure Web Application Firewall (WAF)

W wielu aplikacjach internetowych w ostatnich tygodniach wystąpił szybki wzrost ruchu z powodu coVID-19. Te aplikacje internetowe również mają gwałtowny wzrost złośliwego ruchu, w tym ataki typu "odmowa usługi". Istnieje skuteczny sposób skalowania aplikacji na zewnątrz pod względem skoków ruchu i ochrony przed atakami: skonfigurowanie usługi Azure Front Door przy użyciu usługi Azure WAF jako warstwy przyspieszania, buforowania i zabezpieczeń przed aplikacją internetową. Ten artykuł zawiera wskazówki dotyczące sposobu Azure Front Door z usługą Azure WAF skonfigurowaną dla dowolnej aplikacji internetowej, która działa na platformie Azure lub poza platformą Azure. 

W tym samouczku skonfigurujemy aplikację WAF przy użyciu interfejsu wiersza polecenia platformy Azure. To samo można osiągnąć przy użyciu interfejsów API REST Azure Portal, Azure PowerShell, Azure Resource Manager lub interfejsów API REST platformy Azure. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Utwórz Front Door.
> - Tworzenie zasad usługi Azure WAF.
> - Konfigurowanie zestawów reguł dla zasad WAF.
> - Kojarzenie zasad WAF z Front Door.
> - Konfigurowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Instrukcje w tym samouczku korzystają z interfejsu wiersza polecenia platformy Azure. [Wyświetl ten przewodnik,](/cli/azure/get-started-with-azure-cli) aby rozpocząć pracę z interfejsem wiersza polecenia platformy Azure.

  > [!TIP] 
  > Łatwe i szybkie rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure jest korzystanie z powłoki Bash w [Azure Cloud Shell.](../cloud-shell/quickstart.md)

- Upewnij się, że `front-door` rozszerzenie jest dodane do interfejsu wiersza polecenia platformy Azure:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Aby uzyskać więcej informacji na temat poleceń używanych w tym samouczku, zobacz Azure [CLI reference for Front Door](/cli/azure/ext/front-door)(Informacje o interfejsie wiersza polecenia platformy Azure dla Front Door ).

## <a name="create-an-azure-front-door-resource"></a>Tworzenie zasobu Azure Front Door zasobów

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: w pełni kwalifikowana nazwa domeny (FQDN) aplikacji, którą chcesz chronić. Na przykład `myapplication.contoso.com`.

`--accepted-protocols`: określa protokoły, które Azure Front Door do obsługi aplikacji internetowej. Na przykład `--accepted-protocols Http Https`.

`--name`: nazwa zasobu Azure Front Door zasobów.

`--resource-group`: grupa zasobów, w której chcesz umieścić ten Azure Front Door zasobów. Aby dowiedzieć się więcej na temat grup zasobów, zobacz [Zarządzanie grupami zasobów na platformie Azure.](../azure-resource-manager/management/manage-resource-groups-portal.md)

W odpowiedzi, która zostanie otrzymasz po uruchomieniu tego polecenia, poszukaj klucza `hostName` . Ta wartość będzie potrzebna w późniejszym kroku. To `hostName` nazwa DNS utworzonego zasobu Azure Front Door DNS.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Tworzenie profilu usługi Azure WAF do użycia z Azure Front Door zasobów

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: nazwa nowych zasad usługi Azure WAF.

`--resource-group`: grupa zasobów, w której chcesz umieścić ten zasób WAF. 

Poprzedni kod interfejsu wiersza polecenia utworzy zasady WAF, które są włączone i są w trybie zapobiegania. 

> [!NOTE] 
> Przed podjęciem decyzji o użyciu trybu ochrony warto utworzyć zasady WAF w trybie wykrywania i obserwować, jak wykrywa i rejestruje złośliwe żądania (bez blokowania ich).

W odpowiedzi, która zostanie otrzymasz po uruchomieniu tego polecenia, poszukaj klucza `ID` . Ta wartość będzie potrzebna w późniejszym kroku. 

Pole `ID` powinno mieć ten format:

/subscriptions/**identyfikator subskrypcji**/resourcegroups/**nazwa** grupy zasobów /providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**Nazwa** zasad aplikacji internetowej

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Dodawanie zarządzanych zestawów reguł do zasad WAF

Zarządzane zestawy reguł można dodać do zasad WAF. Zarządzany zestaw reguł to zestaw reguł budowaną i zarządzaną przez firmę Microsoft, która pomaga chronić cię przed klasą zagrożeń. W tym przykładzie dodajemy dwa zestawy reguł:
- Domyślny zestaw reguł, który pomaga chronić przed powszechnymi zagrożeniami internetowymi. 
- Zestaw reguł ochrony przed botami, który pomaga chronić przed złośliwymi botami.

Dodaj domyślny zestaw reguł:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Dodaj zestaw reguł ochrony przed botami:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: nazwa określona dla zasobu usługi Azure WAF.

`--resource-group`: grupa zasobów, w która umieszczono zasób WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Kojarzenie zasad WAF z zasobem Azure Front Door sieci Szkieletowej

W tym kroku skojarzymy utworzone zasady aplikacji internetowej z zasobem usługi Azure Front Door, który znajduje się przed Twoją aplikacją internetową:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: nazwa określona dla zasobu Azure Front Door zasobów.

`--resource-group`: grupa zasobów, w Azure Front Door zasobu.

`--set`: jest to miejsce, w którym aktualizujesz atrybut skojarzony z `WebApplicationFirewallPolicyLink` `frontendEndpoint` zasobem aplikacji Azure Front Door przy użyciu nowych zasad aplikacji sieci Szkieletowej. Identyfikator zasad aplikacji sieci Szkieletowej powinien pochodzić z odpowiedzi, która została utworzona podczas tworzenia profilu aplikacji sieci Szkieletowej wcześniej w tym samouczku.

 > [!NOTE] 
> Poprzedni przykład ma zastosowanie, gdy nie używasz domeny niestandardowej. Jeśli nie używasz żadnych domen niestandardowych do uzyskiwania dostępu do aplikacji internetowych, możesz pominąć następną sekcję. W takim przypadku otrzymasz klientom informacje uzyskane podczas tworzenia zasobu `hostName` Azure Front Door zasobów. Użyje tej `hostName` funkcji, aby przejść do aplikacji internetowej.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Konfigurowanie domeny niestandardowej dla aplikacji internetowej

Niestandardowa nazwa domeny aplikacji internetowej jest nazwą używaną przez klientów do odwołującej się do aplikacji. Na przykład www.contoso.com. Początkowo ta niestandardowa nazwa domeny wskazuje lokalizację, w której była uruchomiona, przed wprowadzeniem Azure Front Door. Po dodaniu Azure Front Door aplikacji sieci Web do aplikacji wpis DNS odpowiadający tej domenie niestandardowej powinien wskazać Azure Front Door aplikacji. Tę zmianę można wprowadzić, ponownie mapować wpis na serwerze DNS na Azure Front Door zanotowany podczas tworzenia Azure Front Door `hostName` zasobów.

Konkretne kroki aktualizowania rekordów DNS będą zależeć od dostawcy usług DNS. Jeśli używasz usługi Azure DNS do hostować nazwę DNS, możesz zapoznać się z dokumentacją, aby uzyskać instrukcje dotyczące aktualizowania [rekordu DNS](../dns/dns-operations-recordsets-cli.md) i wskaż nazwę Azure Front Door `hostName` . 

Należy pamiętać o jednej ważnej rzeczy, jeśli klienci muszą uzyskać dostęp do witryny internetowej przy użyciu wierzchołka strefy (na przykład contoso.com). W takim przypadku należy użyć rekordu Azure DNS jego typu [rekordu aliasu,](../dns/dns-alias.md) aby hostować nazwę DNS. 

Musisz również zaktualizować konfigurację Azure Front Door, aby dodać do niego domenę niestandardową, aby była świadoma tego mapowania. [](./front-door-custom-domain.md)

Na koniec, jeśli używasz domeny niestandardowej, aby uzyskać dostęp do aplikacji internetowej i chcesz włączyć protokół HTTPS. Certyfikaty dla [domeny niestandardowej należy skonfigurować w programie Azure Front Door](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Blokowanie aplikacji internetowej

Zalecamy, aby tylko krawędzie Azure Front Door mogą komunikować się z aplikacją internetową. Dzięki temu nikt nie będzie w stanie pominąć Azure Front Door i bezpośrednio uzyskać dostępu do aplikacji. Aby wykonać tę blokadę, [zobacz Jak mogę zablokować](./front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-)dostęp do zaplecza tylko do Azure Front Door? .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów używanych w tym samouczku, użyj polecenia [az group delete,](/cli/azure/group#az_group_delete) aby usunąć grupę zasobów, Front Door i zasady WAF:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: nazwa grupy zasobów dla wszystkich zasobów używanych w tym samouczku.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozwiązywać problemy z Front Door, zobacz przewodniki dotyczące rozwiązywania problemów:

> [!div class="nextstepaction"]
> [Rozwiązywanie typowych problemów z routingiem](front-door-troubleshoot-routing.md)