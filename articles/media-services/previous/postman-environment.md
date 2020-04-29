---
title: Zaimportuj środowisko programu Poster dla Azure Media Services wywołań REST
description: Ten temat zawiera definicję środowiska programu Poster dla Azure Media Services wywołań REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8254d121c62a20de0a1593920b7793195f8eb50e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78926711"
---
# <a name="import-the-postman-environment"></a>Importowanie środowiska programu Poster  

Ten artykuł zawiera definicję zmiennych środowiskowych programu **Poster** , które są używane w [kolekcji programu Poster](postman-collection.md) , która zawiera zgrupowane żądania HTTP, które wywołują Media Services interfejsów API REST. Pliki środowiska i kolekcji są używane przez kreatora [konfigurowania programu do obsługi wywołań interfejsu API REST Media Services](media-rest-apis-with-postman.md) .

> [!NOTE]
> `AzureADSTSEndpoint `  = Wartość `https://login.microsoftonline.com/{{TenantId}}/oauth2/token`. Aby uzyskać identyfikator dzierżawy, możesz umieścić wskaźnik myszy nad nazwą użytkownika w portalu (w prawym górnym rogu) i będzie on znajdować się w katalogu "katalog: Microsoft ({{TENANTID}}").

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
