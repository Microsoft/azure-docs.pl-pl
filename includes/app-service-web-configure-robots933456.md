---
title: Plik dyrektywy include
description: dołączanie pliku
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78255826"
---
## <a name="robots933456-in-logs"></a>robots933456 w dziennikach

W dziennikach kontenera może zostać wyświetlony następujący komunikat:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Możesz bezpiecznie zignorować ten komunikat. `/robots933456.txt` jest fikcyjną ścieżką adresu URL, za pomocą której usługa App Service sprawdza, czy kontener jest w stanie obsługiwać żądania. Odpowiedź 404 oznacza po prostu, że ścieżka nie istnieje, ale pozwala usłudze App Service sprawdzić, czy kontener jest w dobrej kondycji i jest gotowy do reagowania na żądania.

