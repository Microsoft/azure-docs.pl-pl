---
title: 'Samouczek: Zarządzanie kopią zapasową SAP HANA DB przy użyciu interfejsu wiersza polecenia'
description: W tym samouczku dowiesz się, jak zarządzać kopiami zapasowymi SAP HANA baz danych uruchomionych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 22ff95fe5261a839927aa6ad8123ba370710f178
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323094"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: zarządzanie bazami danych SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza poleceń lub skryptów. W tej dokumentacji szczegółowo opisano sposób zarządzania kopią zapasową bazy danych SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./sap-hana-db-manage.md).

Użyj [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) do uruchamiania poleceń interfejsu wiersza polecenia.

Po zakończeniu tego samouczka będziesz mieć możliwość:

> [!div class="checklist"]
>
> * Monitorowanie zadań tworzenia kopii zapasowej i przywracania
> * Chroń nowe bazy danych dodane do wystąpienia SAP HANA
> * Zmiana zasad
> * Zatrzymywanie ochrony
> * Wznów ochronę

Jeśli utworzono [kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md) , aby utworzyć kopię zapasową bazy danych SAP HANA, wówczas używasz następujących zasobów:

* Grupa zasobów o nazwie *saphanaResourceGroup*
* magazyn o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Obliczenia; saphanaResourceGroup; saphanaVM*
* kopia zapasowa bazy danych/elementu o nazwie *saphanadatabase; HXE; HXE*
* zasoby w regionie *westus2*

Interfejs wiersza polecenia platformy Azure ułatwia zarządzanie SAP HANA bazą danych działającą na maszynie wirtualnej platformy Azure, której kopia zapasowa została utworzona przy użyciu Azure Backup. Ten samouczek zawiera szczegółowe informacje dotyczące operacji zarządzania.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorowanie zadań tworzenia kopii zapasowej i przywracania

Aby monitorować ukończone lub aktualnie uruchomione zadania (kopia zapasowa lub przywracanie), użyj polecenia [AZ Backup Job list](/cli/azure/backup/job#az-backup-job-list) . Interfejs wiersza polecenia umożliwia również [zawieszenie aktualnie uruchomionego zadania](/cli/azure/backup/job#az-backup-job-stop) lub [zaczekanie na ukończenie zadania](/cli/azure/backup/job#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Dane wyjściowe będą przypominać następujące:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Zmień zasady

Aby zmienić zasady źródłowe SAP HANA konfiguracji kopii zapasowej, należy użyć polecenia [AZ Backup Policy Set](/cli/azure/backup/policy#az-backup-policy-set) . Parametr name w tym poleceniu cmdlet odwołuje się do elementu kopii zapasowej, którego zasady chcemy zmienić. W tym samouczku zastąpią zasady naszej bazy danych SAP HANA *saphanadatabase; HXE; HXE* nową zasadą *newsaphanaPolicy*. Nowe zasady można utworzyć za pomocą polecenia [AZ Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) .

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Dane wyjściowe powinny wyglądać podobnie do poniższych:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Chroń nowe bazy danych dodane do wystąpienia SAP HANA

[Zarejestrowanie wystąpienia SAP HANA z magazynem Recovery Services](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) automatycznie odnajduje wszystkie bazy danych w tym wystąpieniu.

Jednak w przypadkach, gdy nowe bazy danych są dodawane do wystąpienia SAP HANA później, użyj polecenia [AZ Backup Protected-Item Initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) . To polecenie cmdlet umożliwia odnalezienie nowych baz danych, które zostały dodane.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Następnie użyj polecenia [AZ Backup Protect-Item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) , aby wyświetlić listę wszystkich baz danych, które zostały odnalezione w wystąpieniu SAP HANA. Ta lista nie obejmuje jednak tych baz danych, na których została już skonfigurowana kopia zapasowa. Po znalezieniu kopii zapasowej bazy danych programu zapoznaj się z  [tematem Włączanie kopii zapasowej w bazie danych SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Nowa baza danych, dla której chcesz utworzyć kopię zapasową, zostanie wyświetlona na tej liście, która będzie wyglądać następująco:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Zatrzymaj ochronę bazy danych SAP HANA

Można zatrzymać ochronę bazy danych SAP HANA na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się na pozostawienie punktów odzyskiwania, pamiętaj o następujących szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone, a całe oczyszczanie zostanie zatrzymane po zatrzymaniu ochrony z zachowaniem danych.
* Zostanie naliczona opłata za chronione wystąpienie i zużyty magazyn.
* Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe zakończą się niepowodzeniem.

Przyjrzyjmy się poszczególnym sposobom, aby zatrzymywać ochronę bardziej szczegółowo.

### <a name="stop-protection-with-retain-data"></a>Zatrzymanie ochrony z zachowaniem danych

Aby zatrzymać ochronę przy zachowaniu danych, należy użyć polecenia [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Dane wyjściowe powinny wyglądać podobnie do poniższych:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retain-data"></a>Zatrzymaj ochronę bez zachowywania danych

Aby zatrzymać ochronę bez zachowywania danych, należy użyć polecenia [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Dane wyjściowe powinny wyglądać podobnie do poniższych:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="resume-protection"></a>Wznów ochronę

Po zatrzymaniu ochrony bazy danych SAP HANA z zachowaniem Zachowaj dane można później wznowić ochronę. Jeśli nie zachowasz kopii zapasowej danych, nie będziesz w stanie wznowić ochrony.

Aby wznowić ochronę, użyj polecenia [AZ Backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) .

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Dane wyjściowe powinny wyglądać podobnie do poniższych:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Aby sprawdzić stan tej operacji, użyj polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć kopię zapasową bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure przy użyciu Azure Portal, zobacz [bazy danych SAP HANA kopii zapasowych na maszynach wirtualnych](./backup-azure-sap-hana-database.md)

* Aby dowiedzieć się, jak zarządzać kopią zapasową bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure przy użyciu Azure Portal, zobacz [Zarządzanie kopiami zapasowymi baz danych SAP HANA na maszynie wirtualnej platformy Azure](./sap-hana-db-manage.md)
