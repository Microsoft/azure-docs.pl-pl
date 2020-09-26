---
title: Autoryzuj konta dewelopera przy użyciu Azure Active Directory
titleSuffix: Azure API Management
description: Dowiedz się, jak autoryzować użytkowników przy użyciu Azure Active Directory w API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 2d3f382635e9a8b8a562f7a7f60f256d68b90b8f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363209"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory w usłudze Azure API Management

W tym artykule opisano sposób włączania dostępu do portalu dla deweloperów dla użytkowników z Azure Active Directory (Azure AD). W tym przewodniku pokazano również, jak zarządzać grupami użytkowników usługi Azure AD, dodając grupy zewnętrzne zawierające użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzuj konta dewelopera przy użyciu usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz pozycję ![Ikona strzałki.](./media/api-management-howto-aad/arrow.png).
3. Wpisz w polu wyszukiwania **interfejs API** .
4. Wybierz pozycję **API Management Services**.
5. Wybierz swoje wystąpienie usługi API Management.
6. W obszarze **Portal deweloperów**wybierz pozycję **tożsamości**.
7. Wybierz pozycję **+ Dodaj** z góry.

    Po prawej stronie pojawi się okienko **Dodawanie dostawcy tożsamości** .
8. W obszarze **Typ dostawcy**wybierz pozycję **Azure Active Directory**.

    Kontrolki, które umożliwiają wprowadzanie innych niezbędnych informacji, są wyświetlane w okienku. Formanty obejmują **Identyfikator klienta** i **klucz tajny klienta**. (Informacje o tych kontrolkach są uzyskiwane w dalszej części artykułu).
