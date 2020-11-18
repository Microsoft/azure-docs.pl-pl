---
title: Uzyskiwanie dostępu do informacji o użytkowniku w usłudze Azure static Web Apps
description: Informacje dotyczące odczytywania dostawcy autoryzacji — zwrócone dane użytkownika.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: d5a1d810c357aa83b8069023b00d76352da124df
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844799"
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

Funkcje interfejsu API dostępne w Web Apps statycznych za pośrednictwem Azure Functions zaplecza mają dostęp do tych samych informacji o użytkowniku, co aplikacja kliencka. Gdy interfejs API otrzymuje informacje identyfikowane przez użytkownika, nie wykonuje jego własnej kontroli, jeśli użytkownik jest uwierzytelniony lub jeśli jest zgodny z wymaganą rolą. Reguły kontroli dostępu są zdefiniowane w [`routes.json`](routes.md) pliku.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

W funkcji języka C# informacje o użytkowniku są dostępne z `x-ms-client-principal` nagłówka, który można deserializować do `ClaimsPrincipal` obiektu lub własnego typu niestandardowego. Poniższy kod ilustruje sposób rozpakowania nagłówka do typu pośredniego, `ClientPrincipal` który następnie jest włączony do `ClaimsPrincipal` wystąpienia.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> operator [Fetch](https://caniuse.com/#feat=fetch) API i [await](https://caniuse.com/#feat=mdn-javascript_operators_await) nie jest obsługiwany w programie Internet Explorer.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień aplikacji](application-settings.md)
