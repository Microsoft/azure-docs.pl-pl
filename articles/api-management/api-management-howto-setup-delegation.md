---
title: Jak delegować rejestrację użytkownika i subskrypcję produktu
description: Dowiedz się, jak delegować rejestrację użytkownika i subskrypcję produktu do innej firmy w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341845"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak delegować rejestrację użytkownika i subskrypcję produktu

Delegowanie umożliwia korzystanie z istniejącej witryny sieci Web do obsługi logowania/tworzenia konta i subskrypcji dla deweloperów, w przeciwieństwie do korzystania z wbudowanych funkcji w portalu dla deweloperów. Umożliwia ona korzystanie z danych użytkownika przez witrynę internetową i wykonywanie walidacji tych kroków w niestandardowy sposób.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegowanie logowania i tworzenia konta dewelopera

Aby delegować deweloperów, zaloguj się do istniejącej witryny sieci Web i zaloguj się do niej, musisz utworzyć specjalny punkt końcowy delegowania w witrynie. Musi działać jako punkt wejścia dla każdego żądania zainicjowanego w portalu deweloperów API Management.

Końcowy przepływ pracy będzie następujący:

1. Kliknięcie linku Zaloguj się lub Utwórz konto w portalu dla deweloperów API Management
2. Przeglądarka została przekierowana do punktu końcowego delegowania
3. Punkt końcowy delegowania w przypadku zwrotów zwraca lub przedstawia użytkownika z monitem o zalogowanie się lub zarejestrowanie
4. Po sukcesie użytkownik zostanie przekierowany z powrotem do strony portalu deweloperów API Management, z której uruchomiono

Aby rozpocząć, najpierw Skonfiguruj API Management, aby kierować żądania za pośrednictwem punktu końcowego delegowania. W Azure Portal Wyszukaj **zabezpieczenia** w API Management zasobów, a następnie kliknij element **delegowania** . Kliknij pole wyboru, aby włączyć funkcję "delegowanie logowania &".

![Strona delegowania][api-management-delegation-signin-up]

* Zdecyduj, jak ma być adresem URL specjalnego punktu końcowego delegowania, a następnie wprowadź go w polu **adres URL punktu końcowego delegowania** . 
* W polu klucz uwierzytelniania delegowania wprowadź klucz tajny, który zostanie użyty do obliczenia sygnatury dostarczonej przez Ciebie w celu weryfikacji, aby upewnić się, że żądanie rzeczywiście pochodzi z usługi Azure API Management. Możesz kliknąć przycisk **Generuj** , aby API Management losowo wygenerować klucz.

Teraz musisz utworzyć **punkt końcowy delegowania**. Musi wykonać kilka akcji:

1. Odbierz żądanie w następującej postaci:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&ReturnUrl = {URL strony źródłowej} &soli = {String} &SIG = {String}*
   
    Parametry zapytania dotyczące przypadku logowania/rejestracji:
   
   * **operacja** : określa, jakiego typu żądanie delegowania jest — może tylko **zalogować** się w tym przypadku
   * **ReturnUrl** : adres URL strony, na której użytkownik kliknął link logowania lub rejestracji
   * **sól** : specjalny ciąg soli używany do obliczania skrótu zabezpieczeń
   * **SIG** : obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczanym skrótem
2. Sprawdź, czy żądanie pochodzi z usługi Azure API Management (opcjonalne, ale zdecydowanie zalecane na potrzeby zabezpieczeń)
   
   * Oblicza wartość skrótu HMAC-SHA512 ciągu na podstawie parametrów zapytania **ReturnUrl** i **soli** ( [przykładowego kodu podanego poniżej]):
     
     > HMAC ( **sól** + ' \n ' + **ReturnUrl** )

   * Porównaj powyższe-obliczony skrót do wartości parametru kwerendy **SIG** . Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie Odmów żądania.
