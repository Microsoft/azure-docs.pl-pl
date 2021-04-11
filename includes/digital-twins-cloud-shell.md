---
author: baanders
description: plik dołączany dla usługi Azure Digital bliźniaczych reprezentacji — konfiguracja Cloud Shell i rozszerzenie IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105067"
---
Aby rozpocząć pracę z usługą Azure Digital bliźniaczych reprezentacji w otwartym oknie [Azure Cloud Shell](https://shell.azure.com) , najpierw należy się zalogować i ustawić kontekst powłoki dla tej sesji. Uruchom następujące polecenia w Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Możesz również użyć nazwy subskrypcji zamiast identyfikatora w powyższym poleceniu. 

Jeśli korzystasz z tej subskrypcji po raz pierwszy przy użyciu usługi Azure Digital bliźniaczych reprezentacji, Uruchom to polecenie, aby zarejestrować się w przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. (Jeśli nie masz pewności, możesz go uruchomić ponownie nawet wtedy, gdy kiedyś upłynął czas w przeszłości).

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Następnie dodasz do Cloud Shell [**Microsoft Azure IoT Extension dla interfejsu wiersza polecenia platformy Azure**](/cli/azure/service-page/azure%20iot) , aby włączyć obsługę komunikacji z usługą Azure Digital bliźniaczych reprezentacji i innymi usługami IoT. Uruchom to polecenie, aby upewnić się, że masz najnowszą wersję rozszerzenia:

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

Teraz możesz przystąpić do pracy z usługą Azure Digital bliźniaczych reprezentacji w Cloud Shell.

Możesz to sprawdzić, uruchamiając `az dt -h` w dowolnym momencie, aby wyświetlić listę dostępnych poleceń usługi Azure Digital bliźniaczych reprezentacji.