---
title: Szybka skalowanie i ochrona aplikacji sieci Web przy użyciu usług frontonu platformy Azure i zapory aplikacji sieci Web platformy Azure (WAF) | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, jak używać zapory aplikacji sieci Web z usługą Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: duau
ms.openlocfilehash: a0252004b01e64b195b372d72682f6b777012258
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89535435"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Szybka skalowanie i ochrona aplikacji sieci Web przy użyciu usług frontonu platformy Azure i zapory aplikacji sieci Web platformy Azure (WAF)

Wiele aplikacji sieci Web miało szybki wzrost ruchu w ostatnich tygodniach związanych z COVID-19. Ponadto te aplikacje sieci Web również obsłużą wzrost złośliwego ruchu, w tym ataki typu "odmowa usługi". Efektywnym sposobem obsługi obu tych potrzeb, skalowanie w poziomie w celu przepięcia ruchu i ochrona przed atakami polega na skonfigurowaniu drzwi platformy Azure z użyciem usługi Azure WAF jako akceleracja, buforowanie i zabezpieczenia przed aplikacją sieci Web. Ten artykuł zawiera wskazówki dotyczące szybkiego uzyskiwania tych drzwi platformy Azure za pomocą Instalatora usługi Azure WAF dla wszystkich aplikacji sieci Web działających na platformie Azure lub poza nią. 

Będziemy używać interfejsu wiersza polecenia platformy Azure w celu skonfigurowania WAF w tym samouczku, ale wszystkie te kroki są również w pełni obsługiwane w Azure Portal, Azure PowerShell, Azure ARM i Azure REST API. 

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

