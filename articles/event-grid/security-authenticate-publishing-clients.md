---
title: Uwierzytelnianie klientów, którzy publikują zdarzenia w celu Event Grid niestandardowych tematów lub domen
description: W tym artykule opisano różne sposoby uwierzytelniania klientów, którzy publikują zdarzenia w celu Event Grid tematów niestandardowych.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94886264"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Uwierzytelnianie klientów publikowania (Azure Event Grid)
Ten artykuł zawiera informacje dotyczące uwierzytelniania klientów, którzy publikują zdarzenia w celu Azure Event Grid tematów lub domen przy użyciu **klucza dostępu** lub tokenu **sygnatury dostępu współdzielonego (SAS)** . Zalecamy używanie tokenu SAS, ale uwierzytelnianie klucza zapewnia proste programowanie i jest zgodne z wieloma istniejącymi wydawcami elementów webhook.  

## <a name="authenticate-using-an-access-key"></a>Uwierzytelnianie przy użyciu klucza dostępu
Uwierzytelnianie klucza dostępu jest najprostszą formą uwierzytelniania. Klucz dostępu można przekazać jako nagłówek HTTP lub parametr zapytania adresu URL. 

### <a name="access-key-in-a-http-header"></a>Klucz dostępu w nagłówku HTTP
Przekaż klucz dostępu jako wartość dla nagłówka HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Klucz dostępu jako parametr zapytania
Można również określić `aeg-sas-key` jako parametr zapytania. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Aby uzyskać instrukcje dotyczące pobierania kluczy dostępu dla tematu lub domeny, zobacz [pobieranie kluczy dostępu](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Uwierzytelnianie przy użyciu tokenu SAS
Tokeny sygnatury dostępu współdzielonego dla zasobu Event Grid obejmują zasób, czas wygaśnięcia i sygnaturę. Format tokenu sygnatury dostępu współdzielonego to: `r={resource}&e={expiration}&s={signature}` .

Zasób jest ścieżką do tematu usługi Event Grid, do którego są wysyłane zdarzenia. Na przykład prawidłowa ścieżka zasobu to: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Aby wyświetlić wszystkie obsługiwane wersje interfejsu API, zobacz [typy zasobów Microsoft. EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Najpierw program programowo generuje token sygnatury dostępu współdzielonego, a następnie używa `aeg-sas-token` nagłówka lub `Authorization SharedAccessSignature` nagłówka do uwierzytelniania za pomocą Event Grid. 

### <a name="generate-sas-token-programmatically"></a>Programowe generowanie tokenu SAS
Poniższy przykład tworzy token sygnatury dostępu współdzielonego do użycia z Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Korzystanie z nagłówka AEG-SAS-token
Oto przykład przekazywania tokenu sygnatury dostępu współdzielonego jako wartości `aeg-sas-token` nagłówka. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Korzystanie z nagłówka autoryzacji
Oto przykład przekazywania tokenu sygnatury dostępu współdzielonego jako wartości `Authorization` nagłówka. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Następne kroki
Zobacz temat [uwierzytelnianie dostarczania zdarzeń](security-authentication.md) , aby dowiedzieć się więcej o uwierzytelnianiu za pomocą programów obsługi zdarzeń w celu dostarczenia zdarzeń. 
