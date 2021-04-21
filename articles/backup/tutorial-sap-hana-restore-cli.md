---
title: Samouczek — przywracanie bazy SAP HANA DB na platformie Azure przy użyciu interfejsu wiersza polecenia
description: Z tego samouczka dowiesz się, jak przywrócić bazy danych SAP HANA uruchomione na maszynie wirtualnej platformy Azure z magazynu Azure Backup Recovery Services przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a249ab63aa72c1d39ab1626e72ff3b2037f3f723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768455"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: przywracanie baz SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pośrednictwem skryptów. W tej dokumentacji szczegółowo opisano sposób przywracania kopii zapasowej bazy SAP HANA na maszynie wirtualnej platformy Azure — przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./sap-hana-db-restore.md).

Użyj [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) polecenia do uruchamiania poleceń interfejsu wiersza polecenia.

Po zakończeniu tego samouczka będziesz w stanie:

> [!div class="checklist"]
>
> * Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych
> * Przywracanie bazy danych

W tym samouczku przyjęto założenie, że masz SAP HANA na maszynie wirtualnej platformy Azure, dla których jest back-up z systemem Azure Backup. Jeśli używasz funkcji back up an [SAP HANA database in Azure](tutorial-sap-hana-backup-cli.md) using CLI to back up your SAP HANA database (Kopię zapasową bazy danych na platformie Azure przy użyciu interfejsu wiersza polecenia) używasz następujących zasobów:

* grupa zasobów o nazwie *saphanaResourceGroup*
* magazyn o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* baza danych kopii zapasowej/element o *nazwie saphanadatabase;hxe;hxe*
* zasoby w *regionie westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych

Aby wyświetlić listę wszystkich punktów odzyskiwania dla bazy danych, użyj polecenia cmdlet [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) w następujący sposób:

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

Jak widać, na powyższej liście znajdują się trzy punkty odzyskiwania: po jednym dla kopii zapasowej pełnej, różnicowej i dziennika.

>[!NOTE]
>Możesz również wyświetlić punkty rozpoczęcia i zakończenia każdego łańcucha kopii zapasowej dziennika bez zabezpieczeń, używając polecenia cmdlet [az backup recoverypoint show-log-chain.](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain)

## <a name="prerequisites-to-restore-a-database"></a>Wymagania wstępne dotyczące przywracania bazy danych

Przed przywróceniem bazy danych upewnij się, że spełnione są następujące wymagania wstępne:

* Bazę danych można przywrócić tylko do SAP HANA, które jest w tym samym regionie
* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co źródło
* Azure Backup nie można zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. Dlatego przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Azure Backup można przywrócić SAP HANA baz danych uruchomionych na maszyn wirtualnych platformy Azure w następujący sposób:

* Przywróć do określonej daty lub godziny (do drugiej) przy użyciu kopii zapasowych dziennika. Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dzienników, które są wymagane do przywrócenia na podstawie wybranego czasu.
* Przywracanie do określonej pełnej lub różnicowej kopii zapasowej w celu przywrócenia do określonego punktu odzyskiwania.

