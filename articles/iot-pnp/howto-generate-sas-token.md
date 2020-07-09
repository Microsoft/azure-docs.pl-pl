---
title: Generuj token zabezpieczający, aby uzyskać dostęp do repozytorium IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Generuj token sygnatury dostępu współdzielonego, który będzie używany podczas programistycznego uzyskiwania dostępu do repozytorium modeli Plug and Play IoT.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159204"
---
# <a name="generate-sas-token"></a>Generuj token SAS

W tym przewodniku opisano sposób programowego generowania tokenu sygnatury dostępu współdzielonego (SAS) do użycia z interfejsami API repozytorium modelu IoT Plug and Play w wersji zapoznawczej.

## <a name="python"></a>Python

Poniższy fragment kodu przedstawia sposób generowania tokenu sygnatury dostępu współdzielonego przy użyciu języka Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Poniższy fragment kodu przedstawia sposób generowania tokenu sygnatury dostępu współdzielonego przy użyciu języka C \# :

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Używanie tokenu sygnatury dostępu współdzielonego

Po wygenerowaniu tokenu sygnatury dostępu współdzielonego można użyć go do utworzenia żądania HTTP POST. Na przykład:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

W przypadku przyznania Klientowi tokenu SAS klient nie ma klucza podstawowego zasobu i nie może wycofać skrótu w celu uzyskania go. Token sygnatury dostępu współdzielonego zapewnia kontrolę nad tym, co klient może uzyskać dostęp, oraz o ile długo. Zmiana klucza podstawowego w zasadach powoduje unieważnienie wszystkich tokenów sygnatury dostępu współdzielonego utworzonych na podstawie tego poziomu.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak generować tokeny zabezpieczające, które mają być używane w celu uzyskania dostępu do modelu repozytoria modeli IoT Plug and Play w wersji zapoznawczej, sugerowanym następnym krokiem jest zapoznawanie się z [przewodnikiem dewelopera dotyczącym programu iot Plug and Play Preview](concepts-developer-guide.md).