9. Zanotuj zawartość **adresu URL przekierowania**.
    
   ![Procedura dodawania dostawcy tożsamości w Azure Portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. W przeglądarce Otwórz inną kartę. 
11. Przejdź do [Rejestracje aplikacji Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908) , aby zarejestrować aplikację w Active Directory.
12. W obszarze **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
13. Wybierz pozycję **Nowa rejestracja**. Na stronie **zarejestruj aplikację** Ustaw wartości w następujący sposób:
    
    * Ustaw **nazwę** na opisową. na przykład *deweloper — Portal*
    * Ustaw **obsługiwane typy kont** **tylko na konta w tym katalogu organizacji**. 
    * Ustaw **Identyfikator URI przekierowania** na wartość uzyskaną w kroku 9. 
    * Wybierz pozycję **zarejestruj**. 

14.  Po zarejestrowaniu aplikacji Skopiuj **Identyfikator aplikacji (klienta)** ze strony **Przegląd** . 
15. Wróć do wystąpienia API Management. W oknie **Dodawanie dostawcy tożsamości** wklej wartość **identyfikatora aplikacji (klienta)** do pola **Identyfikator klienta** .
16. Przejdź z powrotem do konfiguracji usługi Azure AD, wybierz pozycję **certyfikaty & wpisy tajne** w obszarze **Zarządzaj**. Wybierz przycisk **Nowy klucz tajny klienta**. Wprowadź wartość w polu **Opis**, wybierz dowolną opcję w polu **Wygasa** i wybierz polecenie **Dodaj**. Skopiuj wartość klucza tajnego klienta przed opuszczeniem strony. Będą one potrzebne w kolejnym kroku. 
17. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie** , a następnie pozycję **tokeny ID** w obszarze **niejawne udzielenie**
18. Wróć do wystąpienia API Management, wklej wpis tajny do pola **klucz tajny klienta** .

    > [!IMPORTANT]
    > Upewnij się, że klucz **tajny klienta** został zaktualizowany przed wygaśnięciem klucza. 
    >  
    >

19. Okno **Dodawanie dostawcy tożsamości** zawiera również pole tekstowe **dozwolone dzierżawy** . W tym miejscu należy określić domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usług API Management. Można rozdzielić wiele domen z wierszami, spacjami lub przecinkami.

    > [!NOTE]
    > W sekcji **dozwolone dzierżawy** możesz określić wiele domen. Zanim użytkownik będzie mógł zalogować się z innej domeny niż oryginalna domena, w której zarejestrowano aplikację, Administrator globalny innej domeny musi udzielić uprawnienia aplikacji dostępu do danych katalogu. Aby udzielić uprawnienia, Administrator globalny powinien: a. Przejdź do `https://<URL of your developer portal>/aadadminconsent` (na przykład https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Wpisz nazwę domeny dzierżawy usługi Azure AD, do której chcesz udzielić dostępu.
    > c. Wybierz pozycję **Prześlij**. 

20.  Po określeniu odpowiedniej konfiguracji wybierz pozycję **Dodaj**.

Po zapisaniu zmian użytkownicy w określonym wystąpieniu usługi Azure AD mogą zalogować się do portalu dla deweloperów, wykonując czynności opisane w temacie [Logowanie do portalu dla deweloperów przy użyciu konta usługi Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Dodaj zewnętrzną grupę usługi Azure AD

Po włączeniu dostępu dla użytkowników w dzierżawie usługi Azure AD można dodać grupy usługi Azure AD do API Management. W związku z tym można kontrolować widoczność produktu przy użyciu grup usługi Azure AD.

Aby dodać zewnętrzną grupę usługi Azure AD do usługi APIM, należy najpierw wykonać poprzednią sekcję. Ponadto zarejestrowana aplikacja musi mieć udzielony dostęp do interfejsu API Microsoft Graph z `Directory.Read.All` uprawnieniami, wykonując następujące czynności: 

1. Wróć do rejestracji aplikacji, która została utworzona w poprzedniej sekcji.
2. Wybierz pozycję **uprawnienia interfejsu API**, a następnie kliknij pozycję **+ Dodaj uprawnienie**. 
3. W okienku **uprawnienia do interfejsu API żądania** wybierz kartę **interfejsy API firmy Microsoft** , przewiń w dół, a następnie wybierz kafelek **Azure Active Directory Graf** . Wybierz pozycję **uprawnienia aplikacji**, Wyszukaj **katalog**, a następnie wybierz uprawnienie **katalog. odczyt. wszystkie** . 
4. Kliknij pozycję **Dodaj uprawnienia** u dołu okienka, a następnie kliknij pozycję **Udziel zgody administratorowi {dzierżawca}** , aby przyznać dostęp wszystkim użytkownikom w tym katalogu. 

Teraz możesz dodać zewnętrzne grupy usługi Azure AD z karty **grupy** wystąpienia API Management.

1. Wybierz kartę **Grupy**.
2. Wybierz przycisk **Dodaj grupę usługi AAD** .
    ![Przycisk "Dodaj grupę usługi AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Wybierz grupę, którą chcesz dodać.
4. Naciśnij przycisk **Wybierz** .

Po dodaniu zewnętrznej grupy usługi Azure AD można przejrzeć i skonfigurować jej właściwości. Wybierz nazwę grupy na karcie **grupy** . W tym miejscu można edytować **nazwy** i informacje **opisu** dla grupy.
 
Użytkownicy ze skonfigurowanego wystąpienia usługi Azure AD mogą teraz logować się do portalu dla deweloperów. Mogą oni wyświetlać i subskrybować wszystkie grupy, do których mają wgląd.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Portal dla deweloperów — Dodawanie uwierzytelniania konta usługi Azure AD

W portalu dla deweloperów logowanie za pomocą usługi AAD jest możliwe za pomocą **przycisku logowania: widżet uwierzytelniania OAuth** . Element widget jest już uwzględniony na stronie logowania domyślnej zawartości portalu deweloperów.

Mimo że nowe konto zostanie utworzone automatycznie za każdym razem, gdy nowy użytkownik zaloguje się za pomocą usługi AAD, można rozważyć dodanie tego samego widżetu do strony rejestracji.

**Formularz rejestracji: element widget uwierzytelniania OAuth** reprezentuje formularz używany do rejestracji przy użyciu protokołu OAuth.

> [!IMPORTANT]
> Aby zmiany w usłudze AAD zaczęły obowiązywać, należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Starszy Portal dla deweloperów — jak zalogować się przy użyciu usługi Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Aby zalogować się do portalu dla deweloperów przy użyciu konta usługi Azure AD skonfigurowanego w poprzednich sekcjach:

1. Otwórz nowe okno przeglądarki, używając adresu URL logowania z konfiguracji aplikacji Active Directory, a następnie wybierz pozycję **Azure Active Directory**.

   ![Strona logowania][api-management-dev-portal-signin]

1. Wprowadź poświadczenia jednego z użytkowników w usłudze Azure AD, a następnie wybierz pozycję **Zaloguj**.

   ![Logowanie przy użyciu nazwy użytkownika i hasła][api-management-aad-signin]

1. Jeśli wymagane są dodatkowe informacje, może zostać wyświetlony monit z formularzem rejestracji. Wypełnij formularz rejestracji i wybierz pozycję **zarejestruj się**.

   ![Przycisk "Utwórz konto" w formularzu rejestracji][api-management-complete-registration]

Użytkownik jest teraz zalogowany do portalu dla deweloperów dla Twojego wystąpienia usługi API Management.

![Portal dla deweloperów po zakończeniu rejestracji][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
