---
title: Zaimportuj środowisko programu Poster dla Azure Media Services wywołań REST
description: Ten temat zawiera definicję środowiska programu Poster dla Azure Media Services wywołań REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007849"
---
# <a name="import-the-postman-environment"></a>Importowanie środowiska programu Poster

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Ten artykuł zawiera definicję zmiennych środowiskowych programu **Poster** , które są używane w [kolekcji programu Poster](postman-collection.md) , która zawiera zgrupowane żądania HTTP, które wywołują Media Services interfejsów API REST. Pliki środowiska i kolekcji są używane przez kreatora [konfigurowania programu do obsługi wywołań interfejsu API REST Media Services](media-rest-apis-with-postman.md) .

> [!NOTE]
> Wartość `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` . Aby uzyskać identyfikator dzierżawy, możesz umieścić wskaźnik myszy nad nazwą użytkownika w portalu (w prawym górnym rogu) i będzie on znajdować się w katalogu "katalog: Microsoft ({{TENANTID}}").

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
