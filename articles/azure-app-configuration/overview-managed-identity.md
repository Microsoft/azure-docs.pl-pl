---
title: Konfigurowanie zarządzanych tożsamości za pomocą usługi Azure App Configuration
description: Dowiedz się, jak zarządzane tożsamości działają w konfiguracji aplikacji platformy Azure i jak skonfigurować tożsamość zarządzaną
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 270f8d471a95e67939a31357bc1cc056fe8ea4a1
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072802"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Jak używać tożsamości zarządzanych do konfiguracji aplikacji platformy Azure

W tym temacie pokazano, jak utworzyć zarządzaną tożsamość dla konfiguracji aplikacji platformy Azure. Zarządzana tożsamość z usługi Azure Active Directory (AAD) umożliwia usłudze Azure App Configuration łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę AAD, takich jak Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure. Nie wymaga to aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze AAD, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może mieć przyznane dwa typy tożsamości:

- **Tożsamość przypisana do systemu** jest powiązana z Twoim magazynem konfiguracji. Jest ona usuwana, jeśli magazyn konfiguracji został usunięty. Magazyn konfiguracji może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do Twojego magazynu konfiguracji. Magazyn konfiguracji może mieć wiele tożsamości przypisanych do użytkownika.

## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Utworzenie magazynu konfiguracji aplikacji z tożsamością przypisaną do systemu wymaga ustawienia dodatkowej właściwości w sklepie.

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [AZ AppConfig Identity Assign] względem istniejącego magazynu konfiguracji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2,1 lub nowszej), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia.

Poniższe kroki przeprowadzą Cię przez proces tworzenia magazynu konfiguracji aplikacji i przypisywania go tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz magazyn konfiguracji aplikacji przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia z konfiguracją aplikacji platformy Azure, zobacz [przykłady interfejsu wiersza polecenia konfiguracji aplikacji](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Uruchom polecenie [AZ AppConfig Identity Assign] , aby utworzyć tożsamość przypisaną przez system dla tego magazynu konfiguracji:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej do użytkownika

Utworzenie magazynu konfiguracji aplikacji z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie przypisania jej identyfikatora zasobu do magazynu.

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [AZ AppConfig Identity Assign] względem istniejącego magazynu konfiguracji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.31 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia.

Poniższe kroki przeprowadzą Cię przez proces tworzenia tożsamości przypisanej do użytkownika i magazynu konfiguracji aplikacji, a następnie przypisania tożsamości do magazynu przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz magazyn konfiguracji aplikacji przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia z konfiguracją aplikacji platformy Azure, zobacz [przykłady interfejsu wiersza polecenia konfiguracji aplikacji](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Utwórz tożsamość przypisaną przez użytkownika o nazwie `myUserAssignedIdentity` przy użyciu interfejsu wiersza polecenia.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    W danych wyjściowych tego polecenia Zanotuj wartość `id` właściwości.

1. Uruchom polecenie [AZ AppConfig Identity Assign] , aby przypisać nową tożsamość przypisaną przez użytkownika do tego magazynu konfiguracji. Użyj wartości `id` Właściwości zanotowanej w poprzednim kroku.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Usuwanie tożsamości

Tożsamość przypisana przez system można usunąć, wyłączając funkcję za pomocą polecenia [AZ AppConfig Identity Remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) w interfejsie wiersza polecenia platformy Azure. Tożsamości przypisane do użytkownika można usuwać pojedynczo. Usunięcie tożsamości przypisanej do systemu w ten sposób spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi AAD po usunięciu zasobu aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration](quickstart-aspnet-core-app.md)

[AZ AppConfig Identity Assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login