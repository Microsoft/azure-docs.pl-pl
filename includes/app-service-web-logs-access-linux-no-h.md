---
title: plik dołączania
description: plik dołączania
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84905629"
---
Możesz uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz kontenera. Najpierw Włącz rejestrowanie kontenerów, uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, naciśnij kombinację klawiszy `Ctrl`+`C`.
