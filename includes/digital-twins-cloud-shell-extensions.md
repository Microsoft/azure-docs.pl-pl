---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — Konfigurowanie najnowszego rozszerzenia IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606397"
---
Najpierw uruchom to polecenie, aby wyświetlić listę wszystkich zainstalowanych już rozszerzeń.

```azurecli-interactive
az extension list
```

Wyjście jest tablicą wszystkich aktualnie posiadanych rozszerzeń. Wyszukaj `"name"` pole dla każdego wpisu listy, aby wyświetlić nazwy rozszerzeń.

Użyj danych wyjściowych, aby określić, które z poniższych poleceń mają być uruchamiane dla instalacji rozszerzenia (można uruchomić więcej niż jeden).
* Jeśli lista zawiera `azure-iot` : masz już rozszerzenie. Uruchom to polecenie, aby upewnić się, że masz najnowszą aktualizację i nie ma więcej dostępnych aktualizacji:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Jeśli lista **nie zawiera** : należy `azure-iot` zainstalować rozszerzenie. Użyj następującego polecenia:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Jeśli lista zawiera `azure-iot-cli-ext` : jest to starsza wersja rozszerzenia. W danej chwili należy zainstalować tylko jedną wersję rozszerzenia, dlatego należy odinstalować starsze rozszerzenie. Użyj następującego polecenia:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```