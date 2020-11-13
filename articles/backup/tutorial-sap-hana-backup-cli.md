---
title: Samouczek — Tworzenie kopii zapasowej bazy danych SAP HANA w systemie Azure przy użyciu interfejsu wiersza polecenia Azure
description: W tym samouczku dowiesz się, jak utworzyć kopię zapasową SAP HANA baz danych działających na maszynie wirtualnej platformy Azure do magazynu Azure Backup Recovery Services przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41869ac4dfc0d3964fb48463e5f142b8391e2d94
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579257"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Tworzenie kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza poleceń lub skryptów. W tej dokumentacji szczegółowo przedstawiono sposób tworzenia kopii zapasowej bazy danych SAP HANA i wyzwalania kopii zapasowych na żądanie — wszystko to za pomocą interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./backup-azure-sap-hana-database.md).

W tym dokumencie przyjęto założenie, że na maszynie wirtualnej platformy Azure jest już zainstalowana baza danych SAP HANA. (Możesz również [utworzyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)). Po zakończeniu tego samouczka będziesz mieć możliwość:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Rejestrowanie wystąpienia SAP HANA i odnajdywanie w nim baz danych
> * Włączanie kopii zapasowej w bazie danych SAP HANA
> * Wyzwalanie kopii zapasowej na żądanie

