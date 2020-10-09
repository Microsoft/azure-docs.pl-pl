---
title: Samouczek — przywracanie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przywrócić dysk i utworzyć odzyskaną maszynę wirtualną na platformie Azure przy użyciu usługi Backup i Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 861c911e84c9de02467d443751902e71d2504422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324993"
---
# <a name="restore-a-vm-with-azure-cli"></a>Przywracanie maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub poszczególne pliki. W tym artykule opisano sposób przywracania całej maszyny wirtualnej przy użyciu interfejsu wiersza polecenia. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyświetlanie listy i wybieranie punktów odzyskiwania
> * Przywracanie dysku z punktu odzyskiwania
> * Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Aby uzyskać informacje na temat przywracania dysku i tworzenia odzyskanej maszyny wirtualnej przy użyciu programu PowerShell, zobacz [Back up and restore Azure VMs with PowerShell (Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell)](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego samouczka jest wymagana maszyna wirtualna z systemem Linux, chroniona przy użyciu usługi Azure Backup. Aby zasymulować proces przypadkowego usunięcia maszyny wirtualnej i jej odzyskania, utworzysz maszynę wirtualną na podstawie dysku w punkcie odzyskiwania. Jeśli potrzebujesz maszyny wirtualnej z systemem Linux chronionej przy użyciu usługi Azure Backup, zobacz [Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Omówienie usługi Backup

Po zainicjowaniu tworzenia kopii zapasowej przez platformę Azure rozszerzenie kopii zapasowej na maszynie wirtualnej tworzy migawkę punktu w czasie. Rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej w momencie pierwszego żądania utworzenia kopii zapasowej. Azure Backup może również utworzyć migawkę magazynu bazowego, jeśli maszyna wirtualna nie jest uruchomiona podczas tworzenia kopii zapasowej.

Domyślnie usługa Azure Backup tworzy kopię zapasową spójną na poziomie systemu plików. Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu usługi Recovery Services. Aby zmaksymalizować wydajność, usługa Azure Backup rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

## <a name="list-available-recovery-points"></a>Wyświetlanie listy dostępnych punktów odzyskiwania

Aby przywrócić dysk, należy wybrać punkt odzyskiwania będący źródłem danych do odzyskania. Domyślne zasady przewidują tworzenie punktów odzyskiwania codziennie i przechowywanie ich przez 30 dni, dzięki czemu możesz wybrać określony punkt w czasie do odzyskania z dostępnego zestawu punktów odzyskiwania.

Aby wyświetlić listę dostępnych punktów odzyskiwania, użyj polecenia [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). Do odzyskiwania dysków używana jest **nazwa** punktu odzyskiwania. W tym samouczku użyjemy najnowszego dostępnego punktu odzyskiwania. Parametr `--query [0].name` wybiera nazwę najnowszego punktu odzyskiwania, jak pokazano poniżej:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Przywracanie dysku maszyny wirtualnej

> [!IMPORTANT]
> Bardzo zdecydowanie zaleca się użycie polecenia AZ CLI Version 2.0.74 lub nowszego, aby uzyskać wszystkie zalety szybkiego przywracania, w tym przywracania dysków zarządzanych. Najlepiej jest używać najnowszej wersji.

### <a name="managed-disk-restore"></a>Przywracanie dysku zarządzanego

Jeśli kopia zapasowa maszyny wirtualnej ma dyski zarządzane i chcesz przywrócić dyski zarządzane z punktu odzyskiwania, najpierw Podaj konto usługi Azure Storage. To konto magazynu służy do przechowywania konfiguracji maszyny wirtualnej i szablonu wdrożenia, którego można później użyć do wdrożenia maszyny wirtualnej z przywróconych dysków. Następnie podaj docelową grupę zasobów dla dysków zarządzanych, do których mają zostać przywrócone.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp *ciąg myrecoverypointname nazwą* nazwą punktu odzyskiwania uzyskaną w danych wyjściowych z poprzedniego polecenia [AZ Backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) . ***Podaj również docelową grupę zasobów, do której zostaną przywrócone dyski zarządzane***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Jeśli nie podano **grupy Target-Resource-Group** , dyski zarządzane zostaną przywrócone jako dyski niezarządzane do danego konta magazynu. Będzie to miało znaczący wpływ na czas przywracania, ponieważ czas potrzebny do przywrócenia dysków jest całkowicie zależny od danego konta magazynu. Skorzystaj z zalet natychmiastowego przywrócenia tylko wtedy, gdy zostanie określony parametr Target-Resource-Group. Jeśli zamiarem jest przywrócenie dysków zarządzanych jako niezarządzanych, nie należy podawać parametru **Target-Resource-Group** i zamiast tego podać parametr **"Przywracanie jako niezarządzany-dysk"** , jak pokazano poniżej. Ten parametr jest dostępny w AZ 3.4.0 lub nowszym.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Spowoduje to przywrócenie dysków zarządzanych jako dysków niezarządzanych do danego konta magazynu i nie będzie korzystać z funkcji przywracania natychmiastowej. W przyszłych wersjach interfejsu wiersza polecenia będzie wymagane podanie parametru " **Target-Resource-Group"** lub " **Restore as Unmanaged-Disk** ".

### <a name="unmanaged-disks-restore"></a>Przywracanie dysków niezarządzanych

Jeśli kopia zapasowa maszyny wirtualnej zawiera dyski niezarządzane i chcesz przywrócić dyski z punktu odzyskiwania, najpierw Podaj konto usługi Azure Storage. To konto magazynu służy do przechowywania konfiguracji maszyny wirtualnej i szablonu wdrożenia, którego można później użyć do wdrożenia maszyny wirtualnej z przywróconych dysków. Domyślnie dyski niezarządzane zostaną przywrócone do ich oryginalnych kont magazynu. Jeśli chcesz przywrócić wszystkie dyski niezarządzane do jednego miejsca, można również użyć danego konta magazynu jako lokalizacji przejściowej dla tych dysków.

Dodatkowe czynności obejmują tworzenie maszyny wirtualnej przy użyciu przywróconego dysku.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp ciąg *myRecoveryPointName* nazwą punktu odzyskiwania uzyskaną wcześniej przy użyciu polecenia [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Jak wspomniano powyżej, dyski niezarządzane zostaną przywrócone do oryginalnego konta magazynu. Zapewnia to najlepszą wydajność przywracania. Jeśli jednak wszystkie dyski niezarządzane muszą zostać przywrócone do danego konta magazynu, użyj odpowiedniej flagi, jak pokazano poniżej.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Dane wyjściowe są podobne do następującego przykładu informującego, że zadanie przywracania jest w toku (*InProgress*):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Po ukończeniu *stanu* zadania przywracania wymagane informacje ( *Completed*konfiguracja maszyny wirtualnej i szablon wdrożenia) zostały przywrócone do konta magazynu.

## <a name="create-a-vm-from-the-restored-disk"></a>Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Ostatnim krokiem jest utworzenie maszyny wirtualnej na podstawie przywróconych dysków. Aby utworzyć maszynę wirtualną, można użyć szablonu wdrożenia pobranego do danego konta magazynu.

### <a name="fetch-the-job-details"></a>Pobierz szczegóły zadania

Szczegóły zadania wynikowego zawierają identyfikator URI szablonu, który można zbadać i wdrożyć. Aby uzyskać więcej informacji na temat wyzwolonego zadania, należy użyć polecenia Pokaż zadanie.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Dane wyjściowe tego zapytania będą zawierać wszystkie szczegóły, ale interesuje tylko zawartość konta magazynu. Można użyć [funkcji zapytania](/cli/azure/query-azure-cli) interfejsu wiersza polecenia platformy Azure, aby pobrać odpowiednie szczegóły

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Pobierz szablon wdrożenia

Szablon nie jest dostępny bezpośrednio, ponieważ znajduje się na koncie magazynu klienta i podanym kontenerze. Do uzyskania dostępu do tego szablonu potrzebny jest pełny adres URL (wraz z tymczasowym tokenem SAS).

Najpierw Wyodrębnij identyfikator URI obiektu BLOB szablonu ze szczegółowych informacji o zadaniu

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Identyfikator URI obiektu BLOB szablonu będzie miał następujący format i Wyodrębnij nazwę szablonu

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Dlatego nazwa szablonu z powyższego przykładu będzie zawierać nazwę ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` kontenera ```myVM-daa1931199fd4a22ae601f46d8812276```

Uzyskaj teraz token sygnatury dostępu współdzielonego dla tego kontenera i szablonu zgodnie z opisem w [tym miejscu](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Wdróż szablon, aby utworzyć maszynę wirtualną

Teraz Wdróż szablon, aby utworzyć maszynę wirtualną zgodnie z opisem w [tym miejscu](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Aby potwierdzić utworzenie maszyny wirtualnej na podstawie odzyskanego dysku, wyświetl listę maszyn wirtualnych w grupie zasobów przy użyciu polecenia [az vm list](/cli/azure/vm#az-vm-list), jak pokazano poniżej:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem przywrócono dysk z punktu odzyskiwania i utworzono na jego podstawie maszynę wirtualną. W tym samouczku omówiono:

> [!div class="checklist"]
>
> * Wyświetlanie listy i wybieranie punktów odzyskiwania
> * Przywracanie dysku z punktu odzyskiwania
> * Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Przejdź do następnego samouczka, aby dowiedzieć się więcej o przywracaniu poszczególnych plików z punktu odzyskiwania.

> [!div class="nextstepaction"]
> [Przywracanie plików na maszynę wirtualną na platformie Azure](tutorial-restore-files.md)
