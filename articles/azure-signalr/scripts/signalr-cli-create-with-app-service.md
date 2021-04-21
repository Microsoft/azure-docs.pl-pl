---
title: Tworzenie aplikacji SignalR Service pomocą App Service interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure, aby SignalR Service za pomocą App Service. Poznaj wszystkie polecenia interfejsu wiersza polecenia dla Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e6e2484dc7fffdcecb64ef3b3afa3a7a4343d29c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787309"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Tworzenie usługi SignalR Service za pomocą usługi App Service

Ten przykładowy skrypt tworzy nowy zasób usługi Azure SignalR Service, który jest używany do wypychania w czasie rzeczywistym aktualizacji zawartości do klientów. Skrypt ten dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji o nazwie *AzureSignalRConnectionString*, aby nawiązać połączenie z nowym zasobem usługi SignalR Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

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
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az signalr create](/cli/azure/signalr#az_signalr_create) | Tworzy zasób usługi Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az_signalr_key_list) | Wyświetla listę kluczy, które będą używane przez Twoją aplikację przy wypychaniu w czasie rzeczywistym aktualizacji zawartości za pomocą usługi SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi Azure App Service do hostowania aplikacji internetowych. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Tworzy aplikację internetową platformy Azure za pomocą planu hostingu usługi App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Dodaje nowe ustawienie aplikacji dla aplikacji internetowej. To ustawienie aplikacji umożliwia przechowywanie parametrów połączenia usługi SignalR. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia usługi Azure SignalR Service można znaleźć w [dokumentacji usługi Azure SignalR Service](../signalr-reference-cli.md).
