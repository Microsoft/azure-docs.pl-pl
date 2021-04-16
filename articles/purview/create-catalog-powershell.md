---
title: 'Szybki start: tworzenie konta usługi Azure Purview przy użyciu interfejsu wiersza Azure PowerShell/interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)'
description: W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Purview przy użyciu Azure PowerShell interfejsu wiersza polecenia platformy Azure.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530868"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Szybki start: tworzenie konta usługi Azure Purview przy użyciu interfejsu wiersza Azure PowerShell platformy Azure

> [!IMPORTANT]
> Usługa Azure Purview jest obecnie dostępna w wersji zapoznawczej. Dodatkowe warunki użytkowania wersji [zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) programu Microsoft Azure obejmują dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.

W tym przewodniku Szybki start utworzysz konto usługi Azure Purview przy użyciu Azure PowerShell/interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto użytkownika, za pomocą których logujesz się do platformy Azure, musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Zainstaluj Azure PowerShell interfejsu wiersza polecenia platformy Azure na komputerze klienckim, aby wdrożyć szablon: [Wdrożenie przy użyciu wiersza polecenia](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

W razie potrzeby wykonaj następujące kroki, aby skonfigurować subskrypcję, aby umożliwić uruchamianie usługi Azure Purview w ramach subskrypcji:

   1. W skrypcie Azure Portal wyszukaj i wybierz **pozycję Subskrypcje.**

   1. Z listy subskrypcji wybierz subskrypcję, której chcesz użyć. Wymagane jest uprawnienie dostępu administracyjnego dla subskrypcji.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania subskrypcji w Azure Portal.":::

   1. W przypadku subskrypcji wybierz pozycję **Dostawcy zasobów.** W **okienku Dostawcy zasobów** wyszukaj i zarejestruj wszystkich trzech dostawców zasobów: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Jeśli użytkownik nie jest zarejestrowany, zarejestruj go, wybierając pozycję **Zarejestruj.**

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania dostawcy zasobów Microsoft dot Azure Purview w Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Tworzenie wystąpienia konta usługi Azure Purview

1. Logowanie się przy użyciu poświadczeń platformy Azure

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Jeśli masz wiele subskrypcji platformy Azure, wybierz subskrypcję, której chcesz użyć:

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Utwórz grupę zasobów dla konta programu Purview. Możesz pominąć ten krok, jeśli już istnieje:

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Utwórz plik szablonu programu Purview, taki jak `purviewtemplate.json` . Możesz zaktualizować `name` , , i ( lub `location` `capacity` `4` `16` ):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Wdrażanie szablonu programu Purview

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Polecenie wdrożenia zwraca wyniki. Sprawdź, `ProvisioningState` czy wdrożenie zakończyło się pomyślnie.
    
## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Purview.

W następnym artykule dowiesz się, jak zezwolić użytkownikom na dostęp do konta usługi Azure Purview. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure Purview](catalog-permissions.md)
