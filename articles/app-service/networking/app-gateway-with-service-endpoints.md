---
title: Application Gateway z punktami końcowymi usługi — Azure App Service | Microsoft Docs
description: Opisuje sposób integracji Application Gateway z Azure App Service zabezpieczoną za pomocą punktów końcowych usługi.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374917"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integracji z punktami końcowymi usługi
Istnieją trzy odmiany typów App Service które wymagają nieco innej konfiguracji integracji z Azure Application Gateway. Odmiany obejmują zwykłe App Service — znane również jako wielodostępne, wewnętrzne Load Balancer (ILB) App Service Environment (ASE) i zewnętrzne. W tym artykule opisano sposób konfigurowania go przy użyciu App Service (wielodostępnych) i omówiono zagadnienia dotyczące wewnętrznego równoważenia obciążenia i zewnętrznego źródła danych.

## <a name="integration-with-app-service-multi-tenant"></a>Integracja z App Service (wielodostępna)
App Service (wielodostępna) ma publiczny punkt końcowy z dostępem do Internetu. Za [pomocą punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) można zezwolić na ruch tylko z określonej podsieci w ramach usługi Azure Virtual Network i blokować wszystkie inne. W poniższym scenariuszu użyjemy tej funkcji, aby zapewnić, że wystąpienie App Service może odbierać ruch tylko z określonego Application Gateway wystąpienia.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram przedstawiający internet przepływający do Application Gateway w usłudze Azure Virtual Network i przepływający z tej witryny przez ikonę zapory do wystąpień aplikacji w App Service.":::

