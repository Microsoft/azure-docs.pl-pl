---
title: Dołączanie domeny głównej lub wierzchołka do istniejących przednich drzwi Azure Portal
description: Dowiedz się, jak dołączyć domenę główną lub Apex do istniejących przednich drzwi przy użyciu Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 44813a7662420ab4dedcd0bf99cc1eec7e9d9d2d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819078"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Dołączanie domeny głównej lub domeny wierzchołkowej do usługi Front Door
Drzwi frontonu platformy Azure używają rekordów CNAME do weryfikowania własności domeny do dołączania domen niestandardowych. Drzwi z przodu nie ujawniają adresu IP frontonu skojarzonego z Twoim profilem drzwi. W związku z tym nie można zmapować domeny Apex na adres IP, jeśli zamierzasz dołączyć ją do usługi Azure front-drzwi.

Protokół DNS uniemożliwia przypisanie rekordów CNAME w wierzchołku strefy. Na przykład jeśli Twoja domena to `contoso.com` ; można utworzyć rekordy CNAME dla `somelabel.contoso.com` , ale nie można utworzyć rekordu CNAME dla `contoso.com` samego siebie. To ograniczenie powoduje problem dla właścicieli aplikacji, którzy mają aplikacje o zrównoważonym obciążeniu za drzwi platformy Azure. Ze względu na to, że użycie profilu front-drzwi wymaga utworzenia rekordu CNAME, nie jest możliwe wskazanie w profilu przednim drzwi ze wierzchołka strefy.

Ten problem można rozwiązać przy użyciu rekordów aliasów w Azure DNS. W przeciwieństwie do rekordów CNAME, rekordy aliasów są tworzone w wierzchołku strefy. Właściciele aplikacji mogą używać go do wskazywania ich rekordu wierzchołka strefy w profilu frontonu, który ma publiczne punkty końcowe. Właściciele aplikacji wskazują ten sam profil przedni, który jest używany przez dowolną inną domenę w ramach strefy DNS. Na przykład, `contoso.com` i `www.contoso.com` może wskazywać na ten sam profil przedni drzwi. 

Mapowanie wierzchołka lub domeny katalogu głównego na profil dla drzwi przednich zasadniczo wymaga spłaszczenia CNAME lub kartach DNS. Mechanizm, w którym dostawca DNS rekursywnie rozpoznaje wpis CNAME do momentu, gdy trafi na adres IP. Ta funkcja jest obsługiwana przez Azure DNS dla punktów końcowych z przodu. 

> [!NOTE]
> Istnieją również inni dostawcy DNS, którzy obsługują spłaszczanie CNAME lub kartach systemu DNS, ale przede wszystkim klienci na platformie Azure zalecają korzystanie z Azure DNS klientom do hostowania swoich domen.

Możesz użyć Azure Portal do dołączenia domeny wierzchołka na swoich drzwiach i włączenia protokołu HTTPS na ten temat, kojarząc go z certyfikatem dla zakończenia protokołu TLS. Domeny Apex są również nazywane domenami głównymi lub wykorzystanymi.

## <a name="create-an-alias-record-for-zone-apex"></a>Utwórz rekord aliasu dla wierzchołka strefy

1. Otwórz **Azure DNS** konfigurację dla domeny, która ma zostać dołączona.

1. Utwórz lub Edytuj rekord wierzchołka strefy.

1. Wybierz **Typ** *rekordu jako rekord, a* następnie wybierz pozycję *tak* dla **zestawu rekordów aliasu**. **Typ aliasu** powinien być ustawiony na *zasób platformy Azure*.

1. Wybierz subskrypcję platformy Azure, w której znajduje się profil z drzwiami wstępnymi. Następnie wybierz zasób z drzwiami z listy rozwijanej **zasobów platformy Azure** .

1. Wybierz pozycję **OK** , aby przesłać zmiany.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Rekord aliasu dla wierzchołka strefy&quot;:::

1. Powyższy krok spowoduje utworzenie rekordu wierzchołka strefy wskazującego na zasób z Drzwiem przednim, a także mapowania rekordu CNAME &quot;afdverify" (przykład- `afdverify.contosonews.com` ) do tego, aby można było używać go do dołączania do domeny w profilu przednim drzwi.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Dołącz domenę niestandardową do swoich drzwi

1. Na karcie Projektant drzwi przednich wybierz ikonę "+" w sekcji hosty frontonu, aby dodać nową domenę niestandardową.

1. Wprowadź nazwę domeny głównej lub Apex w polu Nazwa hosta niestandardowego, na przykład `contosonews.com` .

1. Po sprawdzeniu poprawności mapowania CNAME z domeny do swoich pierwszych drzwi wybierz pozycję **Dodaj** , aby dodać domenę niestandardową.

1. Wybierz pozycję **Zapisz** , aby przesłać zmiany.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Rekord aliasu dla wierzchołka strefy&quot;:::

1. Powyższy krok spowoduje utworzenie rekordu wierzchołka strefy wskazującego na zasób z Drzwiem przednim, a także mapowania rekordu CNAME &quot;afdverify":::

## <a name="enable-https-on-your-custom-domain"></a>Włączanie protokołu HTTPS w domenie niestandardowej

1. Wybierz domenę niestandardową, która została dodana, i poniżej sekcji adres **https domeny niestandardowej**Zmień stan na **włączone**.

1. Wybierz  **Typ zarządzania certyfikatami** , aby *użyć swojego certyfikatu*.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Rekord aliasu dla wierzchołka strefy&quot;:::

1. Powyższy krok spowoduje utworzenie rekordu wierzchołka strefy wskazującego na zasób z Drzwiem przednim, a także mapowania rekordu CNAME &quot;afdverify":::    

   > [!WARNING]
   > Typ zarządzania certyfikatami z przodu nie jest obecnie obsługiwany dla wierzchołków lub domen głównych. Jedyną opcją dostępną w przypadku włączania protokołu HTTPS na wierzchołku lub domenie głównej dla drzwi zewnętrznych jest użycie własnego niestandardowego certyfikatu TLS/SSL hostowanego na Azure Key Vault.

1. Przed przejściem do następnego kroku upewnij się, że skonfigurowano odpowiednie uprawnienia do czołowych drzwi w celu uzyskania dostępu do magazynu kluczy, który został wskazany w interfejsie użytkownika.

1. Wybierz **konto Key Vault** z bieżącej subskrypcji, a następnie wybierz odpowiednią wersję **klucza tajnego** i **wpisu tajnego** w celu zamapowania na odpowiedni certyfikat.

1. Wybierz pozycję **Aktualizuj** , aby zapisać zaznaczenie, a następnie wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Odśwież** po kilku minutach, a następnie ponownie wybierz domenę niestandardową, aby zobaczyć postęp aprowizacji certyfikatów. 

> [!WARNING]
> Upewnij się, że utworzono odpowiednie reguły routingu dla domeny Apex lub dodano domenę do istniejących reguł routingu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
