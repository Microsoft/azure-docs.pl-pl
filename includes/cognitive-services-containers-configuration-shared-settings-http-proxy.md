---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320537"
---
Jeśli musisz skonfigurować serwer proxy HTTP do tworzenia żądań wychodzących, użyj następujących dwóch argumentów:

| Nazwa | Typ danych | Opis |
|--|--|--|
|HTTP_PROXY|ciąg|Serwer proxy do użycia, na przykład`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|ciąg|Wszystkie poświadczenia potrzebne do uwierzytelnienia w odniesieniu do serwera proxy, na przykład username: Password.|
|`<proxy-user>`|ciąg|Użytkownik serwera proxy.|
|`<proxy-password>`|ciąg|Hasło skojarzone z `<proxy-user>` serwerem proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
