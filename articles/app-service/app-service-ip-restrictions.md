---
title: Azure App Service ograniczeń dostępu
description: Dowiedz się, jak zabezpieczyć aplikację w Azure App Service, ustawiając ograniczenia dostępu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 541af6d0051d06de5721b22616fbf1e2867b71d6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833368"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Konfigurowanie Azure App Service dostępu

Przez skonfigurowanie ograniczeń dostępu można zdefiniować listę zezwalania/odmowy uporządkowaną według priorytetu, która kontroluje dostęp sieciowy do aplikacji. Lista może zawierać adresy IP lub podsieci Virtual Network Azure. Jeśli istnieje co najmniej jeden wpis, niejawna odmowa *wszystkich* istnieje na końcu listy.

Możliwość ograniczeń dostępu działa ze wszystkimi obciążeniami hostowanych Azure App Service hostowanych. Obciążenia mogą obejmować aplikacje internetowe, aplikacje interfejsu API, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje.

Po zażądaniu do aplikacji adres FROM jest oceniany na tle reguł na liście ograniczeń dostępu. Jeśli adres FROM znajduje się w podsieci skonfigurowanej z punktami końcowymi usługi dla witryny Microsoft.Web, źródłową podsieć porówna się z regułami sieci wirtualnej na liście ograniczeń dostępu. Jeśli adres nie jest dozwolony na podstawie reguł na liście, usługa odpowiada kodem [stanu HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

Możliwość ograniczenia dostępu jest zaimplementowana w App Service frontonie, które są nadrzędne od hostów procesu roboczego, na których działa kod. W związku z tym ograniczenia dostępu są efektywnie listami kontroli dostępu do sieci (ACL).

Możliwość ograniczenia dostępu do aplikacji internetowej z sieci wirtualnej platformy Azure jest włączona przez [punkty końcowe usługi][serviceendpoints]. Za pomocą punktów końcowych usługi można ograniczyć dostęp do usługi wielodostępnej z wybranych podsieci. Ograniczanie ruchu do aplikacji hostowanych w chmurze nie App Service Environment. Jeśli jesteś w twojej App Service Environment, możesz kontrolować dostęp do aplikacji, stosując reguły adresów IP.

> [!NOTE]
> Punkty końcowe usługi muszą być włączone zarówno po stronie sieci, jak i dla usługi platformy Azure, z która jest włączona. Aby uzyskać listę usług platformy Azure, które obsługują punkty końcowe usługi, [zobacz Virtual Network końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagram przedstawiający przepływ ograniczeń dostępu.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Zarządzanie regułami ograniczeń dostępu w portalu

Aby dodać regułę ograniczeń dostępu do aplikacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie Azure Portal.

1. W okienku po lewej stronie wybierz pozycję **Sieć.**

1. W **okienku Sieć** w obszarze **Ograniczenia dostępu** wybierz pozycję Konfiguruj **ograniczenia dostępu.**

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Zrzut ekranu przedstawiający App Service opcji sieciowych w Azure Portal.":::

1. Na stronie **Ograniczenia dostępu** przejrzyj listę reguł ograniczeń dostępu zdefiniowanych dla aplikacji.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Zrzut ekranu przedstawiający stronę Ograniczenia dostępu w Azure Portal z listą reguł ograniczeń dostępu zdefiniowanych dla wybranej aplikacji.":::

   Lista zawiera wszystkie bieżące ograniczenia, które są stosowane do aplikacji. Jeśli aplikacja ma ograniczenia dotyczące sieci wirtualnej, w tabeli pokazano, czy punkty końcowe usługi są włączone dla witryny Microsoft.Web. Jeśli w aplikacji nie zdefiniowano żadnych ograniczeń, będzie ona dostępna z dowolnego miejsca.

### <a name="add-an-access-restriction-rule"></a>Dodawanie reguły ograniczeń dostępu

Aby dodać regułę ograniczeń dostępu do aplikacji, w okienku **Ograniczenia dostępu** wybierz pozycję **Dodaj regułę**. Po dodaniu reguły zaczyna ona obowiązywać natychmiast. 

Reguły są wymuszane w kolejności priorytetów, począwszy od najniższej liczby w **kolumnie** Priorytet. Niejawna *odmowa wszystkiego* obowiązywa po dodaniu nawet jednej reguły.

Podczas **tworzenia reguły** w okienku Dodawanie ograniczeń dostępu wykonaj następujące czynności:

1. W **obszarze Akcja** wybierz pozycję **Zezwalaj lub** **Odmów.**  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodawanie ograniczeń dostępu&quot;.":::

1. Opcjonalnie wprowadź nazwę i opis reguły.
1. W polu **Priorytet** wprowadź wartość priorytetu.
1. Z **listy rozwijanej** Typ wybierz typ reguły.

Różne typy reguł opisano w poniższych sekcjach.

> [!NOTE]
> - Istnieje limit 512 reguł ograniczeń dostępu. Jeśli potrzebujesz więcej niż 512 reguł ograniczeń dostępu, zalecamy rozważenie zainstalowania autonomicznego produktu zabezpieczającego, takiego jak Azure Front Door, Azure App Gateway lub alternatywna aplikacja WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Ustawianie reguły opartej na adresach IP

Postępuj zgodnie z procedurą, jak opisano w poprzedniej sekcji, ale z następującym dodatkiem:
* W kroku 4 z **listy rozwijanej** Typ wybierz pozycję **IPv4** **lub IPv6.** 

Określ blok **adresów IP w** notacji cidr (Classless Inter-Domain Routing) dla adresów IPv4 i IPv6. Aby określić adres, możesz użyć adresu podobnego do *1.2.3.4/32,* gdzie pierwsze cztery oktety reprezentują Twój adres IP, a */32 to* maska. Notacja CIDR protokołu IPv4 dla wszystkich adresów to 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, zobacz [Classless Inter-Domain Routing (Routing Inter-Domain klasy).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

#### <a name="set-a-service-endpoint-based-rule"></a>Ustawianie reguły opartej na punkcie końcowym usługi

* W kroku 4 z **listy rozwijanej** Typ wybierz pozycję **Virtual Network**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodaj ograniczenie&quot; z wybranym Virtual Network typie.":::

Określ listy **rozwijane Subskrypcja,** **Virtual Network**, **i** Podsieć, pasujące do tego, do czego chcesz ograniczyć dostęp.

Za pomocą punktów końcowych usługi można ograniczyć dostęp do wybranych podsieci sieci wirtualnej platformy Azure. Jeśli punkty końcowe usługi nie zostały jeszcze włączone w witrynie Microsoft.Web dla wybranej podsieci, zostaną one automatycznie włączone, chyba że zaznaczysz pole wyboru Ignoruj brakujące punkty końcowe usługi **Microsoft.Web.** Scenariusz, w którym można włączyć punkty końcowe usługi w aplikacji, ale nie w podsieci, zależy głównie od tego, czy masz uprawnienia do włączania ich w podsieci. 

Jeśli chcesz, aby inna osoba włączyć punkty końcowe usługi w podsieci, zaznacz pole wyboru Ignoruj brakujące punkty końcowe usługi **Microsoft.Web.** Aplikacja zostanie skonfigurowana dla punktów końcowych usługi w przewidywaniu ich późniejszego włączeniu w podsieci. 

Punktów końcowych usługi nie można używać do ograniczania dostępu do aplikacji uruchamianych w App Service Environment. Gdy aplikacja znajduje się w App Service Environment, możesz kontrolować dostęp do niego, stosując reguły dostępu IP. 

Punkty końcowe usługi mogą konfigurować aplikację za pomocą bram aplikacji lub innych urządzeń zapory aplikacji internetowej. Można również skonfigurować aplikacje wielowarstwowe z bezpiecznymi zakańcami. Aby uzyskać więcej informacji, zobacz [Networking features and App Service](networking-features.md) and Application Gateway integration with service endpoints (Funkcje sieciowe oraz integracja Application Gateway z [punktami końcowymi usługi).](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Punkty końcowe usługi nie są obecnie obsługiwane w przypadku aplikacji internetowych, które używają wirtualnego Secure Sockets Layer IP (VIP) protokołu IP (SSL).
>
#### <a name="set-a-service-tag-based-rule"></a>Ustawianie reguły opartej na tagach usługi

* W kroku 4 z **listy rozwijanej** Typ wybierz pozycję **Tag usługi**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodaj ograniczenie&quot; z wybranym typem tagu usługi.":::

Każdy tag usługi reprezentuje listę zakresów adresów IP z usług platformy Azure. Listę tych usług i linki do określonych zakresów można znaleźć w [dokumentacji tagów usługi][servicetags].

Wszystkie dostępne tagi usługi są obsługiwane w zasadach ograniczeń dostępu. Dla uproszczenia tylko lista najczęściej występujących tagów jest dostępna za pośrednictwem Azure Portal. Użyj Azure Resource Manager szablonów lub skryptów, aby skonfigurować bardziej zaawansowane reguły, takie jak reguły o zakresie regionalnym. Są to tagi dostępne za pośrednictwem Azure Portal:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Edytowanie reguły

1. Aby rozpocząć edytowanie istniejącej reguły ograniczeń dostępu, na stronie **Ograniczenia** dostępu wybierz regułę, którą chcesz edytować.

1. W **okienku Edytowanie ograniczeń dostępu** dokonaj zmian, a następnie wybierz pozycję **Aktualizuj regułę**. Zmiany są od razu skuteczne, w tym zmiany kolejności priorytetów.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Edytowanie ograniczeń dostępu&quot; w Azure Portal, pokazujące pola dla istniejącej reguły ograniczeń dostępu.":::

   > [!NOTE]
   > Podczas edytowania reguły nie można przełączać się między typami reguł. 

### <a name="delete-a-rule"></a>Usuwanie reguły

Aby usunąć regułę, na stronie **Ograniczenia** dostępu wybierz wielokropek (**...**) obok reguły, którą chcesz usunąć, a następnie wybierz pozycję **Usuń**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Zrzut ekranu strony &quot;Ograniczenia dostępu&quot; przedstawiający wielokropek &quot;Usuń&quot; obok reguły ograniczeń dostępu do usunięcia.":::

## <a name="access-restriction-advanced-scenarios"></a>Zaawansowane scenariusze ograniczeń dostępu
W poniższych sekcjach opisano niektóre zaawansowane scenariusze korzystające z ograniczeń dostępu.

### <a name="filter-by-http-header"></a>Filtrowanie według nagłówka HTTP

W ramach dowolnej reguły można dodać dodatkowe filtry nagłówka HTTP. Obsługiwane są następujące nazwy nagłówków HTTP:
* X-Forwarded-for
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Dla każdej nazwy nagłówka można dodać maksymalnie 8 wartości rozdzielonych przecinkami. Filtry nagłówka HTTP są oceniane po zastosowaniu samej reguły i oba warunki muszą mieć wartość true.

### <a name="multi-source-rules"></a>Reguły wielu źródeł

Reguły wielu źródeł umożliwiają łączenie maksymalnie 8 zakresów adresów IP lub 8 tagów usługi w ramach jednej reguły. Można jej użyć, jeśli masz więcej niż 512 zakresów adresów IP lub chcesz utworzyć reguły logiczne, w których wiele zakresów adresów IP jest połączonych z jednym filtrem nagłówka HTTP.

Reguły wielu źródeł są definiowane w taki sam sposób, jak reguły pojedynczego źródła, ale z każdym zakresem oddzielonym przecinkami.

Przykład programu PowerShell:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Blokuj pojedynczy adres IP

Po dodaniu pierwszej reguły ograniczeń dostępu usługa dodaje jawną regułę *Odmów* wszystkim z priorytetem 2147483647. W praktyce  jawna reguła Odmów wszystkim jest końcową regułą do wykonania i blokuje dostęp do dowolnego adresu IP, który nie jest jawnie dozwolony przez *regułę zezwalania.*

W scenariuszu, w którym chcesz jawnie zablokować pojedynczy adres IP lub blok adresów IP, ale zezwolić na dostęp do wszystkiego innego, dodaj jawną *regułę Zezwalaj na* wszystko.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;Ograniczenia dostępu&quot; w Azure Portal, pokazujący pojedynczy zablokowany adres IP.":::

### <a name="restrict-access-to-an-scm-site"></a>Ograniczanie dostępu do lokacji SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji można ograniczyć dostęp do witryny SCM używanej przez aplikację. Witryna SCM to zarówno punkt końcowy wdrażania w Sieci Web, jak i konsola Kudu. Ograniczenia dostępu do witryny SCM można przypisać oddzielnie z aplikacji lub użyć tego samego zestawu ograniczeń zarówno dla aplikacji, jak i witryny SCM. Po zaznaczeniu pola **wyboru Te same ograniczenia \<app name>** co wszystko jest puste. Jeśli to pole wyboru zostanie wyczyszszone, ustawienia lokacji SCM zostaną ponownie dotyczące. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;Ograniczenia dostępu&quot; w witrynie Azure Portal, pokazujący, że nie ustawiono żadnych ograniczeń dostępu dla witryny SCM lub aplikacji.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Ograniczanie dostępu do określonego Azure Front Door wystąpienia
Ruch z Azure Front Door do aplikacji pochodzi z dobrze znanego zestawu zakresów adresów IP zdefiniowanych w tagu usługi AzureFrontDoor.Backend. Za pomocą reguły ograniczeń tagu usługi można ograniczyć ruch do ruchu pochodzącego tylko z Azure Front Door. Aby upewnić się, że ruch pochodzi tylko z określonego wystąpienia, należy dodatkowo filtrować żądania przychodzące na podstawie unikatowego nagłówka HTTP, który Azure Front Door wysyła.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Zrzut ekranu przedstawiający stronę &quot;Ograniczenia dostępu&quot; w Azure Portal, pokazujący sposób dodawania Azure Front Door ograniczeń.":::

Przykład programu PowerShell:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Programowe zarządzanie regułami ograniczeń dostępu

Ograniczenia dostępu można dodać programowo, wykonując jedną z następujących czynności: 

* Użyj [interfejsu wiersza polecenia platformy Azure.](/cli/azure/webapp/config/access-restriction) Na przykład:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Użyj [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Na przykład:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Praca z tagami usługi, nagłówkami HTTP lub regułami wielu źródeł wymaga co najmniej wersji 5.7.0. Wersję zainstalowanego modułu możesz sprawdzić za pomocą: **Get-InstalledModule -Name Az**

Wartości można również ustawić ręcznie, wykonując jedną z następujących czynności:

* Użyj operacji [PUT interfejsu API REST](/rest/api/azure/) platformy Azure dla konfiguracji aplikacji w Azure Resource Manager. Lokalizacja tych informacji w Azure Resource Manager to:

  management.azure.com/subscriptions/ subskrypcji/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/ nazwa aplikacji **internetowej**/config/web?api-version=2020-06-01

* Użyj szablonu Resource Manager szablonu. Na przykład możesz użyć resources.azure.com i edytować blok ipSecurityRestrictions, aby dodać wymagany dane JSON.

  Składnia JSON dla wcześniejszego przykładu jest następująca:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  Składnia JSON dla zaawansowanego przykładu korzystającego z ograniczenia tagu usługi i nagłówka HTTP jest następująca:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Konfigurowanie Azure Functions dostępu

Ograniczenia dostępu są również dostępne dla aplikacji funkcji z taką samą funkcjonalnością jak App Service planów. Włączenie ograniczeń dostępu powoduje również wyłączenie edytora Azure Portal dla wszystkich niedozwolonego dostępu ip.

## <a name="next-steps"></a>Następne kroki
[Ograniczenia dostępu dla Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integracji z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
