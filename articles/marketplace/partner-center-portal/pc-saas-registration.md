---
title: Rejestrowanie aplikacji SaaS — Azure Marketplace
description: Dowiedz się, jak za pomocą Azure Portal zarejestrować aplikację SaaS i odebrać Azure Active Directory token zabezpieczający.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 39a0830806d2d9c7358d175cae703e9c81c45b02
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130021"
---
# <a name="register-a-saas-application"></a>Rejestrowanie aplikacji SaaS

W tym artykule wyjaśniono, jak zarejestrować aplikację SaaS przy użyciu [Azure Portal](https://portal.azure.com/) firmy Microsoft i uzyskać token dostępu wydawcy (Azure Active Directory token dostępu). Wydawca użyje tego tokenu do uwierzytelnienia aplikacji SaaS przez wywołanie interfejsów API realizacji SaaS.  Interfejsy API realizacji wykorzystują poświadczenia klienta OAuth 2,0, aby przyznać przepływ na Azure Active Directory (v 1.0) punkty końcowe do żądania tokenu dostępu między usługami.

W witrynie Azure Marketplace nie są narzucane żadne ograniczenia dotyczące metody uwierzytelniania używanej przez usługę SaaS dla użytkowników końcowych. Poniższy przepływ jest wymagany tylko do uwierzytelniania usługi SaaS w portalu Azure Marketplace.

Aby uzyskać więcej informacji na temat usługi Azure AD (Active Directory), zobacz [co to jest uwierzytelnianie](../../active-directory/develop/authentication-vs-authorization.md)?

## <a name="register-an-azure-ad-secured-app"></a>Rejestrowanie aplikacji zabezpieczonej przy użyciu usługi Azure AD

Każda aplikacja, która ma korzystać z funkcji usługi Azure AD, musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji obejmuje podanie szczegółowych informacji o aplikacji w usłudze Azure AD. Aby zarejestrować nową aplikację przy użyciu Azure Portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Jeśli Twoje konto daje dostęp do więcej niż jednego, kliknij swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie kliknij usługę **Azure Active Directory** kliknij pozycję **rejestracje aplikacji** , a następnie kliknij pozycję **rejestracja nowej aplikacji** .

    ![SaaS rejestracji aplikacji usługi AD](./media/saas-offer-app-registration-v1.png)

4. Na stronie Tworzenie wprowadź \' Informacje o rejestracji aplikacji:
    -   **Nazwa** : wprowadź zrozumiałą nazwę aplikacji
    -   **Typ aplikacji** :  
        
        Wybierz pozycję **aplikacja sieci Web/interfejs API** dla [aplikacji klienckich](../../active-directory/develop/developer-glossary.md#client-application)) i [aplikacje typu zasób/interfejs API](../../active-directory/develop/developer-glossary.md#resource-server), które są zainstalowane na serwerze zabezpieczonym. To ustawienie jest używane w przypadku [klientów w sieci Web](../../active-directory/develop/developer-glossary.md#web-client)poufnych z uwierzytelnianiem OAuth i publicznych [klientów opartych na agentach](../../active-directory/develop/developer-glossary.md#user-agent-based-client).
        Ta sama aplikacja może ujawniać zarówno klienta, jak i interfejs API lub zasób.

        Aby zapoznać się z konkretnymi przykładami aplikacji sieci Web, zapoznaj się z przewodnikiem Szybki Start, które są dostępne w sekcji [wprowadzenie](../../active-directory/develop/quickstart-create-new-tenant.md) w [przewodniku deweloperów usługi Azure AD](../../active-directory/develop/index.yml).

5. Po zakończeniu kliknij pozycję **zarejestruj** .  Usługa Azure AD przypisuje do nowej aplikacji unikatowy *Identyfikator aplikacji* . Zalecamy zarejestrowanie jednej aplikacji, która uzyskuje dostęp do interfejsu API, i jako pojedynczej dzierżawy.

6. Aby utworzyć klucz tajny klienta, przejdź do **strony certyfikaty &** wpisy tajne i kliknij pozycję **+ nowy klucz tajny klienta** .  Pamiętaj, aby skopiować wartość klucza tajnego w celu użycia jej w kodzie.

**Identyfikator aplikacji usługi Azure AD** jest SKOJARZONY z identyfikatorem wydawcy, dlatego upewnij się, że ten sam *Identyfikator aplikacji* jest używany we wszystkich ofertach.

>[!Note]
>Jeśli Wydawca ma dwa różne konta w centrum partnerskim, należy użyć dwóch różnych identyfikatorów aplikacji usługi Azure AD.  Każde konto partnera w centrum partnerskim musi używać unikatowego identyfikatora aplikacji usługi Azure AD dla wszystkich ofert SaaS opublikowanych za pomocą tego konta.

## <a name="how-to-get-the-publishers-authorization-token"></a>Jak uzyskać Token autoryzacji wydawcy

Po zarejestrowaniu aplikacji można programowo zażądać tokenu autoryzacji wydawcy (token dostępu usługi Azure AD przy użyciu punktu końcowego usługi Azure AD w wersji 1). Wydawca musi używać tego tokenu podczas wywoływania różnych interfejsów API realizacji SaaS. Ten token jest prawidłowy tylko przez jedną godzinę. 

Aby uzyskać więcej informacji na temat tych tokenów, zobacz [Azure Active Directory tokeny dostępu](../../active-directory/develop/access-tokens.md).  Należy zauważyć, że w przepływie jest używany token punktu końcowego v1.

### <a name="get-the-token-with-an-http-post"></a>Pobieranie tokenu przy użyciu protokołu HTTP POST

#### <a name="http-method"></a>Metoda HTTP

Wpis<br>

##### <a name="request-url"></a>*Adres URL żądania* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*Parametr URI*

|  Nazwa parametru    |  Wymagane         |  Opis |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  Prawda      |  Identyfikator dzierżawy zarejestrowanej aplikacji usługi AAD. |

##### <a name="request-header"></a>*Nagłówek żądania*

|  Nazwa nagłówka       |  Wymagane         |  Opis |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  Prawda      |  Typ zawartości skojarzony z żądaniem. Wartość domyślna to `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Treść żądania*

|  Nazwa właściwości     |  Wymagane         |  Opis |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  Prawda      |  Typ udzielania. Użyj polecenia `"client_credentials"`. |
|  `client_id`       |  Prawda      |  Identyfikator klienta/aplikacji skojarzony z aplikacją usługi Azure AD. |
|  `client_secret`   |  Prawda      |  Wpis tajny skojarzony z aplikacją usługi Azure AD. |
|  `resource`        |  Prawda      |  Zasób docelowy, dla którego zażądano tokenu. Użyj `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` , ponieważ interfejs API SaaS Marketplace jest zawsze zasobem docelowym w tym przypadku. |

##### <a name="response"></a>*Odpowiedź*

|  Nazwa     |  Typ         |  Opis |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  Żądanie powiodło się. |

##### <a name="tokenresponse"></a>*TokenResponse*

Przykładowa odpowiedź:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`Wartość pola w odpowiedzi to `<access_token>` parametr, który zostanie przekazany jako parametry autoryzacji podczas wywoływania wszystkich interfejsów API realizacji SaaS i pomiaru witryny Marketplace.

## <a name="next-steps"></a>Następne kroki

Aplikacja zabezpieczona za pomocą usługi Azure AD może teraz korzystać z [interfejsu API realizacji SaaS w wersji 2](./pc-saas-fulfillment-api-v2.md).