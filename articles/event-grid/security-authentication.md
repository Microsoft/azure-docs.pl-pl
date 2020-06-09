---
title: Azure Event Grid zabezpieczenia i uwierzytelnianie
description: W tym artykule opisano różne sposoby uwierzytelniania dostępu do zasobów Event Grid (webhook, subskrypcje, tematy niestandardowe)
services: event-grid
author: femila
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: femila
ms.openlocfilehash: 8335d5a41dc2f322623c163e08f8a4a2c1be8360
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558994"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Uwierzytelnianie dostępu do zasobów Azure Event Grid
Ten artykuł zawiera informacje dotyczące następujących scenariuszy:  

- Uwierzytelnianie klientów, którzy publikują zdarzenia do Azure Event Grid tematów przy użyciu sygnatury dostępu współdzielonego (SAS) lub klucza. 
- Zabezpiecz punkt końcowy elementu webhook, który jest używany do odbierania zdarzeń z Event Grid przy użyciu Azure Active Directory (Azure AD) lub wspólnego klucza tajnego.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Uwierzytelnianie klientów publikowania przy użyciu sygnatury dostępu współdzielonego lub klucza
Tematy niestandardowe wykorzystują sygnaturę dostępu współdzielonego (SAS) lub uwierzytelnianie klucza. Zalecamy używanie sygnatury dostępu współdzielonego, ale uwierzytelnianie klucza zapewnia proste programowanie i jest zgodne z wieloma istniejącymi wydawcami elementów webhook.

Wartość uwierzytelniania należy uwzględnić w nagłówku HTTP. Dla sygnatury dostępu współdzielonego Użyj **AEG-SAS-token** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą klucza Użyj wartości nagłówka **AEG-SAS-Key** .

### <a name="key-authentication"></a>Uwierzytelnianie klucza

Uwierzytelnianie klucza jest najprostszą formą uwierzytelniania. Użyj formatu: `aeg-sas-key: <your key>` w nagłówku komunikatu.

Na przykład przekazujesz klucz z:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Można również określić `aeg-sas-key` jako parametr zapytania. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatury dostępu współdzielonego dla Event Grid obejmują zasób, czas wygaśnięcia i sygnaturę. Format tokenu sygnatury dostępu współdzielonego to: `r={resource}&e={expiration}&s={signature}` .

Zasób jest ścieżką do tematu usługi Event Grid, do którego są wysyłane zdarzenia. Na przykład prawidłowa ścieżka zasobu to: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Aby wyświetlić wszystkie obsługiwane wersje interfejsu API, zobacz [typy zasobów Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Sygnatura jest generowana z klucza.

Na przykład prawidłowa wartość **AEG-SAS-token** to:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

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

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Wszystkie zdarzenia lub dane zapisywane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, dzięki czemu są szyfrowane w stanie spoczynku. Ponadto maksymalny okres czasu, przez jaki zdarzenia lub dane są przechowywane, wynosi 24 godziny zgodnie z [zasadami ponowienia Event Grid](delivery-and-retry.md). Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas wygaśnięcia zdarzenia na żywo, w zależności od tego, która wartość jest mniejsza.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Uwierzytelnianie dostarczania zdarzeń do punktów końcowych elementu webhook
W poniższych sekcjach opisano sposób uwierzytelniania dostarczania zdarzeń do punktów końcowych elementu webhook. Musisz użyć mechanizmu uzgadniania poprawności, niezależnie od używanej metody. Aby uzyskać szczegółowe informacje, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md) . 

### <a name="using-azure-active-directory-azure-ad"></a>Korzystanie z Azure Active Directory (Azure AD)
Możesz zabezpieczyć punkt końcowy elementu webhook, który jest używany do odbierania zdarzeń z Event Grid przy użyciu usługi Azure AD. Musisz utworzyć aplikację usługi Azure AD, utworzyć rolę i nazwę główną usługi w aplikacji, która autoryzuje Event Grid, i skonfiguruje subskrypcję zdarzeń do korzystania z aplikacji usługi Azure AD. Dowiedz się, jak [skonfigurować Azure Active Directory przy użyciu Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Używanie klucza tajnego klienta jako parametru zapytania
Punkt końcowy elementu webhook można także zabezpieczyć przez dodanie parametrów zapytania do docelowego adresu URL elementu webhook określonego w ramach tworzenia subskrypcji zdarzeń. Ustaw jeden z parametrów zapytania jako klucz tajny klienta, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token) lub wspólny klucz tajny. Usługa Event Grid obejmuje wszystkie parametry zapytania w każdym żądaniu dostarczania zdarzeń do elementu webhook. Usługa elementu webhook może pobrać klucz tajny i sprawdzić jego poprawność. Jeśli klucz tajny klienta zostanie zaktualizowany, należy również zaktualizować subskrypcję zdarzeń. Aby uniknąć niepowodzeń dostarczania w ramach tego samego klucza tajnego, przed zaktualizowaniem subskrypcji zdarzeń przy użyciu nowego klucza tajnego upewnij się, że element webhook zaakceptuje zarówno stare, jak i nowe wpisy tajne. 

Ponieważ parametry zapytania mogą zawierać wpisy tajne klienta, są one obsługiwane z dodatkowymi zapytaniami. Są one przechowywane jako zaszyfrowane i nie są dostępne dla operatorów usług. Nie są one rejestrowane w ramach dzienników usługi/śladów. Podczas pobierania właściwości subskrypcji zdarzeń, parametry zapytania docelowego nie są domyślnie zwracane. Na przykład: parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) ma być używany w [interfejsie wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)platformy Azure.

Aby uzyskać więcej informacji na temat dostarczania zdarzeń do elementów webhook, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid obsługuje tylko punkty końcowe elementu webhook **protokołu HTTPS** . 

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [Informacje o Event Grid](overview.md)