Zapoznaj się z [aktualnie obsługiwanymi scenariuszami](./sap-hana-backup-support-matrix.md#scenario-support) dla SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.30 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest kontenerem logicznym, który przechowuje dane kopii zapasowej dla każdego chronionego zasobu, takie jak maszyny wirtualne platformy Azure lub obciążenia działające na maszynach wirtualnych platformy Azure, takich jak bazy danych SQL lub HANA. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Utwórz magazyn usługi Recovery Services za pomocą polecenia [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Określ taką samą grupę zasobów i lokalizację, jak w przypadku maszyny wirtualnej, która ma być chroniona. Dowiedz się, jak utworzyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure z tym [przewodniku szybki start](../virtual-machines/linux/quick-create-cli.md).

W tym samouczku będziemy korzystać z następujących czynności:

* Grupa zasobów o nazwie *saphanaResourceGroup*
* Maszyna wirtualna o nazwie *saphanaVM*
* zasoby w lokalizacji *westus2* .

Będziemy tworzyć magazyn o nazwie *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Domyślnie magazyn usługi Recovery Services jest ustawiony na magazyn geograficznie nadmiarowy. Geo-Redundant Storage gwarantuje, że dane kopii zapasowej są replikowane do dodatkowego regionu świadczenia usługi Azure, który znajduje się setki kilometrów od regionu podstawowego. Jeśli należy zmodyfikować ustawienie nadmiarowości magazynu, użyj polecenia [AZ Backup Storage Backup-Properties Set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) .

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Aby sprawdzić, czy magazyn został pomyślnie utworzony, użyj polecenia [AZ Backup magazynu list](/cli/azure/backup/vault#az-backup-vault-list) . Zostanie wyświetlona następująca odpowiedź:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Zarejestruj i Chroń wystąpienie SAP HANA

Na maszynie SAP HANA należy uruchomić [skrypt przed rejestracją](https://aka.ms/scriptforpermsonhana) dla SAP HANA wystąpienia (maszyna wirtualna z zainstalowanym SAP HANA), który ma zostać odnaleziony przez usługi platformy Azure. Przed uruchomieniem skryptu upewnij się, że spełniono wszystkie [wymagania wstępne](./tutorial-backup-sap-hana-db.md#prerequisites) . Aby dowiedzieć się więcej na temat działania skryptu, zapoznaj się z sekcją co to jest [skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

Po uruchomieniu skryptu wystąpienie SAP HANA może zostać zarejestrowane przy użyciu magazynu Recovery Services utworzonego wcześniej. Aby zarejestrować wystąpienie, użyj polecenia cmdlet [AZ Backup Container Register](/cli/azure/backup/container#az-backup-container-register) . *VMResourceId* to identyfikator zasobu maszyny wirtualnej, który został utworzony w celu zainstalowania SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Jeśli maszyna wirtualna nie znajduje się w tej samej grupie zasobów co magazyn, *saphanaResourceGroup* odwołuje się do grupy zasobów, w której utworzono magazyn.

Zarejestrowanie wystąpienia SAP HANA automatycznie odnajduje wszystkie jego bieżące bazy danych. Jednak w celu odnalezienia nowych baz danych, które mogą zostać dodane w przyszłości, zapoznaj się z sekcją [odnajdywanie nowych baz danych dodanych do zarejestrowanego wystąpienia SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) .

Aby sprawdzić, czy wystąpienie SAP HANA zostało pomyślnie zarejestrowane w magazynie, użyj polecenia cmdlet [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) . Zostanie wyświetlona następująca odpowiedź:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumna "name" w powyższej danych wyjściowych odwołuje się do nazwy kontenera. Ta nazwa kontenera zostanie użyta w następnych sekcjach, aby włączyć wykonywanie kopii zapasowych i wyzwolić je. W tym przypadku jest to *VMAppContainer; COMPUTE; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Włącz tworzenie kopii zapasowej w bazie danych SAP HANA

Polecenie [AZ Backup Protected-Item](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) list Wyświetla wszystkie bazy danych odnalezione w wystąpieniu SAP HANA zarejestrowanym w poprzednim kroku.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Baza danych, której kopia zapasowa ma zostać utworzona na tej liście, powinna wyglądać następująco:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Jak widać z powyższych danych wyjściowych, identyfikator SID systemu SAP HANA jest HXE. W tym samouczku skonfigurujemy tworzenie kopii zapasowej bazy danych *saphanadatabase; HXE; HXE* , która znajduje się na serwerze *hxehost* .

Aby jednocześnie chronić i konfigurować kopie zapasowe bazy danych, użyj polecenia [AZ Backup Protection Enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) . Podaj nazwę zasad, których chcesz użyć. Aby utworzyć zasady przy użyciu interfejsu wiersza polecenia, użyj polecenia [AZ Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) . W tym samouczku będziemy używać zasad *sapahanaPolicyymi* .

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Aby sprawdzić, czy powyższa Konfiguracja kopii zapasowej została ukończona, użyj polecenia cmdlet [AZ Backup Job list](/cli/azure/backup/job#az-backup-job-list) . Dane wyjściowe będą wyświetlane w następujący sposób:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Polecenie polecenia [AZ Backup Job list](/cli/azure/backup/job#az-backup-job-list) wyświetla wszystkie zadania tworzenia kopii zapasowej (zaplanowane lub na żądanie), które zostały uruchomione lub są aktualnie uruchomione w chronionej bazie danych, a także inne operacje, takie jak rejestrowanie, konfigurowanie kopii zapasowej i usuwanie danych kopii zapasowej.

>[!NOTE]
>Azure Backup nie dostosowuje się automatycznie podczas tworzenia kopii zapasowej bazy danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure.
>
>Zmodyfikuj zasady ręcznie w razie konieczności.

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie kopii zapasowej na żądanie

Powyższa sekcja zawiera szczegółowe informacje dotyczące konfigurowania zaplanowanej kopii zapasowej, w tym sekcję Informacje o wyzwalaniu kopii zapasowej na żądanie. W tym celu użyj polecenia [AZ Backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

>[!NOTE]
> Zasady przechowywania kopii zapasowej na żądanie są określane przez podstawowe zasady przechowywania dla bazy danych.

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

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

>[!NOTE]
>Oprócz planowania pełnej lub różnicowej kopii zapasowej, mogą one być również obecnie wyzwalane ręcznie. Kopie zapasowe dzienników są automatycznie wyzwalane i zarządzane przez SAP HANA wewnętrznie.
>
> Przyrostowe kopie zapasowe nie są obecnie obsługiwane przez Azure Backup.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia, przejdź do samouczka — [przywracanie SAP HANAj bazy danych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-restore-cli.md)

* Aby dowiedzieć się, jak utworzyć kopię zapasową bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure przy użyciu Azure Portal, zobacz [Tworzenie kopii zapasowych baz danych SAP HANA na maszynach](./backup-azure-sap-hana-database.md)
