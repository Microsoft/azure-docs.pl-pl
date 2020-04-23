---
title: Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory
titleSuffix: Azure API Management
description: Dowiedz się, jak autoryzować użytkowników przy użyciu usługi Azure Active Directory w usłudze API Management.
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
ms.openlocfilehash: 41f9f267880d199d2e221453eea5c3584ce96881
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868393"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory w usłudze Azure API Management

W tym artykule pokazano, jak włączyć dostęp do portalu dla deweloperów dla użytkowników z usługi Azure Active Directory (Azure AD). W tym przewodniku pokazano również, jak zarządzać grupami użytkowników usługi Azure AD, dodając grupy zewnętrzne, które zawierają użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończ następujące przewodniki Szybki start: [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzowanie kont deweloperów przy użyciu usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
3. Wpisz **interfejs API** w polu wyszukiwania.
4. Wybierz **pozycję Usługi zarządzania interfejsami API**.
5. Wybierz swoje wystąpienie usługi API Management.
6. W **obszarze Portal deweloperów**wybierz pozycję **Tożsamości**.
7. Wybierz **+Dodaj** od góry.

    Po prawej stronie pojawi się okienko **Dodaj dostawcę tożsamości.**
8. W obszarze **Typ dostawcy**wybierz pozycję Azure **Active Directory**.

    Formanty umożliwiające wprowadzanie innych niezbędnych informacji są wyświetlane w okienku. Formanty obejmują **identyfikator klienta** i klucz **tajny klienta.** (W dalszej części artykułu można uzyskać informacje o tych formantach).
9. Zanotuj zawartość **adresu URL przekierowania**.
    
   ![Kroki dotyczące dodawania dostawcy tożsamości w witrynie Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. W przeglądarce otwórz inną kartę. 
11. Przejdź do [witryny Azure portal — rejestracje aplikacji,](https://go.microsoft.com/fwlink/?linkid=2083908) aby zarejestrować aplikację w usłudze Active Directory.
12. W obszarze **Zarządzanie**wybierz pozycję **Rejestracje aplikacji**.
13. Wybierz **pozycję Nowa rejestracja**. Na stronie **Zarejestruj aplikację** ustaw wartości w następujący sposób:
    
    * Ustaw **nazwę** na znaczącą nazwę. np. *developer-portal*
    * Ustaw **obsługiwane typy kont** tylko na Konta w tym katalogu **organizacyjnym**. 
    * Ustaw **zestaw przekierowania identyfikatora URI** do wartości, którą otrzymałeś z kroku 9. 
    * Wybierz **pozycję Zarejestruj**. 

14.  Po zarejestrowaniu aplikacji skopiuj **identyfikator aplikacji (klienta)** ze strony **Przegląd.** 
15. Wróć do wystąpienia usługi API Management. W oknie **Dodaj dostawcę tożsamości** wklej wartość **identyfikatora aplikacji (klienta)** w polu **Identyfikator klienta.**
16. Przełącz się z powrotem do konfiguracji usługi Azure AD, wybierz **certyfikaty & wpisy tajne** w obszarze **Zarządzanie**. Wybierz przycisk **Nowy klucz tajny klienta**. Wprowadź wartość w polu **Opis**, wybierz dowolną opcję w polu **Wygasa** i wybierz polecenie **Dodaj**. Skopiuj wartość tajnego klienta przed opuszczeniem strony. Będą one potrzebne w kolejnym kroku. 
17. W obszarze **Zarządzanie**wybierz **pozycję Uwierzytelnianie,** a następnie wybierz pozycję **Tokeny identyfikatorów** w obszarze **Niejawna dotacja**
18. Wróć do wystąpienia zarządzania interfejsami API, wklej klucz tajny do tajnego pola **klienta.**

    > [!IMPORTANT]
    > Upewnij się, że kod **tajny klienta zostanie** zaktualizowany przed wygaśnięciem klucza. 
    >  
    >

19. Okno **Dodaj dostawcę tożsamości** zawiera również pole **tekstowe Dozwolone dzierżawy.** Tam określ domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usługi zarządzania interfejsami API. Można oddzielić wiele domen za pomocą nowych linii, spacji lub przecinków.

    > [!NOTE]
    > Można określić wiele domen w sekcji **Dozwolone dzierżawy.** Aby użytkownik mógł zalogować się z innej domeny niż oryginalna domena, w której aplikacja została zarejestrowana, administrator globalny innej domeny musi udzielić aplikacji uprawnień do uzyskiwania dostępu do danych katalogu. Aby udzielić uprawnień, administrator globalny powinien: a. Przejdź `https://<URL of your developer portal>/aadadminconsent` do (na https://contoso.portal.azure-api.net/aadadminconsent)przykład .
    > b. Wpisz nazwę domeny dzierżawy usługi Azure AD, do których mają zostać nadać dostęp.
    > d. Wybierz pozycję **Prześlij**. 

20.  Po określeniu żądanej konfiguracji wybierz pozycję **Dodaj**.

Po zapisaniu zmian użytkownicy w określonym wystąpieniu usługi Azure AD mogą logować się do portalu dla deweloperów, wykonując kroki opisane w [logowanie do portalu dla deweloperów przy użyciu konta usługi Azure AD.](#log_in_to_dev_portal)

## <a name="add-an-external-azure-ad-group"></a>Dodawanie zewnętrznej grupy usługi Azure AD

Po włączeniu dostępu dla użytkowników w dzierżawie usługi Azure AD można dodać grupy usługi Azure AD do usługi API Management. W rezultacie można kontrolować widoczność produktu przy użyciu grup usługi Azure AD.

Aby dodać zewnętrzną grupę usługi Azure AD do interfejsu APIM, należy najpierw wykonać poprzednią sekcję. Ponadto zarejestrowana aplikacja musi mieć dostęp do interfejsu API `Directory.Read.All` programu Microsoft Graph z uprawnieniami, wykonując następujące kroki: 

1. Wróć do rejestracji aplikacji utworzonej w poprzedniej sekcji.
2. Wybierz pozycję **Uprawnienia interfejsu API**, a następnie kliknij przycisk **+Dodaj uprawnienie**. 
3. W okienku **Uprawnienia interfejsu API żądania** wybierz kartę **Interfejsy API firmy Microsoft,** a następnie wybierz kafelek **programu Microsoft Graph.** Wybierz **pozycję Uprawnienia aplikacji**, wyszukaj pozycję **Katalog**, a następnie wybierz **uprawnienie Directory.Read.All.** 
4. Kliknij **pozycję Dodaj uprawnienia** u dołu okienka, a następnie kliknij pozycję **Udzielaj zgody administratora dla {tenantname},** aby udzielić dostępu wszystkim użytkownikom w tym katalogu. 

Teraz możesz dodać zewnętrzne grupy usługi Azure AD z karty **Grupy** wystąpienia usługi API Management.

1. Wybierz kartę **Grupy**.
2. Wybierz przycisk **Dodaj grupę AAD.**
    ![Przycisk "Dodaj grupę AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Zaznacz grupę, którą chcesz dodać.
4. Naciśnij przycisk **Wybierz.**

Po dodaniu zewnętrznej grupy usługi Azure AD można przejrzeć i skonfigurować jej właściwości. Wybierz nazwę grupy na karcie **Grupy.** W tym miejscu można edytować informacje o **nazwie** i **opisie** grupy.
 
Użytkownicy ze skonfigurowanym wystąpienia usługi Azure AD mogą teraz logować się do portalu deweloperów. Mogą wyświetlać i subskrybować dowolne grupy, dla których mają widoczność.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Portal dla deweloperów — dodawanie uwierzytelniania konta usługi Azure AD

W portalu dla deweloperów logowanie za pomocą aad jest możliwe za pomocą **przycisku Zaloguj się: Widżet OAuth.** Widżet jest już uwzględniony na stronie logowania domyślnej zawartości portalu dla deweloperów.

Mimo że nowe konto zostanie utworzone automatycznie, gdy nowy użytkownik zaloguje się za pomocą aad, możesz dodać ten sam widżet do strony rejestracji.

Formularz rejestracji: Widżet **OAuth** reprezentuje formularz używany do rejestracji w OAuth.

> [!IMPORTANT]
> Należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany aad zostały wprowadzone.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Starszy portal dla deweloperów — jak zalogować się za pomocą usługi Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Aby zalogować się do portalu dla deweloperów przy użyciu konta usługi Azure AD skonfigurowanego w poprzednich sekcjach:

1. Otwórz nowe okno przeglądarki przy użyciu adresu URL logowania z konfiguracji aplikacji usługi Active Directory i wybierz pozycję **Azure Active Directory**.

   ![Strona logowania][api-management-dev-portal-signin]

1. Wprowadź poświadczenia jednego z użytkowników w usłudze Azure AD i wybierz pozycję **Zaloguj się**.

   ![Logowanie się przy użyciu nazwy użytkownika i hasła][api-management-aad-signin]

1. Jeśli wymagane są dodatkowe informacje, może zostać wyświetlony monit z formularzem rejestracyjnym. Wypełnij formularz rejestracyjny i wybierz pozycję **Zarejestruj się**.

   ![Przycisk "Zarejestruj się" na formularzu rejestracyjnym][api-management-complete-registration]

Użytkownik jest teraz zalogowany do portalu dla deweloperów dla wystąpienia usługi zarządzania interfejsem API.

![Portal dla deweloperów po zakończeniu rejestracji][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
