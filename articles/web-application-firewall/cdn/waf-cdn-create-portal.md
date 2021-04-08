---
title: 'Samouczek: Tworzenie zasad WAF dla Azure CDN-Azure Portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) na Azure CDN przy użyciu Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92132775"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Samouczek: Tworzenie zasad WAF na Azure CDN przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web (WAF) platformy Azure i zastosować je do punktu końcowego w usłudze Azure Content Delivery Network (CDN).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad WAF
> * Skojarz ją z punktem końcowym usługi CDN. Zasady WAF można skojarzyć tylko z punktami końcowymi, które są hostowane w **standardzie Azure CDN z jednostki SKU firmy Microsoft** .
> * Konfigurowanie reguł WAF

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil Azure CDN i punkt końcowy, postępując zgodnie z instrukcjami w [przewodniku szybki start: Tworzenie profilu Azure CDN i punktu końcowego](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Tworzenie zasad zapory aplikacji sieci Web

Najpierw Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu portalu.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**>wyszukaj **WAF**>wybierz pozycję **Zapora aplikacji sieci Web** > wybierz pozycję **Utwórz**.
2. Na karcie **podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Zasady dla            |Wybierz pozycję Azure CDN (wersja zapoznawcza).|
    | Subskrypcja            |Wybierz nazwę subskrypcji z drzwiami przednimi.|
    | Grupa zasobów          |Wybierz nazwę grupy zasobów drzwi.|
    | Nazwa zasady             |Wprowadź unikatową nazwę zasad WAF.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad W języku F z przyciskiem Przejrzyj i Utwórz oraz wartościami wprowadzonymi dla różnych ustawień." border="false":::

3. Na karcie **skojarzenie** na stronie **Tworzenie zasad WAF** wybierz pozycję **Dodaj punkt końcowy usługi CDN**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Profil CDN              | Wybierz nazwę profilu CDN.|
    | Punkt końcowy           | Wybierz nazwę punktu końcowego, a następnie wybierz pozycję **Dodaj**.|
    
    > [!NOTE]
    > Jeśli punkt końcowy jest skojarzony z zasadami WAFymi, jest wyświetlany szary. Musisz najpierw usunąć punkt końcowy ze skojarzonych zasad, a następnie ponownie skojarzyć punkt końcowy z nowymi zasadami WAF.
1. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurowanie zasad zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Domyślnie zasady WAF są w trybie *wykrywania* podczas tworzenia zasad WAF. W trybie *wykrywania* WAF nie blokuje żadnych żądań. Zamiast tego żądania zgodne z regułami WAF są rejestrowane w dziennikach WAF.

Aby wyświetlić WAF w działaniu, można zmienić ustawienia trybu z *wykrywania* na *zapobieganie*. W trybie *zapobiegania* żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Zrzut ekranu przedstawiający sekcję ustawień zasad. Przełącznik trybów jest ustawiony na zapobieganie." border="false":::

### <a name="custom-rules"></a>Reguły niestandardowe

Aby utworzyć regułę niestandardową, wybierz pozycję **Dodaj regułę** niestandardową w sekcji **reguły niestandardowe** . Spowoduje to otwarcie strony Konfiguracja reguły niestandardowej. Istnieją dwa typy reguł niestandardowych: **Reguła dopasowania** i reguła **limitu szybkości** .

Poniższy zrzut ekranu przedstawia niestandardową regułę dopasowania w celu zablokowania żądania, jeśli ciąg zapytania zawiera wartość **blockme**.

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="Zrzut ekranu przedstawiający stronę konfiguracji reguła niestandardowa przedstawiającą ustawienia reguły, która sprawdza, czy zmienna QueryString zawiera wartość blockme." border="false":::

Reguły limitu szybkości wymagają dwóch dodatkowych pól: **Limit szybkości trwania** i **próg limitu szybkości (żądania)** , jak pokazano w następującym przykładzie:

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Zrzut ekranu przedstawiający stronę konfiguracji reguły limitu szybkości. Pole listy czas trwania limitu szybkości i próg limitu szybkości (żądania) są widoczne." border="false":::

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Zestaw reguł domyślnych zarządzanych przez platformę Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz pole wyboru przed numerem reguły, a następnie na karcie powyżej wybierz pozycję **Wyłącz** . Aby zmienić typy akcji dla poszczególnych reguł w zestawie reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz powyższą kartę **Zmień akcję** .

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Zrzut ekranu strony reguły zarządzane przedstawiający zestaw reguł, grupy reguł, reguły oraz przyciski włączania, wyłączania i zmieniania akcji. Zaznaczono jedną regułę." border="false":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zaporze aplikacji sieci Web platformy Azure](../overview.md)
