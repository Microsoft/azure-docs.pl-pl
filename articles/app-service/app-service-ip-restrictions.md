---
title: Ograniczenia dostępu Azure App Service
description: Dowiedz się, jak zabezpieczyć aplikację w Azure App Service przez skonfigurowanie ograniczeń dostępu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 420dade645d1a4ee32bb888aecb76b033d5756e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731307"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Konfigurowanie ograniczeń dostępu Azure App Service

Konfigurując ograniczenia dostępu, można zdefiniować priorytetową listę dozwolonych/zablokowanych priorytetów, która kontroluje dostęp sieciowy do aplikacji. Lista może zawierać adresy IP lub podsieci Virtual Network platformy Azure. W przypadku co najmniej jednego wpisu na końcu listy istnieje niejawne *odmowa* .

Funkcja ograniczeń dostępu współpracuje ze wszystkimi obsługiwanymi Azure App Service obciążeniami. Obciążenia mogą obejmować aplikacje sieci Web, aplikacje interfejsu API, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje.

Gdy żądanie zostanie wysłane do aplikacji, adres od jest oceniany względem reguł na liście ograniczeń dostępu. Jeśli adres NADAWCy należy do podsieci, która jest skonfigurowana za pomocą punktów końcowych usługi do firmy Microsoft. Web, podsieć źródłowa jest porównywana z regułami sieci wirtualnej na liście ograniczeń dostępu. Jeśli adres nie zezwala na dostęp na podstawie reguł na liście, usługa odpowie z kodem stanu [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funkcja ograniczeń dostępu jest implementowana w ramach ról frontonu App Service, które są nadrzędne w stosunku do hostów procesów roboczych, na których działa kod. W związku z tym ograniczenia dostępu są skutecznymi listami kontroli dostępu do sieci (ACL).

Możliwość ograniczenia dostępu do aplikacji sieci Web z sieci wirtualnej platformy Azure jest włączana przez [punkty końcowe usługi][serviceendpoints]. Za pomocą punktów końcowych usługi można ograniczyć dostęp do usługi z wieloma dzierżawcami z wybranych podsieci. Nie działa w sposób ograniczający ruch do aplikacji hostowanych w App Service Environment. Jeśli jesteś w App Service Environment, możesz kontrolować dostęp do aplikacji, stosując reguły adresów IP.

> [!NOTE]
> Punkty końcowe usługi muszą być włączone zarówno po stronie sieci, jak i dla usługi platformy Azure, z którą są włączane. Aby uzyskać listę usług platformy Azure, które obsługują punkty końcowe usługi, zobacz [Virtual Network punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagram przepływu ograniczeń dostępu.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Zarządzanie regułami ograniczeń dostępu w portalu

Aby dodać regułę ograniczeń dostępu do aplikacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie Azure Portal.

1. W okienku po lewej stronie wybierz pozycję **Sieć**.

1. W okienku **Sieć** w obszarze **ograniczenia dostępu** wybierz pozycję **Konfiguruj ograniczenia dostępu**.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Zrzut ekranu przedstawiający okienko opcje sieci App Service w Azure Portal.":::

1. Na stronie **ograniczenia dostępu** Przejrzyj listę reguł ograniczeń dostępu, które są zdefiniowane dla Twojej aplikacji.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Zrzut ekranu strony ograniczenia dostępu w Azure Portal zawierający listę reguł ograniczeń dostępu zdefiniowanych dla wybranej aplikacji.":::

   Na liście zostaną wyświetlone wszystkie bieżące ograniczenia, które są stosowane do aplikacji. Jeśli używasz ograniczenia sieci wirtualnej w aplikacji, w tabeli przedstawiono, czy punkty końcowe usługi są włączone dla Microsoft. Web. Jeśli w aplikacji nie zdefiniowano żadnych ograniczeń, aplikacja jest dostępna z dowolnego miejsca.

### <a name="add-an-access-restriction-rule"></a>Dodawanie reguły ograniczeń dostępu

Aby dodać regułę ograniczeń dostępu do aplikacji, w okienku **ograniczenia dostępu** wybierz pozycję **Dodaj regułę**. Po dodaniu reguły zaczyna obowiązywać natychmiast. 

Reguły są wymuszane w kolejności priorytetu, rozpoczynając od najniższej liczby w kolumnie **priorytet** . Niejawne *odmowa* jest stosowane po dodaniu nawet jednej reguły.

W okienku **Dodaj ograniczenie dostępu** podczas tworzenia reguły wykonaj następujące czynności:

1. W obszarze **Akcja** wybierz opcję **Zezwalaj** lub **Odmów**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodawanie ograniczenia dostępu&quot;.":::

1. Opcjonalnie wprowadź nazwę i opis reguły.
1. W polu **priorytet** wprowadź wartość priorytetu.
1. Z listy rozwijanej **Typ** wybierz typ reguły.

Różne typy reguł są opisane w poniższych sekcjach.

> [!NOTE]
> - Obowiązuje limit 512 reguł ograniczeń dostępu. Jeśli potrzebujesz więcej niż 512 reguł ograniczeń dostępu, Zalecamy rozważenie instalacji autonomicznego produktu zabezpieczeń, takiego jak Azure front-drzwi, Azure App Gateway lub alternatywny WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Ustawianie reguły opartej na adresie IP

Postępuj zgodnie z procedurą przedstawioną w poprzedniej sekcji, ale z następującym dodatkiem:
* W kroku 4, z listy rozwijanej **Typ** wybierz pozycję **IPv4** lub **IPv6**. 

Określ **blok adresów IP** w notacji CIDR (Classless Inter-Domain Routing) dla adresów IPv4 i IPv6. Aby określić adres, można użyć czegoś takiego jak *1.2.3.4/32*, gdzie pierwsze cztery oktety reprezentują adres IP i */32* jest maską. Notacja CIDR protokołu IPv4 dla wszystkich adresów ma wartość 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, zobacz [Inter-Domain Routing bezklasowy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Ustawianie reguły opartej na punkcie końcowym usługi

* W kroku 4, z listy rozwijanej **Typ** wybierz pozycję **Virtual Network**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodaj ograniczenie&quot; z wybranym typem Virtual Network.":::

Określ listę rozwijaną **subskrypcja**, **Virtual Network** i **podsieć** , odpowiadającą tym, do czego chcesz ograniczyć dostęp.

Korzystając z punktów końcowych usługi, można ograniczyć dostęp do wybranych podsieci sieci wirtualnej platformy Azure. Jeśli punkty końcowe usługi nie są już włączone w usłudze Microsoft. Web dla wybranej podsieci, zostaną automatycznie włączone, chyba że zaznaczysz pole wyboru **Ignoruj brakujące punkty końcowe usługi sieci Web firmy Microsoft.** Scenariusz, w którym można włączyć punkty końcowe usługi w aplikacji, ale nie podsieć, zależy głównie od tego, czy masz uprawnienia do włączania ich w podsieci. 

Jeśli chcesz, aby ktoś inny włączył punkty końcowe usługi w podsieci, zaznacz pole wyboru **Ignoruj brakujące punkty końcowe usługi sieci Web firmy Microsoft** . Aplikacja zostanie skonfigurowana dla punktów końcowych usługi w przewidywaniu włączenia ich później w podsieci. 

Punktów końcowych usługi nie można używać do ograniczania dostępu do aplikacji, które działają w App Service Environment. Gdy aplikacja znajduje się w App Service Environment, możesz kontrolować dostęp do niej, stosując reguły dostępu do adresów IP. 

Za pomocą punktów końcowych usługi można skonfigurować aplikację przy użyciu bram aplikacji lub innych urządzeń zapory aplikacji sieci Web (WAF). Możesz również skonfigurować wielowarstwowe aplikacje z bezpiecznymi zapleczami. Aby uzyskać więcej informacji, zobacz [funkcje sieciowe i App Service](networking-features.md) i [Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Punkty końcowe usługi nie są obecnie obsługiwane w przypadku aplikacji sieci Web, które używają wirtualnego adresu IP (VIP) protokołu IP Secure Sockets Layer (SSL).
>
#### <a name="set-a-service-tag-based-rule"></a>Ustawianie reguły opartej na tagu usługi

* W kroku 4, z listy rozwijanej **Typ** wybierz pozycję **tag usługi**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Zrzut ekranu przedstawiający okienko &quot;Dodaj ograniczenie&quot; z wybranym typem tagu usługi.":::

Każdy tag usługi reprezentuje listę zakresów adresów IP z usług platformy Azure. Listę tych usług i linki do określonych zakresów można znaleźć w [dokumentacji znacznika usługi][servicetags].

Wszystkie dostępne Tagi usług są obsługiwane w regułach ograniczeń dostępu. Dla uproszczenia tylko lista najpopularniejszych tagów jest dostępna za pomocą Azure Portal. Użyj szablonów Azure Resource Manager lub skryptów, aby skonfigurować bardziej zaawansowane reguły, takie jak regionalne reguły o określonym zakresie. Oto Tagi dostępne za pomocą Azure Portal:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor. zaplecza
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Edytowanie reguły

1. Aby rozpocząć edytowanie istniejącej reguły ograniczeń dostępu, na stronie **ograniczenia dostępu** wybierz regułę, którą chcesz edytować.

1. W okienku **Edycja ograniczenia dostępu** wprowadź zmiany, a następnie wybierz pozycję **Aktualizuj regułę**. Zmiany są skuteczne od razu, w tym zmiany w kolejności priorytetów.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Zrzut ekranu okienka &quot;Edycja ograniczenia dostępu&quot; w Azure Portal, pokazujący pola istniejącej reguły ograniczeń dostępu.":::

   > [!NOTE]
   > Podczas edytowania reguły nie można przełączać się między typami reguł. 

### <a name="delete-a-rule"></a>Usuwanie reguły

Aby usunąć regułę, na stronie **ograniczenia dostępu** wybierz wielokropek (**...**) obok reguły, którą chcesz usunąć, a następnie wybierz pozycję **Usuń**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Zrzut ekranu strony &quot;ograniczenia dostępu&quot; pokazujący wielokropek obok reguły ograniczenia dostępu do usunięcia.":::

## <a name="access-restriction-advanced-scenarios"></a>Zaawansowane scenariusze ograniczeń dostępu
W poniższych sekcjach opisano niektóre zaawansowane scenariusze korzystające z ograniczeń dostępu.

### <a name="filter-by-http-header"></a>Filtruj według nagłówka http

W ramach każdej reguły można dodać dodatkowe filtry nagłówka HTTP. Obsługiwane są następujące nazwy nagłówków http:
* X-Forwarded-For
* X-Forward-Host
* X-Azure-FDID
* X-FD-HealthProbe

Dla każdej nazwy nagłówka można dodać maksymalnie 8 wartości rozdzielonych przecinkami. Filtry nagłówka HTTP są oceniane po samej regule, a oba warunki muszą mieć wartość true, aby można było zastosować regułę.

### <a name="multi-source-rules"></a>Reguły dla wiele źródeł

Reguły wieloźródła umożliwiają połączenie maksymalnie 8 zakresów adresów IP lub 8 tagów usługi w jednej regule. Można tego użyć, jeśli masz więcej niż 512 zakresów IP lub chcesz utworzyć reguły logiczne, w których wiele zakresów adresów IP jest połączonych z pojedynczym filtrem nagłówka HTTP.

Reguły dla wielu źródeł są zdefiniowane w taki sam sposób, w jaki definiuje się reguły pojedynczego źródła, ale z każdym zakresem oddzielonym przecinkami.

Przykład programu PowerShell:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Blokuj pojedynczy adres IP

Po dodaniu pierwszej reguły ograniczeń dostępu do usługi dodawana jest jawna *odmowa* reguły o priorytecie 2147483647. W przypadku jawnej reguły *Odrzuć wszystkie* jest ostatnią regułą, która ma zostać wykonana, i blokuje dostęp do dowolnego adresu IP, który nie jest jawnie dozwolony przez regułę *zezwalania* .

W przypadku scenariusza, w którym chcesz jawnie zablokować pojedynczy adres IP lub blok adresów IP, ale zezwolić na dostęp do wszystkiego innego, Dodaj jawną regułę *Zezwalaj na wszystkie* .

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Zrzut ekranu strony &quot;ograniczenia dostępu&quot; w Azure Portal zawierający pojedynczy zablokowany adres IP.":::

### <a name="restrict-access-to-an-scm-site"></a>Ograniczanie dostępu do witryny SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji można ograniczyć dostęp do witryny SCM używanej przez aplikację. Lokacja SCM to zarówno punkt końcowy Web Deploy, jak i konsola kudu. Ograniczenia dostępu można przypisać do witryny SCM z poziomu aplikacji osobno lub użyć tego samego zestawu ograniczeń zarówno dla aplikacji, jak i dla witryny SCM. Po wybraniu tych **samych ograniczeń co \<app name>** pole wyboru wszystko jest puste. Jeśli wyczyścisz to pole wyboru, zostaną ponownie zastosowane ustawienia witryny SCM. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Zrzut ekranu strony &quot;ograniczenia dostępu&quot; w Azure Portal, informujący, że nie ustawiono żadnych ograniczeń dostępu dla witryny SCM lub aplikacji.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Ograniczanie dostępu do określonego wystąpienia usługi Azure front-drzwi
Ruch z usług frontonu platformy Azure do aplikacji pochodzi z dobrze znanego zestawu zakresów adresów IP zdefiniowanych w tagu usługi AzureFrontDoor. zaplecza. Korzystając z reguły ograniczeń tagu usługi, można ograniczyć ruch do programu pochodzącego tylko z usług frontonu platformy Azure. Aby zapewnić, że ruch pochodzi tylko z określonego wystąpienia, należy dodatkowo filtrować przychodzące żądania na podstawie unikatowego nagłówka HTTP, który wysyła z przodu z platformy Azure.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Zrzut ekranu strony &quot;ograniczenia dostępu&quot; w Azure Portal, w którym pokazano, jak dodać ograniczenie dotyczące drzwi platformy Azure.":::

Przykład programu PowerShell:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Programistyczne zarządzanie regułami ograniczeń dostępu

Ograniczenia dostępu można dodać programowo, wykonując jedną z następujących czynności: 

* Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/config/access-restriction). Na przykład:
   
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
   > Praca z tagami usług, nagłówkami HTTP lub regułami wieloźródłowymi wymaga co najmniej wersji 5.7.0. Wersję zainstalowanego modułu można sprawdzić za pomocą: **Get-InstalledModule-Name AZ**

Możesz również ustawić wartości ręcznie, wykonując jedną z następujących czynności:

* Użyj operacji Put [interfejsu API REST platformy Azure](/rest/api/azure/) w konfiguracji aplikacji w Azure Resource Manager. Lokalizacja tych informacji w Azure Resource Manager:

  **Identyfikator subskrypcji** Management.Azure.com/subscriptions//resourceGroups/**grupy zasobów**/Providers/Microsoft.Web/Sites/**Nazwa aplikacji sieci Web**/config/Web? API-Version = 2020-06-01

* Użyj szablonu Menedżer zasobów. Na przykład można użyć resources.azure.com i edytować blok ipSecurityRestrictions, aby dodać wymagany kod JSON.

  Składnia JSON dla poprzedniego przykładu:

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
  Składnia JSON dla zaawansowanego przykładu przy użyciu znacznika usługi i ograniczenia nagłówka HTTP to:
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
## <a name="set-up-azure-functions-access-restrictions"></a>Konfigurowanie ograniczeń dostępu Azure Functions

Ograniczenia dostępu są również dostępne dla aplikacji funkcji o takich samych funkcjach jak plany App Service. Po włączeniu ograniczeń dostępu należy również wyłączyć Edytor kodu Azure Portal dla wszelkich niedozwolonych adresów IP.

## <a name="next-steps"></a>Następne kroki
[Ograniczenia dostępu dla Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md