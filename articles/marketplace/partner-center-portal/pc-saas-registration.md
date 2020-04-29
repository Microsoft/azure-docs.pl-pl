---
title: Rejestrowanie aplikacji SaaS | Portal Azure Marketplace
description: Wyjaśnia, jak zarejestrować aplikację SaaS przy użyciu Azure Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275717"
---
# <a name="register-a-saas-application"></a>Rejestrowanie aplikacji SaaS

W tym artykule wyjaśniono, jak zarejestrować aplikację SaaS przy użyciu [Azure Portal](https://portal.azure.com/)firmy Microsoft.  Po pomyślnej rejestracji otrzymasz token zabezpieczający Azure Active Directory (Azure AD), za pomocą którego można uzyskać dostęp do interfejsów API realizacji SaaS.  Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Przepływ uwierzytelniania między usługami

Na poniższym diagramie przedstawiono przepływ subskrypcji nowego klienta i używane są następujące interfejsy API:

![Przepływ interfejsu API oferty SaaS](./media/saas-offer-publish-api-flow-v1.png)

Platforma Azure nie nakłada żadnych ograniczeń dotyczących uwierzytelniania, które usługa SaaS ujawnia użytkownikom końcowym. Uwierzytelnianie za pomocą interfejsów API realizacji SaaS jest jednak realizowane przy użyciu tokenu zabezpieczającego usługi Azure AD, który zwykle jest uzyskiwany przez zarejestrowanie aplikacji SaaS za pomocą Azure Portal. 


## <a name="register-an-azure-ad-secured-app"></a>Rejestrowanie aplikacji zabezpieczonej przy użyciu usługi Azure AD

Każda aplikacja, która ma korzystać z funkcji usługi Azure AD, musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji obejmuje podanie szczegółowych informacji o usłudze Azure AD, takich jak adres URL, pod którym znajduje się adres URL służący do wysyłania odpowiedzi po uwierzytelnieniu użytkownika, identyfikator URI, który identyfikuje aplikację i tak dalej.  Aby zarejestrować nową aplikację przy użyciu Azure Portal, wykonaj następujące czynności:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Jeśli Twoje konto daje dostęp do więcej niż jednego, kliknij swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
3.  W okienku nawigacji po lewej stronie kliknij usługę **Azure Active Directory** kliknij pozycję **rejestracje aplikacji**, a następnie kliknij pozycję **rejestracja nowej aplikacji**.

    ![SaaS rejestracji aplikacji usługi AD](./media/saas-offer-app-registration-v1.png)

4.  Na stronie Tworzenie wprowadź informacje o rejestracji aplikacji\':
    -   **Nazwa**: wprowadź zrozumiałą nazwę aplikacji
    -   **Typ aplikacji**: 
        - Wybierz opcję **Natywna** dla [aplikacji klienckich ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), które są zainstalowane lokalnie na urządzeniu. To ustawienie jest używane w przypadku [klientów natywnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publicznego protokołu OAuth.
        - Wybierz pozycję **aplikacja sieci Web/interfejs API** dla aplikacji [klienckich](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) i [aplikacji typu zasób/interfejs API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) , które są zainstalowane na serwerze zabezpieczonym. To ustawienie jest używane w przypadku [klientów sieci Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) poufnych z uwierzytelnianiem OAuth i publicznych [klientów opartych na agentach](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Ta sama aplikacja może ujawniać zarówno klienta, jak i interfejs API lub zasób.
    -   **Adres URL logowania**: w przypadku aplikacji sieci Web/interfejsów API Podaj podstawowy adres URL aplikacji. **http://localhost:31544** Może to być na przykład adres URL aplikacji sieci Web działającej na komputerze lokalnym. Następnie użytkownicy będą używać tego adresu URL do logowania się do aplikacji klienckiej sieci Web.
    -   **Identyfikator URI przekierowania**: w przypadku aplikacji natywnych podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi na tokeny. Wprowadź na przykład **http://MyFirstAADApp**wartość specyficzną dla Twojej aplikacji.

        ![SaaS rejestracji aplikacji usługi AD](./media/saas-offer-app-registration-v1-2.png)

        Aby zapoznać się z konkretnymi przykładami aplikacji sieci Web lub aplikacji natywnych, zapoznaj się z przewodnikiem Szybki Start, które są dostępne w sekcji *wprowadzenie* w [przewodniku deweloperów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Po zakończeniu kliknij pozycję **Gotowe**. Usługa Azure AD przypisuje do aplikacji unikatowy *Identyfikator aplikacji* i\'ponowią próbę na stronie\'rejestracji głównej aplikacji. W zależności od tego, czy aplikacja jest internetowa czy natywna, dostępne są różne opcje umożliwiające dodawanie dodatkowych funkcji do aplikacji.

>[!Note]
>Domyślnie nowo zarejestrowana aplikacja jest skonfigurowana tak, aby zezwalać na logowanie się do aplikacji tylko użytkownikom z tej samej dzierżawy.


## <a name="using-the-azure-ad-security-token"></a>Korzystanie z tokenu zabezpieczeń usługi Azure AD

Po zarejestrowaniu aplikacji można programowo zażądać tokenu zabezpieczeń usługi Azure AD.  Wydawca powinien używać tego tokenu i wysłać żądanie rozwiązania problemu.  W przypadku korzystania z różnych interfejsów API realizacji, parametr zapytania tokenu znajduje się w adresie URL, gdy użytkownik zostanie przekierowany do witryny internetowej SaaS z platformy Azure.  Ten token jest prawidłowy tylko przez jedną godzinę.  Ponadto należy zdekodować wartość tokenu z przeglądarki przed jej użyciem.

Aby uzyskać więcej informacji na temat tych tokenów, zobacz [Azure Active Directory tokeny dostępu](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Uzyskaj token oparty na aplikacji usługi Azure AD

Metoda HTTP

`POST`

*Adres URL żądania*

**https://login.microsoftonline.com/*{tenantId}*/OAuth2/token**

*Parametr URI*

|  **Nazwa parametru**  | **Wymagane**  | **Opis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | Prawda          | Identyfikator dzierżawy zarejestrowanej aplikacji usługi AAD   |
|  |  |  |


*Nagłówek żądania*

|  **Nazwa nagłówka**  | **Wymagane** |  **Opis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | Prawda         | Typ zawartości skojarzony z żądaniem. Wartością domyślną jest `application/x-www-form-urlencoded`.  |
|  |  |  |


*Treść żądania*

| **Nazwa właściwości**   | **Wymagane** |  **Opis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | Prawda         | Typ udzielania. Wartością domyślną jest `client_credentials`.                    |
|  Client_id          | Prawda         |  Identyfikator klienta/aplikacji skojarzony z aplikacją usługi Azure AD.                  |
|  client_secret      | Prawda         |  Hasło skojarzone z aplikacją usługi Azure AD.                               |
|  Zasób           | Prawda         |  Zasób docelowy, dla którego zażądano tokenu. Wartością domyślną jest `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Reakcji*

|  **Nazwa**  | **Typ**       |  **Opis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Żądanie powiodło się   |
|  |  |  |

*TokenResponse*

Przykładowy token odpowiedzi:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Następne kroki

Aplikacja zabezpieczona za pomocą usługi Azure AD może teraz korzystać z [interfejsu API realizacji SaaS w wersji 2](./pc-saas-fulfillment-api-v2.md).
