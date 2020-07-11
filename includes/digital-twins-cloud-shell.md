---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — konfiguracja Cloud Shell i rozszerzenie IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277783"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell

Po otwarciu okna Cloud Shell najpierw Zaloguj się i ustaw kontekst powłoki na subskrypcję dla tej sesji. Uruchom następujące polecenia w Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Możesz również ustawić subskrypcję przy użyciu nazwy subskrypcji. Użyj następującego polecenia: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Jeśli korzystasz z tej subskrypcji po raz pierwszy przy użyciu usługi Azure Digital bliźniaczych reprezentacji, Uruchom to polecenie, aby zarejestrować się w przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. (Jeśli nie masz pewności, możesz go uruchomić ponownie nawet wtedy, gdy kiedyś upłynął czas w przeszłości).

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Następnie dodasz do Cloud Shell [**Microsoft Azure IoT Extension dla interfejsu wiersza polecenia platformy Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) , aby włączyć obsługę komunikacji z usługą Azure Digital bliźniaczych reprezentacji i innymi usługami IoT. 

Najpierw uruchom to polecenie, aby wyświetlić listę wszystkich zainstalowanych już rozszerzeń.

```azurecli-interactive
az extension list
```

W danych wyjściowych poszukaj `"name"` pola dla każdego wpisu listy, aby wyświetlić nazwy rozszerzeń.

Użyj danych wyjściowych, aby określić, które z poniższych poleceń mają być uruchamiane dla instalacji rozszerzenia (można uruchomić więcej niż jeden).
* Jeśli lista zawiera `azure-iot` : masz już rozszerzenie. Uruchom to polecenie, aby upewnić się, że masz najnowszą aktualizację:

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