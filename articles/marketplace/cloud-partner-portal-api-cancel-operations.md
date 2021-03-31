---
title: Interfejs API operacji anulowania — Microsoft Commercial Marketplace
description: Interfejs API służący do anulowania operacji obecnie w toku dla oferty
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287918"
---
# <a name="cancel-operation"></a>Anuluj operację

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Ten interfejs API anuluje aktualnie wykonywaną operację na ofercie. Użyj [interfejsu API pobierania operacji](./cloud-partner-portal-api-retrieve-operations.md) , aby uzyskać dostęp do `operationId` tego interfejsu API. Anulowanie jest zazwyczaj operacją synchroniczną, ale w niektórych złożonych scenariuszach może być wymagana Nowa operacja, aby anulować istniejącą operację. W takim przypadku treść odpowiedzi HTTP zawiera lokalizację operacji, która powinna być używana do wykonywania zapytań o stan.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identyfikator wydawcy, na przykład `contoso`         |   Ciąg          |
| offerId      |  Identyfikator oferty                                     |   Ciąg          |
| api-version  |  Bieżąca wersja interfejsu API                               |    Date (Data)           |
|  |  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartość**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  Okaziciela — TOKEN |
|  |  |

## <a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Żądanie

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Właściwości treści żądania

|  **Nazwa**                |  **Opis**                                               |
|  --------                |  ---------------                                               |
|  powiadomienie — wiadomości e-mail     | Rozdzielana przecinkami lista identyfikatorów e-mail do powiadamiania o postępie operacji publikowania. |
|  |  |

### <a name="response"></a>Reakcja

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niemigrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                       |
|  ---------            |    ----------                      |
| Lokalizacja    | Ścieżka względna do pobrania stanu tej operacji. |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod**  |  **Opis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Żądanie zostało pomyślnie przetworzone i operacja została anulowana synchronicznie. |
|  202      | Przyjmować. Żądanie zostało pomyślnie przetworzone i trwa proces anulowania operacji. Lokalizacja operacji anulowania jest zwracana w nagłówku odpowiedzi. |
|  400      | Nieprawidłowe lub źle sformułowane żądanie. Treść odpowiedzi na błąd może dostarczyć więcej informacji.  |
|  403      | Dostęp zabroniony. Klient nie ma dostępu do przestrzeni nazw określonej w żądaniu. |
|  404      | Nie znaleziono. Określona jednostka nie istnieje. |
|  |  |
