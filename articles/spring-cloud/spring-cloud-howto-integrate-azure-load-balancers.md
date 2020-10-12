---
title: Samouczek — Integruj chmurę z usługą Azure wiosną z rozwiązaniami równoważenia obciążenia platformy Azure
description: Jak zintegrować chmurę ze sprężyną Azure z rozwiązaniami równoważenia obciążenia platformy Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 02772f153cdda7e3f3c866c727d589e755e19033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906940"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integracja usługi Azure Spring Cloud z rozwiązaniami do równoważenia obciążenia platformy Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Chmura sprężynowa platformy Azure obsługuje mikrousługi na platformie Azure.  Zwiększenie firmy może wymagać wielu centrów danych z zarządzaniem wieloma wystąpieniami chmury wiosennej platformy Azure.

Platforma Azure udostępnia już różne rozwiązania do równoważenia obciążenia. Istnieją trzy opcje integrowania chmur z platformą Azure z rozwiązaniami równoważenia obciążenia platformy Azure:

1.  Integruj chmurę z platformą Azure z usługą Azure Traffic Manager
2.  Integracja chmurowej platformy Azure z usługą Azure App Gateway
3.  Integruj chmurę z platformą Azure z usługą Azure front-drzwi

## <a name="prerequisites"></a>Wymagania wstępne

* Chmura Wiosenna platformy Azure: [jak utworzyć usługę w chmurze Azure wiosną](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart)
* Azure Traffic Manager: [jak utworzyć usługę Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure App Gateway: [jak utworzyć bramę aplikacji](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Moje drzwi platformy Azure: [jak utworzyć drzwi tylne](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integruj chmurę z platformą Azure z usługą Azure Traffic Manager

Aby zintegrować chmurę z usługą Azure wiosną Traffic Manager, Dodaj swoje publiczne punkty końcowe jako punkty końcowe usługi Traffic Manager, a następnie skonfiguruj domenę niestandardową dla usług Traffic Manager i Azure wiosennej.

### <a name="add-endpoint-in-traffic-manager"></a>Dodaj punkt końcowy w Traffic Manager
Dodawanie punktów końcowych w usłudze Traffic Manager:
1.  Określ **Typ** jako *zewnętrzny punkt końcowy*.
1.  Wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) każdego publicznego punktu końcowego chmury platformy Azure.
1. Kliknij przycisk **OK**.

    ![Traffic Manager 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Konfiguruj domenę niestandardową
Aby zakończyć konfigurację:
1.  Zaloguj się do witryny sieci Web dostawcy domeny i Utwórz mapowanie rekordu CNAME z domeny niestandardowej na domyślną nazwę domeny platformy Azure usługi Traffic Manager.
1.  Postępuj zgodnie z instrukcjami [, jak dodać domenę niestandardową do chmury wiosennej platformy Azure](spring-cloud-tutorial-custom-domain.md).
1. Dodaj powyższe niestandardowe powiązanie domeny do usługi Traffic Manager do chmury wiosennej platformy Azure odpowiadającej usłudze App Service i przekaż certyfikat SSL w tym miejscu.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integracja chmurowej platformy Azure z usługą Azure App Gateway

Aby przeprowadzić integrację z usługą Azure wiosną Cloud, wykonaj następujące czynności konfiguracyjne:

### <a name="configure-backend-pool"></a>Konfigurowanie puli zaplecza
1. Określ **Typ docelowy** jako *adres IP* lub *nazwę FQDN*.
1. Wprowadź swoje publiczne punkty końcowe chmury platformy Azure.

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Dodaj niestandardową sondę
1. Wybierz pozycję **sondy kondycji** , a następnie **Dodaj** , aby otworzyć okno dialogowe **sondy** niestandardowej. 
1. Kluczowym punktem jest wybranie opcji *tak* w przypadku **wybrania nazwy hosta na stronie ustawień protokołu HTTP zaplecza** .

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Konfigurowanie ustawienia protokołu http
1.  Wybierz pozycję **Ustawienia http** , a następnie **Dodaj** , aby dodać ustawienie http.
1.  **Przesłoń z nową nazwą hosta:** wybierz pozycję *tak*.
1.  **Zastąpienie nazwy hosta**: wybierz pozycję Wybierz **nazwę hosta z celu zaplecza**.
1.  **Użyj sondy niestandardowej**: wybierz opcję *tak* , a następnie wybierz niestandardową sondę utworzoną powyżej.

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integruj chmurę z platformą Azure z usługą Azure front-drzwi

Aby przeprowadzić integrację z usługą Azure wiosną w chmurze i skonfigurować pulę zaplecza, 
1. **Dodaj pulę zaplecza**.
1. Określ punkt końcowy zaplecza, dodając hosta.

    ![Drzwi z przodu 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Określ **typ hosta zaplecza** jako *hosta niestandardowego*.
1.  Wprowadź nazwę FQDN publicznych punktów końcowych w chmurze Azure wiosennej w **nazwie hosta zaplecza**.
1.  Zaakceptuj wartość domyślną **nagłówka hosta zaplecza** , która jest taka sama jak **Nazwa hosta zaplecza**.

    ![Drzwi tylne 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Następne kroki
* [Jak utworzyć Menedżera ruchu](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [Jak utworzyć bramę aplikacji](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [Jak utworzyć drzwi tylne](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
