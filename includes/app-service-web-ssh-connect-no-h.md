---
title: dołączanie pliku
description: dołączanie pliku
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183710"
---
Aby otworzyć bezpośrednią sesję SSH z kontenerem, należy uruchomić aplikację.

Wklej następujący adres URL do przeglądarki i Zastąp ciąg \<app-name> nazwą swojej aplikacji:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Jeśli nie masz jeszcze uwierzytelnienia, musisz uwierzytelnić się w ramach subskrypcji platformy Azure, aby nawiązać połączenie. Po uwierzytelnieniu zostanie wyświetlona powłoka w przeglądarce, w której można uruchamiać polecenia w kontenerze.

![Połączenie SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
