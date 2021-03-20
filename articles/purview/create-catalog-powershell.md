---
title: 'Szybki Start: Tworzenie konta usługi Azure kontrolą za pomocą usługi Azure PowerShell/interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)'
description: W tym przewodniku szybki start opisano sposób tworzenia konta usługi Azure kontrolą przy użyciu interfejsu wiersza polecenia Azure PowerShell/platformy Azure.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880003"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Szybki Start: Tworzenie konta usługi Azure kontrolą za pomocą Azure PowerShell/interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym przewodniku szybki start utworzysz konto usługi Azure kontrolą przy Azure PowerShell użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Zainstaluj Azure PowerShell lub interfejs wiersza polecenia platformy Azure na komputerze klienckim, aby wdrożyć szablon: [wdrażanie w wierszu poleceń](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

W razie potrzeby wykonaj następujące kroki, aby skonfigurować swoją subskrypcję, aby umożliwić uruchamianie usługi Azure kontrolą w ramach subskrypcji:

   1. W Azure Portal Wyszukaj i wybierz pozycję **subskrypcje**.

   1. Z listy subskrypcji wybierz subskrypcję, której chcesz użyć. Wymagane jest uprawnienie dostępu administracyjnego dla subskrypcji.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania subskrypcji w Azure Portal.":::

   1. W przypadku subskrypcji wybierz pozycję **dostawcy zasobów**. W okienku **dostawcy zasobów** Wyszukaj i zarejestruj wszystkich trzech dostawców zasobów: 
       1. **Microsoft. kontrolą**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Jeśli nie są zarejestrowane, zarejestruj je, wybierając pozycję **zarejestruj**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania dostawcy zasobów usługi Azure kontrolą firmy Microsoft w Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Tworzenie wystąpienia konta usługi Azure kontrolą

1. Zaloguj się przy użyciu poświadczeń platformy Azure

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

1. Utwórz grupę zasobów dla konta usługi kontrolą. Możesz pominąć ten krok, jeśli masz już jeden:

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

1. Utwórz plik szablonu kontrolą, taki jak `purviewtemplate.json` . Możesz zaktualizować `name` , `location` i `capacity` ( `4` lub `16` ):

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

1. Wdróż szablon kontrolą

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

1. Polecenie wdrożenia zwraca wyniki. Wyszukaj `ProvisioningState` , aby sprawdzić, czy wdrożenie zakończyło się pomyślnie.
    
## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure kontrolą.

Przejdź do następnego artykułu, aby dowiedzieć się, jak umożliwić użytkownikom dostęp do konta usługi Azure kontrolą. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure kontrolą](catalog-permissions.md)