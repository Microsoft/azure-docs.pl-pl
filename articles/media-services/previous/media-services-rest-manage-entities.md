---
title: Zarządzanie jednostkami Media Services przy użyciu interfejsu REST | Microsoft Docs
description: W tym artykule pokazano, jak zarządzać jednostkami Media Services za pomocą interfejsu API REST.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015560"
---
# <a name="managing-media-services-entities-with-rest"></a>Zarządzanie jednostkami Media Services przy użyciu interfejsu REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services to usługa oparta na protokole REST wbudowana w Protokół OData V3. Można dodawać, wysyłać zapytania, aktualizować i usuwać jednostki w taki sam sposób jak w przypadku innych usług OData. Wyjątki zostaną wywołane w razie potrzeby. Aby uzyskać więcej informacji na temat usługi OData, zobacz artykuł [Open Data Protocol Documentation](https://www.odata.org/documentation/).

W tym temacie pokazano, jak zarządzać jednostkami Azure Media Services przy użyciu interfejsu REST.

>[!NOTE]
> Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 każdy rekord zadania na koncie starszym niż 31 grudnia 2016 zostanie automatycznie usunięty. Jeśli zachodzi potrzeba archiwizowania informacji o zadaniu/zadaniu, można użyć kodu opisanego w tym temacie.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Dodawanie jednostek
Każda jednostka w Media Services jest dodawana do zestawu jednostek, takiego jak zasoby, przez żądanie POST HTTP.

Poniższy przykład pokazuje, jak utworzyć AccessPolicy.

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>Wykonywanie zapytań względem jednostek
Wykonywanie zapytań i wyświetlanie listy jednostek jest proste i obejmuje tylko żądania GET HTTP i opcjonalne operacje OData.
Poniższy przykład pobiera listę wszystkich jednostek MediaProcessor.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Możesz również pobrać konkretną jednostkę lub wszystkie zestawy jednostek skojarzone z konkretną jednostką, na przykład w następujących przykładach:

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Poniższy przykład zwraca tylko Właściwość State wszystkich zadań.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Poniższy przykład zwraca wszystkie JobTemplates o nazwie "SampleTemplate".

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> Operacja $expand nie jest obsługiwana w Media Services, a także w przypadku nieobsługiwanych metod LINQ opisanych w temacie LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie przez duże kolekcje jednostek
Podczas wykonywania zapytania o jednostki istnieje limit 1000 jednostek, które są zwracane w tym samym czasie, ponieważ Public REST v2 ogranicza wyniki zapytania do 1000 wyników. Użyj **Skip** i **Top** do wyliczenia w dużej kolekcji jednostek. 

Poniższy przykład pokazuje, jak używać **pominięcia** i **góry** , aby pominąć pierwsze 2000 zadań i uzyskać następne zadania 1000.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Aktualizowanie jednostek
W zależności od typu jednostki i stanu, w którym się znajduje, można zaktualizować właściwości tej jednostki za pośrednictwem żądań HTTP, PUT lub MERGE. Aby uzyskać więcej informacji na temat tych operacji, zobacz [poprawka/umieszczenie/scalanie](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Poniższy przykład kodu pokazuje, jak zaktualizować Właściwość nazwy w jednostce elementu zawartości.

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>Usuwanie jednostek
Jednostki można usuwać w Media Services przy użyciu żądania usunięcia HTTP. W zależności od jednostki kolejność usuwania jednostek może być ważna. Na przykład jednostki takie jak zasoby wymagają odwołania (lub usunięcia) wszystkich lokalizatorów odwołujących się do danego elementu zawartości przed usunięciem elementu zawartości.

Poniższy przykład pokazuje, jak usunąć lokalizator, który został użyty do przekazania pliku do magazynu obiektów BLOB.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
