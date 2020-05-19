---
title: Uzyskiwanie dostępu do informacji o użytkowniku w usłudze Azure static Web Apps
description: Informacje dotyczące odczytywania dostawcy autoryzacji — zwrócone dane użytkownika.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597751"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Uzyskiwanie dostępu do informacji o użytkowniku w usłudze Azure static Web Apps Preview

Usługa Azure static Web Apps zapewnia informacje o użytkownikach związanych z uwierzytelnianiem za pośrednictwem [punktu końcowego dostępu bezpośredniego](#direct-access-endpoint) oraz do [funkcji interfejsu API](#api-functions).

Wiele interfejsów użytkownika opiera się na danych uwierzytelniania użytkownika. Punkt końcowy bezpośredniego dostępu jest interfejsem API narzędzi, który udostępnia informacje o użytkowniku bez konieczności implementowania funkcji niestandardowej. Poza wygodą punkt końcowy dostępu bezpośredniego nie podlega opóźnieniu na zimne uruchomienie, które są skojarzone z architekturą bezserwerową.

## <a name="client-principal-data"></a>Dane główne klienta

Obiekt danych głównych klienta uwidacznia informacje o tożsamości użytkownika w aplikacji. Następujące właściwości są proponowane w obiekcie głównym klienta:

| Właściwość  | Opis |
|-----------|---------|
| `identityProvider` | Nazwa [dostawcy tożsamości](authentication-authorization.md). |
| `userId` | Unikatowy identyfikator dla użytkownika na potrzeby statycznego Web Apps platformy Azure. <ul><li>Wartość jest unikatowa dla poszczególnych aplikacji. Na przykład ten sam użytkownik zwraca inną `userId` wartość w innym statycznym zasobie Web Apps.<li>Wartość będzie nadal obowiązywać przez okres istnienia użytkownika. Jeśli usuniesz i dodasz tego samego użytkownika z powrotem do aplikacji, `userId` zostanie wygenerowany nowy.</ul>|
| `userDetails` | Nazwa_użytkownika lub adres e-mail użytkownika. Niektórzy dostawcy zwracają [adres e-mail użytkownika](authentication-authorization.md), podczas gdy inne wysyłają [obsługę użytkownika](authentication-authorization.md). |
| `userRoles`     | Tablica [przypisanych ról użytkownika](authentication-authorization.md). |

Poniższy przykład jest przykładowym obiektem głównym klienta:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Punkt końcowy dostępu bezpośredniego

Można wysłać `GET` żądanie do `/.auth/me` trasy i uzyskać bezpośredni dostęp do danych głównych klienta. Gdy stan Twojego widoku opiera się na danych autoryzacji, Użyj tej metody w celu uzyskania najlepszej wydajności.

W przypadku zalogowanych użytkowników odpowiedź zawiera główny obiekt JSON klienta. Żądania od nieuwierzytelnionych użytkowników zwracają wartość `null` .

Korzystając z interfejsu API [pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> , można uzyskać dostęp do danych głównych klienta przy użyciu następującej składni.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funkcje interfejsu API

Dane podmiotu zabezpieczeń są przesyłane do funkcji interfejsu API w `x-ms-client-principal` nagłówku żądania. Dane podmiotu zabezpieczeń klienta są wysyłane jako ciąg szyfrowany [algorytmem Base64](https://www.wikipedia.org/wiki/Base64)zawierający serializowany obiekt JSON.

W poniższej przykładowej funkcji pokazano, jak odczytywać i zwracać informacje o użytkowniku.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Przy założeniu, że powyższa funkcja ma nazwę `user` , można użyć interfejsu API przeglądarki [pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> , aby uzyskać dostęp do odpowiedzi interfejsu API przy użyciu następującej składni.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> operator [Fetch](https://caniuse.com/#feat=fetch) API i [await](https://caniuse.com/#feat=mdn-javascript_operators_await) nie jest obsługiwany w programie Internet Explorer.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień aplikacji](application-settings.md)
