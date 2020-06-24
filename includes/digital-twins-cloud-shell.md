---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — konfiguracja Cloud Shell i rozszerzenie IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296971"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell

Po otwarciu okna Cloud Shell najpierw Zaloguj się i ustaw kontekst powłoki na subskrypcję dla tej sesji. Uruchom następujące polecenia w Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Jeśli korzystasz z tej subskrypcji po raz pierwszy przy użyciu usługi Azure Digital bliźniaczych reprezentacji, Uruchom to polecenie, aby zarejestrować się w przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. (Jeśli nie masz pewności, możesz go uruchomić ponownie nawet wtedy, gdy kiedyś upłynął czas w przeszłości).

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Następnie dodasz do Cloud Shell [**Microsoft Azure IoT Extension dla interfejsu wiersza polecenia platformy Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) , aby włączyć obsługę komunikacji z usługą Azure Digital bliźniaczych reprezentacji i innymi usługami IoT. Użyj tego polecenia, aby dodać rozszerzenie:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Jeśli wcześniej zainstalowano rozszerzenie, w danych wyjściowych może zostać wyświetlony komunikat "rozszerzenie" Azure-IoT "jest już zainstalowane". W takim przypadku uruchom następujące polecenie, aby upewnić się, że masz najnowszą aktualizację: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Teraz możesz przystąpić do pracy z usługą Azure Digital bliźniaczych reprezentacji w Cloud Shell.

Możesz to sprawdzić, uruchamiając `az dt -h` w dowolnym momencie, aby wyświetlić listę dostępnych poleceń usługi Azure Digital bliźniaczych reprezentacji.