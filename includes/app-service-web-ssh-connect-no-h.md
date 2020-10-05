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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "67183710"
---
Aby otworzyć bezpośrednią sesję SSH w kontenerze, należy uruchomić aplikację.

Wklej następujący adres URL do przeglądarki i zastąp wartość \<app-name> nazwą swojej aplikacji:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Jeśli nie masz jeszcze uwierzytelnienia, musisz uwierzytelnić się w subskrypcji platformy Azure, aby nawiązać połączenie. Po uwierzytelnieniu zostanie wyświetlona powłoka w przeglądarce umożliwiająca uruchamianie poleceń w kontenerze.

![Połączenie SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
