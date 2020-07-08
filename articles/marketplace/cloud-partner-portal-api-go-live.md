---
title: Interfejs API usługi go Live — Azure Marketplace
description: Interfejs API przejdź na żywo inicjuje proces oferty na żywo.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516269"
---
# <a name="go-live"></a>Przejdź na żywo

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Ten interfejs API uruchamia proces wypychania aplikacji do środowiska produkcyjnego. Ta operacja jest zwykle długotrwała. To wywołanie używa listy wiadomości e-mail powiadomień z operacji [publikowania](./cloud-partner-portal-api-publish-offer.md) interfejsu API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identyfikator wydawcy dla oferty do pobrania, na przykład`contoso`       |  String       |
| offerId        | Identyfikator oferty oferty do pobrania                                   |  String       |
| api-version    | Najnowsza wersja interfejsu API                                                   |  Data         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Nazwa**       |     **Wartość**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autoryzacja   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Przykład treści

### <a name="response"></a>Odpowiedź

#### <a name="migrated-offers"></a>Zmigrowane oferty

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Oferty niemigrowane

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Nagłówek odpowiedzi

|  **Nazwa**             |      **Wartość**                                                            |
|  --------             |      ----------                                                           |
| Lokalizacja    |  Ścieżka względna do pobrania stanu tej operacji            |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod** |  **Opis**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-Żądanie zostało pomyślnie zaakceptowane. Odpowiedź zawiera lokalizację do śledzenia stanu operacji. |
|  400     | `Bad/Malformed request`— Dodatkowe informacje o błędzie znajdują się w treści odpowiedzi. |
|  404     |  `Not found`-Określona jednostka nie istnieje.                                       |
|  |  |
