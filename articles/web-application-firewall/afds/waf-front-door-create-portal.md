---
title: 'Samouczek: Tworzenie zasad WAF dla zewnętrznych drzwi platformy Azure Azure Portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) przy użyciu Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122260"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Samouczek: Tworzenie zasad zapory aplikacji sieci Web na platformie Azure front-drzwi przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web (WAF) platformy Azure i zastosować je do hosta frontonu na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad WAF
> * Skojarz ją z hostem frontonu
> * Konfigurowanie reguł WAF

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil z [przodu](../../frontdoor/quickstart-create-front-door.md) lub z [przodu dla warstwy Standardowa/Premium](../../frontdoor/standard-premium/create-front-door-portal.md) . 

## <a name="create-a-web-application-firewall-policy"></a>Tworzenie zasad zapory aplikacji sieci Web

Najpierw Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu portalu. 

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > wyszukaj **WAF** > wybierz opcję **Zapora aplikacji sieci Web (WAF)** > wybierz pozycję **Utwórz**.

1. Na karcie **podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Zasady dla              | Wybierz pozycję **globalne WAF (tylne drzwi)**. |
    | Jednostka SKU z przodu          | Wybierz między podstawową, standardową i Premium jednostką SKU. |
    | Subskrypcja            | Wybierz nazwę subskrypcji z drzwiami przednimi.|
    | Grupa zasobów          | Wybierz nazwę grupy zasobów drzwi.|
    | Nazwa zasady             | Wprowadź unikatową nazwę zasad WAF.|
    | Stan zasad            | Ustaw jako **włączone**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad języka F z przyciskami Przejrzyj i Utwórz oraz polami listy dla subskrypcji, grupy zasobów i nazwy zasad.":::

1. Na karcie **skojarzenie** na stronie **Tworzenie zasad WAF** wybierz pozycję **+ Skojarz profil przeddrzwi**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Profil przeddrzwi              | Wybierz nazwę profilu frontonu. |
    | Domains          | Wybierz domeny, do których chcesz skojarzyć zasady WAF, a następnie wybierz pozycję **Dodaj**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Zrzut ekranu przedstawiający stronę profilu z przednim Drzwiem.":::
    
    > [!NOTE]
    > Jeśli domena jest skojarzona z zasadami WAF, jest wyświetlana jako wyszarzona. Najpierw należy usunąć domenę ze skojarzonych zasad, a następnie ponownie skojarzyć domenę z nowymi zasadami WAF.

1. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurowanie reguł zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Podczas tworzenia zasad WAFymi domyślne zasady WAF są w trybie **wykrywania** . W trybie **wykrywania** WAF nie blokuje żadnych żądań, a zamiast tego żądania zgodne z regułami WAF są rejestrowane w dziennikach WAF.
Aby wyświetlić WAF w działaniu, można zmienić ustawienia trybu z **wykrywania** na **zapobieganie**. W trybie **zapobiegania** żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Zrzut ekranu przedstawiający sekcję ustawień zasad. Przełącznik trybów jest ustawiony na zapobieganie.":::

### <a name="custom-rules"></a>Reguły niestandardowe

Regułę niestandardową można utworzyć, wybierając pozycję **Dodaj regułę niestandardową** w sekcji **reguły niestandardowe** . Spowoduje to uruchomienie strony Konfiguracja reguły niestandardowej. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Zrzut ekranu strony reguł niestandardowych.":::

Poniżej znajduje się przykład konfigurowania reguły niestandardowej do blokowania żądania, jeśli ciąg zapytania zawiera **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Zrzut ekranu przedstawiający stronę konfiguracji reguła niestandardowa przedstawiającą ustawienia reguły, która sprawdza, czy zmienna QueryString zawiera wartość blockme.":::

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Domyślny zestaw reguł zarządzany przez platformę Azure jest domyślnie włączony. Bieżąca wersja domyślna to DefaultRuleSet_1.0. W ramach **reguł zarządzanych** przez program WAF na liście rozwijanej jest dostępny program **Assign**, a ostatnio dostępny zestaw reguł Microsoft_DefaultRuleSet_1 dziesiątki.

Aby wyłączyć pojedynczą regułę, zaznacz **pole wyboru** przed numerem reguły, a następnie wybierz pozycję **Wyłącz** w górnej części strony. Aby zmienić typy akcji dla poszczególnych reguł w zestawie reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz **akcję Zmień** w górnej części strony.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Zrzut ekranu strony reguły zarządzane przedstawiający zestaw reguł, grupy reguł, reguły oraz przyciski włączania, wyłączania i zmieniania akcji." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o drzwiach](../../frontdoor/front-door-overview.md) 
>  frontonu platformy Azure [Dowiedz się więcej o usłudze Azure Front-Standard/Premium](../../frontdoor/standard-premium/overview.md)
