---
title: Azure Active Directory konfigurację tożsamości dla interfejsu API platformy Azure dla FHIR
description: Poznaj zasady tożsamości, uwierzytelniania i autoryzacji dla serwerów usługi Azure FHIR.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020261"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure Active Directory konfigurację tożsamości dla interfejsu API platformy Azure dla FHIR

Ważnym elementem podczas pracy z danymi zdrowotnymi jest upewnienie się, że dane są bezpieczne i nie są dostępne dla nieautoryzowanych użytkowników lub aplikacji. Serwery FHIR używają protokołu [OAuth 2,0](https://oauth.net/2/) do zapewnienia bezpieczeństwa danych. [Interfejs API platformy Azure dla FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) jest zabezpieczony przy użyciu [Azure Active Directory](../../active-directory/index.yml), który jest przykładem dostawcy tożsamości OAuth 2,0. Ten artykuł zawiera omówienie autoryzacji serwera FHIR oraz kroki niezbędne do uzyskania tokenu dostępu do serwera FHIR. Chociaż te kroki zostaną zastosowane do dowolnego serwera FHIR i dowolnego dostawcy tożsamości, przejdziemy przez interfejs API platformy Azure dla usługi FHIR jako serwer FHIR i usługę Azure AD jako nasz dostawca tożsamości w tym artykule.

## <a name="access-control-overview"></a>Omówienie kontroli dostępu

Aby aplikacja kliencka mogła uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR, musi przedstawić token dostępu. Token dostępu to podpisana, zakodowana w [formacie base64](https://en.wikipedia.org/wiki/Base64) kolekcja właściwości (oświadczenia) przekazująca informacje o tożsamości i rolach klienta przyznanych klientowi.

Istnieje wiele sposobów uzyskania tokenu, ale interfejs API platformy Azure dla FHIR nie ma wpływu na to, że token jest uzyskiwany, o ile jest odpowiednio podpisanym tokenem z prawidłowymi oświadczeniami. 

Przy użyciu [przepływu kodu autoryzacji](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) na przykład dostęp do serwera FHIR przechodzi przez cztery poniższe kroki:

![Autoryzacja FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. Klient wysyła żądanie do `/authorize` punktu końcowego usługi Azure AD. Usługa Azure AD przekierowuje klienta do strony logowania, na której użytkownik będzie uwierzytelniany przy użyciu odpowiednich poświadczeń (na przykład nazwa użytkownika i hasło lub uwierzytelnianie dwuskładnikowe). Zobacz szczegóły dotyczące [uzyskiwania kodu autoryzacji](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Po pomyślnym uwierzytelnieniu *kod autoryzacji* jest zwracany do klienta. Usługa Azure AD zezwoli na zwracanie tego kodu autoryzacji tylko na zarejestrowany adres URL odpowiedzi skonfigurowany w rejestracji aplikacji klienta (patrz poniżej).
1. Aplikacja kliencka wymienia kod autoryzacji dla *tokenu dostępu* w `/token` punkcie końcowym usługi Azure AD. Podczas żądania tokenu aplikacja kliencka może wymagać podania klucza tajnego klienta (hasła aplikacji). Zobacz szczegóły dotyczące [uzyskiwania tokenu dostępu](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Klient wysyła żądanie do interfejsu API platformy Azure dla usługi FHIR, na przykład w `GET /Patient` celu wyszukania wszystkich pacjentów. Podczas wykonywania żądania zawiera on token dostępu w nagłówku żądania HTTP, na przykład `Authorization: Bearer eyJ0e...` , gdzie `eyJ0e...` reprezentuje token dostępu szyfrowany algorytmem Base64.
1. Interfejs API platformy Azure dla FHIR sprawdza, czy token zawiera odpowiednie oświadczenia (właściwości w tokenie). Jeśli wszystko jest wyewidencjonowane, zakończy żądanie i zwróci pakiet FHIR z wynikami do klienta.

Należy pamiętać, że interfejs API platformy Azure dla FHIR nie jest związany z walidacją poświadczeń użytkownika i nie wydaje tokena. Tworzenie uwierzytelniania i tokenów odbywa się za pomocą usługi Azure AD. Interfejs API platformy Azure dla usługi FHIR po prostu sprawdza, czy token jest poprawnie podpisany (jest autentyczny) i czy ma odpowiednie oświadczenia.

## <a name="structure-of-an-access-token"></a>Struktura tokenu dostępu

Programowanie aplikacji FHIR często obejmuje Debugowanie problemów z dostępem. Jeśli klientowi odmówiono dostępu do interfejsu API platformy Azure dla usługi FHIR, warto zrozumieć strukturę tokenu dostępu i sposób, w jaki może być zdekodowany, aby sprawdzić zawartość (oświadczenia) tokenu. 

Serwery FHIR zazwyczaj oczekują [tokenu sieci Web JSON](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, czasami wymawiane "notowania"). Składa się z trzech części:

1. Nagłówek, który może wyglądać następująco:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. Ładunek (oświadczenia), na przykład:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Podpis, który jest obliczany przez połączenie zawartości nagłówka i ładunku w formacie base64 oraz obliczanie skrótu kryptograficznego na podstawie algorytmu ( `alg` ) określonego w nagłówku. Serwer będzie mógł uzyskiwać klucze publiczne od dostawcy tożsamości i sprawdzać, czy token został wystawiony przez określonego dostawcę tożsamości i nie został naruszony.

Pełny token obejmuje wersje tych trzech segmentów kodowane algorytmem Base64 (faktycznie zakodowany adres URL). Trzy segmenty są połączone i oddzielane średnikami `.` .

Przykładowy token jest widoczny poniżej:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Token może być zdekodowany i sprawdzany przy użyciu narzędzi, takich jak [https://jwt.ms](https://jwt.ms) . Wynikiem dekodowania tokenu jest:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Uzyskiwanie tokenu dostępu

Jak wspomniano powyżej, istnieje kilka sposobów uzyskania tokenu z usługi Azure AD. Są one szczegółowo opisane w [dokumentacji dewelopera usługi Azure AD](../../active-directory/develop/index.yml).

Usługa Azure AD ma dwie różne wersje punktów końcowych protokołu OAuth 2,0, które są określane jako `v1.0` i `v2.0` . Obie te wersje są punktami końcowymi protokołu OAuth 2,0, a w przypadku `v1.0` i `v2.0` oznaczeń zawarto różnice w sposobie, w jaki usługa Azure AD implementuje ten standard. 

W przypadku korzystania z serwera FHIR można użyć `v1.0` lub `v2.0` punktów końcowych. Wybór może zależeć od bibliotek uwierzytelniania używanych w aplikacji klienckiej.

Odpowiednie sekcje dokumentacji usługi Azure AD:

* `v1.0` punktu końcowego
    * [Przepływ kodu autoryzacji](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Przepływ poświadczeń klienta](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* `v2.0` punktu końcowego
    * [Przepływ kodu autoryzacji](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Przepływ poświadczeń klienta](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Istnieją inne różnice (na przykład w imieniu usługi Flow) umożliwiające uzyskanie tokenu. Szczegółowe informacje znajdują się w dokumentacji usługi Azure AD. W przypadku korzystania z interfejsu API platformy Azure dla FHIR istnieją także pewne skróty umożliwiające uzyskanie tokenu dostępu (na potrzeby debugowania) [przy użyciu interfejsu wiersza polecenia platformy Azure](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono podstawowe koncepcje związane z zabezpieczaniem dostępu do interfejsu API platformy Azure dla usługi FHIR za pomocą usługi Azure AD. Aby dowiedzieć się, jak wdrożyć wystąpienie interfejsu API platformy Azure dla usługi FHIR, przejdź do przewodnika Szybki Start dotyczącego wdrażania.

>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)