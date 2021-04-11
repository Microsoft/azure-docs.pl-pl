---
title: Potwierdzenia klienta (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje na temat obsługi potwierdzeń klientów podpisanych w przypadku poufnych aplikacji klienckich w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 000aeffa982c59f1efbb6ecae73f6b48e95f981e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967323"
---
# <a name="confidential-client-assertions"></a>Poufne potwierdzenia klienta

Aby udowodnić swoją tożsamość, poufne aplikacje klienckie wymieniają klucz tajny z usługą Azure AD. Wpis tajny może być:
- Wpis tajny klienta (hasło aplikacji).
- Certyfikat, który służy do tworzenia podpisanego potwierdzenia zawierającego oświadczenia standardowe.

Ten klucz tajny może również być podpisanym potwierdzeniem bezpośrednio.

MSAL.NET ma cztery metody dostarczania poświadczeń lub potwierdzeń do poufnej aplikacji klienckiej:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Chociaż można używać `WithClientAssertion()` interfejsu API do uzyskiwania tokenów dla klienta poufnego, nie zaleca się używania go domyślnie, ponieważ jest bardziej zaawansowany i został zaprojektowany z myślą o obsłudze bardzo konkretnych scenariuszy, które nie są wspólne. Użycie `.WithCertificate()` interfejsu API umożliwi MSAL.NETom obsługę tego elementu. Ten interfejs API umożliwia dostosowanie żądania uwierzytelniania w razie potrzeby, ale domyślne potwierdzenie utworzone przez `.WithCertificate()` będzie wystarczające dla większości scenariuszy uwierzytelniania. Tego interfejsu API można także użyć jako obejścia w niektórych scenariuszach, w których MSAL.NET nie może wewnętrznie wykonać operacji podpisywania.

### <a name="signed-assertions"></a>Podpisane potwierdzenia

Podpisane potwierdzenie klienta przyjmuje postać podpisanego tokenu JWT z ładunkiem zawierającym wymagane oświadczenia uwierzytelniania przydzielone przez usługę Azure AD, zakodowane w formacie base64. Aby go użyć:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Można również użyć formularza delegata, który umożliwia obliczanie potwierdzenia just in Time:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
```

[Oświadczenia oczekiwane przez usługę Azure AD](active-directory-certificate-credentials.md) w podpisanym potwierdzeniu są następujące:

Typ oświadczenia | Wartość | Opis
---------- | ---------- | ----------
AUD | `https://login.microsoftonline.com/{tenantId}/v2.0` | Deklaracja "AUD" (odbiorcy) identyfikuje odbiorców, dla których jest przeznaczony token JWT (w tym przypadku usługa Azure AD) [, zobacz RFC 7519, sekcja 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  W takim przypadku odbiorcą jest serwer logowania (login.microsoftonline.com).
exp | 1601519414 | Wartość "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po którym nie można zaakceptować tokenu JWT do przetworzenia. Zobacz [dokument RFC 7519, sekcja 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Pozwala to na użycie potwierdzenia do momentu, dlatego należy zachować jego wartość krótko-5-10 min `nbf` .  Usługa Azure AD nie nakłada obecnie ograniczeń na `exp` czas. 
ISS | ClientID | Wniosek "ISS" (wystawca) identyfikuje podmiot zabezpieczeń, który wystawił token JWT, w tym przypadku aplikację kliencką.  Użyj identyfikatora aplikacji identyfikatora GUID.
jti | (identyfikator GUID) | Wartość "JTI" (identyfikator JWT) zapewnia unikatowy identyfikator dla tokenu JWT. Wartość identyfikatora musi być przypisana w sposób, który gwarantuje, że istnieje niewielkie prawdopodobieństwo, że ta sama wartość zostanie przypadkowo przypisana do innego obiektu danych; Jeśli aplikacja używa wielu wystawców, kolizje muszą być blokowane między wartościami wyprodukowanymi przez różne wystawcy. Wartość "JTI" jest ciągiem z uwzględnieniem wielkości liter. [RFC 7519, sekcja 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Wartość "NBF" (nie wcześniej) określa czas, po którym nie można zatwierdzić tokenu JWT do przetwarzania. [RFC 7519, sekcja 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Korzystanie z bieżącego czasu jest odpowiednie. 
Sub | ClientID | Element "Sub" (podmiot) identyfikuje podmiot JWT, w tym przypadku również aplikację. Użyj tej samej wartości co `iss` . 

Oto przykład sposobu przedstawiania tych oświadczeń:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Poniżej przedstawiono sposób przedstawiania podpisanego potwierdzenia klienta:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims()).ToString()));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Metoda alternatywna

Dostępna jest również opcja użycia [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) , aby utworzyć potwierdzenie. Kod będzie bardziej elegancki, jak pokazano w poniższym przykładzie:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Po potwierdzeniu podpisanego klienta można użyć go z interfejsami API MSAL, jak pokazano poniżej.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` Domyślnie program będzie generował podpisane potwierdzenie zawierające oświadczenia oczekiwane przez usługę Azure AD i dodatkowe oświadczenia klienta, które mają zostać wysłane. Oto fragment kodu dotyczący tego, jak to zrobić.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Jeśli jedno z oświadczeń w słowniku, w którym się znajdujesz, jest takie samo jak oświadczenie obowiązkowe, zostanie uwzględniona dodatkowa wartość oświadczenia. Spowoduje to zastąpienie oświadczeń obliczanych przez MSAL.NET.

Jeśli chcesz podać własne oświadczenia, w tym obowiązkowe oświadczenia oczekiwane przez usługę Azure AD, Przekaż `false` do `mergeWithDefaultClaims` parametru.
