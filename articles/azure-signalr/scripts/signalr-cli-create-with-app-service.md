---
title: Tworzenie usługi sygnalizującej za pomocą App Service przy użyciu interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure do utworzenia usługi sygnalizującej z App Service. Uzyskaj informacje na temat wszystkich poleceń interfejsu wiersza polecenia dla usługi Azure Signal.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 673a3583b1ec80fb3ad61ec35a1786a59939f6e8
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563328"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Tworzenie usługi SignalR Service za pomocą usługi App Service

Ten przykładowy skrypt tworzy nowy zasób usługi Azure SignalR Service, który jest używany do wypychania w czasie rzeczywistym aktualizacji zawartości do klientów. Skrypt ten dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji o nazwie *AzureSignalRConnectionString* , aby nawiązać połączenie z nowym zasobem usługi SignalR Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt używa rozszerzenia *signalr* dla interfejsu wiersza polecenia platformy Azure. Uruchom następujące polecenie, aby zainstalować rozszerzenie *signalr* dla interfejsu wiersza polecenia platformy Azure zanim użyjesz tego przykładowego skryptu:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Zostanie ona wyświetlona w danych wyjściowych. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Tworzy zasób usługi Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Wyświetla listę kluczy, które będą używane przez Twoją aplikację przy wypychaniu w czasie rzeczywistym aktualizacji zawartości za pomocą usługi SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi Azure App Service do hostowania aplikacji internetowych. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Tworzy aplikację internetową platformy Azure za pomocą planu hostingu usługi App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Dodaje nowe ustawienie aplikacji dla aplikacji internetowej. To ustawienie aplikacji umożliwia przechowywanie parametrów połączenia usługi SignalR. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia usługi Azure SignalR Service można znaleźć w [dokumentacji usługi Azure SignalR Service](../signalr-reference-cli.md).
