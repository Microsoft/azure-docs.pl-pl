---
title: Przełączanie do trybu failover w wielu punktach końcowych za pomocą Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Dowiedz się, jak skonfigurować tryb failover dla wielu punktów końcowych usługi Azure Content Delivery Network przy użyciu usługi Azure Traffic Manager.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034762"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Tryb failover w wielu punktach końcowych za pomocą usługi Azure Traffic Manager

Podczas konfigurowania usługi Azure Content Delivery Network (CDN) można wybrać optymalny dostawca i warstwę cenową dla potrzeb. 

Azure CDN, z globalnie dystrybuowaną infrastrukturą, domyślnie tworzy nadmiarowość lokalną i geograficzną oraz globalne Równoważenie obciążenia, aby zwiększyć dostępność i wydajność usług. 

Jeśli lokalizacja nie jest dostępna do udostępniania zawartości, żądania są automatycznie kierowane do innej lokalizacji. Optymalny punkt obecności (POP) służy do obsłużenia każdego żądania klienta. Automatyczne kierowanie jest oparte na czynnikach, takich jak lokalizacja żądania i obciążenie serwera.
 
Jeśli masz wiele profilów usługi CDN, możesz jeszcze bardziej zwiększyć dostępność i wydajność dzięki usłudze Azure Traffic Manager. 

Użyj usługi Azure Traffic Manager z Azure CDN, aby zrównoważyć obciążenie między wieloma punktami końcowymi sieci CDN dla:
 
* Tryb failover
* Równoważenie obciążenia geograficznego 

W typowym scenariuszu pracy awaryjnej wszystkie żądania klientów są kierowane do podstawowego profilu CDN. 

Jeśli profil jest niedostępny, żądania są kierowane do profilu pomocniczego.  Żądania są wznawiane do profilu podstawowego po powrocie do trybu online.

Korzystanie z usługi Azure Traffic Manager w ten sposób gwarantuje, że aplikacja sieci Web będzie zawsze dostępna. 

Ten artykuł zawiera wskazówki i przykład sposobu konfigurowania trybu failover przy użyciu profilów z: 

* **Azure CDN Standard z Verizon**
* **Azure CDN Standard z Akamai**

Obsługiwane są również **Azure CDN firmy Microsoft** .

## <a name="create-azure-cdn-profiles"></a>Utwórz profile Azure CDN
Utwórz co najmniej dwa profile Azure CDN i punkty końcowe z różnymi dostawcami.

1. Utwórz dwa profile sieci CDN:
    * **Azure CDN Standard z Verizon**
    * **Azure CDN Standard z Akamai** 

    Utwórz profile, wykonując kroki opisane w temacie [Tworzenie nowego profilu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Wiele profilów usługi CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. W każdym z nowych profilów Utwórz co najmniej jeden punkt końcowy, wykonując kroki opisane w temacie [Tworzenie nowego punktu końcowego usługi CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Utwórz profil Menedżera ruchu
Utwórz profil Traffic Manager platformy Azure i skonfiguruj Równoważenie obciążenia dla punktów końcowych usługi CDN. 

1. Utwórz profil Traffic Manager platformy Azure, wykonując kroki opisane w temacie [Tworzenie profilu Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md). 

    * **Metoda routingu**, wybierz pozycję **priorytet**.

2. Dodaj punkty końcowe usługi CDN w profilu Traffic Manager, wykonując czynności opisane w sekcji [dodawanie Traffic Manager punktów końcowych](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)

    * Wybierz pozycję **zewnętrzne punkty końcowe**.
    * **Priorytet**, wprowadź liczbę.

    Na przykład Utwórz **cdndemo101akamai.azureedge.NET** z priorytetem **1** i **cdndemo101verizon.azureedge.NET** o priorytecie **2**.

   ![Punkty końcowe usługi Traffic Manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Skonfiguruj domenę niestandardową na Azure CDN i na platformie Azure Traffic Manager
Po skonfigurowaniu profilów sieci CDN i Traffic Manager wykonaj następujące kroki, aby dodać mapowanie DNS i zarejestrować domenę niestandardową do punktów końcowych usługi CDN. W tym przykładzie niestandardowa nazwa domeny to **cdndemo101.dustydogpetcare.online**.

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
   
    > [!NOTE]
    > W przypadku implemeting ten tryb failover scenerio oba punkty końcowe muszą znajdować się w różnych profilach, a różne profile powinny należeć do różnych dostawców usługi CDN, aby uniknąć konfliktów nazw domen.
    > 

2.  Z poziomu profilu Azure CDN wybierz pierwszy punkt końcowy usługi CDN (Akamai). Wybierz pozycję **Dodaj domenę niestandardową** i wprowadź **cdndemo101.dustydogpetcare.online**. Sprawdź, czy znacznik wyboru weryfikacji domeny niestandardowej jest zielony. 

    Azure CDN używa poddomeny **cdnverify** do sprawdzania poprawności mapowania DNS w celu ukończenia tego procesu rejestracji. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ten krok umożliwia Azure CDN rozpoznawania domeny niestandardowej, aby mogła ona odpowiadać na żądania.
    
    > [!NOTE]
    > Aby włączyć protokół TLS na **Azure CDN z poziomu profilów Akamai** , należy bezpośrednio odrekordować domenę niestandardową do punktu końcowego. cdnverify do włączenia protokołu TLS nie jest jeszcze obsługiwane. 
    >

3.  Wróć do witryny sieci Web dostawcy domeny niestandardowej. Zaktualizuj pierwsze utworzone mapowanie DNS. Zamapuj domenę niestandardową na drugi punkt końcowy usługi CDN.
                             
    Na przykład: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. W profilu Azure CDN wybierz drugi punkt końcowy usługi CDN (Verizon) i powtórz krok 2. Wybierz pozycję **Dodaj domenę niestandardową**, a następnie wprowadź **cdndemo101.dustydogpetcare.online**.
 
Po wykonaniu tych kroków usługa wiele sieci CDN z możliwością pracy w trybie failover jest konfigurowana przy użyciu usługi Azure Traffic Manager. 

Możesz uzyskać dostęp do testów adresów URL z domeny niestandardowej. 

Aby przetestować funkcje, wyłącz podstawowy punkt końcowy usługi CDN i sprawdź, czy żądanie jest prawidłowo przenoszone do punktu końcowego pomocniczej usługi CDN. 

## <a name="next-steps"></a>Następne kroki
Można skonfigurować inne metody routingu, takie jak geograficzne, aby zrównoważyć obciążenie między różnymi punktami końcowymi usługi CDN. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie metody routingu ruchu geograficznego przy użyciu Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).
