---
title: Jak uzyskać wystąpienie procesora multimediów przy użyciu interfejsu REST | Microsoft Docs
description: Dowiedz się, jak utworzyć składnik procesora multimediów służący do kodowania, konwertowania formatu, szyfrowania lub odszyfrowywania zawartości multimedialnej dla Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ba625aa048360a4c201b91b4a5a4a7ca4dd6277b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269509"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak uzyskać wystąpienie procesora multimediów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Omówienie
Procesory multimediów to składnik obsługujący określone zadanie przetwarzania wideo lub audio, takie jak kodowanie, Konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. Wszystkie zadania przesłane do Media Services wymagają procesora multimediów do kodowania, szyfrowania lub konwertowania zawartości wideo lub audio. 

## <a name="azure-media-processors"></a>Procesory multimediów platformy Azure 

Poniższy temat zawiera listę procesorów multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Pobierz procesor multimediów

Następujące wywołanie REST pokazuje, jak uzyskać wystąpienie procesora multimediów według nazwy (w tym przypadku **Media Encoder Standard**). 

Żądanie:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Odpowiedź:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak uzyskać wystąpienie procesora multimediów, przejdź do artykułu [jak kodować element zawartości](media-services-rest-get-started.md) , który pokazuje, jak używać Media Encoder Standard do kodowania elementu zawartości.

