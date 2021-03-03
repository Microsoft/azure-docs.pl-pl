---
title: Interfejsy API realizacji SaaS w wersji 2 w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać ofertą SaaS na Microsoft AppSource i witrynie Azure Marketplace przy użyciu interfejsów API realizacji w wersji 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712355"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>Interfejsy API realizacji SaaS w wersji 2 na komercyjnej witrynie Marketplace

Ten artykuł zawiera szczegółowe informacje dotyczące interfejsów API, które umożliwiają partnerom sprzedawanie ich oprogramowania jako usługi (SaaS) w Microsoft AppSource i witrynie Azure Marketplace. Do zaimplementowania integracji z tymi interfejsami API w centrum partnerskim jest wymagany Wydawca.

## <a name="managing-the-saas-subscription-life-cycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Komercyjna witryna Marketplace zarządza całym cyklem życia subskrypcji SaaS po zakupie przez użytkownika końcowego. Używa ona strony docelowej, interfejsów API realizacji, interfejsów API operacji i elementu webhook jako mechanizmu do kierowania rzeczywistej aktywacji, użycia, aktualizacji i anulowania subskrypcji SaaS. Rachunek użytkownika końcowego jest oparty na stanie subskrypcji SaaS obsługiwanej przez firmę Microsoft. 

### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

Na poniższym diagramie przedstawiono Stany subskrypcji SaaS i odpowiednie akcje.

