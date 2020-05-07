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
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a35f70251622674205a28af9b7cc64132d0530
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690285"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Poświadczenia certyfikatu uwierzytelniania aplikacji platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft umożliwia aplikacji używanie własnych poświadczeń na potrzeby uwierzytelniania, na przykład w przypadku [poświadczeń klienta OAuth 2,0 Udziel flowv 2.0](v2-oauth2-client-creds-grant-flow.md) i [przepływu w imieniu użytkownika](v2-oauth2-on-behalf-of-flow.md)).

Jedną z poświadczeń, których może używać aplikacja do uwierzytelniania, jest potwierdzenie tokenu sieci Web JSON (JWT) podpisane przy użyciu certyfikatu, którego właścicielem jest aplikacja.

## <a name="assertion-format"></a>Format potwierdzenia
Platforma tożsamości firmy Microsoft w celu obliczenia potwierdzenia można użyć jednej z wielu bibliotek [tokenów sieci Web JSON](https://jwt.ms/) w wybranym języku. Informacje przekazane przez token są następujące:

### <a name="header"></a>Nagłówek

| Parametr |  Dyskusji |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Powinien być odciskiem palca SHA-1 certyfikatu X. 509 |

### <a name="claims-payload"></a>Oświadczenia (ładunek)

| Parametr |  Uwagi |
| --- | --- |
| `aud` | Odbiorcy: powinien być ** https://login.microsoftonline.com/ *tenant_Id*/OAuth2/token** |
| `exp` | Data wygaśnięcia: Data wygaśnięcia tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0: 0Z) UTC do momentu wygaśnięcia ważności tokenu.|
| `iss` | Wystawca: powinien być client_id (Identyfikator aplikacji usługi klienta). |
| `jti` | GUID: Identyfikator JWT |
| `nbf` | Nie przed: Data, przed upływem którego nie można użyć tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0: 0Z) UTC do momentu wystawienia tokenu. |
| `sub` | Podmiot: jako dla `iss`, powinien być CLIENT_ID (Identyfikator aplikacji usługi klienta) |

### <a name="signature"></a>Podpis

Sygnatura jest obliczana przy zastosowaniu certyfikatu zgodnie z opisem w [specyfikacji RFC7519 tokenu internetowego JSON](https://tools.ietf.org/html/rfc7519)

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

Następujący ciąg jest przykładem zaszyfrowanego potwierdzenia. Jeśli zauważysz uważnie, zobaczysz trzy sekcje oddzielone kropkami (.):
* Pierwsza sekcja koduje nagłówek
* Druga sekcja koduje ładunek
* Ostatnia sekcja jest sygnaturą obliczaną przy użyciu certyfikatów z zawartości pierwszych dwóch sekcji

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

Mając certyfikat, należy obliczyć:

- `$base64Thumbprint`, czyli kodowanie Base64 skrótu certyfikatu
- `$base64Value`, czyli kodowanie Base64 danych pierwotnych certyfikatu

Należy również podać identyfikator GUID, aby zidentyfikować klucz w manifeście aplikacji (`$keyId`).

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

   `keyCredentials` Właściwość jest wielowartościowa, więc można przekazać wiele certyfikatów do rozbudowanego zarządzania kluczami.

## <a name="code-sample"></a>Przykład kodu

> [!NOTE]
> Należy obliczyć nagłówek X5T, konwertując go na podstawowy ciąg 64 przy użyciu skrótu certyfikatu. Kod, który ma być wykonywany w języku `System.Convert.ToBase64String(cert.GetCertHash());`C#, to.

Przykład kodu [Aplikacja konsolowa demona .NET Core przy użyciu platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) pokazuje, jak aplikacja korzysta z własnych poświadczeń do uwierzytelniania. Przedstawiono w `New-SelfSignedCertificate` nim również, jak [utworzyć certyfikat z](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) podpisem własnym za pomocą polecenia programu PowerShell. Możesz również wykorzystać [Skrypty tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) do tworzenia certyfikatów, obliczania odcisku palca i tak dalej.
