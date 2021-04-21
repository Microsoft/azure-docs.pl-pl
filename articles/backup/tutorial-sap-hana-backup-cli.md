---
title: Samouczek — tworzenie kopii zapasowej SAP HANA DB na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak kopię zapasową baz danych SAP HANA działających na maszynie wirtualnej platformy Azure do magazynu usługi Azure Backup Recovery Services przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: bebfe852aaac965fc7d07371be889fe515e3da3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768512"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: back up SAP HANA databases in an Azure VM using Azure CLI (Samouczek: SAP HANA kopii zapasowych baz danych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pośrednictwem skryptów. W tej dokumentacji szczegółowo opisano sposób tworzenia kopii zapasowej bazy SAP HANA i wyzwalania kopii zapasowych na żądanie — a wszystko to przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./backup-azure-sap-hana-database.md).

W tym dokumencie przyjęto założenie, że masz już zainstalowaną SAP HANA na maszynie wirtualnej platformy Azure. (Maszynę wirtualną można [również utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure).](../virtual-machines/linux/quick-create-cli.md) Po ukończeniu tego samouczka będziesz wiedzieć, jak wykonać następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Rejestrowanie SAP HANA i odnajdywanie w nim baz danych
> * Włączanie tworzenia kopii zapasowej w SAP HANA danych
> * Wyzwalanie kopii zapasowej na żądanie

