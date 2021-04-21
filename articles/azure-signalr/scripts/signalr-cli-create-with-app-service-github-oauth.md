---
title: Tworzenie aplikacji internetowej przy użyciu SignalR Service i uwierzytelniania usługi GitHub
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji internetowej korzystającej z uwierzytelniania usług SignalR Service i GitHub
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c46ac350c1bf7f7593f334a9a7fa5df1af7b19d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787327"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Tworzenie aplikacji internetowej korzystającej z uwierzytelniania usług SignalR Service i GitHub

Ten przykładowy skrypt tworzy nowy zasób usługi Azure SignalR Service, który jest używany do wypychania w czasie rzeczywistym aktualizacji zawartości do klientów. Skrypt ten dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikacja internetowa jest skonfigurowana z ustawieniami aplikacji do nawiązywania połączenia z nowym zasobem usługi SignalR Service i uwierzytelniania za pomocą [uwierzytelniania GitHub](https://developer.github.com/v3/guides/basics-of-authentication/). Aplikacja internetowa jest również skonfigurowana do używania lokalnego źródła wdrożenia repozytorium git.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt używa rozszerzenia *signalr* dla interfejsu wiersza polecenia platformy Azure. Uruchom następujące polecenie, aby zainstalować rozszerzenie *signalr* dla interfejsu wiersza polecenia platformy Azure zanim użyjesz tego przykładowego skryptu:

```azurecli-interactive
#!/bin/bash

#========================================================================
#=== Update these values based on your desired deployment username    ===
#=== and password.                                                    ===
#========================================================================
deploymentUser=<Replace with your desired username>
deploymentUserPassword=<Replace with your desired password>

#========================================================================
#=== Update these values based on your GitHub OAuth App registration. ===
#========================================================================
GitHubClientId=<Replace with your GitHub OAuth app Client ID>
GitHubClientSecret=<Replace with your GitHub OAuth app Client Secret>


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
  --settings "Azure:SignalR:ConnectionString=$primaryConnectionString" 

#Add app settings to use with GitHub authentication
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientId=$GitHubClientId" 
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientSecret=$GitHubClientSecret" 

# Add the desired deployment user name and password
az webapp deployment user set --user-name $deploymentUser --password $deploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $myWebAppName --resource-group $myResourceGroupName \
  --query [url] -o tsv
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
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Dodaje nowe ustawienia aplikacji dla aplikacji internetowej. Te ustawienia aplikacji są używane do przechowywania parametrów połączenia usługi SignalR i wpisów tajnych aplikacji OAuth usługi GitHub. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) | Aktualizacja poświadczeń wdrożenia. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) | Pobierz adres URL dla punktu końcowego repozytorium git do klonowania i wypychania dla wdrożenia aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia usługi Azure SignalR Service można znaleźć w [dokumentacji usługi Azure SignalR Service](../signalr-reference-cli.md).
