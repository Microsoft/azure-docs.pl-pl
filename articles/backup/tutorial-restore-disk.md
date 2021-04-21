---
title: Samouczek — przywracanie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przywrócić dysk i utworzyć odzyskaną maszynę wirtualną na platformie Azure przy użyciu usługi Backup i Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f4d70f43f76c3a72cd8e53037d06d32e61c3cdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768513"
---
# <a name="restore-a-vm-with-azure-cli"></a>Przywracanie maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub poszczególne pliki. W tym artykule opisano sposób przywracania całej maszyny wirtualnej przy użyciu interfejsu wiersza polecenia. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyświetlanie listy i wybieranie punktów odzyskiwania
> * Przywracanie dysku z punktu odzyskiwania
> * Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Aby uzyskać informacje na temat przywracania dysku i tworzenia odzyskanej maszyny wirtualnej przy użyciu programu PowerShell, zobacz [Back up and restore Azure VMs with PowerShell (Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell)](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

 - Do wykonania tego samouczka jest wymagana maszyna wirtualna z systemem Linux, chroniona przy użyciu usługi Azure Backup. Aby zasymulować proces przypadkowego usunięcia maszyny wirtualnej i jej odzyskania, utworzysz maszynę wirtualną na podstawie dysku w punkcie odzyskiwania. Jeśli potrzebujesz maszyny wirtualnej z systemem Linux chronionej przy użyciu usługi Azure Backup, zobacz [Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Omówienie usługi Backup

Po zainicjowaniu tworzenia kopii zapasowej przez platformę Azure rozszerzenie kopii zapasowej na maszynie wirtualnej tworzy migawkę punktu w czasie. Rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej w momencie pierwszego żądania utworzenia kopii zapasowej. Azure Backup może również utworzyć migawkę bazowego magazynu, jeśli maszyna wirtualna nie jest uruchomiona podczas tworzenia kopii zapasowej.

Domyślnie usługa Azure Backup tworzy kopię zapasową spójną na poziomie systemu plików. Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu usługi Recovery Services. Aby zmaksymalizować wydajność, usługa Azure Backup rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

## <a name="list-available-recovery-points"></a>Wyświetlanie listy dostępnych punktów odzyskiwania

Aby przywrócić dysk, należy wybrać punkt odzyskiwania będący źródłem danych do odzyskania. Domyślne zasady przewidują tworzenie punktów odzyskiwania codziennie i przechowywanie ich przez 30 dni, dzięki czemu możesz wybrać określony punkt w czasie do odzyskania z dostępnego zestawu punktów odzyskiwania.

Aby wyświetlić listę dostępnych punktów odzyskiwania, użyj polecenia [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list). Do odzyskiwania dysków używana jest **nazwa** punktu odzyskiwania. W tym samouczku użyjemy najnowszego dostępnego punktu odzyskiwania. Parametr `--query [0].name` wybiera nazwę najnowszego punktu odzyskiwania, jak pokazano poniżej:

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
> Zdecydowanie zaleca się używanie interfejsu wiersza polecenia Az w wersji 2.0.74 lub nowszej, aby uzyskać wszystkie korzyści wynikające z szybkiego przywracania, w tym przywracania dysku zarządzanego. Jest to najlepsze rozwiązanie, jeśli zawsze używasz najnowszej wersji.

### <a name="managed-disk-restore"></a>Przywracanie dysku zarządzanego

Jeśli maszyna wirtualna z kopią zapasową ma dyski zarządzane, a celem jest przywrócenie dysków zarządzanych z punktu odzyskiwania, należy najpierw podać konto usługi Azure Storage. To konto magazynu jest używane do przechowywania konfiguracji maszyny wirtualnej i szablonu wdrożenia, których można później użyć do wdrożenia maszyny wirtualnej z przywróconych dysków. Następnie należy również podać docelową grupę zasobów dla dysków zarządzanych, do których mają zostać przywrócone.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp *myRecoveryPointName* nazwą punktu odzyskiwania uzyskaną w danych wyjściowych poprzedniego polecenia [az backup recoverypoint list.](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) ***Podaj również docelową grupę zasobów, do której dyski zarządzane są przywracane do systemu***.

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
    > Jeśli **grupa zasobów docelowych** nie zostanie podana, dyski zarządzane zostaną przywrócone jako dyski nieza zarządzania na danym koncie magazynu. Będzie to mieć znaczące konsekwencje dla czasu przywracania, ponieważ czas przywracania dysków całkowicie zależy od danego konta magazynu. Zyskasz korzyść natychmiastowego przywracania tylko wtedy, gdy zostanie podany parametr target-resource-group. Jeśli zamiarem jest przywrócenie dysków zarządzanych jako nieza zarządzanie, nie poświęć parametru **target-resource-group** i zamiast tego podaj parametr **restore-as-unmanaged-disk,** jak pokazano poniżej. Ten parametr jest dostępny w wersji az 3.4.0 lub wersji 3.4.0.

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

Spowoduje to przywrócenie dysków zarządzanych jako dysków nieza zarządzanych na danym koncie magazynu i nie będzie korzystanie z funkcji natychmiastowego przywracania. W przyszłych wersjach interfejsu wiersza polecenia będzie wymagane podanie parametru **target-resource-group** lub **parametru restore-as-unmanaged-disk.**

### <a name="unmanaged-disks-restore"></a>Przywracanie dysków niezamanagedowych

Jeśli maszyna wirtualna z kopią zapasową ma dyski nieza zarządzania, a celem jest przywrócenie dysków z punktu odzyskiwania, należy najpierw podać konto usługi Azure Storage. To konto magazynu służy do przechowywania konfiguracji maszyny wirtualnej i szablonu wdrożenia, których można później użyć do wdrożenia maszyny wirtualnej z przywróconych dysków. Domyślnie dyski niezamaniowane zostaną przywrócone do oryginalnych kont magazynu. Jeśli chcesz przywrócić wszystkie dyski niezamaniowane do jednego miejsca, wówczas dane konto magazynu może być również używane jako lokalizacja przemieszczania dla tych dysków.

Dodatkowe czynności obejmują tworzenie maszyny wirtualnej przy użyciu przywróconego dysku.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp ciąg *myRecoveryPointName* nazwą punktu odzyskiwania uzyskaną wcześniej przy użyciu polecenia [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Jak wspomniano powyżej, dyski niezamaniowane zostaną przywrócone do oryginalnego konta magazynu. Zapewnia to najlepszą wydajność przywracania. Jeśli jednak należy przywrócić wszystkie dyski niezaszybowane na danym koncie magazynu, użyj odpowiedniej flagi, jak pokazano poniżej.

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

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az_backup_job_list):

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

Gdy stan *zadania* przywracania zostanie zgłaszany jako Ukończono, niezbędne informacje (konfiguracja maszyny wirtualnej i szablon wdrożenia) zostaną przywrócone na konto magazynu.

## <a name="create-a-vm-from-the-restored-disk"></a>Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Ostatnim krokiem jest utworzenie maszyny wirtualnej z przywróconych dysków. Aby utworzyć maszynę wirtualną, możesz użyć szablonu wdrożenia pobranego na podane konto magazynu.

### <a name="fetch-the-job-details"></a>Pobieranie szczegółów zadania

Wynikowe szczegóły zadania dają szablonowi URI, który można zbadać i wdrożyć. Użyj polecenia job show, aby uzyskać więcej szczegółów dotyczących wyzwolone zadanie przywracania.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Dane wyjściowe tego zapytania będą dawać wszystkie szczegóły, ale interesuje nas tylko zawartość konta magazynu. Aby pobrać odpowiednie [szczegóły, możemy](/cli/azure/query-azure-cli) użyć możliwości wykonywania zapytań interfejsu wiersza polecenia platformy Azure

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

### <a name="fetch-the-deployment-template"></a>Pobieranie szablonu wdrożenia

Szablon nie jest bezpośrednio dostępny, ponieważ znajduje się on w ramach konta magazynu klienta i danego kontenera. Aby uzyskać dostęp do tego szablonu, potrzebny jest pełny adres URL (wraz z tymczasowym tokenem SAS).

Najpierw wyodrębnij szablonowy obiekt blob URI ze szczegółów zadania

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Szablonowy adres URI obiektu blob będzie miał ten format i wyodrębni nazwę szablonu

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Tak więc nazwą szablonu z powyższego przykładu będzie , ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` a nazwą kontenera będzie ```myVM-daa1931199fd4a22ae601f46d8812276```

Teraz uzyskaj token SAS dla tego kontenera i szablonu zgodnie z informacjami w tym [miejscu](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

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

### <a name="deploy-the-template-to-create-the-vm"></a>Wdrażanie szablonu w celu utworzenia maszyny wirtualnej

Teraz wd wdrażaj szablon, aby utworzyć maszynę wirtualną, jak [wyjaśniono tutaj.](../azure-resource-manager/templates/deploy-cli.md)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Aby potwierdzić utworzenie maszyny wirtualnej na podstawie odzyskanego dysku, wyświetl listę maszyn wirtualnych w grupie zasobów przy użyciu polecenia [az vm list](/cli/azure/vm#az_vm_list), jak pokazano poniżej:

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
