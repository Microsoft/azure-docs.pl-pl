---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491158"
---
Po uruchomieniu przykładowej aplikacji urządzenia w dalszej części tego samouczka potrzebne są następujące wartości konfiguracji:

* Zakres identyfikatorów: w aplikacji IoT Central przejdź do obszarze **administracja > połączenie z urządzeniem**. Zanotuj wartość **identyfikatora Scope** .
* Klucz podstawowy grupy: w aplikacji IoT Central przejdź do obszarze **administracja > połączenie z urządzeniem > SAS-IoT-Devices**. Zanotuj wartość **klucza podstawowego** sygnatury dostępu współdzielonego.

Użyj Cloud Shell do wygenerowania klucza urządzenia z pobranego klucza podstawowego grupy:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Zanotuj wygenerowany klucz urządzenia i użyj go w dalszej części tego samouczka.
