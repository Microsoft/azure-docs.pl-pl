---
title: Samouczek — magazynowanie stanu Terraform w usłudze Azure Storage
description: Wprowadzenie do przechowywania stanu Terraform w usłudze Azure Storage.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: c94c66969c517bd0e51117ab7c6ed6a889149518
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459019"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Samouczek: Przechowywanie stanu Terraform w usłudze Azure Storage

Stan terraform służy do uzgadniania wdrożonych zasobów z konfiguracjami Terraform. Stan umożliwia Terraform wiedzieć, jakie zasoby platformy Azure, aby dodać, zaktualizować lub usunąć. Domyślnie stan Terraform jest przechowywany lokalnie `terraform apply` po uruchomieniu polecenia. Ta konfiguracja nie jest idealna z następujących powodów:

- Stan lokalny nie działa dobrze w środowisku zespołowym lub współpracy.
- Stan terraform może zawierać poufne informacje.
- Przechowywanie stanu lokalnie zwiększa ryzyko niezamierzonego usunięcia.

Terraform obsługuje utrwalanie stanu w magazynie zdalnym. Jednym z takich obsługiwanych zaplecza jest usługa Azure Storage. W tym dokumencie pokazano, jak skonfigurować i używać usługi Azure Storage w tym celu.

## <a name="configure-storage-account"></a>Konfigurowanie konta magazynu

Przed użyciem usługi Azure Storage jako zaplecza należy utworzyć konto magazynu. Konto magazynu można utworzyć za pomocą witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure lub samego terraform. Użyj poniższego przykładu, aby skonfigurować konto magazynu za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Zanotuj nazwę konta magazynu, nazwę kontenera i klucz dostępu do magazynu. Te wartości są potrzebne podczas konfigurowania stanu zdalnego.

## <a name="configure-state-back-end"></a>Konfigurowanie zaplecza stanu

Stan Terraform zaplecza jest skonfigurowany `terraform init` po uruchomieniu polecenia. Następujące dane są potrzebne do skonfigurowania stanu zaplecza:

- **storage_account_name:** Nazwa konta usługi Azure Storage.
- **container_name**: Nazwa kontenera obiektów blob.
- **klucz**: Nazwa pliku magazynu stanu, który ma zostać utworzony.
- **access_key:** Klucz dostępu do magazynu.

Każda z tych wartości może być określona w pliku konfiguracyjnym Terraform lub w wierszu polecenia. Zaleca się użycie zmiennej środowiskowej `access_key` dla wartości. Użycie zmiennej środowiskowej zapobiega zapisaniu klucza na dysku.

Utwórz zmienną `ARM_ACCESS_KEY` środowiskową o nazwie o wartości klucza dostępu usługi Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Aby dodatkowo chronić klucz dostępu do konta usługi Azure Storage, należy przechowywać go w usłudze Azure Key Vault. Zmienną środowiskową można następnie ustawić za pomocą polecenia podobnego do następującego. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [dokumentację usługi Azure Key Vault](../key-vault/secrets/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Aby skonfigurować program Terraform do używania zaplecza, należy wykonać następujące czynności:
- Dołącz `backend` blok konfiguracyjny `azurerm`z typem .
- Dodaj `storage_account_name` wartość do bloku konfiguracji.
- Dodaj `container_name` wartość do bloku konfiguracji.
- Dodaj `key` wartość do bloku konfiguracji.

W poniższym przykładzie konfiguruje terraform zaplecza i tworzy grupę zasobów platformy Azure.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Zainicjować konfigurację, wykonując następujące czynności:

1. Uruchom polecenie `terraform init`.
1. Uruchom polecenie `terraform apply`.

Teraz można znaleźć plik stanu w obiekcie blob usługi Azure Storage.

## <a name="state-locking"></a>Blokowanie stanu

Obiekty BLOB usługi Azure Storage są automatycznie blokowane przed każdą operacją, która zapisuje stan. Ten wzorzec zapobiega równoczesnych operacji stanu, które mogą powodować uszkodzenie. 

Aby uzyskać więcej informacji, zobacz [blokowanie stanu](https://www.terraform.io/docs/state/locking.html) w dokumentacji Terraform.

Blokadę można wyświetlić podczas sprawdzania obiektu blob za pośrednictwem witryny Azure portal lub innych narzędzi zarządzania platformy Azure.

![Obiekt blob platformy Azure z blokadą](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Dane przechowywane w obiekcie blob platformy Azure są szyfrowane przed utrwaleniem. W razie potrzeby Terraform pobiera stan z zaplecza i przechowuje go w pamięci lokalnej. Przy użyciu tego wzorca stan nigdy nie jest zapisywany na dysku lokalnym.

Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku.](../storage/common/storage-service-encryption.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z programu Terraform na platformie Azure](/azure/terraform)
