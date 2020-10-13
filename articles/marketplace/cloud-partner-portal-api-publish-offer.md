---
title: Publikowanie oferty — Azure Marketplace
description: Interfejs API umożliwiający opublikowanie określonej oferty.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272049"
---
# <a name="publish-an-offer"></a>Publikowanie oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Uruchamia proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identyfikator wydawcy, na przykład `contoso`      |   Ciąg       |
|  offerId       | Identyfikator oferty                                 |   Ciąg       |
|  api-version   | Najnowsza wersja interfejsu API                        |   Date         |
|  |  |

## <a name="header"></a>Header
------

|  **Nazwa**        |    **Wartość**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Żądanie

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Właściwości treści żądania

|  **Nazwa**               |   **Opis**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  powiadomienie — wiadomości e-mail    | Rozdzielana przecinkami lista adresów e-mail, które mają być powiadamiane o postępie operacji publikowania. |
|  |  |

### <a name="response"></a>Reakcja

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niemigrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartość**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Lokalizacja    | Ścieżka względna do pobrania stanu tej operacji     |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację, która może służyć do śledzenia uruchomionej operacji. |
| 400   | `Bad/Malformed request` -Treść odpowiedzi na błąd może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity` -Wskazuje, że nie można sprawdzić poprawności jednostki do opublikowania.                                                        |
| 404   | `Not found` -Jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
