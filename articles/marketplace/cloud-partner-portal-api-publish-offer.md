---
title: Publikowanie oferty — Azure Marketplace
description: Interfejs API umożliwiający opublikowanie określonej oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ada8725a8963fc14fd326c244dcf3d5bb3dd372f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516244"
---
# <a name="publish-an-offer"></a>Publikowanie oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Uruchamia proces publikowania dla określonej oferty. To wywołanie jest długotrwałą operacją.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |    **Opis**                               |  **Typ danych** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identyfikator wydawcy, na przykład`contoso`      |   String       |
|  offerId       | Identyfikator oferty                                 |   String       |
|  api-version   | Najnowsza wersja interfejsu API                        |   Data         |
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


### <a name="response"></a>Odpowiedź

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
| 202   | `Accepted`-Żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację, która może służyć do śledzenia uruchomionej operacji. |
| 400   | `Bad/Malformed request`-Treść odpowiedzi na błąd może dostarczyć więcej informacji.                                                               |
| 422   | `Un-processable entity`-Wskazuje, że nie można sprawdzić poprawności jednostki do opublikowania.                                                        |
| 404   | `Not found`-Jednostka określona przez klienta nie istnieje.                                                                              |
|  |  |
