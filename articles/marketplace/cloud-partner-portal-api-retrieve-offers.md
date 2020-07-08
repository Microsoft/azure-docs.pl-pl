---
title: Pobieranie ofert API — Azure Marketplace
description: Interfejs API służący do pobierania podsumowującej listy ofert w przestrzeni nazw wydawcy.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ee8d0c773a5ec4d362eae66e289838b9646247e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516240"
---
<a name="retrieve-offers"></a>Pobieranie oferty
===============

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Pobiera podsumowującą listę ofert w przestrzeni nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametry identyfikatora URI
--------------

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identyfikator wydawcy, na przykład`contoso` |   String    |
|  api-version     | Najnowsza wersja interfejsu API                    |    Data        |
|  |  |


<a name="header"></a>Header
------

|  **Nazwa**        |         **Wartość**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autoryzacja   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Przykład treści
------------

### <a name="response"></a>Odpowiedź

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**       |       **Opis**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identyfikuje typ oferty                                                                                           |
|  publisherId    | Identyfikator, który jednoznacznie identyfikuje wydawcę                                                                      |
|  status         | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz temat [status oferty](#offer-status) poniżej.                         |
|  identyfikator             | Identyfikator GUID, który jednoznacznie identyfikuje ofertę w przestrzeni nazw wydawcy.                                                    |
|  version        | Bieżąca wersja oferty. Nie można zmodyfikować właściwości Version przez klienta. Zwiększa się po każdej publikacji. |
|  definicja     | Zawiera sumaryczny widok rzeczywistej definicji obciążenia. Aby uzyskać szczegółową definicję, użyj interfejsu API [pobierania określonej oferty](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Czas UTC ostatniej modyfikacji oferty                                                                              |
|  |  |


### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod**  |  **Opis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-Żądanie zostało pomyślnie przetworzone i wszystkie oferty w ramach wydawcy zostały zwrócone do klienta.  |
|  400      | `Bad/Malformed request`-Treść odpowiedzi błędu może zawierać więcej informacji.                                    |
|  403      | `Forbidden`-Klient nie ma dostępu do określonego obszaru nazw.                                          |
|  404      | `Not found`-Określona jednostka nie istnieje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    | **Opis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Oferta nie została nigdy opublikowana.                  |
|  NotStarted                  | Oferta jest nowa, ale nie została uruchomiona.                 |
|  WaitingForPublisherReview   | Oferta oczekuje na zatwierdzenie przez wydawcę.         |
|  Działanie                     | Przesyłanie oferty jest przetwarzane.             |
|  Sukces                   | Przesyłanie oferty zostało zakończone.       |
|  Anulowane                    | Przesyłanie oferty zostało anulowane.                   |
|  Niepowodzenie                      | Nie można przesłać oferty.                         |
|  |  |
