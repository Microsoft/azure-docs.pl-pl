---
title: 'Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3340cd0a39b0f5311487fec5b05d37e3376d433a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659255"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Dodaj rozszerzenie HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Pobierz listę poleceń dla HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Utwórz grupę zasobów platformy Azure

Wybierz nazwę grupy zasobów, która będzie zawierać interfejs API platformy Azure dla usługi FHIR, i utwórz ją:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Wdrażanie interfejsu API platformy Azure dla usługi FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Pobieranie instrukcji obsługi interfejsu API FHIR

Uzyskaj instrukcję możliwości z interfejsu API FHIR z:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń grupę zasobów, wykonując następujące czynności:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono interfejs API platformy Azure dla usługi FHIR w ramach subskrypcji. Aby ustawić dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR, należy przejoć do dodatkowych ustawień przewodnika. Jeśli wszystko jest gotowe do rozpoczęcia korzystania z interfejsu API platformy Azure dla usługi FHIR, Przeczytaj więcej na temat sposobu rejestrowania aplikacji.

>[!div class="nextstepaction"]
>[Dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Przegląd rejestracji aplikacji](fhir-app-registration.md)
