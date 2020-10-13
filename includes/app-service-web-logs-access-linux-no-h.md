---
title: plik dołączany
description: plik dołączany
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822827"
---
Możesz uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz kontenera.

Najpierw Włącz rejestrowanie kontenerów, uruchamiając następujące polecenie:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Zamień `<app-name>` i na `<resource-group-name>` nazwy odpowiednie dla aplikacji sieci Web.

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz **Ctrl** + **C**.

Pliki dzienników można także sprawdzić w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
