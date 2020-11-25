---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001195"
---
Jeśli musisz skonfigurować serwer proxy HTTP do tworzenia żądań wychodzących, użyj następujących dwóch argumentów:

| Nazwa | Typ danych | Opis |
|--|--|--|
|HTTPS_PROXY|ciąg|Serwer proxy do użycia, na przykład `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|ciąg|Wszystkie poświadczenia potrzebne do uwierzytelnienia w odniesieniu do serwera proxy, na przykład username: Password.|
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
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
