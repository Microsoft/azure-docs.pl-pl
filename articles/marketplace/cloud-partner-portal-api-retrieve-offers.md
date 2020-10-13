---
title: Pobieranie ofert API — Azure Marketplace
description: Interfejs API służący do pobierania podsumowującej listy ofert w przestrzeni nazw wydawcy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87271964"
---
# <a name="retrieve-offers"></a>Pobieranie oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Pobiera podsumowującą listę ofert w przestrzeni nazw wydawcy.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

| **Nazwa**         |  **Opis**                         |  **Typ danych** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identyfikator wydawcy, na przykład `contoso` |   Ciąg    |
|  api-version     | Najnowsza wersja interfejsu API                    |    Date        |
|  |  |

## <a name="header"></a>Header

|  **Nazwa**        |         **Wartość**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autoryzacja   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Przykład treści

### <a name="response"></a>Reakcja

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
|  Wersja        | Bieżąca wersja oferty. Nie można zmodyfikować właściwości Version przez klienta. Zwiększa się po każdej publikacji. |
|  definicja     | Zawiera sumaryczny widok rzeczywistej definicji obciążenia. Aby uzyskać szczegółową definicję, użyj interfejsu API [pobierania określonej oferty](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | Czas UTC ostatniej modyfikacji oferty                                                                              |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod**  |  **Opis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Żądanie zostało pomyślnie przetworzone i wszystkie oferty w ramach wydawcy zostały zwrócone do klienta.  |
|  400      | `Bad/Malformed request` -Treść odpowiedzi błędu może zawierać więcej informacji.                                    |
|  403      | `Forbidden` -Klient nie ma dostępu do określonego obszaru nazw.                                          |
|  404      | `Not found` -Określona jednostka nie istnieje.                                                                 |
|  |  |

### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    | **Opis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Oferta nie została nigdy opublikowana.                  |
|  NotStarted                  | Oferta jest nowa, ale nie została uruchomiona.                 |
|  WaitingForPublisherReview   | Oferta oczekuje na zatwierdzenie przez wydawcę.         |
|  Uruchomienie                     | Przesyłanie oferty jest przetwarzane.             |
|  Sukces                   | Przesyłanie oferty zostało zakończone.       |
|  Anulowane                    | Przesyłanie oferty zostało anulowane.                   |
|  Niepowodzenie                      | Nie można przesłać oferty.                         |
|  |  |
