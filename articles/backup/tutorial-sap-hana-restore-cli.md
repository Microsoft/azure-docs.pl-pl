---
title: Samouczek — przywracanie SAP HANA bazy danych na platformie Azure przy użyciu interfejsu wiersza polecenia
description: W tym samouczku dowiesz się, jak przywrócić bazy danych SAP HANA uruchomione na maszynie wirtualnej platformy Azure z magazynu Azure Backup Recovery Services przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472072"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Przywracanie SAP HANA baz danych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza poleceń lub skryptów. W tej dokumentacji szczegółowo przedstawiono sposób przywracania bazy danych SAP HANA kopii zapasowej na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Użyj [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) do uruchamiania poleceń interfejsu wiersza polecenia.

Po zakończeniu tego samouczka będziesz mieć możliwość:

> [!div class="checklist"]
>
> * Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych
> * Przywracanie bazy danych

W tym samouczku założono, że masz bazę danych SAP HANA działającą na maszynie wirtualnej platformy Azure, której kopię zapasową utworzono przy użyciu Azure Backup. Jeśli utworzono [kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md) , aby utworzyć kopię zapasową bazy danych SAP HANA, wówczas używasz następujących zasobów:

* Grupa zasobów o nazwie *saphanaResourceGroup*
* magazyn o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Obliczenia; saphanaResourceGroup; saphanaVM*
* kopia zapasowa bazy danych/elementu o nazwie *saphanadatabase; HXE; HXE*
* zasoby w regionie *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych

Aby wyświetlić listę wszystkich punktów odzyskiwania dla bazy danych, użyj polecenia [AZ Backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) w następujący sposób:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Lista punktów odzyskiwania będzie wyglądać następująco:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Jak widać, Powyższa lista zawiera trzy punkty odzyskiwania: jeden dla pełnych, różnicowych i kopii zapasowych dziennika.

>[!NOTE]
>Możesz również wyświetlić punkty początkowe i końcowe każdego nieuszkodzonego łańcucha kopii zapasowych dziennika za pomocą polecenia [AZ Backup recoverypoint show-log-łańcucha](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Wymagania wstępne dotyczące przywracania bazy danych

Przed przystąpieniem do przywracania bazy danych upewnij się, że zostały spełnione następujące wymagania wstępne:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA, które znajduje się w tym samym regionie.
* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co Źródło
* Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. W związku z tym przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Azure Backup można przywrócić SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika. Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.
* Przywróć do konkretnej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

Aby przywrócić bazę danych, użyj polecenia [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) , które wymaga obiektu konfiguracji odzyskiwania jako jednego z danych wejściowych. Ten obiekt można wygenerować za pomocą polecenia [AZ Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Obiekt konfiguracji odzyskiwania zawiera wszystkie szczegóły do wykonania przywracania. Jeden z nich jest trybem przywracania — **OriginalWorkloadRestore** lub **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** — Przywróć dane do tego samego wystąpienia SAP HANA co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych. <br>
> **AlternateWorkloadRestore** — Przywróć bazę danych do alternatywnej lokalizacji i Zachowaj oryginalną źródłową bazę danych.

## <a name="restore-to-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Aby przywrócić bazę danych do innej lokalizacji, użyj **AlternateWorkloadRestore** jako trybu przywracania. Następnie należy wybrać punkt przywracania, który może być wcześniejszym punktem w czasie lub dowolnym z poprzednich punktów przywracania.

W tym samouczku przywrócisz do poprzedniego punktu przywracania. [Wyświetl listę punktów przywracania](#view-restore-points-for-a-backed-up-database) bazy danych i wybierz punkt, do którego chcesz przywrócić. W tym samouczku zostanie użyty punkt przywracania o nazwie *7660777527047692711*.

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, Utwórz teraz obiekt konfiguracji odzyskiwania za pomocą polecenia [AZ Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Przyjrzyjmy się poszczególnym parametrom w tym poleceniu cmdlet:

* **--Target-Item-Name** Jest to nazwa używana przez przywróconą bazę danych. W takim przypadku użyto nazwy *restored_database*.
* **--Target-Server-Name** Jest to nazwa serwera SAP HANA, który został pomyślnie zarejestrowany w magazynie usługi Recovery Services i znajduje się w tym samym regionie, w którym znajduje się baza danych, która ma zostać przywrócona. Na potrzeby tego samouczka będziemy przywracać bazę danych na tym samym serwerze SAP HANA, który został objęty ochroną, o nazwie *hxehost*.
* **--Target-Server-Type** Aby można było przywrócić SAP HANA baz danych, należy użyć **SapHanaDatabase** .

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Odpowiedź na powyższe zapytanie będzie obiektem konfiguracji odzyskiwania, który wygląda następująco:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Teraz, aby przywrócić bazę danych, uruchom polecenie [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Aby użyć tego polecenia, wprowadzimy powyższe dane wyjściowe JSON zapisane w pliku o nazwie *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać następująco:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Przywróć i Zastąp

Aby przywrócić do oryginalnej lokalizacji, użyjemy **OrignialWorkloadRestore** jako trybu przywracania. Następnie należy wybrać punkt przywracania, który może być wcześniejszym punktem w czasie lub dowolnym z poprzednich punktów przywracania.

Na potrzeby tego samouczka wybierzemy poprzedni punkt w czasie "28-11-2019-09:53:00", aby przywrócić wartość. Ten punkt przywracania można podać w następujących formatach: dd-mm-rrrr, dd-mm-rrrr-hh: mm: SS. Aby wybrać prawidłowy punkt w czasie do przywrócenia, użyj polecenia [AZ Backup recoverypoint show-log-łańcucha](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) , które wyświetla listę interwałów tworzenia kopii zapasowych łańcucha dzienników.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Odpowiedź na powyższe zapytanie będzie obiektem konfiguracji odzyskiwania, który wygląda następująco:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Teraz, aby przywrócić bazę danych, uruchom polecenie [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Aby użyć tego polecenia, wprowadzimy powyższe dane wyjściowe JSON zapisane w pliku o nazwie *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać następująco:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure, przejdź do samouczka [Zarządzanie bazą danych SAP HANA na maszynie wirtualnej platformy Azure](tutorial-sap-hana-backup-cli.md)

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA działającą na maszynie wirtualnej platformy Azure przy użyciu Azure Portal, zobacz [przywracanie baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
