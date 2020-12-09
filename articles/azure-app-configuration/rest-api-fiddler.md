---
title: Azure Active Directory interfejsu API REST — testowanie przy użyciu programu Fiddler
description: Korzystanie z programu Fiddler do testowania interfejsu API REST konfiguracji aplikacji platformy Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932612"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testowanie interfejsu API REST usługi Azure App Configuration przy użyciu programu Fiddler

Aby przetestować interfejs API REST przy użyciu [programu Fiddler](https://www.telerik.com/fiddler), należy uwzględnić nagłówki HTTP wymagane do [uwierzytelniania](./rest-api-authentication-hmac.md) w żądaniach. Poniżej przedstawiono sposób konfigurowania programu Fiddler na potrzeby testowania interfejsu API REST, które automatycznie generują nagłówki uwierzytelniania:

1. Upewnij się, że protokół TLS 1,2 jest dozwolonym protokołem:
    1. Przejdź do pozycji **Narzędzia**  >  **Opcje**  >  **https**.
    1. Upewnij się, że zaznaczono opcję **Odszyfruj ruch https** .
    1. Na liście protokołów Dodaj **protokół TLS 1.2** , jeśli nie istnieje.
1. Otwórz **Edytor skryptów programu Fiddler** lub naciśnij **klawisze CTRL-R** w programu Fiddler
1. Dodaj następujący kod wewnątrz `Handlers` klasy przed `OnBeforeRequest` funkcją

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Dodaj następujący kod na końcu `OnBeforeRequest` funkcji. Zaktualizuj klucz dostępu zgodnie ze wskazaniem komentarza do wykonania.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Użyj [kompozytora programu Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) , aby wygenerować i wysłać żądanie
