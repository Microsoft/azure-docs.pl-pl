---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 0e23e537043664929bdc1a3636de359953b66db6
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711402"
---
| Właściwość | Opis |
|:--- |:---|
|**pierwszym** | Czas uniwersalnego czasu koordynowanego (UTC), gdy żądanie zostało odebrane przez magazyn. Przykład: `2018/11/08 21:09:36.6900118`.|
|**Identyfikator** | Identyfikator zasobu konta magazynu. Na przykład: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorii** | Kategoria żądanej operacji. Na przykład: `StorageRead` , `StorageWrite` , lub `StorageDelete` .|
|**operationName** | Typ operacji REST, która została wykonana. <br> Aby uzyskać pełną listę operacji, zobacz [temat analityka magazynu zarejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Wersja usługi magazynu określona podczas żądania. Jest to odpowiednik wartości nagłówka **x-MS-Version** . Przykład: `2017-04-17`.|
|**schemaVersion** | Wersja schematu dziennika. Przykład: `1.0`.|
|**statusCode** | Kod stanu HTTP dla żądania. Jeśli żądanie zostanie przerwane, ta wartość może być ustawiona na `Unknown` . <br> Na przykład: `206` |
|**statusText** | Stan wymaganej operacji.  Pełna lista komunikatów o stanie znajduje się w [temacie analityka magazynu zarejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). W wersji 2017-04-17 i nowszych komunikat o stanie `ClientOtherError` nie jest używany. Zamiast tego pole to zawiera kod błędu. Na przykład: `SASSuccess`  |
|**Milisekundach)** | Łączny czas, wyrażony w milisekundach, w celu wykonania żądanej operacji. Obejmuje to czas odczytywania żądania przychodzącego oraz wysłanie odpowiedzi do osoby żądającej. Przykład: `12`.|
|**callerIpAddress** | Adres IP osoby żądającej, w tym numer portu. Przykład: `192.100.0.102:4362`. |
|**korelacj** | Identyfikator, który jest używany do skorelowania dzienników między zasobami. Przykład: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**przeniesienie** | Lokalizacja konta magazynu. Przykład: `North Europe`. |
|**protokol**|Protokół, który jest używany w operacji. Na przykład: `HTTP` , `HTTPS` , `SMB` lub `NFS`|
| **adresu** | Zażądano identyfikatora Uniform Resource Identifier. |