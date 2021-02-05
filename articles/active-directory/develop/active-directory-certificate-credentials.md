---
title: Poświadczenia certyfikatu platformy tożsamości firmy Microsoft
titleSuffix: Microsoft identity platform
description: W tym artykule omówiono rejestrację i użycie poświadczeń certyfikatu na potrzeby uwierzytelniania aplikacji.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582030"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Poświadczenia certyfikatu uwierzytelniania aplikacji platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft umożliwia aplikacji używanie własnych poświadczeń do uwierzytelniania w dowolnym miejscu, w którym można użyć klucza tajnego klienta, na przykład w przepływie  [przydzielenia poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 i przepływie [w imieniu](v2-oauth2-on-behalf-of-flow.md) (OBO).

Jedną z poświadczeń, których może używać aplikacja do uwierzytelniania, jest potwierdzenie [tokenu sieci Web JSON](./security-tokens.md#json-web-tokens-and-claims) (JWT) podpisane przy użyciu certyfikatu, którego właścicielem jest aplikacja.

## <a name="assertion-format"></a>Format potwierdzenia

Aby obliczyć potwierdzenie, można użyć jednej z wielu bibliotek JWT w wybranym języku — [MSAL obsługuje tę `.WithCertificate()` funkcję ](msal-net-client-assertions.md). Informacje są przewożone przez token w jego [nagłówku](#header), [oświadczeniach](#claims-payload)i [podpisie](#signature).

### <a name="header"></a>Nagłówek

| Parametr |  Dyskusji |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Wartość skrótu certyfikatu X. 509 (określana także jako *odcisk palca* SHA-1 certyfikatu) zakodowana w postaci wartości ciągu Base64url. Na przykład, przy użyciu skrótu certyfikatu X. 509 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (szesnastkowo), wartość `x5t` żądania to `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Oświadczenia (ładunek)

Typ oświadczenia | Wartość | Opis
---------- | ---------- | ----------
AUD | `https://login.microsoftonline.com/{tenantId}/v2.0` | Deklaracja "AUD" (odbiorcy) identyfikuje odbiorców, dla których jest przeznaczony token JWT (w tym przypadku usługa Azure AD) [, zobacz RFC 7519, sekcja 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  W takim przypadku odbiorcą jest serwer logowania (login.microsoftonline.com).
exp | 1601519414 | Wartość "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po którym nie można zaakceptować tokenu JWT do przetworzenia. Zobacz [dokument RFC 7519, sekcja 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Pozwala to na użycie potwierdzenia do momentu, dlatego należy zachować jego wartość krótko-5-10 min `nbf` .  Usługa Azure AD nie nakłada obecnie ograniczeń na `exp` czas. 
ISS | ClientID | Wniosek "ISS" (wystawca) identyfikuje podmiot zabezpieczeń, który wystawił token JWT, w tym przypadku aplikację kliencką.  Użyj identyfikatora aplikacji identyfikatora GUID.
jti | (identyfikator GUID) | Wartość "JTI" (identyfikator JWT) zapewnia unikatowy identyfikator dla tokenu JWT. Wartość identyfikatora musi być przypisana w sposób, który gwarantuje, że istnieje niewielkie prawdopodobieństwo, że ta sama wartość zostanie przypadkowo przypisana do innego obiektu danych; Jeśli aplikacja używa wielu wystawców, kolizje muszą być blokowane między wartościami wyprodukowanymi przez różne wystawcy. Wartość "JTI" jest ciągiem z uwzględnieniem wielkości liter. [RFC 7519, sekcja 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Wartość "NBF" (nie wcześniej) określa czas, po którym nie można zatwierdzić tokenu JWT do przetwarzania. [RFC 7519, sekcja 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Korzystanie z bieżącego czasu jest odpowiednie. 
Sub | ClientID | Element "Sub" (podmiot) identyfikuje podmiot JWT, w tym przypadku również aplikację. Użyj tej samej wartości co `iss` . 

### <a name="signature"></a>Podpis

Podpis jest obliczany przez zastosowanie certyfikatu zgodnie z opisem w [specyfikacji RFC7519 tokenu internetowego JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Przykład dekodowanego potwierdzenia JWT

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Przykład zaszyfrowanego potwierdzenia JWT

Następujący ciąg jest przykładem zaszyfrowanego potwierdzenia. Jeśli zauważysz uważnie, zobaczysz trzy sekcje oddzielone kropkami ( `.` ):

* Pierwsza sekcja koduje *nagłówek*
* Druga sekcja koduje *oświadczenia* (ładunek)
* Ostatnia sekcja jest *sygnaturą* obliczaną przy użyciu certyfikatów z zawartości pierwszych dwóch sekcji

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Rejestrowanie certyfikatu przy użyciu platformy tożsamości firmy Microsoft

Poświadczenie certyfikatu można skojarzyć z aplikacją kliencką na platformie tożsamości firmy Microsoft za pośrednictwem Azure Portal przy użyciu dowolnej z następujących metod:

### <a name="uploading-the-certificate-file"></a>Przekazywanie pliku certyfikatu

W usłudze Azure App Registration dla aplikacji klienckiej:
1. Wybierz pozycję **certyfikaty & wpisy tajne**.
2. Kliknij pozycję **Przekaż certyfikat** i wybierz plik certyfikatu do przekazania.
3. Kliknij pozycję **Dodaj**.
  Po przekazaniu certyfikatu zostaną wyświetlone wartości odcisku palca, Data rozpoczęcia i wygaśnięcie.

### <a name="updating-the-application-manifest"></a>Aktualizowanie manifestu aplikacji

Po uzyskaniu certyfikatu Oblicz następujące wartości:

- `$base64Thumbprint` — Zakodowana algorytmem Base64 wartość skrótu certyfikatu
- `$base64Value` — Zakodowana algorytmem Base64 wartość danych pierwotnych certyfikatu

Podaj identyfikator GUID, aby zidentyfikować klucz w manifeście aplikacji ( `$keyId` ).

W usłudze Azure App Registration dla aplikacji klienckiej:
1. Wybierz pozycję **manifest** , aby otworzyć manifest aplikacji.
2. Zastąp Właściwość *poświadczeniami* , podając nowe informacje o certyfikacie, korzystając z poniższego schematu.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Zapisz zmiany w manifeście aplikacji, a następnie Przekaż manifest do platformy tożsamości firmy Microsoft.

   `keyCredentials`Właściwość jest wielowartościowa, więc można przekazać wiele certyfikatów do rozbudowanego zarządzania kluczami.
   
## <a name="using-a-client-assertion"></a>Korzystanie z potwierdzenia klienta

Potwierdzeń klientów można używać wszędzie tam, gdzie zostanie użyty klucz tajny klienta.  Na przykład w [przepływie kodu autoryzacji](v2-oauth2-auth-code-flow.md)można przekazać w `client_secret` celu potwierdzenia, że żądanie pochodzi z aplikacji. Można je zastąpić za pomocą `client_assertion` `client_assertion_type` parametrów i. 

| Parametr | Wartość | Opis|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Jest to stała wartość, wskazująca, że używane jest poświadczenie certyfikatu. |
|`client_assertion`| JWT |Jest to token JWT utworzony powyżej. |

## <a name="next-steps"></a>Następne kroki

[Biblioteka MSAL.NET obsługuje ten scenariusz](msal-net-client-assertions.md) w jednym wierszu kodu.

[Aplikacja konsolowa demona .NET Core używająca przykładu kodu platformy Microsoft Identity](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) w witrynie GitHub pokazuje, w jaki sposób aplikacja korzysta z własnych poświadczeń do uwierzytelniania. Przedstawiono w nim również, jak [utworzyć certyfikat z](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) podpisem własnym za pomocą `New-SelfSignedCertificate` polecenia cmdlet programu PowerShell. Możesz również użyć [skryptów tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) w przykładowym repozytorium do tworzenia certyfikatów, obliczania odcisku palca i tak dalej.