![Diagram przedstawiający cykl życia oprogramowania jako subskrypcji usługi w portalu Marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Zakupione, ale jeszcze nie aktywowane (*PendingFulfillmentStart*)

Po zakupie przez użytkownika końcowego (lub CSP) oferty SaaS w komercyjnej witrynie Marketplace Wydawca powinien zostać powiadomiony o zakupie. Wydawca może następnie utworzyć i skonfigurować nowe konto SaaS po stronie wydawcy dla użytkownika końcowego.

W celu utworzenia konta:

1. Klient wybiera przycisk **Konfiguruj** , który jest dostępny dla oferty SaaS po pomyślnym zakupie w Microsoft AppSource lub Azure Portal. Alternatywnie klient może użyć przycisku **Konfiguruj** w wiadomości e-mail, który wkrótce otrzyma po zakupie.
2. Firma Microsoft następnie powiadamia partnera o zakupie, otwierając w nowej karcie przeglądarki adres URL strony docelowej z parametrem token (token identyfikacji zakupu z komercyjnej witryny Marketplace).

Przykładem takiego wywołania jest `https://contoso.com/signup?token=<blob>` , podczas gdy adres URL strony docelowej dla tej oferty SaaS w centrum partnerskim jest skonfigurowany jako `https://contoso.com/signup` . Token ten zawiera wydawcę o IDENTYFIKATORze, który jednoznacznie identyfikuje SaaS zakupu i klienta.

>[!NOTE]
>Wydawca nie zostanie powiadomiony o zakupie SaaS, dopóki klient nie zainicjuje procesu konfiguracji po stronie firmy Microsoft.

Adres URL strony docelowej musi być uruchomiony przez cały dzień, codziennie i gotowy do otrzymywania nowych wywołań od firmy Microsoft przez cały czas. Jeśli strona docelowa stanie się niedostępna, klienci nie będą mogli zarejestrować się w usłudze SaaS i rozpocząć korzystanie z niej.

Następnie Wydawca musi przekazać *token* z powrotem do firmy Microsoft, wywołując [interfejs API rozpoznawania SaaS](#resolve-a-purchased-subscription)i wprowadzając token jako wartość `x-ms-marketplace-token header` parametru nagłówka. W wyniku wywołania interfejsu API rozpoznawania token jest wymieniany w celu uzyskania szczegółowych informacji o zakupie SaaS, takich jak unikatowy identyfikator zakupu, zakupionego identyfikatora oferty i identyfikatora zakupionego planu.

Na stronie miejsce docelowe klient powinien być zalogowany na nowym lub istniejącym koncie SaaS za pośrednictwem usługi Azure Active Directory (Azure AD) Logowanie jednokrotne (SSO).

Wydawca powinien zaimplementować Logowanie jednokrotne, aby zapewnić użytkownikowi środowisko wymagane przez firmę Microsoft dla tego przepływu. Upewnij się, że używasz wielodostępnej aplikacji usługi Azure AD i Zezwalaj na konta służbowe lub osobiste konta Microsoft podczas konfigurowania logowania jednokrotnego. To wymaganie dotyczy tylko strony docelowej dla użytkowników, którzy są przekierowywani do usługi SaaS, gdy są już zalogowani przy użyciu poświadczeń firmy Microsoft. Logowanie jednokrotne nie jest wymagane dla wszystkich logowań do usługi SaaS.

> [!NOTE]
>Jeśli logowanie jednokrotne wymaga, aby administrator musi udzielić uprawnienia do aplikacji, opis oferty w centrum partnerskim musi ujawnić, że wymagany jest dostęp na poziomie administratora. To ujawnienie jest zgodne z [komercyjnymi zasadami certyfikacji portalu Marketplace](/legal/marketplace/certification-policies#10003-authentication-options).

Po zalogowaniu klient powinien ukończyć konfigurację SaaS po stronie wydawcy. Następnie Wydawca musi wywołać [interfejs API aktywacji subskrypcji](#activate-a-subscription) , aby wysłać sygnał do portalu Azure Marketplace, że inicjowanie obsługi konta SaaS zostało zakończone.
Ta akcja spowoduje rozpoczęcie cyklu rozliczeniowego klienta. Jeśli wywołanie interfejsu API aktywacji subskrypcji nie powiedzie się, klient nie jest obciążany opłatą za zakup.


![Diagram przedstawiający wywołanie P I dla scenariusza aprowizacji.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktywne (*subskrybowane*)

*Active (subskrybowana)* jest stałym stanem zainicjowanej subskrypcji SaaS. Gdy po stronie firmy Microsoft przetworzy wywołanie [interfejsu API aktywacji subskrypcji](#activate-a-subscription) , subskrypcja SaaS jest oznaczona jako *subskrybowana*. Klient może teraz korzystać z usługi SaaS po stronie wydawcy i będzie rozliczany.

Gdy subskrypcja usługi SaaS jest już aktywna, klient może wybrać pozycję **Zarządzaj usługą SaaS** z poziomu centrum administracyjnego Azure Portal lub Microsoft 365. Ta akcja powoduje również, że firma Microsoft może wywoływać **adres URL strony docelowej** z parametrem *tokenu* tak samo jak w przepływie aktywacji. Wydawca powinien rozróżnić nowe zakupy i zarządzać istniejącymi kontami SaaS i obsłużyć odpowiednie wywołanie adresu URL strony docelowej.

#### <a name="being-updated-subscribed"></a>Aktualizowany (*subskrybowany*)

Ta akcja oznacza, że aktualizacja istniejącej aktywnej subskrypcji SaaS jest przetwarzana przez firmę Microsoft i wydawcę. Taka aktualizacja może zostać zainicjowana przez:

- Klient z komercyjnej witryny Marketplace.
- Dostawca CSP z komercyjnej witryny Marketplace.
- Klient z witryny SaaS wydawcy (ale nie do zakupów dla dostawcy CSP).

Dostępne są dwa typy aktualizacji dla subskrypcji SaaS:

- Zaktualizuj plan, gdy klient wybierze inny plan dla subskrypcji.
- Zaktualizuj ilość, gdy klient zmieni liczbę zakupionych stanowisk dla subskrypcji.

Można aktualizować tylko aktywną subskrypcję. Gdy subskrypcja jest aktualizowana, jej stan pozostaje aktywny po stronie firmy Microsoft.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Aktualizacja zainicjowana z komercyjnego portalu Marketplace

W tym przepływie klient zmienia plan subskrypcji lub liczbę stanowisk z Azure Portal lub Microsoft 365 centrum administracyjnego.

1. Po wprowadzeniu aktualizacji firma Microsoft będzie wywoływała adres URL elementu webhook wydawcy, który został skonfigurowany w polu **elementu webhook połączenia** w centrum partnerskim, z odpowiednią wartością dla *akcji* i innych odpowiednich parametrów. 
1. Po stronie wydawcy należy wprowadzić wymagane zmiany w usłudze SaaS i powiadomić firmę Microsoft po zakończeniu, wywołując [stan aktualizacji interfejsu API operacji](#update-the-status-of-an-operation).
1. Jeśli poprawka zostanie wysłana ze stanem *niepowodzenia* , proces aktualizacji nie zakończy się po stronie firmy Microsoft. Subskrypcja usługi SaaS będzie utrzymywać istniejący plan i ilość miejsc.

> [!NOTE]
> Wydawca powinien wywołać POPRAWKĘ, aby [zaktualizować stan interfejsu API operacji](#update-the-status-of-an-operation) z odpowiedzią na awarię i powodzenie *w 10-sekundowym przedziale czasu* po odebraniu powiadomienia elementu webhook. Jeśli poprawka stanu operacji nie zostanie odebrana w ciągu 10 sekund, plan zmiany zostanie *automatycznie poprawiony jako powodzenie*. 

Sekwencja wywołań interfejsu API dla scenariusza aktualizacji, który jest inicjowany z komercyjnej witryny Marketplace, jest pokazana na poniższym diagramie.

![Diagram przedstawiający wywołanie P dla aktualizacji zainicjowanej w portalu Marketplace.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Aktualizacja zainicjowana przez wydawcę

W tym przepływie klient zmienia plan subskrypcji lub ilość miejsc zakupionych w ramach usługi SaaS. 

1. Kod wydawcy musi wywołać [interfejs API planu zmiany](#change-the-plan-on-the-subscription) i/lub [interfejs API zmiany ilości](#change-the-quantity-of-seats-on-the-saas-subscription) przed wprowadzeniem żądanej zmiany po stronie wydawcy. 

1. Firma Microsoft zastosuje zmianę do subskrypcji, a następnie powiadomi wydawcę za pośrednictwem **elementu webhook** , aby zastosować tę samą zmianę.

1. Tylko wtedy, gdy Wydawca wprowadzi wymaganą zmianę w subskrypcji SaaS i powiadomi firmę Microsoft, gdy zmiana zostanie dokonana, wywołując [stan aktualizacji interfejsu API operacji](#update-the-status-of-an-operation).

Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza aktualizacji, który jest inicjowany po stronie wydawcy.

![Diagram przedstawiający wywołanie P dla aktualizacji zainicjowanej po stronie wydawcy.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Zawieszone (*zawieszone*)

Ten stan wskazuje, że nie odebrano płatności klienta usługi SaaS. Wydawca zostanie powiadomiony o zmianie stanu subskrypcji SaaS przez firmę Microsoft. Powiadomienie jest realizowane za pośrednictwem wywołania elementu webhook z parametrem *akcji* ustawionym na *wstrzymane*.

Wydawca może lub nie może wprowadzać zmian w usłudze SaaS po stronie wydawcy. Firma Microsoft zaleca, aby Wydawca udostępnił te informacje dla zawieszonego klienta i ograniczyć lub zablokować dostęp klienta do usługi SaaS. Istnieje prawdopodobieństwo, że płatność nigdy nie zostanie odebrana.

Firma Microsoft przyznaje klientowi 30-dniowy okres prolongaty przed automatycznym anulowaniem subskrypcji. Gdy subskrypcja jest w stanie *wstrzymania* :

* Partner lub niezależnego dostawcy oprogramowania muszą zachować stan odwracalny, aby można było przywrócić pełną funkcjonalność bez utraty danych lub ustawień.
* Partner lub dostawca oprogramowania powinien oczekiwać na żądanie przywrócenia subskrypcji, jeśli płatność zostanie odebrana w okresie prolongaty lub żądanie anulowania aprowizacji subskrypcji na koniec okresu prolongaty. Oba żądania będą wysyłane za pośrednictwem mechanizmu elementu webhook.

Stan subskrypcji zostanie zmieniony na zawieszony po stronie firmy Microsoft przed wykonaniem jakiejkolwiek akcji przez wydawcę. Można wstrzymać tylko aktywne subskrypcje.

#### <a name="reinstated-suspended"></a>Przywrócono (*zawieszone*)

Ta akcja wskazuje, że instrument płatniczy klienta stał się ważny, płatność dla subskrypcji SaaS i subskrypcja jest przywracana. W takim przypadku: 

1. Firma Microsoft wywołuje element webhook z parametrem *Action* ustawionym na wartość *przywracania* .
1. Wydawca sprawdza, czy subskrypcja jest w pełni funkcjonalna po stronie wydawcy.
1. Wydawca wywołuje [interfejs API operacji patch](#update-the-status-of-an-operation) o stanie sukces.
1. Proces przywracania zakończy się pomyślnie, a klient jest rozliczany ponownie dla subskrypcji SaaS. 

Jeśli poprawka zostanie wysłana ze stanem *niepowodzenia* , proces przywracania nie zakończy się po stronie firmy Microsoft, a subskrypcja pozostanie *zawieszona*.

Można przywrócić tylko zawieszoną subskrypcję. Zawieszona subskrypcja SaaS pozostaje w stanie *wstrzymania* , gdy jest ona przywracana. Po zakończeniu tej operacji stan subskrypcji stanie się *aktywny*.

#### <a name="renewed-subscribed"></a>Odnowione (*subskrybowane*)

Subskrypcja usługi SaaS jest automatycznie odnawiana przez firmę Microsoft pod koniec okresu subskrypcji miesiąca lub roku. *Wartość* domyślna dla ustawienia autoodnawiania dotyczy wszystkich subskrypcji SaaS. Subskrypcje Active SaaS będą nadal odnawiane za pomocą zwykłego erzeu. Firma Microsoft nie powiadamia wydawcy o odnowieniu subskrypcji. Klient może wyłączyć automatyczne odnawianie dla subskrypcji SaaS za pośrednictwem portalu administracyjnego Microsoft 365. W takim przypadku subskrypcja SaaS zostanie automatycznie anulowana na końcu bieżącego okresu rozliczeniowego. Klienci mogą także w dowolnym momencie anulować subskrypcję usługi SaaS.

Tylko aktywne subskrypcje są automatycznie odnawiane. Subskrypcje pozostają aktywne podczas procesu odnawiania i w przypadku pomyślnego odnowienia automatycznego. Po odnowieniu daty rozpoczęcia i zakończenia okresu subskrypcji są aktualizowane do dat nowego okresu.

Jeśli automatyczne odnawianie zakończy się niepowodzeniem z powodu problemu z płatnością, subskrypcja zostanie *zawieszona* , a wydawca zostanie powiadomiony.

#### <a name="canceled-unsubscribed"></a>Anulowane (*niesubskrybowane*) 

Subskrypcje docierają do tego stanu w odpowiedzi na jawną akcję klienta lub dostawcę usług w ramach anulowania subskrypcji z witryny wydawcy, Azure Portal lub Microsoft 365 centrum administracyjnego. Subskrypcję można również anulować niejawnie, w wyniku niepłaty opłat, po upływie okresu *wstrzymania* przez 30 dni.

Gdy Wydawca otrzyma wywołanie elementu webhook anulowania, powinien zachować dane klienta na potrzeby odzyskiwania na żądanie przez co najmniej siedem dni. Tylko dane klienta mogą zostać usunięte.

Subskrypcję usługi SaaS można anulować w dowolnym momencie jej cyklu życia. Po anulowaniu subskrypcji nie można jej ponownie aktywować.

## <a name="api-reference"></a>Dokumentacja interfejsu API

Ta sekcja dokumentuje interfejsy API subskrypcji i operacji SaaS.

**Interfejsy API subskrypcji** powinny służyć do obsługi cyklu życia subskrypcji SaaS z zakupu do anulowania.

**Interfejsy API operacji** powinny służyć do:

* Zweryfikuj i Potwierdź przetworzone wywołania elementu webhook.
* Pobierz listę aplikacji oczekujących na potwierdzenie przez wydawcę.

> [!NOTE]
> Wersja 1,2 protokołu TLS zostanie wymuszona wkrótce po minimalnej wersji komunikacji HTTPS. Upewnij się, że używasz tej wersji protokołu TLS w kodzie. Protokoły TLS w wersji 1,0 i 1,1 wkrótce będą przestarzałe.

### <a name="subscription-apis"></a>Interfejsy API subskrypcji

#### <a name="resolve-a-purchased-subscription"></a>Rozwiązywanie zakupionej subskrypcji

Rozwiązanie punktu końcowego pozwala wydawcy na wymianę tokenu identyfikacji zakupu z komercyjnej witryny Marketplace (nazywanej *tokenem* [zakupionego, ale jeszcze nie aktywowanego](#purchased-but-not-yet-activated-pendingfulfillmentstart)) z trwałym zakupionym identyfikatorem subskrypcji SaaS i jego szczegółami.

Gdy klient zostanie przekierowany do adresu URL strony docelowej partnera, token identyfikacji klienta jest przenoszona jako parametr *tokenu* tego wywołania adresu URL. Partner powinien używać tego tokenu i wysłać żądanie rozwiązania problemu. Rozwiązywanie odpowiedzi interfejsu API zawiera identyfikator subskrypcji SaaS oraz inne szczegóły, które jednoznacznie identyfikują zakup. *Token* dostarczony z adresem URL strony docelowej jest zwykle ważny przez 24 godziny. Jeśli otrzymany *token* już wygasł, zalecamy dostarczenie użytkownikowi końcowemu następujących wskazówek:

"Nie można zidentyfikować tego zakupu. Otwórz ponownie tę subskrypcję SaaS w Azure Portal lub w centrum administracyjnym Microsoft 365 i wybierz pozycję "Konfiguruj konto" lub "Zarządzaj kontem".

Wywołanie interfejsu API rozpoznawania spowoduje zwrócenie informacji o subskrypcji i stanu subskrypcji SaaS we wszystkich obsługiwanych Stanach.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Poubojowego `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.   |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Unikatowy token dostępu identyfikujący wydawcę wykonującą to wywołanie interfejsu API. Format to, `"Bearer <accessaccess_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parametr *tokenu* identyfikacji zakupu do rozwiązania.  Token jest przesyłany do wywołania adresu URL strony docelowej, gdy klient zostanie przekierowany do witryny sieci Web partnera SaaS (na przykład: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Należy zauważyć, że zakodowana wartość *tokenu* jest częścią adresu URL strony docelowej, więc należy ją zdekodować przed użyciem jako parametru w tym wywołaniu interfejsu API.  <br> <br> Oto przykład zakodowanego ciągu w adresie URL: `contoso.com/signup?token=ab%2Bcd%2Fef` , gdzie *token* to `ab%2Bcd%2Fef` .  Ten sam token zdekodowany będzie: `Ab+cd/ef` |
| | |

*Kody odpowiedzi:*

Kod: 200 zwraca unikatowe identyfikatory subskrypcji SaaS na podstawie `x-ms-marketplace-token` podanego typu.

Przykład treści odpowiedzi:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Kod: 400 Nieprawidłowe żądanie. `x-ms-marketplace-token` Brak, źle sformułowany, nieprawidłowy lub wygasł.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która została opublikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

Po skonfigurowaniu konta SaaS dla użytkownika końcowego Wydawca musi wywołać interfejs API aktywacji subskrypcji po stronie firmy Microsoft.  Klient nie będzie obciążany opłatami, chyba że wywołanie interfejsu API zakończy się pomyślnie.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Poubojowego `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Użyj 2018-08-31.   |
| `subscriptionId` | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu [interfejsu API rozpoznawania](#resolve-a-purchased-subscription).
 |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten ciąg skorelowany ze wszystkimi zdarzeniami z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`      |  Unikatowy token dostępu identyfikujący wydawcę wykonującą to wywołanie interfejsu API. Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Przykład ładunku żądania:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Kody odpowiedzi:*

Kod: 200 subskrypcja została oznaczona jako subskrybowana po stronie firmy Microsoft.

Brak treści odpowiedzi dla tego wywołania.

Kod: 400 Nieprawidłowe żądanie: Walidacja nie powiodła się.

* `planId` nie istnieje w ładunku żądania.
* `planId` ładunek żądania nie jest zgodny z zakupionym pakietem.
* `quantity` ładunek żądania nie jest zgodny z zakupionym
* Subskrypcja SaaS jest w stanie *subskrybowanym* lub *zawieszonym* .

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która została opublikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404. Subskrypcja SaaS jest w stanie *niesubskrybowanym* .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Pobierz listę wszystkich subskrypcji

Ten interfejs API pobiera listę wszystkich zakupionych subskrypcji SaaS dla wszystkich ofert opublikowanych przez wydawcę w portalu komercyjnym.  Wszystkie możliwe stany będą zwracane subskrypcje SaaS. Zwracane są również niesubskrybowane subskrypcje SaaS, ponieważ te informacje nie są usuwane po stronie firmy Microsoft.

Interfejs API zwraca wyniki z podziałem na strony 100 na stronę.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Użyj 2018-08-31.  |
| `continuationToken`  | Parametr opcjonalny. Aby pobrać pierwszą stronę wyników, pozostaw to pole puste.  Użyj wartości zwróconej w `@nextLink` parametrze, aby pobrać następną stronę. |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`      |  Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Kody odpowiedzi:*

Kod: 200 zwraca listę wszystkich istniejących subskrypcji dla wszystkich ofert wykonanych przez tego wydawcę na podstawie tokenu autoryzacji wydawcy.

*Przykład treści odpowiedzi:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Jeśli nie znaleziono żadnych zakupionych subskrypcji SaaS dla tego wydawcy, zwracana jest pusta treść odpowiedzi.

Kod: 403 zabroniony. Token autoryzacji jest niedostępny, nieprawidłowy lub wygasł.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Kod: błąd wewnętrzny serwera 500. Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Pobierz subskrypcję

Ten interfejs API pobiera określoną zakupioną subskrypcję SaaS dla oferty SaaS, którą Wydawca publikuje na komercyjnym rynku. Użyj tego wywołania, aby uzyskać wszystkie dostępne informacje dla określonej subskrypcji SaaS za pomocą jej identyfikatora zamiast wywoływania interfejsu API, który służy do pobierania listy wszystkich subskrypcji.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Użyj 2018-08-31. |
| `subscriptionId`     |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `authorization`     | Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API. Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kody odpowiedzi:*

Kod: 200 zwraca szczegóły dotyczące subskrypcji SaaS na podstawie `subscriptionId` podanego elementu.

*Przykład treści odpowiedzi:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie można znaleźć subskrypcji SaaS z określoną usługą `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Wyświetl dostępne plany

Ten interfejs API pobiera wszystkie plany dla oferty SaaS identyfikowanej przez `subscriptionId` określony zakup tej oferty.  Użyj tego wywołania, aby uzyskać listę wszystkich planów prywatnych i publicznych, które beneficjent subskrypcji SaaS może zaktualizować dla subskrypcji.  Zwrócone plany będą dostępne w tej samej lokalizacji geograficznej co już zakupiony plan.

To wywołanie zwraca listę planów dostępnych dla tego klienta (oprócz już zakupionego).  Lista może być prezentowana użytkownikowi końcowemu w witrynie wydawcy.  Użytkownik końcowy może zmienić plan subskrypcji na jeden z planów na liście zwracanych.  Zmiana planu na taki, który nie znajduje się na liście, zakończy się niepowodzeniem.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`    |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `x-ms-correlationid`  |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `authorization`     |  Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kody odpowiedzi:*

Kod: 200 zwraca listę wszystkich dostępnych planów dla istniejącej subskrypcji SaaS, w tym już zakupionej.

Przykład treści odpowiedzi:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Jeśli `subscriptionId` nie zostanie znaleziona, zwracana jest pusta treść odpowiedzi.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie może próbować uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Zmiana planu subskrypcji

Ten interfejs API służy do aktualizowania istniejącego planu zakupionego w ramach subskrypcji SaaS w nowym planie (publicznym lub prywatnym).  Wydawca musi wywołać ten interfejs API w przypadku zmiany planu po stronie wydawcy dla subskrypcji SaaS zakupionej w komercyjnej witrynie Marketplace.

Ten interfejs API można wywołać tylko dla *aktywnych* subskrypcji.  Każdy plan można zmienić na inny istniejący plan (publiczny lub prywatny), ale nie do samego siebie.  W przypadku planów prywatnych dzierżawa klienta musi być zdefiniowana jako część odbiorcy planu w centrum partnerskim.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Wysłana `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
| `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

*Nagłówki żądania:*
 
|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `x-ms-correlationid`  | Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Przykład ładunku żądania:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Kody odpowiedzi:*

Kod: 202 żądanie zmiany planu zostało zaakceptowane i obsłużone asynchronicznie.  Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia żądania zmiany planu.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył *się niepowodzeniem*, *powodzenie* lub odebranie *konfliktu* dla operacji.  Końcowy stan operacji powinien być zwracany szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja będzie gotowa do pomyślnego zakończenia pracy po stronie komercyjnej witryny Marketplace.  Tylko wtedy, gdy Wydawca zmieni plan po stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Adres URL umożliwiający uzyskanie stanu operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

* Nowy plan nie istnieje lub nie jest dostępny dla tej konkretnej subskrypcji SaaS.
* Nowy plan jest taki sam jak bieżący plan.
* Stan subskrypcji SaaS nie jest *subskrybowany*.
* Operacja aktualizacji dla subskrypcji SaaS nie jest uwzględniona w programie `allowedCustomerOperations` .

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Plan lub ilość siedzeń można zmienić jednocześnie, nie obu.

>[!Note]
>Ten interfejs API można wywołać tylko po uzyskaniu jawnej zgody dla zmiany od użytkownika końcowego.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Zmień liczbę stanowisk w ramach subskrypcji SaaS

Użyj tego interfejsu API, aby zaktualizować (zwiększyć lub zmniejszyć) liczbę miejsc zakupionych w ramach subskrypcji SaaS.  Wydawca musi wywołać ten interfejs API, gdy liczba miejsc zostanie zmieniona po stronie wydawcy dla subskrypcji SaaS utworzonej w portalu komercyjnym.

Liczba stanowisk nie może być większa niż dozwolona ilość w bieżącym planie.  W takim przypadku Wydawca powinien zmienić plan przed zmianą liczby stanowisk.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Wysłana `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `x-ms-correlationid`  | Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     | Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Przykład ładunku żądania:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Kody odpowiedzi:*

Kod: 202 żądanie zmiany ilości zostało zaakceptowane i obsłużone asynchronicznie. Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia żądania zmiany ilości.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył *się niepowodzeniem*, *powodzenie* lub odebranie *konfliktu* dla operacji.  Końcowy stan operacji należy zwrócić szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja będzie gotowa do pomyślnego zakończenia pracy po stronie komercyjnej witryny Marketplace.  Tylko wtedy, gdy Wydawca wprowadzi zmianę ilości po stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Połącz z zasobem, aby uzyskać stan operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

* Nowa ilość jest większa lub mniejsza od bieżącego limitu planu.
* Brak nowej ilości.
* Nowa ilość jest taka sama jak Bieżąca ilość.
* Stan subskrypcji SaaS nie jest subskrybowany.
* Operacja aktualizacji dla subskrypcji SaaS nie jest dołączona do programu `allowedCustomerOperations` .

Kod: 403 zabroniony.  Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji, która nie należy do bieżącego wydawcy.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Jednocześnie można zmienić tylko plan lub ilość, ale nie oba naraz.

>[!Note]
>Ten interfejs API można wywołać tylko po uzyskaniu jawnej zgody użytkownika końcowego o zmianę.

#### <a name="cancel-a-subscription"></a>Anulowanie subskrypcji

Użyj tego interfejsu API, aby anulować subskrypcję określonej subskrypcji SaaS.  Wydawca nie musi korzystać z tego interfejsu API i zalecamy kierowanie klientów do komercyjnej witryny Marketplace, aby anulować subskrypcje SaaS.

Jeśli Wydawca zdecyduje się na zaimplementowanie subskrypcji SaaS zakupionej w portalu komercyjnym po stronie wydawcy, musi wywołać ten interfejs API.  Po zakończeniu tego wywołania status subskrypcji stanie się *niesubskrybowany* po stronie firmy Microsoft.

Klient nie zostanie rozliczony w przypadku anulowania subskrypcji w ramach następujących okresów prolongaty:

* Dwadzieścia cztery godziny na comiesięczną subskrypcję po aktywacji.
* Czternaście dni w przypadku subskrypcji rocznej po aktywacji.

Klient zostanie rozliczony, jeśli subskrypcja zostanie anulowana po poprzednich okresach prolongaty.  Klient utraci dostęp do subskrypcji usługi SaaS bezpośrednio po anulowaniu. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Usunięty `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `x-ms-correlationid`  | Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Unikatowy token dostępu identyfikujący wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Kody odpowiedzi:*

Kod: 202 żądanie anulowania subskrypcji zostało zaakceptowane i obsłużone asynchronicznie.  Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia tego żądania.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył *się niepowodzeniem*, *powodzenie* lub odebranie *konfliktu* dla operacji.  Końcowy stan operacji należy zwrócić szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja zostanie zakończona pomyślnie po stronie komercyjnej witryny Marketplace.  Tylko wtedy, gdy Wydawca będzie anulować subskrypcję po stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Połącz z zasobem, aby uzyskać stan operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kod: 400 Nieprawidłowe żądanie.  Usuwanie nie znajduje się na `allowedCustomerOperations` liście dla tej subskrypcji SaaS.

Kod: 403 zabroniony.  Token autoryzacji jest nieprawidłowy, wygasł lub jest niedostępny. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500. Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Interfejsy API operacji

#### <a name="list-outstanding-operations"></a>Utwórz listę zaległych operacji 

Pobierz listę oczekujących operacji dla określonej subskrypcji SaaS.  Wydawca powinien potwierdzić zwrócone operacje przez wywołanie [interfejsu API poprawek operacji](#update-the-status-of-an-operation).

Obecnie tylko **operacje przywracania** są zwracane jako odpowiedź dla tego wywołania interfejsu API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Użyj 2018-08-31.         |
|    `subscriptionId` | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kody odpowiedzi:*

Kod: 200 zwraca oczekującą operację przywracania dla określonej subskrypcji SaaS.

*Przykład ładunku odpowiedzi:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Zwraca pusty kod JSON, jeśli operacje przywracania nie są oczekiwane.

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500. Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Pobierz stan operacji

Ten interfejs API umożliwia wydawcy śledzenie stanu określonej operacji asynchronicznej:  **unsubskrybuj**, **ChangePlan** lub **ChangeQuantity**.

`operationId`Dla tego wywołania interfejsu API można pobrać z wartości zwróconej przez **lokalizację operacji**, wywołanie interfejsu API oczekujących operacji get lub `<id>` wartość parametru odebraną w wywołaniu elementu webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`    |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |
|  `operationId`       |  Unikatowy identyfikator pobieranej operacji. |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Unikatowy token dostępu identyfikujący wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kody odpowiedzi:*

Kod: 200 pobiera szczegóły dla określonej operacji SaaS. 

*Przykład ładunku odpowiedzi:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  

* Nie znaleziono subskrypcji z usługą `subscriptionId` .
* Operacja z `operationId` nie została znaleziona.

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Aktualizowanie stanu operacji

Ten interfejs API służy do aktualizowania stanu oczekującej operacji w celu wskazania sukcesu lub niepowodzenia operacji po stronie wydawcy.

`operationId`Dla tego wywołania interfejsu API można pobrać z wartości zwróconej przez **lokalizację operacji**, wywołanie interfejsu API oczekujących operacji get lub `<id>` wartość parametru odebraną w wywołaniu elementu webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Wysłana `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Użyj 2018-08-31.  |
|   `subscriptionId`   |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu komercyjnego tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |
|   `operationId`      |  Unikatowy identyfikator wykonywanej operacji. |

*Nagłówki żądania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|   `x-ms-correlationid` |  Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  `authorization`     |  Unikatowy token dostępu, który identyfikuje wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Przykład ładunku żądania:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Kody odpowiedzi:*

Kod: 200 wywołanie do informowania o zakończeniu operacji po stronie partnera.  Na przykład ta odpowiedź może sygnalizować zakończenie zmiany stanowisk lub planów po stronie wydawcy.

Kod: 403
- Zabrania.  Token autoryzacji jest niedostępny, jest nieprawidłowy lub wygasł. Żądanie może próbować uzyskać dostęp do subskrypcji, która nie należy do bieżącego wydawcy.
- Zabrania.  Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana z innym identyfikatorem aplikacji usługi Azure AD na podstawie identyfikatora użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.

* Nie znaleziono subskrypcji z usługą `subscriptionId` .
* Operacja z `operationId` nie została znaleziona.

Kod: konflikt 409.  Na przykład nowsze aktualizacje są już spełnione.

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementowanie elementu webhook w usłudze SaaS

W przypadku tworzenia oferty SaaS z transactu w centrum partnerskim partner udostępnia adres URL **elementu webhook połączenia** , który będzie używany jako punkt końcowy HTTP.  Ten element webhook jest wywoływany przez firmę Microsoft przy użyciu wywołania POST protokołu HTTP w celu powiadomienia po stronie wydawcy następujących zdarzeń występujących po stronie firmy Microsoft:

* Gdy subskrypcja SaaS jest w stanie *subskrybowanym* :
    * ChangePlan 
    * ChangeQuantity
    * Wstrzymanie
    * Anuluj subskrypcję
* Gdy subskrypcja SaaS jest w stanie *wstrzymania* :
    * Przywróć
    * Anuluj subskrypcję

Wydawca musi zaimplementować element webhook w usłudze SaaS, aby zapewnić, że stan subskrypcji SaaS jest spójny ze stroną firmy Microsoft.  Usługa SaaS jest wymagana do wywołania interfejsu API operacji get w celu zweryfikowania i autoryzacji danych wywołania i ładunku elementu webhook przed podjęciem działania na podstawie powiadomienia elementu webhook.  Wydawca powinien zwrócić do firmy Microsoft protokół HTTP 200 zaraz po przetworzeniu wywołania elementu webhook.  Ta wartość potwierdza, że wywołanie elementu webhook zostało pomyślnie odebrane przez wydawcę.

>[!Note]
>Usługa URL elementu webhook musi być uruchomiona i działać w systemie 24x7 oraz być gotowa do otrzymywania nowych wywołań od firmy Microsoft przez cały czas.  Firma Microsoft ma zasady ponawiania dla wywołania elementu webhook (500 ponownych prób przez 8 godzin), ale jeśli Wydawca nie akceptuje wywołania i zwróci odpowiedź, operacja, do której powiadamia element webhook, zakończy się niepowodzeniem po stronie firmy Microsoft.

*Przykłady ładunku elementu webhook:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Tworzenie i testowanie

Aby rozpocząć proces opracowywania, zalecamy utworzenie fikcyjnych odpowiedzi interfejsu API po stronie wydawcy.  Odpowiedzi te mogą opierać się na przykładowych odpowiedziach podanych w tym artykule.

Gdy Wydawca jest gotowy do zakończenia testowania:

* Opublikuj ofertę SaaS w ograniczonej grupie odbiorców w wersji zapoznawczej i Zachowaj ją na etapie wersji zapoznawczej.
* Ustaw cenę planu na 0, aby uniknąć wyzwalania rzeczywistych wydatków rozliczeniowych podczas testowania.  Innym rozwiązaniem jest ustawienie ceny niezerowej i anulowanie wszystkich zakupów testowych w ciągu 24 godzin.
* Upewnij się, że wszystkie przepływy są wywoływane na zakończenie, aby symulować prawdziwy scenariusz dla klientów.
* Jeśli Partner chce przetestować pełny przepływ zakupów i rozliczeń, zrób to za pomocą oferty podanej powyżej $0.  Zakup zostanie rozliczony i zostanie wygenerowana faktura.

Przepływ zakupu można wyzwolić z poziomu witryny Azure Portal lub Microsoft AppSource, w zależności od tego, gdzie jest publikowana oferta.

Akcje *zmiany planu*, *ilości* i *anulowania subskrypcji* są testowane po stronie wydawcy.  Po stronie firmy Microsoft *Anulowanie subskrypcji* może być wyzwalane zarówno przez centrum Azure Portal, jak i administratora (w portalu, w którym Microsoft AppSource zakupy są zarządzane).  *Zmiany ilości i planu* można wyzwolić tylko z poziomu Centrum administracyjnego.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Zobacz [Pomoc techniczna dla komercyjnego programu w witrynie Marketplace w centrum partnerskim](../support.md) dla opcji pomocy technicznej wydawcy.

## <a name="next-steps"></a>Następne kroki

Zobacz [komercyjne interfejsy API usługi pomiarowej Marketplace](marketplace-metering-service-apis.md) , aby uzyskać więcej opcji dla ofert SaaS w komercyjnej witrynie Marketplace.

Zapoznaj się z klientami i używaj ich [w różnych językach programowania i przykładach](https://github.com/microsoft/commercial-marketplace-samples).
