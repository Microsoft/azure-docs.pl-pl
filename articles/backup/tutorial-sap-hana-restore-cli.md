---
title: Samouczek — przywracanie SAP HANA bazy danych na platformie Azure przy użyciu interfejsu wiersza polecenia
description: W tym samouczku dowiesz się, jak przywrócić bazy danych SAP HANA uruchomione na maszynie wirtualnej platformy Azure z magazynu Azure Backup Recovery Services przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334807"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: Przywracanie SAP HANA baz danych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza poleceń lub skryptów. W tej dokumentacji szczegółowo przedstawiono sposób przywracania bazy danych SAP HANA kopii zapasowej na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./sap-hana-db-restore.md).

Użyj [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) do uruchamiania poleceń interfejsu wiersza polecenia.

Po zakończeniu tego samouczka będziesz mieć możliwość:

> [!div class="checklist"]
>
> * Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych
> * Przywracanie bazy danych

W tym samouczku założono, że masz bazę danych SAP HANA działającą na maszynie wirtualnej platformy Azure, której kopia zapasowa została utworzona przy użyciu Azure Backup. Jeśli utworzono [kopię zapasową bazy danych SAP HANA na platformie Azure przy użyciu interfejsu wiersza polecenia](tutorial-sap-hana-backup-cli.md) , aby utworzyć kopię zapasową bazy danych SAP HANA, wówczas używasz następujących zasobów:

* Grupa zasobów o nazwie *saphanaResourceGroup*
* magazyn o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Obliczenia; saphanaResourceGroup; saphanaVM*
* kopia zapasowa bazy danych/elementu o nazwie *saphanadatabase; HXE; HXE*
* zasoby w regionie *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Wyświetlanie punktów przywracania dla kopii zapasowej bazy danych

