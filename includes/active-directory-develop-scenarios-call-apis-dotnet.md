---
title: plik dołączania
description: plik dołączania
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76309036"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Właściwości AuthenticationResult w MSAL.NET

Metody uzyskiwania zwrotów tokenów `AuthenticationResult` . W przypadku metod asynchronicznych `Task<AuthenticationResult>` zwraca wartość.

W programie MSAL.NET `AuthenticationResult` udostępnia:

- `AccessToken` Aby uzyskać dostęp do zasobów za pomocą interfejsu API sieci Web. Ten parametr jest ciągiem, zazwyczaj jest zakodowana przy użyciu Base-64 — JWT. Klient nigdy nie powinien szukać w tokenie dostępu. Format nie gwarantuje, że jest stabilny i może być zaszyfrowany dla zasobu. Pisanie kodu, który zależy od zawartości tokenu dostępu na kliencie, jest jednym z największych źródeł błędów i podziałów logiki klienta. Aby uzyskać więcej informacji, zobacz [tokeny dostępu](../articles/active-directory/develop/access-tokens.md).
- `IdToken` dla użytkownika. Ten parametr jest zakodowaną JWT. Aby uzyskać więcej informacji, zobacz [identyfikatory tokenów](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` informuje datę i godzinę wygaśnięcia tokenu.
- `TenantId` zawiera dzierżawcę, w którym znaleziono użytkownika. Dla użytkowników-Gości w scenariuszach usługi Azure Active Directory (Azure AD) B2B identyfikator dzierżawy jest dzierżawcą gościa, a nie z unikatowym dzierżawcą.
Gdy token jest dostarczany dla użytkownika, `AuthenticationResult` zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klientów, w których tokeny są żądane bez użytkownika aplikacji, informacje o użytkowniku mają wartość null.
- `Scopes`Dla którego został wystawiony token.
- Unikatowy identyfikator użytkownika.

### <a name="iaccount"></a>IAccount

MSAL.NET definiuje pojęcie konta za pomocą `IAccount` interfejsu. Ta przerwana zmiana zapewnia właściwą semantykę. Ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Ponadto MSAL.NET zapewnia lepsze informacje w przypadku scenariuszy gościa, ponieważ są udostępniane informacje o koncie macierzystym.
Na poniższym diagramie przedstawiono strukturę `IAccount` interfejsu.

![Struktura interfejsu IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`Klasa identyfikuje konto w określonej dzierżawie z właściwościami podanymi w poniższej tabeli.

| Właściwość | Opis |
|----------|-------------|
| `TenantId` | Ciąg reprezentujący identyfikator GUID, który jest IDENTYFIKATORem dzierżawy, w której znajduje się konto. |
| `ObjectId` | Ciąg reprezentujący identyfikator GUID, który jest IDENTYFIKATORem użytkownika, który jest właścicielem konta w dzierżawie. |
| `Identifier` | Unikatowy identyfikator konta. `Identifier` jest połączeniem `ObjectId` i `TenantId` rozdzielony przecinkami. Nie są one zakodowane w podstawowej 64. |

`IAccount`Interfejs reprezentuje informacje o pojedynczym koncie. Ten sam użytkownik może być obecny w różnych dzierżawach, co oznacza, że użytkownik może mieć wiele kont. Jego członkowie są pokazani w poniższej tabeli.

| Właściwość | Opis |
|----------|-------------|
| `Username` | Ciąg, który zawiera wartości, które można wyświetlić w formacie UserPrincipalName (UPN), na przykład john.doe@contoso.com . Ten ciąg może mieć wartość null, w przeciwieństwie do HomeAccountId i HomeAccountId. Identifier, która nie ma wartości null. Ta właściwość zastępuje `DisplayableId` Właściwość `IUser` w poprzednich wersjach MSAL.NET. |
| `Environment` | Ciąg, który zawiera dostawcę tożsamości dla tego konta, na przykład `login.microsoftonline.com` . Ta właściwość zastępuje `IdentityProvider` Właściwość `IUser` , z tą różnicą, że `IdentityProvider` również zawiera informacje o dzierżawie, oprócz środowiska chmury. W tym miejscu wartość jest tylko hostem. |
| `HomeAccountId` | Identyfikator konta dla konta głównego użytkownika. Ta właściwość jednoznacznie identyfikuje użytkownika w dzierżawach usługi Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Używanie tokenu do wywoływania chronionego interfejsu API

Po `AuthenticationResult` zwróceniu przez MSAL w programie `result` należy dodać go do nagłówka autoryzacji http przed wywołaniem dostępu do chronionego internetowego interfejsu API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```