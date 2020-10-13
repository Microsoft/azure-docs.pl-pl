---
title: 'Samouczek: Tworzenie zasad WAF dla zewnętrznych drzwi platformy Azure Azure Portal'
description: W tym samouczku dowiesz się, jak utworzyć zasady zapory aplikacji sieci Web (WAF) przy użyciu Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602221"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Samouczek: Tworzenie zasad zapory aplikacji sieci Web na platformie Azure front-drzwi przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć podstawowe zasady zapory aplikacji sieci Web (WAF) platformy Azure i zastosować je do hosta frontonu na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

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

   ![Tworzenie zasad WAF](../media/waf-front-door-create-portal/basic.png)

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

 ![Zmień tryb zasad WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Reguły niestandardowe

Regułę niestandardową można utworzyć, wybierając pozycję **Dodaj regułę niestandardową** w sekcji **reguły niestandardowe** . Spowoduje to uruchomienie strony Konfiguracja reguły niestandardowej. Poniżej znajduje się przykład konfigurowania reguły niestandardowej do blokowania żądania, jeśli ciąg zapytania zawiera **blockme**.

![Reguły niestandardowe](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Domyślny zestaw reguł (DRS)

Domyślny zestaw reguł zarządzany przez platformę Azure jest domyślnie włączony. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz **pole wyboru** przed numerem reguły, a następnie na karcie powyżej wybierz pozycję **Wyłącz** . Aby zmienić typy akcji dla poszczególnych reguł w zestawie reguł, zaznacz pole wyboru przed numerem reguły, a następnie wybierz powyższą kartę **Zmień akcję** .

 ![Zmień zestaw reguł WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o drzwiach frontonu platformy Azure](../../frontdoor/front-door-overview.md)