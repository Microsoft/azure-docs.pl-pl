---
title: Utwórz tylne drzwi z przekierowaniami HTTP do HTTPS przy użyciu Azure Portal
description: Dowiedz się, jak utworzyć tylne drzwi z przekierowanym ruchem z protokołu HTTP do HTTPS przy użyciu Azure Portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878004"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Utwórz tylne drzwi z przekierowaniami HTTP do HTTPS przy użyciu Azure Portal

Za pomocą Azure Portal można utworzyć [tylne drzwi](front-door-overview.md) z certyfikatem dla zakończenia protokołu TLS. Reguła routingu służy do przekierowywania ruchu HTTP do protokołu HTTPS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz drzwi z przodu przy użyciu istniejącego zasobu aplikacji sieci Web
> * Dodawanie domeny niestandardowej z certyfikatem TLS/SSL 
> * Konfigurowanie przekierowania protokołu HTTPS w domenie niestandardowej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Utwórz drzwi z przodu przy użyciu istniejącego zasobu aplikacji sieci Web

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wyszukaj **przód drzwi** przy użyciu paska wyszukiwania, a po znalezieniu typu zasobu kliknij przycisk **Utwórz**.
4. Wybierz subskrypcję, a następnie Użyj istniejącej grupy zasobów lub Utwórz nową. Należy pamiętać, że lokalizacja zaproszona w interfejsie użytkownika dotyczy tylko grupy zasobów. Konfiguracja z drzwiami wstępnymi zostanie wdrożona we wszystkich [lokalizacjach pop zewnętrznych drzwi platformy Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Skonfiguruj podstawowe informacje o nowych drzwiach zewnętrznych](./media/front-door-url-redirect/front-door-create-basics.png)

5. Kliknij przycisk **dalej** , aby wprowadzić kartę Konfiguracja. Konfiguracja dla drzwi przednich odbywa się w trzech krokach — dodanie domyślnego hosta frontonu, dodanie zaplecza do puli zaplecza, a następnie utworzenie reguł routingu w celu zamapowania zachowania routingu dla hosta frontonu. 

     ![Projektant konfiguracji front-drzwi](./media/front-door-url-redirect/front-door-designer.png)

