---
title: Interfejs API usługi go Live — Azure Marketplace
description: Interfejs API przejdź na żywo inicjuje proces oferty na żywo.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: 5c5448a853447a0eacc8d974a5b00d5d1e8f4be2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535947"
---
# <a name="go-live"></a>Przejdź na żywo

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Ten interfejs API uruchamia proces wypychania aplikacji do środowiska produkcyjnego. Ta operacja jest zwykle długotrwała. To wywołanie używa listy wiadomości e-mail powiadomień z operacji [publikowania](./cloud-partner-portal-api-publish-offer.md) interfejsu API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

|  **Nazwa**      |   **Opis**                                                           | **Typ danych** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identyfikator wydawcy dla oferty do pobrania, na przykład`contoso`       |  String (ciąg)       |
| offerId        | Identyfikator oferty oferty do pobrania                                   |  String (ciąg)       |
| api-version    | Najnowsza wersja interfejsu API                                                   |  Data         |
|  |  |  |

## <a name="header"></a>Nagłówek
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
