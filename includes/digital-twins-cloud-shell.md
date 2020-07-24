---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — konfiguracja Cloud Shell i rozszerzenie IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032220"
---
Aby rozpocząć pracę z usługą Azure Digital bliźniaczych reprezentacji w otwartym oknie [Azure Cloud Shell](https://shell.azure.com) , najpierw należy się zalogować i ustawić kontekst powłoki dla tej sesji. Uruchom następujące polecenia w Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Możesz również użyć nazwy subskrypcji zamiast identyfikatora w powyższym poleceniu. 

Jeśli korzystasz z tej subskrypcji po raz pierwszy przy użyciu usługi Azure Digital bliźniaczych reprezentacji, Uruchom to polecenie, aby zarejestrować się w przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. (Jeśli nie masz pewności, możesz go uruchomić ponownie nawet wtedy, gdy kiedyś upłynął czas w przeszłości).

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Następnie dodasz do Cloud Shell [**Microsoft Azure IoT Extension dla interfejsu wiersza polecenia platformy Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) , aby włączyć obsługę komunikacji z usługą Azure Digital bliźniaczych reprezentacji i innymi usługami IoT. 

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

Teraz możesz przystąpić do pracy z usługą Azure Digital bliźniaczych reprezentacji w Cloud Shell.

Możesz to sprawdzić, uruchamiając `az dt -h` w dowolnym momencie, aby wyświetlić listę dostępnych poleceń usługi Azure Digital bliźniaczych reprezentacji.