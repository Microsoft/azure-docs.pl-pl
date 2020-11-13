---
title: Application Gateway integrację z punktami końcowymi usługi — Azure App Service | Microsoft Docs
description: Opisuje, w jaki sposób Application Gateway integruje się z Azure App Service zabezpieczonymi punktami końcowymi usługi.
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
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 954e94063ec91cd2a6d67d154dfd7da553e0935a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560897"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integrację z punktami końcowymi usługi
Istnieją trzy różnice App Service, które wymagają nieco innej konfiguracji integracji z usługą Azure Application Gateway. Różnice obejmują regularne App Service — nazywane również wieloma dzierżawcami, wewnętrznymi Load Balancer (ILB) App Service Environment (ASE) i zewnętrznym środowiskiem ASE. W tym artykule opisano sposób konfigurowania tego programu przy użyciu App Service (wiele dzierżawców) i omówienia zagadnień dotyczących ILB oraz zewnętrznego środowiska ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integracja z usługą App Service (wiele dzierżawców)
App Service (wiele dzierżawców) ma publiczny internetowy punkt końcowy. Za pomocą [punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) można zezwalać na ruch tylko z określonej podsieci w ramach usługi Azure Virtual Network i blokować wszystkie inne elementy. W poniższym scenariuszu ta funkcja zostanie użyta w celu upewnienia się, że wystąpienie App Service może odbierać tylko ruch z określonego wystąpienia Application Gateway.

