---
title: Dokumentacja modelu danych szablonu API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej o jednostkach i typach reprezentacji dla typowych elementów używanych w modelach danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 868ad3d1c6e7e7ef2cf32dcf675bc471a614f3ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86243158"
---
# <a name="azure-api-management-template-data-model-reference"></a>Dokumentacja modelu danych szablonu API Management platformy Azure
W tym temacie opisano reprezentacje jednostek i typów dla typowych elementów używanych w modelach danych dla szablonów portalu dla deweloperów w usłudze Azure API Management.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Odwołanie

-   [Interfejs API](#API)  
-   [Podsumowanie interfejsu API](#APISummary)  
-   [Aplikacja](#Application)  
-   [Załącznik](#Attachment)  
-   [Przykład kodu](#Sample)  
-   [Komentarz](#Comment)  
-   [Filtrowanie](#Filtering)  
-   [Nagłówek](#Header)  
-   [Żądanie HTTP](#HTTPRequest)  
-   [Odpowiedź HTTP](#HTTPResponse)  
-   [Wykonaj](#Issue)  
-   [Operacja](#Operation)  
-   [Menu operacje](#Menu)  
-   [Element menu operacji](#MenuItem)  
-   [Stronicowanie](#Paging)  
-   [Parametr](#Parameter)  
-   [Product](#Product)  
-   [Dostawca](#Provider)  
-   [Reprezentowana](#Representation)  
-   [Subskrypcja](#Subscription)  
-   [Podsumowanie subskrypcji](#SubscriptionSummary)  
-   [Informacje o koncie użytkownika](#UserAccountInfo)  
-   [Logowanie użytkowników](#UseSignIn)  
-   [Rejestracja użytkownika](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a> INTERFEJSU API  
 `API`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis interfejsu API. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`serviceUrl`|ciąg|Bezwzględny adres URL usługi wewnętrznej bazy danych implementującej ten interfejs API.|  
|`path`|ciąg|Względny adres URL, który jednoznacznie identyfikuje ten interfejs API i wszystkie jego ścieżki zasobów w ramach wystąpienia usługi API Management. Jest dołączany do podstawowego adresu URL punktu końcowego interfejsu API określonego podczas tworzenia wystąpienia usługi, aby utworzyć publiczny adres URL dla tego interfejsu API.|  
|`protocols`|tablica liczb|Opisuje protokoły, w których można wywołać operacje w tym interfejsie API. Dozwolone wartości to `1 - http` i i `2 - https` .|  
|`authenticationSettings`|[Ustawienia uwierzytelniania serwera autoryzacji](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekcja ustawień uwierzytelniania uwzględnionych w tym interfejsie API.|  
|`subscriptionKeyParameterNames`|object|Opcjonalna właściwość, która może służyć do określania nazw niestandardowych dla parametrów zapytania i/lub nagłówka zawierających klucz subskrypcji. Gdy ta właściwość jest obecna, musi zawierać co najmniej jedną z dwóch następujących właściwości.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a> Podsumowanie interfejsu API  
 `API summary`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje interfejs API w bieżącym wystąpieniu usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `apis/{id}` gdzie `{id}` jest identyfikatorem interfejsu API. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa interfejsu API. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis interfejsu API. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
  
##  <a name="application"></a><a name="Application"></a> Aplikacja  
 `application`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Unikatowy identyfikator aplikacji.|  
|`Title`|ciąg|Tytuł aplikacji.|  
|`Description`|ciąg|Opis aplikacji.|  
|`Url`|URI|Identyfikator URI aplikacji.|  
|`Version`|ciąg|Informacje o wersji aplikacji.|  
|`Requirements`|ciąg|Opis wymagań aplikacji.|  
|`State`|liczba|Bieżący stan aplikacji.<br /><br /> -0-zarejestrowano<br /><br /> -1-przesłane<br /><br /> -2 — Opublikowano<br /><br /> -3 — odrzucono<br /><br /> -4 — nieopublikowany|  
|`RegistrationDate`|DateTime|Data i godzina zarejestrowania aplikacji.|  
|`CategoryId`|liczba|Kategoria aplikacji (Finanse, rozrywka itp.)|  
|`DeveloperId`|ciąg|Unikatowy identyfikator dewelopera, który przesłał aplikację.|  
|`Attachments`|Kolekcja jednostek [załączników](#Attachment) .|Wszystkie załączniki dla aplikacji, takie jak zrzuty ekranu lub ikony.|  
|`Icon`|[Załącznik](#Attachment)|Ikona dla aplikacji.|  
  
##  <a name="attachment"></a><a name="Attachment"></a> Umieszczenie  
 `attachment`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`UniqueId`|ciąg|Unikatowy identyfikator załącznika.|  
|`Url`|ciąg|Adres URL zasobu.|  
|`Type`|ciąg|Typ załącznika.|  
|`ContentType`|ciąg|Typ nośnika załącznika.|  
  
##  <a name="code-sample"></a><a name="Sample"></a> Przykładowy kod  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`title`|ciąg|Nazwa operacji.|  
|`snippet`|ciąg|Ta właściwość jest przestarzała i nie powinna być używana.|  
|`brush`|ciąg|Który szablon kolorowania składni kodu ma być używany podczas wyświetlania przykładu kodu. Dozwolone wartości to,,,,,, `plain` `php` `java` `xml` `objc` `python` `ruby` i `csharp` .|  
|`template`|ciąg|Nazwa tego szablonu przykładowego kodu.|  
|`body`|ciąg|Symbol zastępczy fragmentu kodu.|  
|`method`|ciąg|Metoda HTTP operacji.|  
|`scheme`|ciąg|Protokół, który ma być używany dla żądania operacji.|  
|`path`|ciąg|Ścieżka operacji.|  
|`query`|ciąg|Przykład ciągu zapytania ze zdefiniowanymi parametrami.|  
|`host`|ciąg|Adres URL bramy usługi API Management dla interfejsu API, który zawiera tę operację.|  
|`headers`|Kolekcja jednostek                                  [nagłówka](#Header) .|Nagłówki dla tej operacji.|  
|`parameters`|Kolekcja jednostek [parametrów](#Parameter) .|Parametry, które są zdefiniowane dla tej operacji.|  
  
##  <a name="comment"></a><a name="Comment"></a> Komentować  
 `API`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|liczba|Identyfikator komentarza.|  
|`CommentText`|ciąg|Treść komentarza. Może zawierać kod HTML.|  
|`DeveloperCompany`|ciąg|Nazwa firmy dewelopera.|  
|`PostedOn`|DateTime|Data i godzina opublikowania komentarza.|  
  
##  <a name="issue"></a><a name="Issue"></a> Wykonaj  
 `issue`Jednostka ma następujące właściwości.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Unikatowy identyfikator problemu.|  
|`ApiID`|ciąg|Identyfikator interfejsu API, dla którego zgłoszono ten problem.|  
|`Title`|ciąg|Tytuł problemu.|  
|`Description`|ciąg|Opis problemu.|  
|`SubscriptionDeveloperName`|ciąg|Imię i nazwisko dewelopera, który zgłosił problem.|  
|`IssueState`|ciąg|Bieżący stan problemu. Możliwe wartości są proponowane, otwarte i zamknięte.|  
|`ReportedOn`|DateTime|Data i godzina zgłoszenia problemu.|  
|`Comments`|Kolekcja jednostek [komentarzy](#Comment) .|Komentarze dotyczące tego problemu.|  
|`Attachments`|Kolekcja jednostek [załączników](api-management-template-data-model-reference.md#Attachment) .|Dowolnych załączników do problemu.|  
|`Services`|Kolekcja jednostek [interfejsu API](#API) .|Interfejsy API subskrybowane przez użytkownika, który zgłosił problem.|  
  
##  <a name="filtering"></a><a name="Filtering"></a> Identyfikatorów  
 `filtering`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Pattern`|ciąg|Bieżący termin wyszukiwania; lub `null` Jeśli nie ma wyszukiwanego terminu.|  
|`Placeholder`|ciąg|Tekst, który ma być wyświetlany w polu wyszukiwania, gdy nie określono terminu wyszukiwania.|  
  
##  <a name="header"></a><a name="Header"></a> Nagłówki  
 W tej sekcji opisano `parameter` reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|ciąg|Nazwa parametru.|  
|`description`|ciąg|Opis parametru.|  
|`value`|ciąg|Wartość nagłówka.|  
|`typeName`|ciąg|Typ danych wartości nagłówka.|  
|`options`|ciąg|Opcje.|  
|`required`|boolean|Określa, czy nagłówek jest wymagany.|  
|`readOnly`|boolean|Czy nagłówek jest tylko do odczytu.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a> Żądanie HTTP  
 W tej sekcji opisano `request` reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`description`|ciąg|Opis żądania operacji.|  
|`headers`|Tablica jednostek [nagłówka](#Header) .|Nagłówki żądania.|  
|`parameters`|Tablica [parametrów](#Parameter)|Kolekcja parametrów żądania operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacji żądań operacji.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a> Odpowiedź HTTP  
 W tej sekcji opisano `response` reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`statusCode`|dodatnia liczba całkowita|Kod stanu odpowiedzi operacji.|  
|`description`|ciąg|Opis odpowiedzi operacji.|  
|`representations`|Tablica [reprezentacji](#Representation)|Kolekcja reprezentacji odpowiedzi operacji.|  
  
##  <a name="operation"></a><a name="Operation"></a> Operacje  
 `operation`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`id`|ciąg|Identyfikator zasobu. Unikatowa identyfikacja operacji w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie, `apis/{aid}/operations/{id}` gdzie `{aid}` jest identyfikatorem interfejsu API i `{id}` jest identyfikatorem operacji. Ta właściwość jest tylko do odczytu.|  
|`name`|ciąg|Nazwa operacji. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`description`|ciąg|Opis operacji. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`scheme`|ciąg|Opisuje protokoły, w których można wywołać operacje w tym interfejsie API. Dozwolone wartości to `http` , `https` , lub `http` i `https` .|  
|`uriTemplate`|ciąg|Szablon względnego adresu URL identyfikujący zasób docelowy dla tej operacji. Może zawierać parametry. Przykład: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|ciąg|Adres URL bramy API Management, który hostuje interfejs API.|  
|`httpMethod`|ciąg|Metoda HTTP operacji.|  
|`request`|[Żądanie HTTP](#HTTPRequest)|Jednostka zawierająca szczegóły żądania.|  
|`responses`|Tablica [odpowiedzi HTTP](#HTTPResponse)|Tablica obiektów [odpowiedzi HTTP](#HTTPResponse) operacji.|  
  
##  <a name="operation-menu"></a><a name="Menu"></a> Menu operacje  
 `operation menu`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`ApiId`|ciąg|Identyfikator bieżącego interfejsu API.|  
|`CurrentOperationId`|ciąg|Identyfikator bieżącej operacji.|  
|`Action`|ciąg|Typ menu.|  
|`MenuItems`|Kolekcja jednostek [elementów menu operacji](#MenuItem) .|Operacje dla bieżącego interfejsu API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a> Element menu operacji  
 `operation menu item`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator operacji.|  
|`Title`|ciąg|Opis operacji.|  
|`HttpMethod`|ciąg|Metoda http operacji.|  
  
##  <a name="paging"></a><a name="Paging"></a> Stronicowania  
 `paging`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Page`|liczba|Numer bieżącej strony.|  
|`PageSize`|liczba|Maksymalna liczba wyników do wyświetlenia na jednej stronie.|  
|`TotalItemCount`|liczba|Liczba elementów do wyświetlenia.|  
|`ShowAll`|boolean|Czy wszystkie wyniki mają być pożądane na jednej stronie.|  
|`PageCount`|liczba|Liczba stron wyników.|  
  
##  <a name="parameter"></a><a name="Parameter"></a> Konstruktora  
 W tej sekcji opisano `parameter` reprezentację.  
  
|Właściwość|Typ|Opis|  
|--------------|-----------------|----------|  
|`name`|ciąg|Nazwa parametru.|  
|`description`|ciąg|Opis parametru.|  
|`value`|ciąg|Wartość parametru.|  
|`options`|tablica ciągów|Wartości zdefiniowane dla wartości parametrów zapytania.|  
|`required`|boolean|Określa, czy parametr jest wymagany, czy nie.|  
|`kind`|liczba|Określa, czy ten parametr jest parametrem ścieżki (1), czy parametrem QueryString (2).|  
|`typeName`|ciąg|Typ parametru.|  
  
##  <a name="product"></a><a name="Product"></a> Iloczyn  
 `product`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Unikatowy identyfikator produktu w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie, `products/{pid}` gdzie `{pid}` jest identyfikatorem produktu. Ta właściwość jest tylko do odczytu.|  
|`Title`|ciąg|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Description`|ciąg|Opis produktu. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`Terms`|ciąg|Warunki użytkowania produktu. Deweloperzy próbujący subskrybować produkt będą przedstawiani i zobowiązani do zaakceptowania tych warunków przed ukończeniem procesu subskrypcji.|  
|`ProductState`|liczba|Określa, czy produkt jest publikowany, czy nie. Opublikowane produkty są odnajdywane przez deweloperów w portalu dla deweloperów. Nieopublikowane produkty są widoczne tylko dla administratorów.<br /><br /> Dopuszczalne wartości dla stanu produktu to:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Określa, czy użytkownik może jednocześnie korzystać z wielu subskrypcji tego produktu.|  
|`MultipleSubscriptionsCount`|liczba|Maksymalna liczba subskrypcji tego produktu, które może mieć użytkownik, w tym samym czasie.|  
  
##  <a name="provider"></a><a name="Provider"></a> Dostawcy  
 `provider`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Properties`|słownik ciągów|Właściwości tego dostawcy uwierzytelniania.|  
|`AuthenticationType`|ciąg|Typ dostawcy. (Azure Active Directory, logowanie do serwisu Facebook, konto Google, konto Microsoft, Twitter).|  
|`Caption`|ciąg|Nazwa wyświetlana dostawcy.|  
  
##  <a name="representation"></a><a name="Representation"></a> Reprezentowana  
 W tej sekcji opisano a `representation` .  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`contentType`|ciąg|Określa zarejestrowany lub niestandardowy typ zawartości dla tej reprezentacji, na przykład `application/xml` .|  
|`sample`|ciąg|Przykład reprezentacji.|  
  
##  <a name="subscription"></a><a name="Subscription"></a> Ramach  
 `subscription`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest Identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`ProductId`|ciąg|Identyfikator zasobu produktu subskrybowanego produktu. Wartość jest prawidłowym względnym adresem URL w formacie, `products/{pid}` gdzie `{pid}` jest identyfikatorem produktu.|  
|`ProductTitle`|ciąg|Nazwa produktu. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`ProductDescription`|ciąg|Opis produktu. Nie może być pusty. Może zawierać Tagi formatowania HTML. Maksymalna długość to 1000 znaków.|  
|`ProductDetailsUrl`|ciąg|Względny adres URL do szczegółowych informacji o produkcie.|  
|`state`|ciąg|Stan subskrypcji. Możliwe stany to:<br /><br /> - `0 - suspended` — subskrypcja została zablokowana i subskrybent nie może wywołać żadnych interfejsów API produktu.<br /><br /> - `1 - active` — subskrypcja jest aktywna.<br /><br /> - `2 - expired` — subskrypcja osiągnęła swoją datę wygaśnięcia i została zdezaktywowana.<br /><br /> - `3 - submitted` — żądanie subskrypcji zostało wykonane przez dewelopera, ale jeszcze nie zostało zatwierdzone lub odrzucone.<br /><br /> - `4 - rejected` — żądanie subskrypcji zostało odrzucone przez administratora.<br /><br /> - `5 - cancelled` — subskrypcja została anulowana przez dewelopera lub administratora.|  
|`DisplayName`|ciąg|Nazwa wyświetlana subskrypcji.|  
|`CreatedDate`|Data i godzina|Data utworzenia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z` .|  
|`CanBeCancelled`|boolean|Czy subskrypcja może zostać anulowana przez bieżącego użytkownika.|  
|`IsAwaitingApproval`|boolean|Czy subskrypcja oczekuje na zatwierdzenie.|  
|`StartDate`|Data i godzina|Data rozpoczęcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z` .|  
|`ExpirationDate`|Data i godzina|Data wygaśnięcia subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z` .|  
|`NotificationDate`|Data i godzina|Data powiadomienia dla subskrypcji w formacie ISO 8601: `2014-06-24T16:25:00Z` .|  
|`primaryKey`|ciąg|Klucz subskrypcji podstawowej. Maksymalna długość to 256 znaków.|  
|`secondaryKey`|ciąg|Pomocniczy klucz subskrypcji. Maksymalna długość to 256 znaków.|  
|`CanBeRenewed`|boolean|Czy subskrypcja może zostać odnowiona przez bieżącego użytkownika.|  
|`HasExpired`|boolean|Czy subskrypcja wygasła.|  
|`IsRejected`|boolean|Czy żądanie subskrypcji zostało odrzucone.|  
|`CancelUrl`|ciąg|Względny adres URL, aby anulować subskrypcję.|  
|`RenewUrl`|ciąg|Względny adres URL do odnowienia subskrypcji.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a> Podsumowanie subskrypcji  
 `subscription summary`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Id`|ciąg|Identyfikator zasobu. Jednoznacznie identyfikuje subskrypcję w ramach bieżącego wystąpienia usługi API Management. Wartość jest prawidłowym względnym adresem URL w formacie `subscriptions/{sid}` gdzie `{sid}` jest Identyfikator subskrypcji. Ta właściwość jest tylko do odczytu.|  
|`DisplayName`|ciąg|Nazwa wyświetlana subskrypcji|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a> Informacje o koncie użytkownika  
 `user account info`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`FirstName`|ciąg|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|ciąg|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`Email`|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|ciąg|Hasło konta użytkownika.|  
|`NameIdentifier`|ciąg|Identyfikator konta, taki sam jak adres e-mail użytkownika.|  
|`ProviderName`|ciąg|Nazwa dostawcy uwierzytelniania.|  
|`IsBasicAccount`|boolean|Prawda, jeśli to konto zostało zarejestrowane przy użyciu poczty e-mail i hasła; wartość false, jeśli konto zostało zarejestrowane przy użyciu dostawcy.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a> Logowanie użytkownika  
 `user sign in`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Email`|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`Password`|ciąg|Hasło konta użytkownika.|  
|`ReturnUrl`|ciąg|Adres URL strony, na której użytkownik kliknął przycisk Zaloguj.|  
|`RememberMe`|boolean|Czy zapisać informacje o bieżącym użytkowniku.|  
|`RegistrationEnabled`|boolean|Czy rejestracja jest włączona.|  
|`DelegationEnabled`|boolean|Czy delegowane logowanie jest włączone.|  
|`DelegationUrl`|ciąg|Delegowany adres URL logowania, jeśli jest włączony.|  
|`SsoSignUpUrl`|ciąg|Adres URL logowania jednokrotnego dla użytkownika, jeśli jest obecny.|  
|`AuxServiceUrl`|ciąg|Jeśli bieżący użytkownik jest administratorem, jest to link do wystąpienia usługi w Azure Portal.|  
|`Providers`|Kolekcja jednostek [dostawcy](#Provider)|Dostawcy uwierzytelniania dla tego użytkownika.|  
|`UserRegistrationTerms`|ciąg|Warunki, które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsEnabled`|boolean|Czy warunki są włączone.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a> Rejestracja użytkownika  
 `user sign up`Jednostka ma następujące właściwości:  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`Password`|ciąg|Hasło konta użytkownika.|  
|`PasswordVerdictLevel`|liczba|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|ciąg|Warunki, które użytkownik musi wyrazić zgodę przed zalogowaniem się.|  
|`UserRegistrationTermsOptions`|liczba|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`Email`|ciąg|Adres e-mail. Nie może być pusta i musi być unikatowa w obrębie wystąpienia usługi. Maksymalna długość to 254 znaków.|  
|`FirstName`|ciąg|Imię. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`LastName`|ciąg|Nazwisko. Nie może być pusty. Maksymalna długość to 100 znaków.|  
|`UserData`|ciąg|Wartość używana przez formant [rejestracji](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|ciąg|Wartość używana przez formant rejestracji w celu zarejestrowania [się](api-management-page-controls.md#sign-up).|  
|`ProviderName`|ciąg|Nazwa dostawcy uwierzytelniania.|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