3. Sprawdź, czy otrzymujesz żądanie logowania/rejestracji: parametr zapytania **operacji** zostanie ustawiony na " **Logowanie** ".
4. Zaprezentowanie użytkownikowi interfejsu użytkownika w celu zalogowania się lub zarejestrowania
5. Jeśli użytkownik jest zalogowany, musisz utworzyć odpowiednie konto dla nich w API Management. [Utwórz użytkownika] przy użyciu interfejsu API REST API Management. W tym celu należy się upewnić, że identyfikator użytkownika jest ustawiony na taką samą wartość jak w magazynie użytkownika lub na identyfikator, który można śledzić.
6. Po pomyślnym uwierzytelnieniu użytkownika:
   
   * [Żądanie tokenu dostępu współdzielonego] za pośrednictwem interfejsu API REST API Management
   * Dołącz parametr zapytania returnUrl do adresu URL logowania jednokrotnego otrzymanego z wywołania interfejsu API powyżej:
     
     > na przykład `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * Przekieruj użytkownika do powyższego wygenerowanego adresu URL

Oprócz operacji **logowania** można także wykonać zarządzanie kontami, wykonując poprzednie kroki i wykonując jedną z następujących czynności:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **Wylogowaniu**

Należy przekazać następujące parametry zapytania dla operacji zarządzania kontami.

* **operacja** : określa, jakiego typu żądanie delegowania jest (ChangePassword, ChangeProfile lub CloseAccount)
* **userId** : identyfikator użytkownika konta do zarządzania
* **sól** : specjalny ciąg soli używany do obliczania skrótu zabezpieczeń
* **SIG** : obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczanym skrótem

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegowanie subskrypcji produktu

Delegowanie subskrypcji produktu działa podobnie do delegowania logowania użytkowników. Końcowy przepływ pracy będzie następujący:

1. Deweloper wybiera produkt w portalu dla deweloperów API Management i klika przycisk Subskrybuj.
2. Przeglądarka została przekierowana do punktu końcowego delegowania.
3. Punkt końcowy delegowania wykonuje wymagane kroki subskrypcji produktu. Jest to konieczne do zaprojektowania kroków. Mogą one obejmować przekierowanie do innej strony w celu żądania informacji dotyczących rozliczeń, zadawania dodatkowych pytań lub po prostu przechowywania informacji i niewymagających żadnych działań użytkownika.

Aby włączyć tę funkcję, na stronie **delegowania** kliknij pozycję **Deleguj subskrypcję produktu**.

Następnie upewnij się, że punkt końcowy delegowania wykonuje następujące czynności:

1. Odbierz żądanie w następującej postaci:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation? Operation = {Operation} &ProductID = {produkt do subskrybowania} &userId = {userion Request} &sól = {String} &SIG = {String}*
   >
   
    Parametry zapytania dotyczące przypadku subskrypcji produktu:
   
   * **operacja** : określa, jakiego typu żądanie delegowania. W przypadku żądań subskrypcji produktu prawidłowe opcje są następujące:
     * "Subskrybuj": żądanie subskrybowania użytkownika dla danego produktu o podanym IDENTYFIKATORze (patrz poniżej)
     * "Anuluj subskrypcję": żądanie anulowania subskrypcji użytkownika z produktu
     * "Odnów": żądanie odnowienia subskrypcji (na przykład może wygasnąć)
   * **ProductID** : w *subskrypcji* — identyfikator produktu, którego użytkownik zażądał subskrybowania
   * Identyfikator **subskrypcji: po** *anulowaniu subskrypcji* i *odnowieniu* identyfikatora
   * **userId** : w *subskrypcji* — identyfikator użytkownika, dla którego wykonano żądanie
   * **sól** : specjalny ciąg soli używany do obliczania skrótu zabezpieczeń
   * **SIG** : obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczanym skrótem

2. Sprawdź, czy żądanie pochodzi z usługi Azure API Management (opcjonalne, ale zdecydowanie zalecane na potrzeby zabezpieczeń)
   
   * Oblicza wartość HMAC-SHA512 ciągu w oparciu o parametry **ProductID** , **userId** i **soli** zapytania:
     
     > HMAC ( **sól** + ' \n ' + **ProductID** + ' \n ' + **userId** )
     > 
     > 
   * Porównaj powyższe-obliczony skrót do wartości parametru kwerendy **SIG** . Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie Odmów żądania.
3. Przetwarzaj subskrypcję produktu na podstawie typu operacji zażądanej w **operacji** — na przykład rozliczenia, dalsze pytania itd.
4. Po pomyślnym zasubskrybowaniu użytkownikowi produktu po stronie Zasubskrybuj użytkownika API Management produkt, [wywołując interfejs API REST dla subskrypcji].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Przykładowy kod

Te przykłady kodu pokazują, jak:

* Wypełnij *klucz weryfikacji delegowania* , który jest ustawiany na ekranie delegowania portalu wydawcy
* Utwórz HMAC, który jest następnie używany do weryfikacji podpisu, potwierdzając ważność pozostałej returnUrl.

Ten sam kod działa w przypadku identyfikatorów productId i userId z niewielkim modyfikacją.

**Kod C# do generowania skrótu returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Kod NodeJS do generowania skrótu returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Aby zmiany delegowania zaczęły obowiązywać, należy [ponownie opublikować Portal dla deweloperów](api-management-howto-developer-portal-customize.md#publish) .

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat delegowania, zobacz następujące wideo:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Zażądaj tokenu dostępu współdzielonego]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[Tworzenie użytkownika]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[Wywoływanie interfejsu API REST dla subskrypcji]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[przykładowy kod podany poniżej]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
