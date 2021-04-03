---
title: Plik dyrektywy include
description: dołączanie pliku
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91822787"
---
Aby otworzyć bezpośrednią sesję SSH w kontenerze, należy uruchomić aplikację.

Wklej następujący adres URL do przeglądarki i zastąp wartość `<app-name>` nazwą swojej aplikacji:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Jeśli nie masz jeszcze uwierzytelnienia, musisz uwierzytelnić się w subskrypcji platformy Azure, aby nawiązać połączenie. Po uwierzytelnieniu zostanie wyświetlona powłoka w przeglądarce umożliwiająca uruchamianie poleceń w kontenerze.

![Połączenie SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
