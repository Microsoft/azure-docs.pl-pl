---
title: Plik dyrektywy include
description: dołączanie pliku
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997944"
---
Aby uzyskać dostęp do dzienników konsoli wygenerowanych wewnątrz kodu aplikacji w usłudze App Service, włącz rejestrowanie diagnostyczne, uruchamiając następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Możliwe wartości parametru `--level` to: `Error`, `Warning`, `Info` i `Verbose`. Każdy kolejny poziom obejmuje poprzedni poziom. Na przykład poziom `Error` obejmuje tylko komunikaty o błędach, a poziom `Verbose` obejmuje wszystkie komunikaty.

Po włączeniu rejestrowania diagnostycznego uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, naciśnij kombinację klawiszy `Ctrl`+`C`.