![Diagram przedstawia Internet przepływający do Application Gateway na platformie Azure Virtual Network i przepływający przez ikonę zapory do wystąpień aplikacji w App Service.](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Istnieją dwie części tej konfiguracji Oprócz tworzenia App Service i Application Gateway. Pierwsza część włącza punkty końcowe usługi w podsieci Virtual Network, w której wdrożono Application Gateway. Punkty końcowe usługi zapewniają, że cały ruch sieciowy opuszczający podsieć do App Service zostanie oznaczony określonym IDENTYFIKATORem podsieci. Druga część to ustawienie ograniczenia dostępu do określonej aplikacji sieci Web, aby upewnić się, że dozwolony jest tylko ruch oznaczony przy użyciu tego konkretnego identyfikatora podsieci. Można go skonfigurować przy użyciu różnych narzędzi w zależności od preferencji.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
W przypadku Azure Portal należy wykonać cztery kroki, aby zainicjować i skonfigurować konfigurację. Jeśli masz istniejące zasoby, możesz pominąć pierwsze kroki.
1. Utwórz App Service przy użyciu jednego z przewodników szybki start w dokumentacji App Service, na przykład [.NET Core szybki start](../quickstart-dotnetcore.md)
2. Utwórz Application Gateway za pomocą [przewodnika Szybki Start dla portalu](../../application-gateway/quick-create-portal.md), ale Pomiń sekcję Dodawanie elementów docelowych zaplecza.
3. Skonfiguruj [App Service jako zaplecze w Application Gateway](../../application-gateway/configure-web-app-portal.md), ale Pomiń sekcję ograniczanie dostępu.
4. Na koniec Utwórz [ograniczenie dostępu za pomocą punktów końcowych usługi](../../app-service/app-service-ip-restrictions.md#use-service-endpoints).

Teraz możesz uzyskiwać dostęp do App Service za pośrednictwem Application Gateway, ale jeśli spróbujesz uzyskać bezpośrednio dostęp do App Service, należy otrzymać błąd HTTP 403 wskazujący, że witryna sieci Web jest zatrzymana.

![Zrzut ekranu przedstawia tekst błędu 403 — ta aplikacja sieci Web jest zatrzymana.](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
[Szablon wdrożenia Menedżer zasobów][template-app-gateway-app-service-complete] będzie inicjować cały scenariusz. Scenariusz składa się z wystąpienia App Service zablokowanego za pomocą punktów końcowych usługi i ograniczenia dostępu, aby odbierać tylko ruch z Application Gateway. Szablon zawiera wiele inteligentnych ustawień domyślnych i unikatowych przyrostków dodawanych do nazw zasobów, które mają być proste. Aby je zastąpić, należy sklonować repozytorium lub pobrać szablon i edytować go. 

Aby zastosować szablon, można użyć przycisku Wdróż na platformie Azure znajdującego się w opisie szablonu lub użyć odpowiedniego programu PowerShell/interfejsu wiersza polecenia.

## <a name="using-azure-command-line-interface"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W [przykładzie interfejsu wiersza polecenia platformy Azure](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) zostanie zainicjowana App Service zablokowany za pomocą punktów końcowych usługi i ograniczenia dostępu, aby odbierać tylko ruch z Application Gateway. Jeśli konieczne jest izolowanie ruchu tylko do istniejącego App Service z istniejących Application Gateway, wystarczające jest następujące polecenie.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

W domyślnej konfiguracji polecenie zapewni zarówno konfigurację konfiguracji punktu końcowego usługi w podsieci, jak i ograniczenia dostępu w App Service.

## <a name="considerations-for-ilb-ase"></a>Zagadnienia dotyczące programu ILB ASE
ILB ASE nie jest narażony na Internet i ruch między wystąpieniem, a Application Gateway jest już izolowany do Virtual Network. Poniższy [Przewodnik](../environment/integrate-with-application-gateway.md) po konfiguracji programu ILB ASE i integruje go z Application Gateway przy użyciu Azure Portal. 

Jeśli chcesz upewnić się, że tylko ruch z podsieci Application Gateway osiągnie środowisko ASE, możesz skonfigurować grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń), która ma wpływ na wszystkie aplikacje sieci Web w środowisku ASE. W przypadku sieciowej grupy zabezpieczeń można określić zakres adresów IP podsieci i opcjonalnie porty (80/443). Upewnij się, że nie zastąpisz [wymaganych reguł sieciowej grupy zabezpieczeń](../environment/network-info.md#network-security-groups) dla środowiska ASE, aby działały prawidłowo.

Aby izolować ruch do pojedynczej aplikacji sieci Web, należy użyć ograniczeń dostępu opartych na protokole IP, ponieważ punkty końcowe usługi nie będą działały w środowisku ASE. Adres IP powinien być prywatnym adresem IP wystąpienia Application Gateway.

## <a name="considerations-for-external-ase"></a>Zagadnienia dotyczące zewnętrznego środowiska ASE
Zewnętrzne środowisko ASE ma publiczny, dostępny moduł równoważenia obciążenia, taki jak App Service wielodostępności. Punkty końcowe usługi nie działają w środowisku ASE i dlatego należy używać ograniczeń dostępu opartych na protokole IP przy użyciu publicznego adresu IP wystąpienia Application Gateway. Aby utworzyć zewnętrzny środowisko ASE przy użyciu Azure Portal, możesz wykonać czynności opisane w tym [przewodniku szybki start](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Szablon Azure Resource Manager na potrzeby pełnego scenariusza"

## <a name="considerations-for-kuduscm-site"></a>Zagadnienia dotyczące witryny kudu/SCM
Witryna SCM, znana również jako kudu, jest witryną administracyjną, która istnieje dla każdej aplikacji sieci Web. Zwrotny serwer proxy lokacji SCM nie jest możliwy i najprawdopodobniej Zablokuj go do poszczególnych adresów IP lub określonej podsieci.

Jeśli chcesz użyć tych samych ograniczeń dostępu co w przypadku lokacji głównej, możesz odziedziczyć ustawienia za pomocą następującego polecenia.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Jeśli chcesz ustawić indywidualne ograniczenia dostępu dla witryny SCM, możesz dodać ograniczenia dostępu przy użyciu flagi--SCM-site, jak pokazano poniżej.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat App Service Environment, zobacz [dokumentację dotyczącą App Service Environment](/azure/app-service/environment).

Aby dodatkowo zabezpieczyć aplikację sieci Web, informacje o zaporze aplikacji sieci Web na Application Gateway można znaleźć w [dokumentacji zapory aplikacji sieci Web platformy Azure](../../web-application-firewall/ag/ag-overview.md).
