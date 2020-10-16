---
title: 'Samouczek: Tworzenie zasad WAF dla zewnętrznych drzwi platformy Azure Azure Portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) przy użyciu Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: ba344c3b1570c041e1602bdfcde1b3a4055dc396
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132758"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Samouczek: Tworzenie zasad zapory aplikacji sieci Web na platformie Azure front-drzwi przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web (WAF) platformy Azure i zastosować je do hosta frontonu na platformie Azure.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie zasad WAF
> * Skojarz ją z hostem frontonu
> * Konfigurowanie reguł WAF

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil z drzwiami wstępnymi, wykonując instrukcje opisane w [przewodniku szybki start: Tworzenie profilu front-drzwi](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Tworzenie zasad zapory aplikacji sieci Web

Najpierw Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu portalu. 

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**>wyszukaj **WAF**>wybierz opcję **Zapora aplikacji sieci Web (wersja zapoznawcza)** > wybierz pozycję **Utwórz**.
2. Na karcie **podstawy** na stronie **Tworzenie zasad WAF** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja            |Wybierz nazwę subskrypcji z drzwiami przednimi.|
    | Grupa zasobów          |Wybierz nazwę grupy zasobów drzwi.|
    | Nazwa zasady             |Wprowadź unikatową nazwę zasad WAF.|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad języka F z przyciskami Przejrzyj i Utwórz oraz polami listy dla subskrypcji, grupy zasobów i nazwy zasad." border="false":::

3. Na karcie **skojarzenie** na stronie **Tworzenie zasad WAF** wybierz pozycję **Dodaj hosta frontonu**, wprowadź następujące ustawienia, a następnie wybierz pozycję **Dodaj**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Drzwi z przodu              | Wybierz nazwę profilu frontonu.|
    | Host frontonu           | Wybierz nazwę hosta z drzwiami przednimi, a następnie wybierz pozycję **Dodaj**.|
    
    > [!NOTE]
    > Jeśli host frontonu jest skojarzony z zasadami WAF, jest pokazywany jako wyszarzony. Najpierw należy usunąć hosta frontonu ze skojarzonych zasad, a następnie ponownie skojarzyć hosta frontonu z nowymi zasadami WAF.
1. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurowanie reguł zapory aplikacji sieci Web (opcjonalnie)

### <a name="change-mode"></a>Zmień tryb

Podczas tworzenia zasad WAFymi domyślne zasady WAF są w trybie **wykrywania** . W trybie **wykrywania** WAF nie blokuje żadnych żądań, a zamiast tego żądania zgodne z regułami WAF są rejestrowane w dziennikach WAF.
Aby wyświetlić WAF w działaniu, można zmienić ustawienia trybu z **wykrywania** na **zapobieganie**. W trybie **zapobiegania** żądania zgodne z regułami zdefiniowanymi w domyślnym zestawie reguł (DRS) są blokowane i rejestrowane w dziennikach WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad języka F z przyciskami Przejrzyj i Utwórz oraz polami listy dla subskrypcji, grupy zasobów i nazwy zasad." border="false":::

### <a name="custom-rules"></a>Reguły niestandardowe

Regułę niestandardową można utworzyć, wybierając pozycję **Dodaj regułę niestandardową** w sekcji **reguły niestandardowe** . Spowoduje to uruchomienie strony Konfiguracja reguły niestandardowej. Poniżej znajduje się przykład konfigurowania reguły niestandardowej do blokowania żądania, jeśli ciąg zapytania zawiera **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad języka F z przyciskami Przejrzyj i Utwórz oraz polami listy dla subskrypcji, grupy zasobów i nazwy zasad." border="false":::

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Domyślny zestaw reguł zarządzany przez platformę Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz **pole wyboru** przed numerem reguły, a następnie na karcie powyżej wybierz pozycję **Wyłącz** . Aby zmienić typy akcji dla poszczególnych reguł w zestawie reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz powyższą kartę **Zmień akcję** .

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie zasad języka F z przyciskami Przejrzyj i Utwórz oraz polami listy dla subskrypcji, grupy zasobów i nazwy zasad." border="false":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o drzwiach frontonu platformy Azure](../../frontdoor/front-door-overview.md)