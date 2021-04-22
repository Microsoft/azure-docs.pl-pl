---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 69857915620eada94586754a6c934edaf0b294a9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880250"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/dotnet/)
- Instalowanie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Tworzenie zasobu komunikacji platformy Azure

Aby utworzyć zasób Azure Communication Services, [zaloguj się do interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli) Możesz to zrobić za pośrednictwem terminalu, ```az login``` używając polecenia i podając swoje poświadczenia. Uruchom następujące polecenie, aby utworzyć zasób:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Jeśli chcesz wybrać określoną subskrypcję, możesz również określić ```--subscription``` flagę i podać identyfikator subskrypcji.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Możesz skonfigurować zasób Communication Services za pomocą następujących opcji:

* Grupa zasobów
* Nazwa zasobu Communication Services zasobów
* Lokalizacja geograficzna, z która zostanie skojarzona zasób

W następnym kroku możesz przypisać tagi do zasobu. Tagi mogą służyć do organizowania zasobów platformy Azure. Aby uzyskać [więcej informacji na temat tagów,](../../../azure-resource-manager/management/tag-resources.md) zobacz dokumentację tagowania zasobów.

## <a name="manage-your-communication-services-resource"></a>Zarządzanie zasobem Communication Services zasobów

Aby dodać tagi do zasobu Communication Services, uruchom następujące polecenia. Można również określić docelową subskrypcję.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Aby uzyskać informacje na temat dodatkowych poleceń, zobacz [az communication](/cli/azure/communication).
