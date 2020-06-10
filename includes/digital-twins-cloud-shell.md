---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — konfiguracja Cloud Shell i rozszerzenie IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612893"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell

Po otwarciu okna Cloud Shell najpierw Zaloguj się i ustaw kontekst powłoki na subskrypcję dla tej sesji. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Jeśli korzystasz z tej subskrypcji po raz pierwszy przy użyciu usługi Azure Digital bliźniaczych reprezentacji, Uruchom to polecenie, aby zarejestrować się w przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. (Jeśli nie masz pewności, możesz go uruchomić ponownie nawet wtedy, gdy kiedyś upłynął czas w przeszłości).

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Następnie uruchom następujące polecenie w wystąpieniu Cloud Shell, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> W tym artykule jest stosowana Najnowsza wersja rozszerzenia usługi Azure IoT o nazwie `azure-iot` . Starsza wersja jest wywoływana `azure-iot-cli-ext` . W danym momencie powinna być zainstalowana tylko jedna wersja. Za pomocą polecenia można `az extension list` sprawdzić poprawność zainstalowanych rozszerzeń.
> Służy `az extension remove --name azure-cli-iot-ext` do usuwania starszej wersji rozszerzenia.
> Użyj `az extension add --name azure-iot` , aby dodać nową wersję rozszerzenia. Aby zobaczyć, jakie rozszerzenia zostały zainstalowane, użyj programu `az extension list` .

> [!TIP]
> Możesz uruchomić `az dt -h` polecenie, aby wyświetlić bliźniaczych reprezentacji cyfrowe polecenia platformy Azure.