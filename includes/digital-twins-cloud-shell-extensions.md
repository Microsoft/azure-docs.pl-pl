---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — Konfigurowanie najnowszego rozszerzenia IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606397"
---
Najpierw uruchom to polecenie, aby wyświetlić listę wszystkich zainstalowanych już rozszerzeń.

```azurecli
az extension list
```

Wyjście jest tablicą wszystkich aktualnie posiadanych rozszerzeń. Wyszukaj `"name"` pole dla każdego wpisu listy, aby wyświetlić nazwy rozszerzeń.

Użyj danych wyjściowych, aby określić, które z poniższych poleceń mają być uruchamiane dla instalacji rozszerzenia (można uruchomić więcej niż jeden).
* Jeśli lista zawiera `azure-iot` : masz już rozszerzenie. Uruchom to polecenie, aby upewnić się, że masz najnowszą aktualizację i nie ma więcej dostępnych aktualizacji:

   ```azurecli
   az extension update --name azure-iot
   ```

* Jeśli lista **nie zawiera** : należy `azure-iot` zainstalować rozszerzenie. Użyj następującego polecenia:

    ```azurecli
    az extension add --name azure-iot
    ```

* Jeśli lista zawiera `azure-iot-cli-ext` : jest to starsza wersja rozszerzenia. W danej chwili należy zainstalować tylko jedną wersję rozszerzenia, dlatego należy odinstalować starsze rozszerzenie. Użyj następującego polecenia:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```