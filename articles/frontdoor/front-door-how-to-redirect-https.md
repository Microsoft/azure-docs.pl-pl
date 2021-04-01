---
title: Utwórz tylne drzwi z przekierowaniami HTTP do HTTPS przy użyciu Azure Portal
description: Dowiedz się, jak utworzyć tylne drzwi z przekierowanym ruchem z protokołu HTTP do HTTPS przy użyciu Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626646"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Utwórz tylne drzwi z przekierowaniami HTTP do HTTPS przy użyciu Azure Portal

Za pomocą Azure Portal można [utworzyć tylne drzwi](quickstart-create-front-door.md) z certyfikatem dla zakończenia protokołu TLS. Reguła routingu służy do przekierowywania ruchu HTTP do protokołu HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Utwórz drzwi z przodu przy użyciu istniejącego zasobu aplikacji sieci Web

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób** znajdujący się w lewym górnym rogu Azure Portal.

1. Wyszukaj **przód drzwi** przy użyciu paska wyszukiwania, a po znalezieniu typu zasobu wybierz pozycję **Utwórz**.

1. Wybierz *subskrypcję* , a następnie Użyj istniejącej grupy zasobów lub Utwórz nową. Wybierz pozycję **dalej** , aby wprowadzić kartę Konfiguracja.

    > [!NOTE]
    > Lokalizacja wyświetlana w interfejsie użytkownika dotyczy tylko grupy zasobów. Konfiguracja z drzwiami wstępnymi zostanie wdrożona we wszystkich [lokalizacjach pop zewnętrznych drzwi platformy Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Skonfiguruj podstawowe informacje o nowych drzwiach zewnętrznych":::

1. Konfiguracja dla drzwi przednich odbywa się w trzech krokach — dodanie domyślnego hosta frontonu, dodanie zaplecza do puli zaplecza, a następnie utworzenie reguł routingu w celu zamapowania zachowania routingu dla hosta frontonu. Wybierz ikonę " **+** " na _hostach frontonu_ , aby utworzyć hosta frontonu.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Projektant konfiguracji front-drzwi":::

1. Wprowadź globalnie unikatową nazwę domyślnego hosta frontonu dla drzwi z przodu. Wybierz pozycję **Dodaj** , aby przejść do następnego kroku.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Dodawanie hosta frontonu":::

### <a name="create-backend-pool"></a>Utwórz pulę zaplecza

1. Wybierz ikonę " **+** " w _pulach zaplecza_ , aby utworzyć pulę zaplecza. Podaj nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj wewnętrzną bazę danych**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Pula zaplecza projektanta konfiguracji front-drzwi":::

1. Wybierz typ hosta zaplecza jako _usługę App Service_. Wybierz subskrypcję, w której jest hostowana aplikacja sieci Web, a następnie wybierz konkretną aplikację sieci Web z listy rozwijanej **Nazwa hosta zaplecza**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Dodawanie zaplecza w puli zaplecza":::

1. Wybierz pozycję **Dodaj** , aby zapisać zaplecze, a następnie ponownie wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza. 

## <a name="create-http-to-https-redirect-rule"></a>Utwórz regułę przekierowania HTTP do HTTPS

1. Wybierz ikonę " **+** " w *regułach routingu* , aby utworzyć trasę. Podaj nazwę dla trasy, na przykład "HttpToHttpsRedirect", a następnie ustaw pole *zaakceptowanego protokołu* na wartość **"http Only"**. Upewnij się, że wybrano odpowiednie *frontony/domeny* .  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Reguła routingu projektanta konfiguracji front-drzwi":::

1. W sekcji *szczegóły trasy* Ustaw *Typ trasy* do **przekierowania**. Upewnij się, że *Typ przekierowania* jest ustawiony na wartość **znaleziono (302)** i *Przekieruj protokół* get **tylko na https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Dodawanie trasy HTTP do protokołu HTTPS":::

1. Wybierz pozycję **Dodaj** , aby zapisać regułę routingu dla przekierowania HTTP na https.

## <a name="create-forwarding-rule"></a>Utwórz regułę przekazywania

1. Dodaj kolejną regułę routingu do obsługi ruchu HTTPS. Wybierz pozycję " **+** " w *regułach routingu* i podaj nazwę trasy, na przykład "DefaultForwardingRoute". Następnie ustaw wartość pola *akceptowane protokoły* na **https**. Upewnij się, że wybrano odpowiednie *frontony/domeny* .

1. W sekcji Szczegóły trasy Ustaw *Typ trasy* na **Prześlij dalej**. Upewnij się, że wybrano odpowiednią pulę zaplecza, a *Protokół przekazywania* jest ustawiony **tylko na https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Dodawanie trasy przesyłania dalej dla ruchu HTTPS" border="false":::

1. Wybierz pozycję **Dodaj** , aby zapisać regułę routingu na potrzeby przekazywania żądań.

1. Wybierz pozycję **Przegląd + Utwórz** , a następnie pozycję **Utwórz**, aby utworzyć profil z drzwiami wstępnymi. Przejdź do zasobu po utworzeniu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Dowiedz się więcej o [przekierowaniu adresu URL na wierzchu drzwi](front-door-url-redirect.md).
