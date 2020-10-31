---
title: Autoryzuj konta dewelopera przy użyciu protokołu OAuth 2,0 w API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak autoryzować użytkowników przy użyciu protokołu OAuth 2,0 w API Management. Uwierzytelnianie OAuth 2,0 zabezpiecza interfejs API, dzięki czemu użytkownicy mogą uzyskiwać dostęp tylko do zasobów, do których są uprawnieni.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077255"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak autoryzować konta dewelopera przy użyciu protokołu OAuth 2,0 na platformie Azure API Management

Wiele interfejsów API obsługuje protokół [OAuth 2,0](https://oauth.net/2/) , aby zabezpieczyć interfejs API i upewnić się, że tylko Prawidłowi użytkownicy mają dostęp, i mogą uzyskać dostęp tylko do zasobów, do których są uprawnieni. Aby można było używać interaktywnej konsoli dewelopera platformy Azure API Management z takimi interfejsami API, Usługa umożliwia skonfigurowanie wystąpienia usługi do pracy z interfejsem API z włączonym protokołem OAuth 2,0.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Wymagania wstępne

W tym przewodniku pokazano, jak skonfigurować wystąpienie usługi API Management do korzystania z autoryzacji OAuth 2,0 dla kont deweloperów, ale nie pokazuje, jak skonfigurować dostawcę OAuth 2,0. Konfiguracja dla każdego dostawcy OAuth 2,0 jest różna, chociaż czynności są podobne, a wymagane fragmenty informacji używane podczas konfigurowania uwierzytelniania OAuth 2,0 w wystąpieniu usługi API Management są takie same. W tym temacie przedstawiono przykłady użycia Azure Active Directory jako dostawcy protokołu OAuth 2,0.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania protokołu OAuth 2,0 przy użyciu Azure Active Directory, zobacz przykład [webapp-GraphAPI-dotnet][WebApp-GraphAPI-DotNet] .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurowanie serwera autoryzacji OAuth 2,0 w API Management

> [!NOTE]
> Jeśli nie utworzono jeszcze wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].

1. Kliknij kartę OAuth 2,0 w menu po lewej stronie i kliknij pozycję **+ Dodaj** .

    ![Menu OAuth 2,0](./media/api-management-howto-oauth2/oauth-01.png)

2. Wprowadź nazwę i opcjonalny opis w polach **Nazwa** i **Opis** .

    > [!NOTE]
    > Te pola służą do identyfikowania serwera autoryzacji OAuth 2,0 w ramach bieżącego wystąpienia usługi API Management i ich wartości nie pochodzą z serwera OAuth 2,0.

3. Wprowadź **adres URL strony rejestracji klienta** . Na tej stronie można tworzyć konta i zarządzać nimi oraz zmieniać się w zależności od używanego dostawcy protokołu OAuth 2,0. **Adres URL strony rejestracji klienta** wskazuje na stronę, za pomocą której użytkownicy mogą tworzyć i konfigurować własne konta dla dostawców OAuth 2,0, którzy obsługują zarządzanie kontami, na przykład `https://contoso.com/login` . Niektóre organizacje nie konfigurują ani nie używają tej funkcji, nawet jeśli obsługują ją dostawca OAuth 2,0. Jeśli dostawca OAuth 2,0 nie ma skonfigurowanych kont zarządzanie użytkownikami, wprowadź w tym miejscu zastępczy adres URL, na przykład adres URL firmy lub adres URL, taki jak `https://placeholder.contoso.com` .

    ![Nowy serwer OAuth 2,0](./media/api-management-howto-oauth2/oauth-02.png)

4. Następna sekcja formularza zawiera **typy przyzwoleń autoryzacji** , **adres URL punktu końcowego autoryzacji** i ustawienia **metody żądania autoryzacji** .

    Określ **typy przyzwoleń autoryzacji** , sprawdzając odpowiednie typy. **Kod autoryzacji** jest domyślnie określony.

    Wprowadź **adres URL punktu końcowego autoryzacji** . W przypadku Azure Active Directory ten adres URL będzie podobny do następującego adresu URL, gdzie `<tenant_id>` jest zastępowany identyfikatorem dzierżawy usługi Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoda żądania autoryzacji** określa, w jaki sposób żądanie autoryzacji jest wysyłane do serwera OAuth 2,0. Domyślnie jest wybrana wartość **Pobierz** .

5. Następnie należy określić **adres URL punktu końcowego tokenu** , **metody uwierzytelniania klienta** , **metodę wysyłania tokenu dostępu** i **zakres domyślny** .

    ![Zrzut ekranu przedstawiający ekran Dodawanie usługi OAuth2.](./media/api-management-howto-oauth2/oauth-03.png)

    W przypadku serwera Azure Active Directory OAuth 2,0 **adres URL punktu końcowego tokenu** będzie miał następujący format, gdzie `<TenantID>`  ma format `yourapp.onmicrosoft.com` .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Domyślnym ustawieniem dla **metod uwierzytelniania klienta** jest **podstawowa** , a  **metoda wysyłania tokenu dostępu** jest **nagłówkiem autoryzacji** . Te wartości są konfigurowane w tej sekcji formularza wraz z **zakresem domyślnym** .