Ta konfiguracja ma dwie części oprócz tworzenia App Service i Application Gateway. Pierwsza część to włączenie punktów końcowych usługi w podsieci Virtual Network, Application Gateway jest wdrażana. Punkty końcowe usługi zapewnią, że cały ruch sieciowy opuszczacy podsieć do App Service zostanie otagowany za pomocą określonego identyfikatora podsieci. Druga część to ustawienie ograniczenia dostępu dla określonej aplikacji internetowej w celu zapewnienia, że dozwolony jest tylko ruch otagowany za pomocą tego identyfikatora podsieci. Można go skonfigurować przy użyciu różnych narzędzi w zależności od preferencji.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
W Azure Portal wykonaj cztery kroki, aby aprowizować i skonfigurować konfigurację. Jeśli masz istniejące zasoby, możesz pominąć pierwsze kroki.
1. Tworzenie aplikacji App Service jednym z przewodników Szybki start w dokumentacji App Service, na przykład Szybki start dla programu [.NET Core](../quickstart-dotnetcore.md)
2. Utwórz konto Application Gateway portalu [Szybki start,](../../application-gateway/quick-create-portal.md)ale pomiń sekcję Dodawanie obiektów docelowych zaplecza.
3. Skonfiguruj [App Service jako zaplecza w](../../application-gateway/configure-web-app-portal.md)Application Gateway , ale pomiń sekcję Ograniczanie dostępu.
4. Na koniec utwórz [ograniczenie dostępu przy użyciu punktów końcowych usługi](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Teraz możesz uzyskać dostęp do usługi App Service za pośrednictwem usługi Application Gateway, ale jeśli spróbujesz uzyskać bezpośredni dostęp do usługi App Service, powinien zostać wyświetlony błąd HTTP 403 wskazujący, że witryna internetowa została zatrzymana.

![Zrzut ekranu przedstawia tekst błędu 403 — dostęp zabroniony.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Szablon [Resource Manager wdrożenia aprowizuje][template-app-gateway-app-service-complete] kompletny scenariusz. Scenariusz składa się z wystąpienia App Service zablokowanego z punktami końcowymi usługi i ograniczeń dostępu w celu odbierania ruchu tylko z Application Gateway. Szablon zawiera wiele inteligentnych wartości domyślnych i unikatowych poprawek dodanych do nazw zasobów, aby był prosty. Aby je zastąpić, musisz sklonować repo lub pobrać szablon i go edytować. 

Aby zastosować szablon, możesz użyć przycisku Wd wdrażaj na platformie Azure w opisie szablonu lub użyć odpowiedniego programu PowerShell/interfejsu wiersza polecenia.

## <a name="using-azure-command-line-interface"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Przykładowy [interfejs wiersza polecenia platformy Azure](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) aprowizuje App Service zablokowany przy użyciu punktów końcowych usługi i ograniczeń dostępu, aby odbierać tylko ruch z Application Gateway. Jeśli wystarczy odizolować ruch do istniejącej App Service z istniejącej Application Gateway, następujące polecenie jest wystarczające.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

W konfiguracji domyślnej polecenie zapewni zarówno konfigurację konfiguracji punktu końcowego usługi w podsieci, jak i ograniczenie dostępu w App Service.

## <a name="considerations-for-ilb-ase"></a>Zagadnienia dotyczące ase z równoważeniem obciążenia
W związku z tym środowisku ASE z równoważeniem obciążenia nie ma dostępu do Internetu, a ruch między wystąpieniem i Application Gateway jest już izolowany od Virtual Network. Poniższy przewodnik [z konfigurować](../environment/integrate-with-application-gateway.md) ilb ASE i integruje go z siecią Application Gateway przy użyciu Azure Portal. 

Jeśli chcesz upewnić się, że tylko ruch z podsieci Application Gateway dociera do ase, możesz skonfigurować sieciową grupę zabezpieczeń, która ma wpływ na wszystkie aplikacje internetowe w tym ase. Dla sieciowej sieciowej organizacji możesz określić zakres adresów IP podsieci i opcjonalnie porty (80/443). Upewnij się, że nie zastępuj [wymaganych reguł sieciowej sieci,](../environment/network-info.md#network-security-groups) aby zapewnić poprawne działanie tego ustawienia.

Aby odizolować ruch do poszczególnych aplikacji internetowych, należy użyć ograniczeń dostępu opartych na adresach IP, ponieważ punkty końcowe usługi nie będą działać w środowisku ASE. Adres IP powinien być prywatnym adresem IP Application Gateway wystąpienia.

## <a name="considerations-for-external-ase"></a>Zagadnienia dotyczące zewnętrznego ase
Zewnętrzny system ASE ma publiczny usługę równoważenia obciążenia, na przykład wielodostępną App Service. Punkty końcowe usługi nie działają w przypadku usługi ASE i dlatego należy używać ograniczeń dostępu opartych na adresach IP przy użyciu publicznego adresu IP Application Gateway wystąpienia. Aby utworzyć zewnętrzny program ASE przy użyciu Azure Portal, możesz wykonać czynności z tego przewodnika [Szybki start](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager szablonu dla kompletnego scenariusza"

## <a name="considerations-for-kuduscm-site"></a>Zagadnienia dotyczące witryny kudu/scm
Witryna scm, znana również jako kudu, to witryna administracyjna, która istnieje dla każdej aplikacji internetowej. Nie można odwrócić serwera proxy lokacji scm i najprawdopodobniej chcesz również zablokować ją do poszczególnych adresów IP lub określonej podsieci.

Jeśli chcesz używać tych samych ograniczeń dostępu co lokacja główna, możesz dziedziczyć ustawienia za pomocą następującego polecenia.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Jeśli chcesz ustawić indywidualne ograniczenia dostępu dla witryny scm, możesz dodać ograniczenia dostępu przy użyciu flagi --scm-site, jak pokazano poniżej.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat App Service Environment, zobacz [dokumentację App Service Environment .](/azure/app-service/environment)

Aby jeszcze bardziej zabezpieczyć aplikację internetową, informacje o Web Application Firewall na Application Gateway można znaleźć w [dokumentacji](../../web-application-firewall/ag/ag-overview.md)Azure Web Application Firewall .