Aby przywrócić bazę danych, użyj polecenia cmdlet [az restore-azurewl,](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) które wymaga obiektu konfiguracji odzyskiwania jako jednego z danych wejściowych. Ten obiekt można wygenerować za pomocą [polecenia cmdlet az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Obiekt konfiguracji odzyskiwania zawiera wszystkie szczegóły do wykonania przywracania. Jednym z nich jest tryb przywracania **— OriginalWorkloadRestore** lub **AlternateWorkloadRestore.**

>[!NOTE]
> **OriginalWorkloadRestore** — przywraca dane do tego samego SAP HANA co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych. <br>
> **AlternateWorkloadRestore** — przywracanie bazy danych do lokalizacji alternatywnej i zachowanie oryginalnej źródłowej bazy danych.

## <a name="restore-to-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

Aby przywrócić bazę danych do lokalizacji alternatywnej, użyj **alternateWorkloadRestore** jako trybu przywracania. Następnie należy wybrać punkt przywracania, który może być poprzednim punktem w czasie lub dowolnym z poprzednich punktów przywracania.

W tym samouczku przywrócisz poprzedni punkt przywracania. [Wyświetl listę punktów przywracania dla](#view-restore-points-for-a-backed-up-database) bazy danych i wybierz punkt, do którego chcesz przywrócić. W tym samouczku użyjemy punktu przywracania o *nazwie 7660777527047692711.*

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, utwórzmy obiekt konfiguracji odzyskiwania przy użyciu polecenia cmdlet [az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Przyjrzyjmy się, co oznaczają poszczególne pozostałe parametry tego polecenia cmdlet:

* **--target-item-name** Jest to nazwa, która będzie używać przywróconej bazy danych. W tym przypadku umyliśmy *nazwę* restored_database .
* **--target-server-name** Jest to nazwa serwera usługi SAP HANA, który został pomyślnie zarejestrowany w magazynie usługi Recovery Services i znajduje się w tym samym regionie co baza danych do przywrócenia. W tym samouczku przywrócimy bazę danych na ten sam serwer SAP HANA chroniony przez nas o nazwie *hxehost.*
* **--target-server-type** Aby przywrócić bazy SAP HANA, należy użyć platformy **HANAInstance.**

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

Odpowiedzią na powyższe zapytanie będzie obiekt konfiguracji odzyskiwania, który wygląda podobnie do tego:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Teraz, aby przywrócić bazę danych, uruchom [polecenie cmdlet az restore restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Aby użyć tego polecenia, wprowadź powyższe dane wyjściowe JSON zapisane w pliku o *nazwierecoveryconfig.jsw pliku*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać tak:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź będzie zawierała nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania przy użyciu [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-and-overwrite"></a>Przywracanie i zastępowanie

Aby przywrócić do oryginalnej lokalizacji, użyjemy trybu **przywracania OrignialWorkloadRestore.** Następnie należy wybrać punkt przywracania, który może być poprzednim punktem w czasie lub dowolnym z poprzednich punktów przywracania.

W tym samouczku wybierzemy poprzedni punkt w czasie "28-11-2019-09:53:00", do których ma zostać przywrócony. Ten punkt przywracania można podać w następujących formatach: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Aby wybrać prawidłowy punkt w czasie do przywrócenia, użyj polecenia cmdlet [az backup recoverypoint show-log-chain,](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) które zawiera listę interwałów nieuprawnianych kopii zapasowych łańcucha dzienników.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Odpowiedzią na powyższe zapytanie będzie obiekt konfiguracji odzyskiwania, który wygląda następująco:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Teraz, aby przywrócić bazę danych, uruchom [polecenie cmdlet az restore restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Aby użyć tego polecenia, wprowadź powyższe dane wyjściowe JSON zapisane w pliku o *nazwierecoveryconfig.jspliku*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Dane wyjściowe będą wyglądać tak:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpowiedź będzie zawierała nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą [polecenia az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

## <a name="restore-as-files"></a>Przywróć jako pliki

Aby przywrócić dane kopii zapasowej jako pliki zamiast bazy danych, użyjemy **trybu przywracania RestoreAsFiles.** Następnie wybierz punkt przywracania, który może być poprzednim punktem w czasie lub dowolnym z poprzednich punktów przywracania. Po zrzucie plików do określonej ścieżki można je zabrać do dowolnej maszyny SAP HANA, na której chcesz przywrócić je jako bazę danych. Ponieważ możesz przenieść te pliki na dowolną maszynę, możesz teraz przywrócić dane między subskrypcjami i regionami.

W tym samouczku wybierzemy poprzedni punkt w czasie do przywrócenia oraz lokalizację, do których mają zostać zrzucane pliki kopii zapasowej, jak na tym samym `28-11-2019-09:53:00` `/home/saphana/restoreasfiles` SAP HANA serwera. Ten punkt przywracania można podać w jednym z następujących formatów: **dd-mm-yyyy lub** **dd-mm-yyyy-hh:mm:ss.** Aby wybrać prawidłowy punkt w czasie do przywrócenia, użyj polecenia cmdlet [az backup recoverypoint show-log-chain,](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) które zawiera listę interwałów nieuprawnianych kopii zapasowych łańcucha dzienników.

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, utwórzmy obiekt konfiguracji odzyskiwania przy użyciu polecenia cmdlet [az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Przyjrzyjmy się, co oznaczają poszczególne pozostałe parametry tego polecenia cmdlet:

* **--target-container-name** Jest to nazwa serwera usługi SAP HANA, który został pomyślnie zarejestrowany w magazynie usługi Recovery Services i znajduje się w tym samym regionie co baza danych do przywrócenia. W tym samouczku przywrócimy bazę danych jako pliki na ten sam serwer SAP HANA chroniony przez nas o nazwie *hxehost.*
* **--rp-name** W przypadku przywracania do punktu w czasie nazwą punktu przywracania będzie **DefaultRangeRecoveryPoint**

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

Odpowiedzią na powyższe zapytanie będzie obiekt konfiguracji odzyskiwania, który wygląda następująco:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Teraz, aby przywrócić bazę danych jako pliki, uruchom [polecenie cmdlet az restore restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Aby użyć tego polecenia, wprowadź dane wyjściowe JSON powyżej, które są zapisywane w pliku *o nazwierecoveryconfig.jsna .*

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Dane wyjściowe będą wyglądać tak:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Odpowiedź będzie zawierała nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania przy użyciu [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

Pliki, które są zrzucane do kontenera docelowego, to:

* Pliki kopii zapasowej bazy danych
* Pliki wykazu
* Pliki metadanych JSON (dla każdego związanego z tym pliku kopii zapasowej)

Zazwyczaj ścieżka udziału sieciowego lub ścieżka zainstalowanego udziału plików platformy Azure określona jako ścieżka docelowa umożliwia łatwiejszy dostęp do tych plików przez inne maszyny w tej samej sieci lub z zainstalowanym na nich tym samym udziałem plików platformy Azure.

>[!NOTE]
>Aby przywrócić pliki kopii zapasowej bazy danych w udziałach plików platformy Azure zainstalowanych na docelowej zarejestrowanej maszynie wirtualnej, upewnij się, że konto główne ma uprawnienia do odczytu/zapisu w udziałach plików platformy Azure.

W zależności od wybranego typu punktu przywracania **(punkt** w czasie lub pełna & różnicowa) zobaczysz co najmniej jeden folder utworzony w ścieżce docelowej. Jeden z folderów o nazwie zawiera pełne i różnicowe kopie zapasowe, a drugi folder o nazwie `Data_<date and time of restore>` `Log` zawiera kopie zapasowe dzienników.

Przenieś te przywrócone pliki na serwer SAP HANA, na którym chcesz przywrócić je jako bazę danych. Następnie wykonaj następujące kroki, aby przywrócić bazę danych:

1. Ustaw uprawnienia w folderze/katalogu, w którym są przechowywane pliki kopii zapasowej, za pomocą następującego polecenia:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Uruchom następny zestaw poleceń jako `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Wygeneruj plik wykazu do przywrócenia. Wyodrębnij **nazwę BackupId** z pliku metadanych JSON w celu tworzenia pełnej kopii zapasowej, która będzie używana w dalszej części operacji przywracania. Upewnij się, że pełne kopie zapasowe i kopie zapasowe dzienników znajdują się w różnych folderach, a następnie usuń pliki wykazu i pliki metadanych JSON w tych folderach.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    W powyższym poleceniu:

    * `<DataFileDir>` — folder zawierający pełne kopie zapasowe
    * `<LogFilesDir>` — folder zawierający kopie zapasowe dzienników
    * `<PathToPlaceCatalogFile>` — folder, w którym musi zostać wygenerowany plik wykazu

1. Przywróć przy użyciu nowo wygenerowanego pliku wykazu za pośrednictwem programu HANA Studio lub uruchom zapytanie dotyczące przywracania HDBSQL z tym nowo wygenerowanym wykazem. Poniżej przedstawiono zapytania HDBSQL:

    * Aby przywrócić do punktu w czasie:

        Jeśli tworzysz nową przywróconą bazę danych, uruchom polecenie HDBSQL, aby utworzyć nową bazę danych, a następnie zatrzymaj bazę danych w `<DatabaseName>` celu przywrócenia. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych.

        Następnie uruchom następujące polecenie, aby przywrócić bazę danych:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - Nazwa nowej bazy danych lub istniejącej bazy danych, którą chcesz przywrócić
        * `<Timestamp>` - Dokładny znacznik czasu przywracania do punktu w czasie
        * `<DatabaseName@HostName>` - Nazwa bazy danych, której kopia zapasowa jest używana do przywracania, oraz **nazwa** SAP HANA serwera, na którym znajduje się ta baza danych. Opcja `USING SOURCE <DatabaseName@HostName>` określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż nazwa docelowej maszyny SAP HANA danych. Dlatego nie trzeba go określać dla przywracania wykonywanego na tym samym serwerze HANA, z którego jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` - Ścieżka do pliku katalogu wygenerowanego w **kroku 3**
        * `<DataFileDir>` — folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` — folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` — **backupid wyodrębniony** w **kroku 3**

    * Aby przywrócić do określonej pełnej lub różnicowej kopii zapasowej:

        Jeśli tworzysz nową przywróconą bazę danych, uruchom polecenie HDBSQL, aby utworzyć nową bazę danych, a następnie zatrzymaj bazę `<DatabaseName>` danych w celu przywrócenia. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` — nazwa nowej bazy danych lub istniejącej bazy danych, którą chcesz przywrócić
        * `<Timestamp>` — dokładny znacznik czasu przywracania do punktu w czasie
        * `<DatabaseName@HostName>` — nazwa bazy danych, której kopia zapasowa jest używana do przywracania, oraz **nazwa** hosta/SAP HANA serwera, na którym znajduje się ta baza danych. Opcja `USING SOURCE <DatabaseName@HostName>`  określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż nazwa SAP HANA docelowej. Dlatego nie trzeba go określać dla przywracania na tym samym serwerze HANA, z którego jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` — ścieżka do pliku katalogu wygenerowanego w **kroku 3**
        * `<DataFileDir>` — folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` — folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` — **backupid wyodrębniony** w **kroku 3**

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak zarządzać bazami danych SAP HANA, których kopię zapasową pisano przy użyciu interfejsu wiersza polecenia platformy Azure, przejdź do samouczka Manage an SAP HANA database in Azure VM using CLI (Zarządzanie bazą danych usługi azure database na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza [polecenia)](tutorial-sap-hana-backup-cli.md)

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure przy użyciu maszyny Azure Portal, zapoznaj się z tematem Restore [an SAP HANA databases on Azure VMs](./sap-hana-db-restore.md) (Przywracanie bazy danych SAP HANA na maszynach wirtualnych platformy Azure)
