---
title: Dostęp do usługi Azure Blob Storage przy użyciu usług Azure Databricks i Azure Key Vault
description: Z tego samouczka dowiesz się, jak uzyskać dostęp do Azure Blob Storage z usługi Azure Databricks przy użyciu tajnego Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: be1b0da23841b5a63ec044e04a5465e29345f9d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772203"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Samouczek: uzyskiwanie Azure Blob Storage przy użyciu Azure Databricks i Azure Key Vault

Z tego samouczka dowiesz się, jak uzyskać dostęp do Azure Blob Storage z Azure Databricks przy użyciu tajnego Azure Key Vault. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu i kontenera obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure
> * Tworzenie Key Vault i ustawianie tajnego
> * Tworzenie obszaru Azure Databricks i dodawanie Key Vault zakresu tajnego
> * Uzyskiwanie dostępu do kontenera obiektów blob z Azure Databricks roboczego

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Przed rozpoczęciem tego samouczka zainstaluj interfejs wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli-windows)

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Tworzenie konta magazynu i kontenera obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure

Najpierw należy utworzyć konto magazynu ogólnego przeznaczenia, aby używać obiektów blob. Jeśli nie masz grupy zasobów, [utwórz](/cli/azure/group#az_group_create)grupę przed uruchomieniem polecenia. Następujące polecenie tworzy i wyświetla metadane kontenera magazynu. Skopiuj **identyfikator**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Dane wyjściowe konsoli powyższego polecenia. Identyfikator jest wyróżniony na zielono, aby użytkownik końcowy widział.](../media/databricks-command-output-1.png)

Przed utworzeniem kontenera w celu przekazania obiektu blob należy przypisać do siebie rolę Współautor danych obiektu blob usługi [Storage.](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) W tym przykładzie rola zostanie przypisana do wcześniej wykonanego konta magazynu.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Teraz, po przypisaniu roli do konta magazynu, możesz utworzyć kontener dla obiektu blob.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Po utworzeniu kontenera możesz przekazać do tego kontenera obiekt blob (plik do wyboru). W tym przykładzie zostanie przekazany plik txt z ciągiem helloworld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Wylikuj listę obiektów blob w kontenerze, aby sprawdzić, czy kontener je zawiera.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Dane wyjściowe konsoli powyższego polecenia. Wyświetla plik, który został właśnie zapisany w kontenerze.](../media/databricks-command-output-2.png)

Pobierz wartość **key1** kontenera magazynu przy użyciu następującego polecenia. Skopiuj wartość w dół.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Dane wyjściowe konsoli powyższego polecenia. Wartość klucza key1 jest wyróżniona w zielonym polu.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Tworzenie Key Vault i ustawianie tajnego

Utworzysz nową Key Vault za pomocą następującego polecenia. To polecenie spowoduje również wyświetlenie metadanych Key Vault. Skopiuj identyfikator **i identyfikator** **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Dane ](../media/databricks-command-output-4.png)
 ![ wyjściowe konsoli obrazu powyższego polecenia. Identyfikator i identyfikator vaultUri są wyróżnione na zielono, aby użytkownik zobaczył.](../media/databricks-command-output-5.png)

Aby utworzyć klucz tajny, użyj następującego polecenia. Ustaw wartość klucza tajnego na **wartość key1** z konta magazynu.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Tworzenie obszaru Azure Databricks i dodawanie Key Vault zakresu tajnego

Tej sekcji nie można ukończyć za pomocą wiersza polecenia. Skorzystaj z tego [przewodnika](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Musisz uzyskać dostęp do [Azure Portal:](https://ms.portal.azure.com/#home)

1. Tworzenie zasobu Azure Databricks zasobów
1. Uruchamianie obszaru roboczego
1. Tworzenie zakresu Key Vault tajnego z kopią zapasową

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Uzyskiwanie dostępu do kontenera obiektów blob z Azure Databricks roboczego

Nie można ukończyć tej sekcji za pomocą wiersza polecenia. Skorzystaj z tego [przewodnika](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Obszar roboczy aplikacji musi Azure Databricks do:

1. Tworzenie nowego **klastra**
1. Tworzenie nowego **notesu**
1. Wypełnij odpowiednie pola w skrypcie języka Python
1. Uruchamianie skryptu języka Python

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Następne kroki

Upewnij się, że Key Vault można odzyskać:
> [!div class="nextstepaction"]
> [Czyszczenie zasobów](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)