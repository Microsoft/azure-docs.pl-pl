---
title: Tryb failover dla wielu punktów końcowych Azure CDN z Traffic Manager
description: Dowiedz się, jak skonfigurować tryb failover dla wielu punktów końcowych usługi Azure Content Delivery Network przy użyciu usługi Azure Traffic Manager.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b55e418393d6d446ae0d3557f2d1f4cf98d89293
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192506"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurowanie trybu failover dla wielu punktów końcowych Azure CDN przy użyciu usługi Azure Traffic Manager

Podczas konfigurowania usługi Azure Content Delivery Network (CDN) można wybrać optymalny dostawca i warstwę cenową dla potrzeb. Azure CDN, z globalnie dystrybuowaną infrastrukturą, domyślnie tworzy nadmiarowość lokalną i geograficzną oraz globalne Równoważenie obciążenia, aby zwiększyć dostępność i wydajność usług. Jeśli lokalizacja nie jest dostępna do udostępniania zawartości, żądania są automatycznie kierowane do innej lokalizacji, a optymalny punkt POP (na podstawie takich czynników, jak lokalizacja żądania i obciążenie serwera) jest używany do obsłużenia każdego żądania klienta. 
 
Jeśli masz wiele profilów usługi CDN, możesz jeszcze bardziej zwiększyć dostępność i wydajność dzięki usłudze Azure Traffic Manager. Za pomocą usługi Azure Traffic Manager z Azure CDN można zrównoważyć obciążenie między wieloma punktami końcowymi sieci CDN w celu przełączenia w tryb failover, równoważenia obciążenia geograficznego i innych scenariuszy. W typowym scenariuszu pracy awaryjnej wszystkie żądania klientów są najpierw kierowane do podstawowego profilu CDN. Jeśli profil nie jest dostępny, żądania są następnie przenoszone do profilu pomocniczej sieci CDN do momentu powrotu do trybu online podstawowego profilu CDN. Korzystanie z usługi Azure Traffic Manager w ten sposób gwarantuje, że aplikacja sieci Web będzie zawsze dostępna. 

Ten artykuł zawiera wskazówki i przykład sposobu konfigurowania trybu failover przy użyciu **standardu Azure CDN Verizon** i **Azure CDN Standard from Akamai** profile.

## <a name="set-up-azure-cdn"></a>Skonfiguruj Azure CDN 
Utwórz co najmniej dwa profile Azure CDN i punkty końcowe z różnymi dostawcami.

1. Utwórz **standard Azure CDN od Verizon** i **Azure CDN Standard z profilu Akamai** , wykonując kroki opisane w temacie [Tworzenie nowego profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Wiele profilów usługi CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. W każdym z nowych profilów Utwórz co najmniej jeden punkt końcowy, wykonując kroki opisane w temacie [Tworzenie nowego punktu końcowego usługi CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurowanie usługi Azure Traffic Manager
Utwórz profil Traffic Manager platformy Azure i skonfiguruj Równoważenie obciążenia dla punktów końcowych usługi CDN. 

1. Utwórz profil Traffic Manager platformy Azure, wykonując kroki opisane w temacie [Tworzenie profilu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    W obszarze **Metoda routingu**wybierz pozycję **priorytet**.

2. Dodaj punkty końcowe usługi CDN w profilu Traffic Manager, wykonując czynności opisane w sekcji [dodawanie Traffic Manager punktów końcowych](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    W obszarze **Typ**wybierz pozycję **zewnętrzne punkty końcowe**. W obszarze **priorytet**wprowadź liczbę.

    Na przykład Utwórz *cdndemo101akamai.azureedge.NET* z priorytetem *1* i *cdndemo101verizon.azureedge.NET* o priorytecie *2*.

   ![Punkty końcowe usługi Traffic Manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Skonfiguruj domenę niestandardową na Azure CDN i na platformie Azure Traffic Manager
Po skonfigurowaniu profilów sieci CDN i Traffic Manager wykonaj następujące kroki, aby dodać mapowanie DNS i zarejestrować domenę niestandardową do punktów końcowych usługi CDN. W tym przykładzie niestandardowa nazwa domeny to *cdndemo101. dustydogpetcare. online*.

1. Przejdź do witryny sieci Web dostawcy domeny niestandardowej, takiej jak GoDaddy, i Utwórz dwa wpisy CNAME DNS. 

    a. W przypadku pierwszego wpisu CNAME Mapuj domenę niestandardową z poddomeną cdnverify na punkt końcowy usługi CDN. Ten wpis jest wymaganym krokiem w celu zarejestrowania domeny niestandardowej w punkcie końcowym usługi CDN, który został dodany do Traffic Manager w kroku 2.

      Przykład: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Dla drugiego wpisu CNAME Mapuj domenę niestandardową bez poddomeny cdnverify do punktu końcowego usługi CDN. Ten wpis mapuje domenę niestandardową na Traffic Manager. 

      Na przykład: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Jeśli Twoja domena jest obecnie aktywna i nie można jej przerwać, zrób to w ostatnim kroku. Przed zaktualizowaniem DNS domeny niestandardowej na Traffic Manager należy sprawdzić, czy są używane punkty końcowe sieci CDN i domeny usługi Traffic Manager.
    >


2.  Z poziomu profilu Azure CDN wybierz pierwszy punkt końcowy usługi CDN (Akamai). Wybierz pozycję **Dodaj domenę niestandardową** i wprowadź *cdndemo101. dustydogpetcare. online*. Sprawdź, czy znacznik wyboru weryfikacji domeny niestandardowej jest zielony. 

    Azure CDN używa poddomeny *cdnverify* do sprawdzania poprawności mapowania DNS w celu ukończenia tego procesu rejestracji. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ten krok umożliwia Azure CDN rozpoznawania domeny niestandardowej, aby mogła ona odpowiadać na żądania.
    
    > [!NOTE]
    > Aby włączyć protokół TLS na **Azure CDN z poziomu profilów Akamai** , należy bezpośrednio odrekordować domenę niestandardową do punktu końcowego. cdnverify do włączenia protokołu TLS nie jest jeszcze obsługiwane. 
    >

3.  Wróć do witryny sieci Web dostawcy domeny niestandardowej i zaktualizuj pierwsze mapowanie DNS utworzone w programie, tak aby domena niestandardowa została zamapowana na drugi punkt końcowy usługi CDN.
                             
    Na przykład: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. W profilu Azure CDN wybierz drugi punkt końcowy usługi CDN (Verizon) i powtórz krok 2. Wybierz pozycję **Dodaj domenę niestandardową**i wprowadź *cdndemo101. dustydogpetcare. online*.
 
Po wykonaniu tych kroków usługa wiele sieci CDN z funkcjami trybu failover jest skonfigurowana za pomocą usługi Azure Traffic Manager. Będzie można uzyskać dostęp do testów adresów URL z domeny niestandardowej. Aby przetestować funkcje, wyłącz podstawowy punkt końcowy usługi CDN i sprawdź, czy żądanie jest prawidłowo przenoszone do punktu końcowego pomocniczej usługi CDN. 

## <a name="next-steps"></a>Następne kroki
Można także skonfigurować inne metody routingu, takie jak geograficzne, aby zrównoważyć obciążenie między różnymi punktami końcowymi usługi CDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie metody routingu ruchu geograficznego przy użyciu Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