Aby wyświetlić listę wszystkich punktów odzyskiwania dla bazy danych, użyj polecenia [AZ Backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) w następujący sposób:

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
>Możesz również wyświetlić punkty początkowe i końcowe każdego nieuszkodzonego łańcucha kopii zapasowych dziennika za pomocą polecenia [AZ Backup recoverypoint show-log-łańcucha](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Wymagania wstępne dotyczące przywracania bazy danych

Przed przystąpieniem do przywracania bazy danych upewnij się, że zostały spełnione następujące wymagania wstępne:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA znajdującego się w tym samym regionie.
* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co Źródło
* Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. W związku z tym przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Azure Backup można przywrócić SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika. Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.
* Przywróć do konkretnej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

Aby przywrócić bazę danych, użyj polecenia [AZ Restore Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) , które wymaga obiektu konfiguracji odzyskiwania jako jednego z danych wejściowych. Ten obiekt można wygenerować za pomocą polecenia [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) . Obiekt konfiguracji odzyskiwania zawiera wszystkie szczegóły do wykonania przywracania. Jeden z nich jest trybem przywracania — **OriginalWorkloadRestore** lub **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** — Przywróć dane do tego samego wystąpienia SAP HANA co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych. <br>
> **AlternateWorkloadRestore** — Przywróć bazę danych do alternatywnej lokalizacji i Zachowaj oryginalną źródłową bazę danych.

## <a name="restore-to-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Aby przywrócić bazę danych do innej lokalizacji, użyj **AlternateWorkloadRestore** jako trybu przywracania. Następnie należy wybrać punkt przywracania, który może być wcześniejszym punktem w czasie lub dowolnym z poprzednich punktów przywracania.

W tym samouczku przywrócisz do poprzedniego punktu przywracania. [Wyświetl listę punktów przywracania](#view-restore-points-for-a-backed-up-database) bazy danych i wybierz punkt, do którego chcesz przywrócić. W tym samouczku zostanie użyty punkt przywracania o nazwie *7660777527047692711*.

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, Utwórz teraz obiekt konfiguracji odzyskiwania za pomocą polecenia [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) . Przyjrzyjmy się poszczególnym parametrom w tym poleceniu cmdlet:

* **--Target-Item-Name** Jest to nazwa używana przez przywróconą bazę danych. W takim przypadku użyto nazwy *restored_database*.
* **--Target-Server-Name** Jest to nazwa serwera SAP HANA, który został pomyślnie zarejestrowany w magazynie Recovery Services i znajduje się w tym samym regionie, w którym znajduje się baza danych, która ma zostać przywrócona. W tym samouczku będziemy przywracać bazę danych na tym samym serwerze SAP HANA, który został objęty ochroną, o nazwie *hxehost*.
* **--Target-Server-Type** Aby można było przywrócić SAP HANA baz danych, należy użyć **HANAInstance** .

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
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Teraz, aby przywrócić bazę danych, uruchom polecenie [AZ Restore Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Aby użyć tego polecenia, wprowadzimy powyższe dane wyjściowe JSON zapisane w pliku o nazwie *recoveryconfig.jsna*.

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

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Przywróć i Zastąp

Aby przywrócić do oryginalnej lokalizacji, użyjemy **OrignialWorkloadRestore** jako trybu przywracania. Następnie należy wybrać punkt przywracania, który może być wcześniejszym punktem w czasie lub dowolnym z poprzednich punktów przywracania.

Na potrzeby tego samouczka wybierzemy poprzedni punkt w czasie "28-11-2019-09:53:00", aby przywrócić wartość. Ten punkt przywracania można podać w następujących formatach: dd-mm-rrrr, dd-mm-rrrr-hh: mm: SS. Aby wybrać prawidłowy punkt w czasie do przywrócenia, użyj polecenia [AZ Backup recoverypoint show-log-łańcucha](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , które wyświetla listę interwałów tworzenia kopii zapasowych łańcucha dzienników.

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
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Teraz, aby przywrócić bazę danych, uruchom polecenie [AZ Restore Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Aby użyć tego polecenia, wprowadzimy powyższe dane wyjściowe JSON zapisane w pliku o nazwie *recoveryconfig.jsna*.

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

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-as-files"></a>Przywróć jako pliki

Aby przywrócić dane kopii zapasowej jako pliki zamiast bazy danych, użyjemy **RestoreAsFiles** jako trybu przywracania. Następnie wybierz punkt przywracania, który może być wcześniejszym punktem w czasie lub dowolnym z poprzednich punktów przywracania. Po zrzucie plików do określonej ścieżki można wykonać te pliki na dowolnym komputerze SAP HANA, na którym chcesz je przywrócić jako bazę danych. Ponieważ można przenieść te pliki na dowolną maszynę, można teraz przywrócić dane między subskrypcjami i regionami.

Na potrzeby tego samouczka wybierzemy poprzedni punkt w czasie, w którym ma zostać `28-11-2019-09:53:00` przywrócona wartość, i lokalizacja do zrzutu plików kopii zapasowej `/home/saphana/restoreasfiles` na tym samym serwerze SAP HANA. Ten punkt przywracania można podać w dowolnym z następujących formatów: **dd-mm-rrrr** lub **dd-mm-rrrr-hh: mm: SS**. Aby wybrać prawidłowy punkt w czasie do przywrócenia, użyj polecenia [AZ Backup recoverypoint show-log-łańcucha](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , które wyświetla listę interwałów tworzenia kopii zapasowych łańcucha dzienników.

Korzystając z powyższej nazwy punktu przywracania i trybu przywracania, Utwórz obiekt konfiguracji odzyskiwania za pomocą polecenia [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) . Przyjrzyjmy się poszczególnym parametrom w tym poleceniu cmdlet:

* **--Target-Container-Name** Jest to nazwa serwera SAP HANA, który został pomyślnie zarejestrowany w magazynie Recovery Services i znajduje się w tym samym regionie, w którym znajduje się baza danych, która ma zostać przywrócona. W tym samouczku przywrócono bazę danych jako pliki do tego samego serwera SAP HANA, który został objęty ochroną, o nazwie *hxehost*.
* **--RP-Name** W przypadku przywracania do punktu w czasie nazwa punktu przywracania będzie **DefaultRangeRecoveryPoint**

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

Odpowiedź na powyższe zapytanie będzie obiektem konfiguracji odzyskiwania, który wygląda następująco:

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

Teraz, aby przywrócić bazę danych jako pliki, uruchom polecenie [AZ Restore Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Aby użyć tego polecenia, wprowadzimy dane wyjściowe JSON powyżej, które są zapisywane w pliku o nazwie *recoveryconfig.jsna*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Dane wyjściowe będą wyglądać następująco:

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

Odpowiedź będzie zawierać nazwę zadania. Ta nazwa zadania może służyć do śledzenia stanu zadania za pomocą polecenia [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

Pliki, które są zrzucane do kontenera docelowego:

* Pliki kopii zapasowej bazy danych
* Pliki wykazu
* Pliki metadanych JSON (dla każdego pliku kopii zapasowej, który jest powiązany)

Zwykle ścieżka udziału sieciowego lub ścieżka zainstalowanego udziału plików platformy Azure, gdy zostanie określona jako ścieżka docelowa, zapewnia łatwiejszy dostęp do tych plików przez inne maszyny w tej samej sieci lub z tym samym udziałem plików platformy Azure.

>[!NOTE]
>Aby przywrócić pliki kopii zapasowej bazy danych w udziale plików platformy Azure zainstalowanym na docelowej zarejestrowanej maszynie wirtualnej, upewnij się, że konto główne ma uprawnienia do odczytu/zapisu w udziale plików platformy Azure.

W oparciu o typ wybranego punktu przywracania (**punkt w czasie** lub **pełny & różnicowa**) zobaczysz co najmniej jeden folder utworzony w ścieżce docelowej. Jeden z folderów o nazwie `Data_<date and time of restore>` zawiera pełne i różnicowe kopie zapasowe, a inny folder o nazwie `Log` zawiera kopie zapasowe dziennika.

Przenieś te przywrócone pliki na serwer SAP HANA, na którym chcesz je przywrócić jako bazę danych. Następnie wykonaj następujące kroki, aby przywrócić bazę danych:

1. Ustaw uprawnienia do folderu/katalogu, w którym są przechowywane pliki kopii zapasowej, przy użyciu następującego polecenia:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Uruchom następny zestaw poleceń jako `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Wygeneruj plik wykazu do przywrócenia. Wyodrębnij **BackupId** z pliku metadanych JSON dla pełnej kopii zapasowej, która zostanie użyta w dalszej części operacji przywracania. Upewnij się, że kopie zapasowe pełnych i dzienników znajdują się w różnych folderach i Usuń pliki wykazu oraz pliki metadanych JSON w tych folderach.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    W powyższym poleceniu:

    * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
    * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
    * `<PathToPlaceCatalogFile>` -folder, w którym został wygenerowany plik wykazu, musi być umieszczony

1. Przywróć przy użyciu nowo wygenerowanego pliku wykazu za pośrednictwem platformy HANA Studio lub uruchom zapytanie HDBSQL Restore z tym nowo wygenerowanym wykazem. Poniżej wymieniono zapytania HDBSQL:

    * Aby przywrócić do punktu w czasie:

        W przypadku tworzenia nowej przywróconej bazy danych Uruchom polecenie HDBSQL w celu utworzenia nowej bazy danych, `<DatabaseName>` a następnie Zatrzymaj przywracanie bazy danych. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych.

        Następnie uruchom następujące polecenie, aby przywrócić bazę danych:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` -Nazwa nowej bazy danych lub istniejącej bazy danych, która ma zostać przywrócona
        * `<Timestamp>` -Dokładne sygnatura czasowa przywracania do punktu w czasie
        * `<DatabaseName@HostName>` -Nazwa bazy danych, której kopia zapasowa jest używana do przywracania i nazwa serwera **hosta** /SAP HANA, na którym znajduje się ta baza danych. `USING SOURCE <DatabaseName@HostName>`Opcja określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż docelowa maszyna SAP HANA. Dlatego nie trzeba określać operacji przywracania wykonanej na tym samym serwerze HANA, w którym jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` -Ścieżka do pliku wykazu wygenerowanego w **kroku 3**
        * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` - **BackupId** wyodrębniony w **kroku 3**

    * Aby przywrócić do konkretnej pełnej lub różnicowej kopii zapasowej:

        W przypadku tworzenia nowej przywróconej bazy danych Uruchom polecenie HDBSQL w celu utworzenia nowej bazy danych, `<DatabaseName>` a następnie Zatrzymaj przywracanie bazy danych. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` — Nazwa nowej bazy danych lub istniejącej bazy danych, którą chcesz przywrócić.
        * `<Timestamp>` -dokładne sygnatura czasowa przywracania do punktu w czasie
        * `<DatabaseName@HostName>` — Nazwa bazy danych, której kopia zapasowa jest używana do przywracania i nazwa serwera **hosta** /SAP HANA, na którym znajduje się ta baza danych. `USING SOURCE <DatabaseName@HostName>`Opcja określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż docelowa maszyna SAP HANA. Dlatego nie trzeba określać operacji przywracania na tym samym serwerze HANA, w którym jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` -ścieżka do pliku wykazu wygenerowanego w **kroku 3**
        * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` - **BackupId** wyodrębniony w **kroku 3**

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure, przejdź do samouczka [Zarządzanie bazą danych SAP HANA na maszynie wirtualnej platformy Azure](tutorial-sap-hana-backup-cli.md)

* Aby dowiedzieć się, jak przywrócić bazę danych SAP HANA działającą na maszynie wirtualnej platformy Azure przy użyciu Azure Portal, zobacz [przywracanie baz danych SAP HANA na maszynach wirtualnych platformy Azure](./sap-hana-db-restore.md)
