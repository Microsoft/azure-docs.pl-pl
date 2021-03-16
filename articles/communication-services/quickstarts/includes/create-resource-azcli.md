---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 6ed6544b8014e973eaf92c763ca18687ad89e5a7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495898"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Tworzenie zasobu komunikacyjnego platformy Azure

Aby utworzyć zasób usługi Azure Communications Services, [Zaloguj się do interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), a następnie uruchom następujące polecenie:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Zasób usług komunikacyjnych można skonfigurować przy użyciu następujących opcji:

* Grupa zasobów
* Nazwa zasobu usług komunikacyjnych
* Geografia, z którą zostanie skojarzony zasób

W następnym kroku można przypisać Tagi do zasobu. Tagi mogą służyć do organizowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat tagów, zobacz [dokumentację tagowania zasobów](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Zarządzanie zasobem usług komunikacyjnych

Aby dodać tagi do zasobu usług komunikacyjnych, uruchom następujące polecenia:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Aby uzyskać więcej informacji na temat dodatkowych poleceń, zobacz [AZ Communication](/cli/azure/ext/communication/communication).