---
title: Publikowanie oferty | Portal Azure Marketplace
description: Interfejs API umożliwiający opublikowanie określonej oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255944"
---
# <a name="publish-an-offer"></a>Publikowanie oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Uruchamia proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identyfikator wydawcy, na przykład`contoso`      |   String       |
|  offerId       | Identyfikator oferty                                 |   String       |
|  api-version   | Najnowsza wersja interfejsu API                        |   Date         |
|  |  |

## <a name="header"></a>Nagłówek
------

|  **Nazwa**        |    **Wartościami**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autoryzacja   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Przykład treści
------------

### <a name="request"></a>Request

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


### <a name="response"></a>Odpowiedź

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niemigrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |    **Wartościami**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Lokalizacja    | Ścieżka względna do pobrania stanu tej operacji     |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |  **Opis**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-Żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację, która może służyć do śledzenia uruchomionej operacji. |
| 400   | `Bad/Malformed request`-Treść odpowiedzi na błąd może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity`-Wskazuje, że nie można sprawdzić poprawności jednostki do opublikowania.                                                        |
| 404   | `Not found`-Jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