Zapoznaj się ze [scenariuszami, które obecnie obsługujemy dla](./sap-hana-backup-support-matrix.md#scenario-support) SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services to logiczny kontener, który przechowuje dane kopii zapasowej dla każdego chronionego zasobu, takiego jak maszyny wirtualne platformy Azure lub obciążenia uruchomione na maszynach wirtualnych platformy Azure — takich jak bazy danych SQL lub HANA. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Utwórz magazyn usługi Recovery Services za pomocą polecenia [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create). Określ taką samą grupę zasobów i lokalizację, jak w przypadku maszyny wirtualnej, która ma być chroniona. Dowiedz się, jak utworzyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure, korzystając z [tego przewodnika Szybki start maszyny wirtualnej.](../virtual-machines/linux/quick-create-cli.md)

W tym samouczku będziemy używać następujących funkcji:

* grupa zasobów o nazwie *saphanaResourceGroup*
* maszyna wirtualna o *nazwie saphanaVM*
* zasobów w *lokalizacji westus2.*

Będziemy tworzyć magazyn o nazwie *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Domyślnie magazyn usługi Recovery Services jest ustawiony na magazyn geograficznie nadmiarowy. Geo-Redundant gwarantuje, że dane kopii zapasowej są replikowane do pomocniczego regionu świadczenia usługi Azure, który znajduje się setki kilometrów od regionu podstawowego. Jeśli należy zmodyfikować ustawienie nadmiarowości magazynu, użyj polecenia cmdlet [az backup-properties set](/cli/azure/backup/vault/backup-properties#az_backup_vault_backup_properties_set) magazynu kopii zapasowych.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Aby sprawdzić, czy magazyn został pomyślnie utworzony, użyj [polecenia cmdlet az backup vault list.](/cli/azure/backup/vault#az_backup_vault_list) Zobaczysz następującą odpowiedź:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Rejestrowanie i ochrona SAP HANA wystąpienia

Aby SAP HANA (maszyna wirtualna z zainstalowanym programem SAP HANA) przez usługi platformy [](https://aka.ms/scriptforpermsonhana) Azure, należy uruchomić skrypt przed rejestracją na maszynie SAP HANA wirtualnej. Przed uruchomieniem [skryptu](./tutorial-backup-sap-hana-db.md#prerequisites) upewnij się, że zostały spełnione wszystkie wymagania wstępne. Aby dowiedzieć się więcej na temat tego, co robi skrypt, zapoznaj się z sekcją [Co](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) robi skrypt przed rejestracją.

Po uruchomieniu skryptu wystąpienie SAP HANA zostać zarejestrowane w utworzonym wcześniej magazynie usługi Recovery Services. Aby zarejestrować wystąpienie, użyj [polecenia cmdlet az backup container register.](/cli/azure/backup/container#az_backup_container_register) *VMResourceId* to identyfikator zasobu maszyny wirtualnej utworzonej w celu zainstalowania SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Jeśli maszyna wirtualna nie znajduje się w tej samej grupie zasobów co magazyn, *grupa saphanaResourceGroup* odwołuje się do grupy zasobów, w której utworzono magazyn.

Zarejestrowanie SAP HANA automatycznie odnajduje wszystkie bieżące bazy danych. Jednak w celu odnajdywania nowych baz danych, które mogą zostać dodane w przyszłości, zapoznaj się z sekcją Odnajdywanie nowych baz danych dodanych do [zarejestrowanego SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) danych.

Aby sprawdzić, czy SAP HANA zostało pomyślnie zarejestrowane w magazynie, użyj polecenia cmdlet [az backup container list.](/cli/azure/backup/container#az_backup_container_list) Zobaczysz następującą odpowiedź:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumna "name" w powyższych danych wyjściowych odwołuje się do nazwy kontenera. Ta nazwa kontenera będzie używana w kolejnych sekcjach do włączania kopii zapasowych i wyzwalania ich. W tym przypadku jest to *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Włączanie tworzenia kopii zapasowej w SAP HANA danych

Polecenie [cmdlet az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) wyświetla listę wszystkich baz danych odnalezionych w wystąpieniu SAP HANA zarejestrowanym w poprzednim kroku.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Na tej liście powinna znaleźć się baza danych, której kopię zapasową chcesz wrócić, która będzie wyglądać następująco:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Jak widać na powyższych danych wyjściowych, identyfikator SID SAP HANA to HXE. W tym samouczku skonfigurujemy kopię zapasową bazy *danych saphanadatabase;hxe;hxe,* która znajduje się na *serwerze hxehost.*

Aby chronić i konfigurować kopie zapasowe bazy danych po jednym na raz, użyjemy polecenia cmdlet [az backup protection enable-for-azurewl.](/cli/azure/backup/protection#az_backup_protection_enable_for_azurewl) Podaj nazwę zasad, których chcesz użyć. Aby utworzyć zasady przy użyciu interfejsu wiersza polecenia, użyj polecenia cmdlet [az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create) W tym samouczku będziemy używać zasad *sapahanaPolicy.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Możesz sprawdzić, czy konfiguracja powyższej kopii zapasowej jest ukończona, używając [polecenia cmdlet az backup job list.](/cli/azure/backup/job#az_backup_job_list) Dane wyjściowe będą wyświetlane w następujący sposób:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Polecenie [cmdlet az backup job list](/cli/azure/backup/job#az_backup_job_list) wyświetla listę wszystkich zadań tworzenia kopii zapasowej (zaplanowanych lub na żądanie), które zostały uruchomione lub są aktualnie uruchomione w chronionej bazie danych, oprócz innych operacji, takich jak rejestrowanie, konfigurowanie kopii zapasowej i usuwanie danych kopii zapasowej.

>[!NOTE]
>Azure Backup nie dostosowuje się automatycznie do zmian czasu letniego podczas tworzenia kopii zapasowej bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure.
>
>W razie potrzeby ręcznie zmodyfikuj zasady.

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

W powyższej sekcji szczegółowo opisano sposób konfigurowania zaplanowanej kopii zapasowej, ale w tej sekcji opisano wyzwalanie kopii zapasowej na żądanie. W tym celu użyjemy [polecenia cmdlet az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

>[!NOTE]
> Zasady przechowywania kopii zapasowej na żądanie są określane przez podstawowe zasady przechowywania bazy danych.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Dane wyjściowe będą wyświetlane w następujący sposób:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Odpowiedź będzie zawierała nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą [polecenia az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

>[!NOTE]
>Kopie zapasowe dzienników są automatycznie wyzwalane i zarządzane SAP HANA wewnętrznie.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia, przejdź do samouczka — Restore an SAP HANA database in Azure VM using CLI (Przywracanie bazy danych SAP HANA wirtualnej platformy Azure przy użyciu interfejsu [wiersza polecenia)](tutorial-sap-hana-restore-cli.md)

* Aby dowiedzieć się, jak utworzyć kopię zapasową bazy danych usługi SAP HANA działającej na maszynie wirtualnej platformy Azure przy użyciu programu Azure Portal, zobacz Tworzenie kopii zapasowej baz danych SAP HANA na maszynach wirtualnych [platformy Azure](./backup-azure-sap-hana-database.md)
