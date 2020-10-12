---
title: Interfejsy API realizacji SaaS w wersji 2 w portalu komercyjnym firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać ofertą SaaS na Microsoft AppSource i witrynie Azure Marketplace przy użyciu interfejsów API realizacji w wersji 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4a98207ef5b03f77a4f741894ec210f7551c5933
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378138"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>Interfejsy API realizacji SaaS w wersji 2 na komercyjnej witrynie Marketplace

W tym artykule szczegółowo opisano interfejsy API, które umożliwiają partnerom sprzedawanie ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace. Do zaimplementowania integracji z tymi interfejsami API w centrum partnerskim jest wymagany Wydawca.

## <a name="managing-the-saas-subscription-life-cycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Komercyjna witryna Marketplace zarządza całym cyklem życia subskrypcji SaaS po zakupie przez klienta końcowego.  Korzysta ona ze strony docelowej, interfejsów API realizacji, interfejsów API operacji i elementu webhook jako mechanizmu do kierowania rzeczywistej aktywacji i użycia subskrypcji SaaS, aktualizacji oraz anulowania subskrypcji.  Rachunek klienta końcowego jest oparty na stanie subskrypcji SaaS obsługiwanej przez firmę Microsoft. 

### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

Są wyświetlane Stany subskrypcji SaaS i odpowiednie działania.

