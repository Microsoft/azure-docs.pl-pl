---
title: Anuluj interfejs API operacji | Portal Azure Marketplace
description: Anuluj operacje.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256437"
---
# <a name="cancel-operation"></a>Anuluj operację

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Ten interfejs API anuluje aktualnie wykonywaną operację na ofercie. Użyj [interfejsu API pobierania operacji](./cloud-partner-portal-api-retrieve-operations.md) , aby uzyskać `operationId` dostęp do tego interfejsu API. Anulowanie jest zazwyczaj operacją synchroniczną, ale w niektórych złożonych scenariuszach może być wymagana Nowa operacja, aby anulować istniejącą operację. W takim przypadku treść odpowiedzi HTTP zawiera lokalizację operacji, która powinna być używana do wykonywania zapytań o stan.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

--------------

|  **Nazwa**    |      **Opis**                                  |    **Typ danych**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identyfikator wydawcy, na przykład`contoso`         |   String          |
| offerId      |  Identyfikator oferty                                     |   String          |
| api-version  |  Bieżąca wersja interfejsu API                               |    Date           |
|  |  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**              |  **Wartościami**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autoryzacja         |  Okaziciela — TOKEN |
|  |  |

## <a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Request

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

### <a name="response"></a>Odpowiedź

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niemigrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartościami**                       |
|  ---------            |    ----------                      |
| Lokalizacja    | Ścieżka względna do pobrania stanu tej operacji. |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code**  |  **Opis**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. Żądanie zostało pomyślnie przetworzone i operacja została anulowana synchronicznie. |
|  202      | Przyjmować. Żądanie zostało pomyślnie przetworzone i trwa proces anulowania operacji. Lokalizacja operacji anulowania jest zwracana w nagłówku odpowiedzi. |
|  400      | Nieprawidłowe lub źle sformułowane żądanie. Treść odpowiedzi na błąd może dostarczyć więcej informacji.  |
|  403      | Dostęp zabroniony. Klient nie ma dostępu do przestrzeni nazw określonej w żądaniu. |
|  404      | Nie znaleziono. Określona jednostka nie istnieje. |
|  |  |
