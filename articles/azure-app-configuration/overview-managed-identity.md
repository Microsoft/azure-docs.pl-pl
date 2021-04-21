---
title: Konfigurowanie tożsamości zarządzanych za pomocą Azure App Configuration
description: Dowiedz się, jak działają tożsamości zarządzane w Azure App Configuration i jak skonfigurować tożsamość zarządzaną
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764229"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Jak używać tożsamości zarządzanych na Azure App Configuration

W tym temacie opisano sposób tworzenia tożsamości zarządzanej dla Azure App Configuration. Tożsamość zarządzana z usługi Azure Active Directory (AAD) umożliwia Azure App Configuration łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę AAD, takich jak Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure. Nie wymaga to aprowizować ani obracać żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w usłudze AAD, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

Aplikacji można przyznać dwa typy tożsamości:

- Tożsamość **przypisana przez system jest** powiązana z magazynem konfiguracji. Zostanie on usunięty, jeśli magazyn konfiguracji zostanie usunięty. Magazyn konfiguracji może mieć tylko jedną tożsamość przypisaną przez system.
- Tożsamość **przypisana przez użytkownika to** autonomiczny zasób platformy Azure, który można przypisać do magazynu konfiguracji. Magazyn konfiguracji może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej przez system

Utworzenie App Configuration magazynu z tożsamością przypisaną przez system wymaga ustawienia dodatkowej właściwości w magazynie.

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia [az appconfig identity assign] względem istniejącego magazynu konfiguracji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj", który znajduje się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure (2.1 lub nowszą), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

Poniższe kroki będą zawierały instrukcje tworzenia magazynu App Configuration i przypisywania do niego tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz sklep App Configuration przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia z interfejsem Azure App Configuration, zobacz [App Configuration przykłady interfejsu wiersza polecenia:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Uruchom polecenie [az appconfig identity assign,] aby utworzyć tożsamość przypisaną przez system dla tego magazynu konfiguracji:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej przez użytkownika

Utworzenie App Configuration magazynu z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie przypisania jej identyfikatora zasobu do magazynu.

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az appconfig identity assign] względem istniejącego magazynu konfiguracji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj", który znajduje się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu](/cli/azure/install-azure-cli) wiersza polecenia platformy Azure (2.0.31 lub nowszą), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

W poniższych krokach przedstawiono proces tworzenia tożsamości przypisanej przez użytkownika i magazynu App Configuration, a następnie przypisywania tożsamości do sklepu przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz magazyn App Configuration przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia z interfejsem Azure App Configuration, zobacz [App Configuration przykłady interfejsu wiersza polecenia:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Utwórz tożsamość przypisaną przez użytkownika o nazwie `myUserAssignedIdentity` przy użyciu interfejsu wiersza polecenia.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    W danych wyjściowych tego polecenia zanotuj wartość `id` właściwości .

1. Uruchom polecenie [az appconfig identity assign,] aby przypisać nową tożsamość przypisaną przez użytkownika do tego magazynu konfiguracji. Użyj wartości właściwości `id` zanotowej w poprzednim kroku.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Usuwanie tożsamości

Tożsamość przypisaną przez system można usunąć, wyłączając funkcję za pomocą polecenia [az appconfig identity remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) w interfejsie wiersza polecenia platformy Azure. Tożsamości przypisane przez użytkownika można usuwać pojedynczo. Usunięcie w ten sposób tożsamości przypisanej przez system spowoduje również usunięcie jej z aplikacji AAD. Tożsamości przypisane przez system są również automatycznie usuwane z usługi AAD po usunięciu zasobu aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