6. Sekcja **poświadczenia klienta** zawiera **Identyfikator klienta** i **klucz tajny klienta** , które są uzyskiwane podczas procesu tworzenia i konfiguracji serwera OAuth 2,0. Po określeniu **identyfikatora klienta** i **klucza tajnego klienta** zostanie wygenerowany **redirect_uri** dla **kodu autoryzacji** . Ten identyfikator URI służy do konfigurowania adresu URL odpowiedzi w konfiguracji serwera OAuth 2,0.

    W nowym portalu dla deweloperów sufiks URI ma postać:

    - `/signin-oauth/code/callback/{authServerName}` w przypadku przepływu przyznawania kodu autoryzacji
    - `/signin-oauth/implicit/callback` dla niejawnego przepływu dotacji

    ![Zrzut ekranu pokazujący, gdzie dodać poświadczenia klienta dla nowej usługi OAuth2.](./media/api-management-howto-oauth2/oauth-04.png)

    Jeśli **Typ przydzielenia autoryzacji** jest ustawiony na **hasło właściciela zasobu** , sekcja **poświadczeń hasła właściciela zasobu** służy do określania tych poświadczeń. w przeciwnym razie można pozostawić to pole puste.

    Po zakończeniu formularza kliknij pozycję **Utwórz** , aby zapisać konfigurację serwera autoryzacji API Management OAuth 2,0. Po zapisaniu konfiguracji serwera można skonfigurować interfejsy API do korzystania z tej konfiguracji, jak pokazano w następnej sekcji.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurowanie interfejsu API do korzystania z autoryzacji użytkownika OAuth 2,0

1. Kliknij pozycję **interfejsy API** w menu **API Management** po lewej stronie.

    ![Interfejsy API protokołu OAuth 2,0](./media/api-management-howto-oauth2/oauth-05.png)

2. Kliknij nazwę żądanego interfejsu API, a następnie kliknij pozycję **Ustawienia** . Przewiń do sekcji **zabezpieczenia** , a następnie zaznacz pole wyboru **OAuth 2,0** .

    ![Ustawienia protokołu OAuth 2,0](./media/api-management-howto-oauth2/oauth-06.png)

3. Wybierz żądany **serwer autoryzacji** z listy rozwijanej, a następnie kliknij przycisk **Zapisz** .

    ![Zrzut ekranu, który podświetla wybrany serwer autoryzacji i przycisk Zapisz.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Starszy Portal dla deweloperów — Testowanie autoryzacji użytkownika protokołu OAuth 2,0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Po skonfigurowaniu serwera autoryzacji OAuth 2,0 i skonfigurowaniu interfejsu API do korzystania z tego serwera można go przetestować, przechodząc do portalu dla deweloperów i wywołując interfejs API. Kliknij przycisk **Portal dla deweloperów (starsza wersja)** w górnym menu na stronie **Przegląd** wystąpienia usługi Azure API Management.

Kliknij pozycję **interfejsy API** w górnym menu i wybierz pozycję **echo API** .

![Interfejs Echo API][api-management-apis-echo-api]

> [!NOTE]
> Jeśli istnieje tylko jeden interfejs API skonfigurowany lub widoczny dla Twojego konta, kliknięcie opcji Interfejsy API powoduje przejście bezpośrednio do operacji dla tego interfejsu API.

Wybierz operację **Pobierz zasób** , kliknij pozycję **Otwórz konsolę** , a następnie z listy rozwijanej wybierz pozycję **kod autoryzacji** .

![Otwarta konsola][api-management-open-console]

Po wybraniu **kodu autoryzacji** zostanie wyświetlone okno podręczne z formularzem logowania dostawcy OAuth 2,0. W tym przykładzie formularz logowania jest dostarczany przez Azure Active Directory.

> [!NOTE]
> Jeśli wyskakujące okienka zostały wyłączone, zostanie wyświetlony monit o włączenie ich przez przeglądarkę. Po ich włączeniu ponownie wybierz pozycję **kod autoryzacji** , a zostanie wyświetlony formularz logowania.

![Zaloguj][api-management-oauth2-signin]

Po zalogowaniu **nagłówki żądań** są wypełniane `Authorization : Bearer` nagłówkiem, który autoryzuje żądanie.

![Token nagłówka żądania][api-management-request-header-token]

W tym momencie można skonfigurować odpowiednie wartości dla pozostałych parametrów i przesłać żądanie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z protokołu OAuth 2,0 i API Management, zobacz następujący film wideo i towarzyszący [artykuł](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
