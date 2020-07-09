---
title: dołączanie pliku
description: dołączanie pliku
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905871"
---
Aby uzyskać dostęp do dzienników konsoli wygenerowanych z wnętrza kodu aplikacji w App Service, Włącz rejestrowanie diagnostyki, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Możliwe wartości dla `--level` : `Error` , `Warning` , `Info` i `Verbose` . Każdy kolejny poziom obejmuje poprzedni poziom. Na przykład: `Error` zawiera tylko komunikaty o błędach i `Verbose` zawiera wszystkie komunikaty.

Po włączeniu rejestrowania diagnostycznego Uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz polecenie `Ctrl` + `C` .
