---
title: 'Interfejs wiersza polecenia platformy Azure: Wdrażanie serwera Open Source FHIR Server dla platformy Azure — Azure API for Azure'
description: Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć program Microsoft FHIR Server dla platformy Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843583"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Szybki Start: Wdrażanie serwera FHIR Open Source przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start dowiesz się, jak wdrożyć serwer FHIR Open Source &reg; na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Wybierz nazwę grupy zasobów, która będzie zawierać zasoby, których zainicjowano, i utwórz ją:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Wdrażanie szablonu

Repozytorium Microsoft FHIR Server for Azure [GitHub](https://github.com/Microsoft/fhir-server) zawiera szablon, który będzie wdrażał wszystkie niezbędne zasoby. Wdróż przy użyciu:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Sprawdź, czy serwer FHIR jest uruchomiony

Uzyskaj instrukcję możliwości z serwera FHIR z:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Potrwa to chwilę, zanim serwer zacznie reagować po raz pierwszy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń grupę zasobów, wykonując następujące czynności:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer Microsoft Open Source FHIR dla platformy Azure w ramach subskrypcji. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API FHIR przy użyciu programu Poster, zapoznaj się z samouczkiem dotyczącym programu.
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)
