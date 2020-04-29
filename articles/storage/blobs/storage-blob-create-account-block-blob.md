---
title: Tworzenie konta usługi Block BLOB Storage — Azure Storage | Microsoft Docs
description: Pokazuje, jak utworzyć konto usługi Azure BlockBlobStorage z charakterystyką wydajności Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536908"
---
# <a name="create-a-blockblobstorage-account"></a>Utwórz konto BlockBlobStorage

Rodzaj konta BlockBlobStorage umożliwia tworzenie blokowych obiektów blob z charakterystyką wydajności Premium. Ten typ konta magazynu jest zoptymalizowany pod kątem obciążeń z wysoką stawką transakcji lub wymagających bardzo szybkiego dostępu. W tym artykule przedstawiono sposób tworzenia konta usługi BlockBlobStorage przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Aby uzyskać więcej informacji o kontach BlockBlobStorage, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Ten artykuł instruktażowy wymaga modułu Azure PowerShell AZ Version 1.2.0 lub nowszego. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza poleceń platformy Azure na jeden z dwóch sposobów:

- Poleceń interfejsu wiersza polecenia można uruchomić z poziomu Azure Portal w Azure Cloud Shell.
- Interfejs wiersza polecenia można zainstalować i uruchomić polecenie CLI lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Przycisk uruchamia interaktywną powłokę, której można użyć, aby wykonać kroki opisane w tym artykule:

[![Zrzut ekranu przedstawiający okno Cloud Shell w portalu](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać lokalnie. Ten artykuł z artykułu wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Utwórz konto BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto BlockBlobStorage w Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz pozycję **wszystkie usługi** > kategoria **magazynu** > **konta magazynu**.

1. W obszarze **konta magazynu**wybierz pozycję **Dodaj**.

1. W polu **subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.

1. W polu **Grupa zasobów** wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.

1. W polu **nazwa konta magazynu** wprowadź nazwę konta. Należy zwrócić uwagę na następujące wytyczne:

   - Nazwa musi być unikatowa na platformie Azure.
   - Nazwa musi mieć długość od 3 do 24 znaków.
   - Nazwa może zawierać tylko cyfry i małe litery.

1. W polu **Lokalizacja** wybierz lokalizację konta magazynu lub Użyj domyślnej lokalizacji.

1. W pozostałych ustawieniach skonfiguruj następujące opcje:

   |Pole     |Wartość  |
   |---------|---------|
   |**Wydajność**    |  Wybierz pozycję **Premium**.   |
   |**Rodzaj konta**    | Wybierz pozycję **BlockBlobStorage**.      |
   |**Replikacja**    |  Pozostaw domyślne ustawienie **magazynu lokalnie nadmiarowego (LRS)**.      |

   ![Przedstawia interfejs użytkownika portalu do tworzenia konta blokowego magazynu obiektów BLOB](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia konta magazynu.

1. Wybierz przycisk **Utwórz**.

## <a name="azure-powershell"></a>[Program Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator).

1. Uruchom następujące polecenie, aby upewnić się, że zainstalowano najnowszą wersję modułu `Az` PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otwórz nową konsolę programu PowerShell i zaloguj się przy użyciu konta platformy Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. W razie konieczności Utwórz nową grupę zasobów. Zastąp wartości w cudzysłowach i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w cudzysłowach i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto blokowego obiektu BLOB przy użyciu interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0. 2.0.46 lub nowszą wersję. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zaloguj się do subskrypcji platformy Azure.

   ```azurecli
   az login
   ```

1. W razie konieczności Utwórz nową grupę zasobów. Zastąp wartości w nawiasach (w tym nawiasy klamrowe) i uruchom następujące polecenie.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w nawiasach (w tym nawiasy klamrowe) i uruchom następujące polecenie.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Azure Resource Manager przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
