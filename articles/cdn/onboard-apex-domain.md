---
title: Dołączanie domeny głównej lub wierzchołka do istniejącego punktu końcowego Azure CDN — Azure Portal
description: Dowiedz się, jak dołączyć domenę główną lub Apex do istniejącego punktu końcowego Azure CDN przy użyciu Azure Portal.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370353"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Dołączanie domeny głównej lub wierzchołka do istniejącego punktu końcowego Azure CDN

Azure CDN używa rekordów CNAME do weryfikacji własności domeny w celu dołączenia do domen niestandardowych. Usługa CDN nie ujawnia adresu IP frontonu skojarzonego z Twoim profilem sieci CDN. Nie można zmapować domeny Apex na adres IP, jeśli zamierzasz dołączyć ją do Azure CDN.

Protokół DNS uniemożliwia przypisanie rekordów CNAME w wierzchołku strefy. Na przykład jeśli Twoja domena to `contoso.com` ; można utworzyć rekordy CNAME dla `somelabel.contoso.com` , ale nie można utworzyć rekordu CNAME dla `contoso.com` samego siebie. To ograniczenie powoduje problem dla właścicieli aplikacji, którzy mają aplikacje o zrównoważonym obciążeniu za Azure CDN. Ponieważ użycie profilu CDN wymaga utworzenia rekordu CNAME, nie można wskazać profilu usługi CDN ze wierzchołka strefy.

Ten problem można rozwiązać przy użyciu rekordów aliasów w Azure DNS. W przeciwieństwie do rekordów CNAME, rekordy aliasów są tworzone w wierzchołku strefy. Właściciele aplikacji mogą używać go do wskazywania rekordu wierzchołka strefy w profilu sieci CDN, który ma publiczne punkty końcowe. Właściciele aplikacji wskazują ten sam profil CDN, który jest używany przez inną domenę w ramach strefy DNS. Na przykład `contoso.com` `www.contoso.com` może wskazywać ten sam profil usługi CDN. 

Mapowanie wierzchołka lub domeny głównej do profilu usługi CDN wymaga spłaszczenia CNAME lub kartach DNS. Mechanizm, w którym dostawca DNS rekursywnie rozpoznaje wpis CNAME do momentu, gdy trafi na adres IP. Ta funkcja jest obsługiwana przez Azure DNS dla punktów końcowych usługi CDN. 

> [!NOTE]
> Istnieją również inni dostawcy DNS, którzy obsługują spłaszczanie CNAME lub kartach DNS, Azure CDN zaleca się użycie Azure DNS dla klientów do hostowania swoich domen.

Azure Portal można użyć do dołączenia domeny wierzchołka w sieci CDN i włączenia na niej protokołu HTTPS przez skojarzenie jej z certyfikatem dla zakończenia protokołu TLS. Domeny Apex są również nazywane domenami głównymi lub wykorzystanymi.

## <a name="create-an-alias-record-for-zone-apex"></a>Utwórz rekord aliasu dla wierzchołka strefy

1. Otwórz **Azure DNS** konfigurację dla domeny, która ma zostać dołączona.

2. Wybierz pozycję **+ Zestaw rekordów**.

3. W obszarze **Dodaj zestaw rekordów** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ------|
    | Nazwa | Wprowadź **@** . |
    | Typ | Wybierz pozycję **A**. |
    | Zestaw rekordów aliasu | Wybierz pozycję **Tak**. |
    | Typ aliasu | Wybierz pozycję **zasób platformy Azure**. |
    | Wybierz subskrypcję | Wybierz subskrypcję. |
    | Zasób platformy Azure | Wybierz punkt końcowy usługi CDN. |

4. Wprowadź wartość parametru **TTL**.

5. Wybierz pozycję **OK** , aby przesłać zmiany.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Rekord aliasu dla wierzchołka strefy":::

6. Powyższy krok spowoduje utworzenie rekordu wierzchołka strefy wskazującego na zasób sieci CDN. **Cdnverify** mapowania rekordów CNAME służy do dołączania domeny w Twoim profilu CDN.
    1. Przykład **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Dołączanie domeny niestandardowej do sieci CDN

Po zarejestrowaniu domeny niestandardowej można dodać ją do punktu końcowego usługi CDN. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do profilu usługi CDN zawierającego punkt końcowy, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **Profil CDN** wybierz punkt końcowy usługi CDN do skojarzenia z domeną niestandardową.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Wybór punktu końcowego usługi CDN" border="true":::
    
3. Wybierz pozycję **+ domena niestandardowa**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Przycisk Dodaj domenę niestandardową" border="true":::

4. W obszarze **Dodaj domenę niestandardową** **Nazwa hosta punktu końcowego** jest wstępnie wypełniona i pochodzi od adresu URL punktu końcowego usługi CDN: **\<endpoint-hostname>** . azureedge.NET. Nie można zmienić tej nazwy.

5. W przypadku **niestandardowej nazwy hosta** wprowadź swoją niestandardową domenę root lub Apex jako domenę źródłową rekordu CNAME. 
    1. Na przykład **contoso.com**. **Nie używaj nazwy domeny** podrzędnej cdnverify.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Dodaj domenę niestandardową" border="true":::

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana. 

   Propagacja ustawień nowej domeny niestandardowej do wszystkich węzłów brzegowych usługi CDN może zająć trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut.   

## <a name="enable-https-on-your-custom-domain"></a>Włączanie protokołu HTTPS w domenie niestandardowej

Aby uzyskać więcej informacji na temat włączania protokołu HTTPS w domenie niestandardowej dla Azure CDN, zobacz [Samouczek: Konfigurowanie protokołu HTTPS w domenie niestandardowej Azure CDN](cdn-custom-ssl.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć punkt końcowy usługi CDN](cdn-create-new-endpoint.md).
