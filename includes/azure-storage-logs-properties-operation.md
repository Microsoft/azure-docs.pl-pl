---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011187"
---
| Właściwość | Opis |
|:--- |:---|
|**pierwszym** | Czas uniwersalnego czasu koordynowanego (UTC), gdy żądanie zostało odebrane przez magazyn. Na przykład: `2018/11/08 21:09:36.6900118`.|
|**Identyfikator** | Identyfikator zasobu konta magazynu. Na przykład: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorii** | Kategoria żądanej operacji. Na przykład: `StorageRead` , `StorageWrite` , lub `StorageDelete` .|
|**operationName** | Typ operacji REST, która została wykonana. <br> Aby uzyskać pełną listę operacji, zobacz [temat analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Wersja usługi magazynu określona podczas żądania. Jest to odpowiednik wartości nagłówka **x-MS-Version** . Na przykład: `2017-04-17`.|
|**schemaVersion** | Wersja schematu dziennika. Na przykład: `1.0`.|
|**statusCode** | Kod stanu HTTP dla żądania. Jeśli żądanie zostanie przerwane, ta wartość może być ustawiona na `Unknown` . <br> Na przykład: `206` |
|**statusText** | Stan wymaganej operacji.  Pełna lista komunikatów o stanie znajduje się w [temacie analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). W wersji 2017-04-17 i nowszych komunikat o stanie `ClientOtherError` nie jest używany. Zamiast tego pole to zawiera kod błędu. Na przykład: `SASSuccess`  |
|**Milisekundach)** | Łączny czas, wyrażony w milisekundach, w celu wykonania żądanej operacji. Obejmuje to czas odczytywania żądania przychodzącego oraz wysłanie odpowiedzi do osoby żądającej. Na przykład: `12`.|
|**callerIpAddress** | Adres IP osoby żądającej, w tym numer portu. Na przykład: `192.100.0.102:4362`. |
|**korelacj** | Identyfikator, który jest używany do skorelowania dzienników między zasobami. Na przykład: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**przeniesienie** | Lokalizacja konta magazynu. Na przykład: `North Europe`. |
|**protokol**|Protokół, który jest używany w operacji. Na przykład: `HTTP` , `HTTPS` , `SMB` lub `NFS`|
| **adresu** | Zażądano identyfikatora Uniform Resource Identifier. |