6. Kliknij ikonę "**+**" na _hostach frontonu_ , aby utworzyć hosta frontonu, a następnie wprowadź unikatową nazwę globalną dla domyślnego hosta frontonu (`\<**name**\>.azurefd.net`). Kliknij przycisk **Dodaj** , aby przejść do następnego kroku.

     ![Dodawanie hosta frontonu](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kliknij ikonę "**+**" w _pulach zaplecza_ , aby utworzyć pulę zaplecza. Podaj nazwę puli zaplecza, a następnie kliknij pozycję "**Dodaj wewnętrzną bazę danych**".
8. Wybierz typ hosta zaplecza jako _usługę App Service_. Wybierz subskrypcję, w której jest hostowana aplikacja sieci Web, a następnie wybierz konkretną aplikację sieci Web z listy rozwijanej **Nazwa hosta zaplecza**.
9. Kliknij przycisk **Dodaj** , aby zapisać zaplecze, a następnie ponownie kliknij przycisk **Dodaj** , aby zapisać konfigurację puli zaplecza.   ![Dodawanie zaplecza w puli zaplecza](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Kliknij ikonę "**+**" w _regułach routingu_ , aby utworzyć trasę. Podaj nazwę trasy, powiedz "HttpToHttpsRedirect", a następnie ustaw wartość pola _akceptowane protokoły_ na wartość **"http Only"**. Upewnij się, że wybrano odpowiedni _host frontonu_ .  
11. W sekcji _szczegóły trasy_ Ustaw _Typ trasy_ na **przekierowania**, upewnij się, że _Typ przekierowania_ jest ustawiony na **wartość znaleziono (302)** , a _Protokół przekierowania_ jest ustawiony **tylko na https**. 
12. Kliknij przycisk Dodaj, aby zapisać regułę routingu dla przekierowania HTTP na HTTPS.
     ![Dodawanie trasy HTTP do protokołu HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Dodaj kolejną regułę routingu do obsługi ruchu HTTPS. Kliknij przycisk "**+**" w _regułach routingu_ i podaj nazwę trasy, powiedz "DefaultForwardingRoute", a następnie ustaw pole _akceptowane protokoły_ na wartość **"https Only"**. Upewnij się, że wybrano odpowiedni _host frontonu_ .
14. W sekcji Szczegóły trasy Ustaw _Typ trasy_ na **Prześlij dalej**, upewnij się, że wybrano odpowiednią pulę zaplecza, a _Protokół przekazywania_ jest ustawiony tylko na protokół **https**. 
15. Kliknij przycisk Dodaj, aby zapisać regułę routingu na potrzeby przekazywania żądań.
     ![Dodawanie trasy przesyłania dalej dla ruchu HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz**, aby utworzyć profil z drzwiami wstępnymi. Przejdź do zasobu po utworzeniu.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Dodaj domenę niestandardową do czołowych drzwi i Włącz protokół HTTPS
W poniższych krokach opisano, jak dodać domenę niestandardową do istniejącego zasobu frontonu, a następnie włączyć na nim przekierowywanie protokołu HTTP do protokołu HTTPS. 

### <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej

W tym przykładzie należy dodać rekord CNAME dla `www` domeny podrzędnej (na przykład `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Dodaj rekord CNAME, aby zmapować poddomenę na domyślnego hosta frontonu na początku drzwi (`<name>.azurefd.net`gdzie `<name>` to nazwa profilu przedniego drzwi).

W przypadku `www.contoso.com` domeny na przykład Dodaj rekord CNAME, który mapuje nazwę `www` na. `<name>.azurefd.net`

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Domena niestandardowa CNAME do przednich drzwi](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Dołącz domenę niestandardową do swoich drzwi

1. Na karcie Projektant drzwi przednich kliknij ikonę "+" w sekcji hosty frontonu, aby dodać nową domenę niestandardową. 
2. Wprowadź w pełni kwalifikowaną niestandardową nazwę DNS w polu Nazwa hosta niestandardowego `www.contosonews.com`. 
3. Po sprawdzeniu poprawności mapowania CNAME z domeny do swoich pierwszych drzwi kliknij przycisk **Dodaj** , aby dodać domenę niestandardową.
4. Kliknij przycisk **Zapisz** , aby przesłać zmiany.

![Menu domen niestandardowych](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Włączanie protokołu HTTPS w domenie niestandardowej

1. Kliknij domenę niestandardową, która została dodana, i poniżej sekcji adres **https domeny niestandardowej**Zmień stan na **włączone**.
2. **Typ zarządzania certyfikatami** można pozostawić ustawiony na _frontony zarządzane_ dla bezpłatnego certyfikatu obsługiwanego, zarządzanego i obracanego przez tylne drzwi. Możesz również użyć własnego niestandardowego certyfikatu TLS/SSL przechowywanego w usłudze Azure Key Vault. W tym samouczku przyjęto założenie, że jest używane certyfikaty zarządzane przed drzwiami.
![Włączanie protokołu HTTPS dla domeny niestandardowej](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kliknij przycisk **Aktualizuj** , aby zapisać zaznaczenie, a następnie kliknij przycisk **Zapisz**.
4. Kliknij przycisk **Odśwież** po kilku minutach, a następnie ponownie kliknij domenę niestandardową, aby zobaczyć postęp aprowizacji certyfikatów. 

> [!WARNING]
> Włączenie protokołu HTTPS dla domeny niestandardowej może potrwać kilka minut, a także zależeć od weryfikacji własności domeny, jeśli rekord CNAME nie został bezpośrednio zmapowany `<name>.azurefd.net`na hosta z drzwiczkami. Dowiedz się więcej na temat [włączania protokołu HTTPS dla domeny niestandardowej](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurowanie reguł routingu dla domeny niestandardowej

1. Kliknij utworzoną wcześniej regułę routingu przekierowania.
2. Kliknij listę rozwijaną dla hostów frontonu i wybierz domenę niestandardową, aby zastosować tę trasę również dla Twojej domeny.
3. Kliknij przycisk **Update** (Aktualizuj).
4. Wykonaj tę samą operację dla innej reguły routingu, a także dla trasy przesyłania dalej, aby dodać domenę niestandardową.
5. Kliknij przycisk **Zapisz** , aby przesłać zmiany.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Dowiedz się więcej o [przekierowaniu adresu URL na wierzchu drzwi](front-door-url-redirect.md).