![Cykl życia subskrypcji SaaS na rynku Marketplace](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Zakupione, ale jeszcze nie aktywowane (*PendingFulfillmentStart*)

Po zakupie przez klienta końcowego oferty SaaS w portalu Marketplace Wydawca powinien zostać powiadomiony o zakupie, aby nowe konto SaaS zostało utworzone i skonfigurowane dla klienta końcowego po stronie wydawcy.

W celu utworzenia konta:

1. Klient musi kliknąć przycisk **konfiguracji** , który jest dostępny dla oferty SaaS po pomyślnym zakupie w Microsoft AppSource lub Azure Portal. Lub w wiadomości e-mail, którą klient otrzyma wkrótce po zakupie.
2. Następnie firma Microsoft powiadamia partnera o zakupie, otwierając w nowej karcie przeglądarki adres URL strony docelowej z parametrem token (komercyjny token identyfikacji zakupów w portalu Marketplace).

Przykładem takiego wywołania jest `https://contoso.com/signup?token=<blob>` , podczas gdy adres URL strony docelowej dla tej oferty SaaS w centrum partnerskim jest skonfigurowany jako `https://contoso.com/signup` . Token ten zawiera wydawcę o IDENTYFIKATORze, który jednoznacznie identyfikuje SaaS zakupu i klienta.

>[!NOTE]
>Wydawca nie otrzyma powiadomienia o zakupie SaaS, dopóki klient nie zainicjuje procesu konfiguracji od firmy Microsoft.

Adres URL strony docelowej musi być ustawiony na 24x7 i być gotowy do otrzymywania nowych wywołań od firmy Microsoft przez cały czas. Jeśli strona docelowa stanie się niedostępna, klienci nie będą mogli zarejestrować się w usłudze SaaS i rozpocząć korzystanie z niej.

Następnie *token* musi zostać przesłany z powrotem do firmy Microsoft przez wydawcę, wywołując [interfejs API rozpoznawania SaaS](#resolve-a-purchased-subscription)jako wartość `x-ms-marketplace-token header` parametru nagłówka.  W wyniku wywołania interfejsu API rozpoznawania token jest wymieniany ze szczegółowymi informacjami o zakupie SaaS, takimi jak unikatowy identyfikator zakupu, zakupionego identyfikatora oferty, IDENTYFIKATORem zakupionego planu itd.

Na stronie miejsce docelowe klient powinien być zalogowany na nowym lub istniejącym koncie usługi SaaS za pośrednictwem logowania jednokrotnego (SSO) w usłudze Azure Active Directory (Azure AD).

Wydawca powinien zaimplementować logowanie za pomocą logowania jednokrotnego, aby zapewnić użytkownikowi środowisko wymagane przez firmę Microsoft dla tego przepływu.  Upewnij się, że używasz wielodostępnej aplikacji usługi Azure AD, podczas konfigurowania logowania jednokrotnego Zezwól na konta służbowe lub osobiste konta Microsoft.  To wymaganie dotyczy tylko strony docelowej i dla użytkowników, którzy są przekierowywani do usługi SaaS, gdy jest już zalogowany przy użyciu poświadczeń firmy Microsoft. Nie dotyczy to wszystkich logowań do usługi SaaS.

> [!NOTE]
>Jeśli logowanie za pomocą logowania jednokrotnego wymaga, aby administrator przyznał uprawnienia do aplikacji, opis oferty w centrum partnerskim musi ujawnić, że wymagany jest dostęp na poziomie administratora. Jest to zgodne z [komercyjnymi zasadami certyfikacji portalu Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options).

Po zalogowaniu klient powinien zakończyć konfigurację SaaS po stronie wydawcy. Następnie Wydawca musi wywołać [aktywację subskrypcji API](#activate-a-subscription) , aby wysłać sygnał do witryny Marketplace, że inicjowanie obsługi konta SaaS zostało ukończone.
Spowoduje to rozpoczęcie cyklu rozliczeniowego klienta. Jeśli wywołanie interfejsu API aktywacji subskrypcji nie powiedzie się, klient nie jest rozliczany za zakup.


![Wywołania interfejsu API dla scenariusza aprowizacji](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktywne (subskrybowane)

Ten stan jest stałym stanem zainicjowanej subskrypcji SaaS. Po przetworzeniu wywołania [interfejsu API aktywacji subskrypcji](#activate-a-subscription) po stronie firmy Microsoft subskrypcja SaaS jest oznaczona jako subskrybowana. Usługa SaaS jest teraz gotowa do użycia przez klienta po stronie wydawcy, a klient jest rozliczany.

Gdy subskrypcja usługi SaaS jest już aktywna i klient zdecyduje się na uruchomienie funkcji **zarządzania** SaaS w centrum administracyjnym usługi Azure Portal lub M365, **adres URL strony docelowej** jest ponownie wywoływany przez firmę Microsoft z parametrem *tokenu* tak samo jak w przepływie aktywacji.  Wydawca powinien rozróżnić nowe zakupy i zarządzać istniejącymi kontami SaaS i obsłużyć odpowiednie wywołanie adresu URL strony docelowej.

#### <a name="being-updated-subscribed"></a>Aktualizowany (subskrybowany)

Ta akcja oznacza, że aktualizacja istniejącej aktywnej subskrypcji usługi SaaS jest przetwarzana przez firmę Microsoft i wydawcę. Taka aktualizacja może zostać zainicjowana przez:

- Klient z komercyjnej witryny Marketplace.
- Dostawca CSP z komercyjnej witryny Marketplace.
- Klient z witryny SaaS wydawcy (nie ma zastosowania do zakupu dostawcy CSP).

Dostępne są dwa typy aktualizacji dla subskrypcji SaaS:

- Zaktualizuj plan, gdy klient wybierze inny plan dla subskrypcji.
- Aktualizowanie ilości, gdy klient zmienia liczbę zakupionych stanowisk dla subskrypcji

Można aktualizować tylko aktywną subskrypcję. Gdy subskrypcja jest aktualizowana, jej stan pozostaje aktywny po stronie firmy Microsoft.

##### <a name="update-initiated-from-the-marketplace"></a>Aktualizacja zainicjowana z portalu Marketplace

W tym przepływie klient zmienia plan subskrypcji lub liczbę stanowisk z centrum administracyjnego M365.  

1. Po wprowadzeniu aktualizacji firma Microsoft będzie wywoływała adres URL elementu webhook wydawcy, który został skonfigurowany w polu **elementu webhook** w centrum partnerskim, z odpowiednią wartością dla *akcji* i innych odpowiednich parametrów.  
1. Po stronie wydawcy należy wprowadzić wymagane zmiany w usłudze SaaS i powiadomić firmę Microsoft o zakończeniu zmiany, wywołując [stan aktualizacji interfejsu API operacji](#update-the-status-of-an-operation).
1. Jeśli poprawka zostanie wysłana ze stanem niepowodzenia, proces aktualizacji nie zostanie ukończony po stronie firmy Microsoft.  Subskrypcja usługi SaaS zostanie pozostawiona z istniejącym planem i ilością stanowisk.

Poniżej przedstawiono sekwencję wywołań interfejsu API dla scenariusza aktualizacji zainicjowanej w portalu Marketplace.

![Wywołania interfejsu API dla aktualizacji zainicjowanej w portalu Marketplace](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Aktualizacja zainicjowana przez wydawcę

W tym przepływie klient zmienia plan subskrypcji lub ilość miejsc zakupionych w ramach usługi SaaS. 

1. Kod wydawcy musi wywołać [interfejs API planu zmiany](#change-the-plan-on-the-subscription) i/lub [zmienić liczbę interfejsów API](#change-the-quantity-of-seats-on-the-saas-subscription) , zanim przeniesiesz żądaną zmianę po stronie wydawcy. 

1. Firma Microsoft zastosuje zmianę do subskrypcji, a następnie powiadomi wydawcę za pośrednictwem **elementu webhook** , aby zastosować tę samą zmianę.  

1. Tylko Wydawca powinien wprowadzić wymaganą zmianę w subskrypcji usługi SaaS i powiadomić firmę Microsoft, gdy zmiana zostanie wykonana, wywołując [stan aktualizacji interfejsu API operacji](#update-the-status-of-an-operation).

Sekwencja wywołań interfejsu API dla scenariusza zainicjowanej aktualizacji po stronie wydawcy.

![Wywołania interfejsu API dla aktualizacji zainicjowanej po stronie wydawcy](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Zawieszone (*zawieszone*)

Ten stan wskazuje, że nie odebrano płatności klienta usługi SaaS. Wydawca zostanie powiadomiony o zmianie stanu subskrypcji SaaS przez firmę Microsoft. Powiadomienie jest wykonywane za pośrednictwem wywołania elementu webhook z parametrem *Action* ustawionym na wartość *SUSPENDED*.

Wydawca może lub nie może wprowadzać zmian w usłudze SaaS po stronie wydawcy. Firma Microsoft zaleca, aby Wydawca udostępnił te informacje dla zawieszonego klienta i limitów lub blokuje dostęp klienta do usługi SaaS.  Istnieje prawdopodobieństwo, że płatność nigdy nie zostanie odebrana.

Firma Microsoft przyznaje klientowi 30-dniowy okres prolongaty przed automatycznym anulowaniem subskrypcji. Gdy subskrypcja jest w stanie wstrzymania:

* Niezależny dostawca oprogramowania musi przechowywać konto SaaS w stanie odzyskiwalnym. Pełną funkcjonalność można przywrócić bez utraty danych lub ustawień.
* Należy zwrócić żądanie przywrócenia dla tej subskrypcji, jeśli płatność zostanie odebrana w okresie prolongaty lub gdy żądanie anulowania aprowizacji zostanie zakończone po upływie okresu prolongaty, zarówno za pośrednictwem mechanizmu elementu webhook.

Stan subskrypcji zostanie zmieniony na zawieszony po stronie firmy Microsoft przed wykonaniem jakiejkolwiek akcji przez wydawcę. Można wstrzymać tylko aktywne subskrypcje.

#### <a name="reinstated-suspended"></a>Przywrócono (*zawieszone*)

Trwa przywracanie subskrypcji.

Ta akcja wskazuje, że instrument płatniczy klienta został ponownie ważny i płatność dla subskrypcji SaaS.  Trwa przywracanie subskrypcji. W takim przypadku: 

1. Firma Microsoft wywołuje element webhook z parametrem *Action* ustawionym na wartość *przywracania* .  
1. Wydawca sprawdza, czy ta subskrypcja jest w pełni funkcjonalna po stronie wydawcy.
1. Wydawca wywołuje [interfejs API operacji patch](#update-the-status-of-an-operation) o stanie sukces.  
1. Następnie przywracanie zakończy się pomyślnie, a klient zostanie rozliczony ponownie dla subskrypcji SaaS. 
1. Jeśli poprawka zostanie wysłana ze stanem niepowodzenia, proces przywracania nie zostanie ukończony po stronie firmy Microsoft. Subskrypcja pozostanie zawieszona.

Jeśli poprawka zostanie wysłana ze stanem niepowodzenia, proces przywracania nie zostanie ukończony po stronie firmy Microsoft.  Subskrypcja pozostanie zawieszona.

Można przywrócić tylko zawieszoną subskrypcję.  Gdy subskrypcja SaaS jest przywracana, jej stan pozostaje zawieszony.  Po zakończeniu tej operacji stan subskrypcji stanie się aktywny.

#### <a name="renewed-subscribed"></a>Odnowione (*subskrybowane*)

Po upływie okresu subskrypcji (po upływie miesiąca lub roku) subskrypcja usługi SaaS jest automatycznie odnawiana przez firmę Microsoft.  Ustawienie domyślne dla ustawienia autoodnawiania ma *wartość true* dla wszystkich subskrypcji SaaS. Subskrypcje Active SaaS będą nadal odnawiane za pomocą zwykłego erzeu. Firma Microsoft nie powiadamia wydawcy o odnowieniu subskrypcji. Klient może wyłączyć automatyczne odnawianie dla subskrypcji SaaS za pośrednictwem portalu administratora M365 lub za pośrednictwem Azure Portal.  W takim przypadku subskrypcja SaaS zostanie automatycznie anulowana na końcu bieżącego okresu rozliczeniowego.  Klienci mogą również anulować subskrypcję usługi SaaS w dowolnym momencie.

Tylko aktywne subskrypcje są automatycznie odnawiane.  Subskrypcje pozostają aktywne podczas procesu odnawiania i w przypadku pomyślnego odnowienia automatycznego.  Po odnowieniu daty rozpoczęcia i zakończenia okresu subskrypcji zostaną zaktualizowane do dat nowych warunków.

Jeśli autoodnowienie nie powiedzie się z powodu problemu z płatnością, subskrypcja zostanie zawieszona.  Wydawca zostanie powiadomiony.

#### <a name="canceled-unsubscribed"></a>Anulowane (*niesubskrybowane*) 

Subskrypcje docierają do tego stanu w odpowiedzi na jawną akcję klienta lub dostawcę usług w ramach anulowania subskrypcji z witryny wydawcy, Azure Portal lub centrum administracyjnego M365.  Subskrypcję można również anulować niejawnie, ze względu na niepłatne należności, po upływie okresu wstrzymania przez 30 dni.

Po odebraniu wywołania elementu webhook anulowania Wydawca powinien zachować dane klienta na potrzeby odzyskiwania na żądanie przez co najmniej siedem dni. Tylko dane klienta można usunąć.

Subskrypcję usługi SaaS można anulować w dowolnym momencie jej cyklu życia. Po anulowaniu subskrypcji nie można ponownie aktywować.

## <a name="api-reference"></a>Dokumentacja interfejsu API

Ta sekcja dokumentuje interfejsy API subskrypcji i operacji SaaS.

**Interfejsy API subskrypcji** powinny służyć do obsługi cyklu życia subskrypcji SaaS z zakupu do anulowania.

**Interfejsy API operacji** powinny służyć do:

* Weryfikowanie i Potwierdzanie przetworzonych wywołań elementu webhook
* Pobierz listę aplikacji oczekujących na potwierdzenie przez wydawcę

### <a name="enforcing-tls-12-note"></a>Wymuszanie uwagi dotyczącej protokołu TLS 1,2

Wersja 1,2 protokołu TLS zostanie wymuszona wkrótce po minimalnej wersji komunikacji HTTPS. Upewnij się, że używasz tej wersji protokołu TLS w kodzie.  Protokoły TLS w wersji 1,0 i 1,1 wkrótce będą przestarzałe.

### <a name="subscription-apis"></a>Interfejsy API subskrypcji

#### <a name="resolve-a-purchased-subscription"></a>Rozwiązywanie zakupionej subskrypcji

Rozwiązywanie punktu końcowego umożliwia wydawcy wymianę tokenów identyfikacji zakupu w portalu Marketplace (nazywanego *tokenem* [zakupionego, ale jeszcze nie aktywowanego](#purchased-but-not-yet-activated-pendingfulfillmentstart)) z trwałym zakupionym identyfikatorem subskrypcji SaaS i jego szczegółami.

Gdy klient zostanie przekierowany do adresu URL strony docelowej partnera, token identyfikacji klienta jest przenoszona jako parametr *tokenu* tego wywołania adresu URL. Partner powinien używać tego tokenu i wysłać żądanie rozwiązania problemu. Rozwiązywanie odpowiedzi interfejsu API zawiera identyfikator subskrypcji SaaS oraz inne szczegóły, które jednoznacznie identyfikują zakup. *Token* dostarczony z adresem URL strony docelowej jest zwykle ważny przez 24 godziny. Jeśli otrzymany *token* już wygasł, zalecamy podanie następujących wskazówek dla klienta końcowego:

"Nie można zidentyfikować tego zakupu. ponownie otwórz tę subskrypcję SaaS w Azure Portal lub w centrum administracyjnym M365 i ponownie kliknij przycisk" Skonfiguruj konto "lub" Zarządzaj kontem ".

Wywołanie interfejsu API rozwiązywania problemów zwróci szczegóły subskrypcji i stan subskrypcji SaaS we wszystkich obsługiwanych Stanach.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Poubojowego`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

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
|  `x-ms-marketplace-token`  | Parametr *tokenu* zakupu w witrynie Marketplace do rozwiązania.  Token jest przesyłany do wywołania adresu URL strony docelowej, gdy klient zostanie przekierowany do witryny sieci Web partnera SaaS (na przykład: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *Uwaga:* Zakodowana wartość *tokenu* jest częścią adresu URL strony docelowej, dlatego należy ją zdekodować przed użyciem jako parametru w tym wywołaniu interfejsu API.  <br> <br> Przykład zakodowanego ciągu w adresie URL wygląda następująco: `contoso.com/signup?token=ab%2Bcd%2Fef` , gdzie token jest `ab%2Bcd%2Fef` .  Ten sam token zdekodowany będzie: `Ab+cd/ef` |
| | |

*Kody odpowiedzi:*

Kod: 200 zwraca unikatowe identyfikatory subskrypcji SaaS na podstawie `x-ms-marketplace-token` podanego typu.

Przykład treści odpowiedzi:

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
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
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Kod: 400 Nieprawidłowe żądanie. `x-ms-marketplace-token` Brak, źle sformułowany, nieprawidłowy lub wygasł.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która została opublikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD z klucza użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

Po skonfigurowaniu konta SaaS dla klienta końcowego Wydawca musi wywołać interfejs API aktywacji subskrypcji na stronie firmy Microsoft.  Klient nie będzie obciążany opłatami, chyba że wywołanie interfejsu API zakończy się pomyślnie.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Poubojowego`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Użyj 2018-08-31.   |
| `subscriptionId` | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu [interfejsu API rozpoznawania](#resolve-a-purchased-subscription).
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
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Kody odpowiedzi:*

Kod: 200 subskrypcja została oznaczona jako subskrybowana po stronie firmy Microsoft.

Brak treści odpowiedzi dla tego wywołania.

Kod: 400 Nieprawidłowe żądanie: Walidacja nie powiodła się.

* `planId` nie istnieje w ładunku żądania.
* `planId` w ładunku żądania nie jest zgodny z zakupionym pakietem.
* `quantity` w ładunku żądania nie jest zgodny z zakupionym
* Subskrypcja SaaS jest w stanie subskrybowanym lub zawieszonym.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która została opublikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD z klucza użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404. Subskrypcja SaaS jest w stanie niesubskrybowanym.

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Pobierz listę wszystkich subskrypcji

Pobiera listę wszystkich zakupionych subskrypcji SaaS dla wszystkich ofert opublikowanych przez wydawcę w portalu Marketplace.  Wszystkie możliwe stany będą zwracane subskrypcje SaaS. Zwracane są również niesubskrybowane subskrypcje SaaS, ponieważ te informacje nie zostaną usunięte po stronie firmy Microsoft.

Ten interfejs API zwraca wyniki z podziałem na strony. Rozmiar strony wynosi 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Pobierz`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

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

Kod: 200 zwraca listę wszystkich istniejących subskrypcji dla wszystkich ofert tego wydawcy, na podstawie tokenu autoryzacji wydawcy.

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
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
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
          "allowedCustomerOperations": [
              "Read" 
          ], 
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

Pobiera określoną zakupioną subskrypcję SaaS dla oferty SaaS opublikowanej w portalu Marketplace przez wydawcę. Użyj tego wywołania, aby uzyskać wszystkie dostępne informacje dla określonej subskrypcji SaaS za pomocą jej identyfikatora zamiast wywołania interfejsu API w celu uzyskania listy wszystkich subskrypcji.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Użyj 2018-08-31. |
| `subscriptionId`     |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

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
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
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
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł i nie został podany. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie można znaleźć subskrypcji SaaS z określoną usługą `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Wyświetl dostępne plany

Pobiera wszystkie plany dla oferty SaaS identyfikowanej przez `subscriptionId` określony zakup oferty.  Użyj tego wywołania, aby uzyskać listę wszystkich planów prywatnych i publicznych, które beneficjent subskrypcji SaaS może zaktualizować dla subskrypcji.  Zwrócone plany będą dostępne w tej samej lokalizacji geograficznej co już zakupiony plan.

To wywołanie zwraca listę planów dostępnych dla tego klienta (oprócz już zakupionego).  Lista może być prezentowana klientowi końcowemu w witrynie wydawcy.  Klient końcowy może zmienić plan subskrypcji na jeden z planów na liście zwracanych.  Zmiana planu na taki, który nie znajduje się na liście, zakończy się niepowodzeniem.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`    |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

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
    "plans": [{
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

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie może próbować uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Zmiana planu subskrypcji

Zaktualizuj istniejący plan zakupiony dla subskrypcji SaaS na nowy plan (publiczny lub prywatny).  Wydawca musi wywołać ten interfejs API w przypadku zmiany planu po stronie wydawcy dla subskrypcji SaaS zakupionej w witrynie Marketplace.

Ten interfejs API można wywołać tylko dla aktywnych subskrypcji.  Każdy plan można zmienić na inny istniejący plan (publiczny lub prywatny), ale nie do samego siebie.  W przypadku planów prywatnych dzierżawa klienta musi być zdefiniowana jako część odbiorcy planu w centrum partnerskim.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Wysłana `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
| `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |

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

Kod: 202 żądanie zmiany planu zostało zaakceptowane i obsłużone asynchronicznie.  Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia żądania zmiany planu.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył się niepowodzeniem, powodzenie lub odebranie konfliktu dla operacji.  Końcowy stan operacji powinien być zwracany szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja będzie gotowa do pomyślnego zakończenia pracy po stronie portalu Marketplace.  Tylko Wydawca powinien zmienić plan po stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Adres URL umożliwiający uzyskanie stanu operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

* Nowy plan nie istnieje lub nie jest dostępny dla tej konkretnej subskrypcji SaaS.
* Podjęto próbę zmiany tego samego planu.
* Stan subskrypcji SaaS nie jest subskrybowany.
* Operacja aktualizacji dla subskrypcji SaaS nie jest dołączona do programu `allowedCustomerOperations` .

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Plan lub ilość siedzeń można zmienić jednocześnie, nie obu.

>[!Note]
>Ten interfejs API można wywołać tylko po uzyskaniu jawnej zgody dla zmiany przez klienta końcowego.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Zmień liczbę stanowisk w ramach subskrypcji SaaS

Aktualizowanie (zwiększanie lub zmniejszanie) liczby miejsc zakupionych w ramach subskrypcji SaaS.  Wydawca musi wywołać ten interfejs API, gdy liczba miejsc zostanie zmieniona po stronie wydawcy dla subskrypcji SaaS utworzonej w portalu Marketplace.

Liczba stanowisk nie może być większa niż dozwolona w bieżącym planie.  W takim przypadku należy zmienić plan przed zmianą ilości.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

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

Kod: 202 żądanie zmiany ilości zostało zaakceptowane i obsłużone asynchronicznie. Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia żądania zmiany ilości.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył się niepowodzeniem, powodzenie lub odebranie konfliktu dla operacji.  Końcowy stan operacji należy zwrócić szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja będzie gotowa do pomyślnego zakończenia pracy po stronie portalu Marketplace.  Tylko Wydawca powinien wprowadzać zmiany w stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Połącz z zasobem, aby uzyskać stan operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

* Nowa ilość jest większa lub mniejsza od bieżącego limitu planu.
* Brak nowej ilości.
* Podjęto próbę zmiany tej samej ilości.
* Stan subskrypcji SaaS nie jest subskrybowany.
* Operacja aktualizacji dla subskrypcji SaaS nie jest dołączona do programu `allowedCustomerOperations` .

Kod: 403 zabroniony.  Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji, która nie należy do bieżącego wydawcy.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Jednocześnie można zmienić tylko plan lub ilość, ale nie oba naraz.

>[!Note]
>Ten interfejs API można wywołać tylko po uzyskaniu jawnej zgody dla zmiany przez klienta końcowego.

#### <a name="cancel-a-subscription"></a>Anulowanie subskrypcji

Anuluj subskrypcję określonej subskrypcji SaaS.  Wydawca nie musi korzystać z tego interfejsu API i zalecamy kierowanie klientów do witryny Marketplace, aby anulować subskrypcje SaaS.

Jeśli Wydawca zdecyduje się na wdrożenie anulowania subskrypcji SaaS zakupionej w witrynie Marketplace na stronie wydawcy, musi wywołać ten interfejs API.  Po zakończeniu tego wywołania status subskrypcji stanie się *niesubskrybowany* po stronie firmy Microsoft.

Jeśli subskrypcja zostanie anulowana w ramach następujących okresów prolongaty, klient nie zostanie rozliczony:

* 24 godziny na comiesięczną subskrypcję po aktywacji.
* 14 dni w przypadku subskrypcji rocznej po aktywacji.

Jeśli subskrypcja zostanie anulowana po powyższym okresie prolongaty, klient zostanie rozliczony.  Po pomyślnym zakończeniu anulowania klient natychmiast utraci dostęp do subskrypcji SaaS na stronie firmy Microsoft.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>usuwanie`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`     | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądania:*
 
|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `x-ms-correlationid`  | Unikatowa wartość ciągu dla operacji na kliencie.  Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera.  Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  `authorization`     |  Unikatowy token dostępu identyfikujący wydawcę wykonującą to wywołanie interfejsu API.  Format to, `"Bearer <access_token>"` kiedy wartość tokenu jest pobierana przez wydawcę, zgodnie z opisem w temacie [pobieranie tokenu na podstawie aplikacji usługi Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Kody odpowiedzi:*

Kod: 202 żądanie anulowania subskrypcji zostało zaakceptowane i obsłużone asynchronicznie.  Partner oczekuje na sondowanie **adresu URL lokalizacji operacji** w celu ustalenia sukcesu lub niepowodzenia tego żądania.  Sondowanie należy wykonać co kilka sekund do momentu, aż końcowy stan zakończył się niepowodzeniem, powodzenie lub odebranie konfliktu dla operacji.  Końcowy stan operacji należy zwrócić szybko, ale w niektórych przypadkach może upłynąć kilka minut.

Partner otrzyma również powiadomienie elementu webhook, gdy akcja zostanie zakończona pomyślnie po stronie portalu Marketplace.  A następnie Wydawca powinien anulować subskrypcję po stronie wydawcy.

*Nagłówki odpowiedzi:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Połącz z zasobem, aby uzyskać stan operacji.  Na przykład `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kod: 400 Nieprawidłowe żądanie.  Usuwanie nie znajduje się na `allowedCustomerOperations` liście dla tej subskrypcji SaaS.

Kod: 403 zabroniony.  Token autoryzacji jest nieprawidłowy, wygasł lub niedostępny. Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500. Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Interfejsy API operacji

#### <a name="list-outstanding-operations"></a>Utwórz listę zaległych operacji 

Pobierz listę oczekujących operacji dla określonej subskrypcji SaaS.  Zwrócone operacje powinny zostać potwierdzone przez wydawcę przez wywołanie [interfejsu API poprawek operacji](#update-the-status-of-an-operation).

Obecnie tylko **operacje przywracania** są zwracane jako odpowiedź dla tego wywołania interfejsu API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Użyj 2018-08-31.         |
|    `subscriptionId` | Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |

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
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

Zwraca pusty kod JSON, jeśli operacje przywracania nie są oczekiwane.

Kod: 400 Nieprawidłowe żądanie: Błędy walidacji.

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  Nie znaleziono subskrypcji SaaS `subscriptionId` .

Kod: błąd wewnętrzny serwera 500. Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Pobierz stan operacji

Umożliwia wydawcy śledzenie stanu określonej operacji asynchronicznej:  **unsubskrybuj**, **ChangePlan**lub **ChangeQuantity**.

`operationId`Dla tego wywołania interfejsu API można pobrać z wartości zwracanej przez **lokalizację operacji**, pobrać oczekujące wywołanie interfejsu API operacji lub `<id>` wartość parametru odebraną w wywołaniu elementu webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Pobierz `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Użyj 2018-08-31.  |
|  `subscriptionId`    |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania. |
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
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

Kod: 403 zabroniony. Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) . 

Nie znaleziono kodu: 404.  

* Nie znaleziono subskrypcji z usługą `subscriptionId` .
* Operacja z `operationId` nie została znaleziona.

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Aktualizowanie stanu operacji

Zaktualizuj stan oczekującej operacji, aby wskazać powodzenie lub niepowodzenie operacji po stronie wydawcy.

`operationId`Dla tego wywołania interfejsu API można pobrać z wartości zwracanej przez **lokalizację operacji**, pobrać oczekujące wywołanie interfejsu API operacji lub `<id>` wartość parametru odebraną w wywołaniu elementu webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Wysłana `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|  Parametr         | Wartość             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Użyj 2018-08-31.  |
|   `subscriptionId`   |  Unikatowy identyfikator zakupionej subskrypcji SaaS.  Ten identyfikator jest uzyskiwany po rozwiązaniu tokenu autoryzacji portalu Marketplace przy użyciu interfejsu API rozpoznawania.  |
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
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Kody odpowiedzi:*

Kod: 200 wywołanie do informowania o zakończeniu operacji po stronie partnera.  Na przykład ta odpowiedź może sygnalizować zakończenie zmiany stanowisk lub planów po stronie wydawcy.

Kod: 403 zabroniony.  Token autoryzacji jest niedostępny, nieprawidłowy lub wygasł. Żądanie może próbować uzyskać dostęp do subskrypcji, która nie należy do bieżącego wydawcy.
Zabrania.  Token autoryzacji jest nieprawidłowy, wygasł lub nie został podany.  Żądanie próbuje uzyskać dostęp do subskrypcji SaaS dla oferty, która jest publikowana przy użyciu innego identyfikatora aplikacja usługi Azure AD od użytego do utworzenia tokenu autoryzacji.

Ten błąd jest często objawem niepoprawnego wykonywania [rejestracji SaaS](pc-saas-registration.md) .

Nie znaleziono kodu: 404.

* Nie znaleziono subskrypcji z usługą `subscriptionId` .
* Operacja z `operationId` nie została znaleziona.

Kod: konflikt 409.  Na przykład nowsze aktualizacje są już spełnione.

Kod: błąd wewnętrzny serwera 500.  Ponów wywołanie interfejsu API.  Jeśli błąd będzie się powtarzać, skontaktuj się z [pomocą techniczną firmy Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementowanie elementu webhook w usłudze SaaS

W przypadku tworzenia oferty SaaS z transactu w centrum partnerskim partner udostępnia adres URL **elementu webhook połączenia** , który będzie używany jako punkt końcowy HTTP.  Ten element webhook jest wywoływany przez firmę Microsoft przy użyciu wywołania POST protokołu HTTP w celu powiadomienia po stronie wydawcy następujących zdarzeń po stronie firmy Microsoft:

* Gdy subskrypcja SaaS jest w stanie subskrybowanym:
    * ChangePlan 
    * ChangeQuantity
    * Wstrzymanie
    * Anuluj subskrypcję
* Gdy subskrypcja SaaS jest w stanie wstrzymania:
    * Przywróć
    * Anuluj subskrypcję

Wydawca musi zaimplementować element webhook w usłudze SaaS, aby zapewnić, że stan subskrypcji SaaS jest spójny ze stroną firmy Microsoft.  Usługa SaaS jest wymagana do wywołania interfejsu API operacji get w celu zweryfikowania i autoryzacji danych wywołania i ładunku elementu webhook przed podjęciem działania na podstawie powiadomienia elementu webhook.  Wydawca powinien zwrócić do firmy Microsoft protokół HTTP 200 zaraz po przetworzeniu wywołania elementu webhook.  Ta wartość potwierdza, że wywołanie elementu webhook zostało pomyślnie odebrane przez wydawcę.

>[!Note]
>Usługa URL elementu webhook musi być uruchomiona i działać w systemie 24x7 oraz być gotowa do otrzymywania nowych wywołań od firmy Microsoft przez cały czas.  Firma Microsoft ma zasady ponawiania dla wywołania elementu webhook (500 ponownych prób przez 8 godzin), ale jeśli Wydawca nie akceptuje wywołania i zwróci odpowiedź, operacja, do której powiadamia element webhook, zakończy się niepowodzeniem po stronie firmy Microsoft.

*Przykłady ładunku elementu webhook:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
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
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
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

Aby rozpocząć proces opracowywania, zalecamy utworzenie fikcyjnych odpowiedzi interfejsu API po stronie wydawcy.  Odpowiedzi te mogą opierać się na przykładowych odpowiedziach podanych w tym dokumencie.

Gdy Wydawca jest gotowy do zakończenia testowania: 

* Opublikuj ofertę SaaS w ograniczonej grupie odbiorców w wersji zapoznawczej i Zachowaj ją na etapie wersji zapoznawczej.
* Ta oferta powinna mieć plan z równą 0 cenie, dlatego nie należy wyzwalać rzeczywistych wydatków rozliczeniowych podczas testowania.  Innym rozwiązaniem jest ustawienie ceny niezerowej i anulowanie wszystkich zakupów testowych w ciągu 24 godzin. 
* Upewnij się, że wszystkie przepływy są wywoływane na zakończenie, tak jak w przypadku zakupienia oferty przez klienta. 
* Jeśli Partner chce przetestować pełny przepływ zakupów i rozliczeń, należy to zrobić z ofertą podaną powyżej $0.  Zakup zostanie rozliczony i zostanie wygenerowana faktura.

Przepływ zakupu można wyzwolić z poziomu witryny Azure Portal lub Microsoft AppSource, w zależności od tego, gdzie jest publikowana oferta.

Akcje *zmiany planu*, *ilości*i *anulowania subskrypcji* są testowane po stronie wydawcy.  Po stronie firmy Microsoft można wyzwolenie *anulowania subskrypcji* z poziomu Centrum Azure Portal i administratora (w portalu, w którym Microsoft AppSource zakupy są zarządzane).  *Zmiany ilości i planu* można wyzwolić tylko z poziomu Centrum administracyjnego.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Zobacz [Pomoc techniczna dla komercyjnego programu w witrynie Marketplace w centrum partnerskim](support.md) dla opcji pomocy technicznej wydawcy.


## <a name="next-steps"></a>Następne kroki

Zobacz [komercyjne interfejsy API usługi pomiarowej Marketplace](marketplace-metering-service-apis.md) , aby uzyskać więcej opcji dla ofert SaaS w komercyjnej witrynie Marketplace.

Przejrzyj i Użyj [zestawu SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) , który został utworzony na podstawie interfejsów API opisanych w tym dokumencie.