Instrukcje podane w tym blogu używają interfejsu wiersza polecenia platformy Azure. Obejrzyj ten przewodnik, aby [rozpocząć pracę z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Porada: łatwo & szybko rozpocząć pracę z interfejsem wiersza polecenia platformy Azure, korzystając z usługi [bash w Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Upewnij się, że do interfejsu wiersza polecenia platformy Azure dodano rozszerzenie Front-drzwiczk

```azurecli-interactive 
az extension add --name front-door
```

Uwaga: Aby uzyskać więcej informacji na temat poleceń wymienionych poniżej, zapoznaj się z dokumentacją [interfejsu wiersza polecenia platformy Azure dla drzwi zewnętrznych](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Krok 1. Tworzenie zasobu platformy Azure front-drzwi (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--zaplecze**: adres zaplecza to w pełni kwalifikowana nazwa domeny (FQDN) aplikacji, która ma być chroniona. Na przykład myapplication.contoso.com

**--Accept-Protocols**: akceptowane protokoły określają, jakie protokoły mają być obsługiwane przez AFD dla aplikacji sieci Web. Przykładem może być--Accept-Protocol protokołu HTTP HTTPS.

**--name**: Określ nazwę zasobu AFD

**--Grupa zasobów**: Grupa zasobów, w której ma zostać umieszczony ten zasób AFD.  Aby dowiedzieć się więcej na temat grup zasobów, odwiedź stronę Zarządzanie grupami zasobów na platformie Azure

W odpowiedzi na pomyślne wykonanie tego polecenia poszukaj klucza "hostName" i zanotuj jego wartość, aby można było jej użyć w późniejszym kroku. Nazwa hosta jest nazwą DNS utworzonego zasobu AFD

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Krok 2. Tworzenie profilu usługi Azure WAF do użycia z zasobami platformy Azure dla drzwi

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--Name Podaj nazwę zasad usługi Azure WAF

--Resource-Grupuj grupę zasobów, w której chcesz umieścić ten zasób WAF. 

Powyższy kod interfejsu wiersza polecenia spowoduje utworzenie zasad WAF, które są włączone i są w trybie zapobiegania. 

Uwaga: Możesz również utworzyć WAF w trybie wykrywania i obserwować sposób wykrywania & rejestrowania złośliwych żądań (bez blokowania) przed podjęciem decyzji o zmianie trybu ochrony.

W odpowiedzi na pomyślne wykonanie tego polecenia poszukaj klucza "ID" i zanotuj jego wartość, która zostanie użyta w późniejszym kroku. Pole identyfikatora powinno mieć format

**Identyfikator subskrypcji**/subscriptions//ResourceGroups/**Nazwa grupy zasobów**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF Nazwa zasad**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Krok 3. Dodawanie zarządzanych zestawów reguł do tych zasad WAF

W zasadach WAF można dodawać zarządzane zestawy reguł, które są zestawem zasad skompilowanym i zarządzanym przez firmę Microsoft, a także udostępniają ochronę przed całym klasą zagrożeń. W tym przykładzie dodawane są dwa takie zestaw reguł (1) domyślny zestaw reguł, który chroni przed typowymi zagrożeniami internetowymi i (2) zestaw reguł ochrony bot, który chroni przed złośliwymi botów

(1) Dodaj domyślny zestaw reguł

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Dodaj zestaw reguł bot Manager

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--Policy-Nazwij nazwę nadaną dla zasobu usługi Azure WAF

--Resource-Group grupę zasobów, w której został umieszczony ten zasób WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Krok 4. Kojarzenie zasad WAF z zasobem AFD

W tym kroku zostanie utworzone skojarzenie zasad WAF utworzonych za pomocą zasobu AFD, który znajduje się przed aplikacją sieci Web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--Nazwij nazwę określoną dla zasobu AFD

--Resource-Group grupę zasobów, w której został umieszczony zasób z przodu platformy Azure.

--Ustaw to miejsce, w którym aktualizujesz atrybut WebApplicationFirewallPolicyLink dla frontendEndpoint skojarzonego z zasobem AFD przy użyciu nowo utworzonych zasad WAF. Identyfikator zasad WAF można znaleźć na podstawie odpowiedzi z kroku #2 powyżej

Uwaga: powyższy przykład dotyczy sytuacji, gdy użytkownik nie korzysta z domeny niestandardowej.

Jeśli nie używasz żadnych domen niestandardowych w celu uzyskania dostępu do aplikacji sieci Web, możesz pominąć krok #5. W takim przypadku użytkownik końcowy będzie mógł podać nazwę hosta uzyskaną w kroku #1, aby przejść do aplikacji sieci Web.

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Krok 5. Konfigurowanie domeny niestandardowej dla aplikacji sieci Web

Początkowo niestandardowa nazwa domeny aplikacji sieci Web (używana przez klientów do odwoływania się do aplikacji, na przykład www.contoso.com), została skierowana do miejsca, w którym uruchomiono przed AFD. Po zmianie architektury dodanie AFD + WAF do aplikacji, wpis DNS odpowiadający tej domenie niestandardowej powinien teraz wskazywać na ten zasób AFD. Można to zrobić przez ponowne mapowanie tego wpisu na serwer DNS na nazwę hosta AFD zanotowaną w kroku #1.

Określone kroki aktualizowania rekordów DNS będą zależeć od dostawcy usługi DNS, ale jeśli używasz Azure DNS do hostowania nazwy DNS, możesz zapoznać się z dokumentacją dotyczącą czynności, aby [zaktualizować rekord DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) i wskazać nazwę hosta AFD. 

Jednym z najważniejszych rzeczy jest to, że jeśli chcesz, aby użytkownicy mogli przechodzić do witryny internetowej przy użyciu wierzchołka strefy, na przykład contoso.com, musisz użyć Azure DNS i [typu rekordu aliasu](https://docs.microsoft.com/azure/dns/dns-alias) , aby hostować nazwę DNS. 

Ponadto należy również zaktualizować konfigurację AFD, aby dodać do niej [tę domenę niestandardową](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , tak aby AFD zrozumieć to mapowanie.

Na koniec Jeśli używasz domeny niestandardowej do uzyskiwania dostępu do aplikacji sieci Web i chcesz włączyć protokół HTTPS, musisz mieć [certyfikaty dla konfiguracji domeny niestandardowej w AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Krok 6. zablokowanie aplikacji sieci Web

Jednym z opcjonalnych najlepszych rozwiązań jest upewnienie się, że tylko krawędzie AFD mogą komunikować się z aplikacją sieci Web. Ta akcja zapewni, że nikt nie może ominąć ochrony AFD i bezpośrednio uzyskiwać dostępu do aplikacji. Możesz to zrobić, odwiedzając [sekcję często zadawanych pytań](https://docs.microsoft.com/azure/frontdoor/front-door-faq) dotyczących usługi AFD i odwołując się do pytania dotyczącego blokowania zastępujący dostępu tylko przez AFD.
