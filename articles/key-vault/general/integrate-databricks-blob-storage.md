---
title: Dostęp do usługi Azure Blob Storage przy użyciu usług Azure Databricks i Azure Key Vault
description: W tym samouczku dowiesz się, jak uzyskać dostęp do usługi Azure Blob Storage z Azure Databricks przy użyciu klucza tajnego przechowywanego w Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: fc33b24aac8964d0a8390b4b38c5e1862ed639a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198918"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Blob Storage przy użyciu Azure Databricks i Azure Key Vault

W tym samouczku dowiesz się, jak uzyskać dostęp do usługi Azure Blob Storage z Azure Databricks przy użyciu klucza tajnego przechowywanego w Azure Key Vault. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu i kontenera obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure
> * Utwórz Key Vault i Ustaw klucz tajny
> * Utwórz obszar roboczy Azure Databricks i Dodaj Key Vault zakres tajny
> * Dostęp do kontenera obiektów blob z obszaru roboczego Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-windows).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Tworzenie konta magazynu i kontenera obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure

Aby używać obiektów blob, należy najpierw utworzyć konto magazynu ogólnego przeznaczenia. Jeśli nie masz [grupy zasobów](/cli/azure/group#az-group-create), utwórz ją przed uruchomieniem polecenia. Następujące polecenie tworzy i wyświetla metadane kontenera magazynu. Skopiuj **Identyfikator**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Dane wyjściowe konsoli powyższego polecenia. Identyfikator jest wyróżniony w kolorze zielonym dla użytkownika końcowego, aby zobaczyć.](../media/databricks-command-output-1.png)

Przed utworzeniem kontenera do przekazania obiektu BLOB do programu należy przypisać rolę [współautor danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) do siebie. W ramach tego przykładu rola zostanie przypisana do konta magazynu, które zostało wcześniej utworzone.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Teraz, gdy przypiszesz rolę do konta magazynu, możesz utworzyć kontener dla obiektu BLOB.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Po utworzeniu kontenera można przekazać obiekt BLOB (wybrany plik) do tego kontenera. W tym przykładzie plik txt z usługą HelloWorld zostanie przekazany.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Utwórz listę obiektów BLOB w kontenerze, aby sprawdzić, czy kontener jest w nim.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Dane wyjściowe konsoli powyższego polecenia. Wyświetla plik, który był właśnie przechowywany w kontenerze.](../media/databricks-command-output-2.png)

Pobierz wartość **Klucz1** kontenera magazynu przy użyciu następującego polecenia. Skopiuj wartość w dół.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Dane wyjściowe konsoli powyższego polecenia. Wartość Klucz1 jest wyróżniona w zielonym polu.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Utwórz Key Vault i Ustaw klucz tajny

Utworzysz Key Vault przy użyciu poniższego polecenia. To polecenie spowoduje również wyświetlenie metadanych Key Vault. Skopiuj **identyfikatory** i **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![](../media/databricks-command-output-4.png)
 ![ Dane wyjściowe konsoli obrazów powyższego polecenia. Identyfikator i vaultUri są wyróżnione w kolorze zielonym, aby użytkownik mógł je zobaczyć.](../media/databricks-command-output-5.png)

Aby utworzyć klucz tajny, użyj następującego polecenia. Ustaw wartość wpisu tajnego na wartość **Klucz1** z konta magazynu.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Utwórz obszar roboczy Azure Databricks i Dodaj Key Vault zakres tajny

Tej sekcji nie można wykonać za pomocą wiersza polecenia. Postępuj zgodnie z tym [przewodnikiem](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Musisz uzyskać dostęp do [Azure Portal](https://ms.portal.azure.com/#home) , aby:

1. Tworzenie zasobu Azure Databricks
1. Uruchom obszar roboczy
1. Tworzenie tajnego zakresu Key Vault-kopia zapasowa

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Dostęp do kontenera obiektów blob z obszaru roboczego Azure Databricks

Tej sekcji nie można wykonać za pomocą wiersza polecenia. Postępuj zgodnie z tym [przewodnikiem](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Musisz użyć obszaru roboczego Azure Databricks, aby:

1. Tworzenie **nowego klastra**
1. Tworzenie **nowego notesu**
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

Upewnij się, że Key Vault jest możliwe do odzyskania:
> [!div class="nextstepaction"]
> [Czyszczenie zasobów](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)