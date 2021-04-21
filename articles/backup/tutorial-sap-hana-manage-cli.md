---
title: 'Samouczek: zarządzanie kopiami SAP HANA DB przy użyciu interfejsu wiersza polecenia'
description: Z tego samouczka dowiesz się, jak zarządzać kopiami zapasową baz danych SAP HANA działających na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7090701e3642fd9703737060e0876c8bbfc27994
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765183"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Samouczek: zarządzanie bazami SAP HANA na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub za pośrednictwem skryptów. W tej dokumentacji szczegółowo opisano sposób zarządzania kopią zapasową bazy SAP HANA wirtualnej platformy Azure — a wszystko to za pomocą interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu [Azure Portal](./sap-hana-db-manage.md).

Użyj [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) polecenia do uruchamiania poleceń interfejsu wiersza polecenia.

Po ukończeniu tego samouczka będziesz wiedzieć, jak wykonać następujące czynności:

> [!div class="checklist"]
>
> * Monitorowanie zadań tworzenia kopii zapasowej i przywracania
> * Ochrona nowych baz danych dodanych do SAP HANA danych
> * Zmienianie zasad
> * Zatrzymywanie ochrony
> * Wznawianie ochrony

Jeśli używasz funkcji back up an [SAP HANA database in Azure](tutorial-sap-hana-backup-cli.md) using CLI to back up your SAP HANA database (Kopię zapasową bazy danych na platformie Azure przy użyciu interfejsu wiersza polecenia) używasz następujących zasobów:

* grupa zasobów o nazwie *saphanaResourceGroup*
* magazyn o nazwie *saphanaVault*
* chroniony kontener o nazwie *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* baza danych kopii zapasowej/element o *nazwie saphanadatabase;hxe;hxe*
* zasoby w *regionie westus2*

Interfejs wiersza polecenia platformy Azure ułatwia zarządzanie bazą danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure, dla których jest back up up using Azure Backup. Ten samouczek zawiera szczegółowe informacje o każdej operacji zarządzania.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorowanie zadań tworzenia kopii zapasowej i przywracania

Aby monitorować ukończone lub aktualnie uruchomione zadania (tworzenie kopii zapasowej lub przywracanie), użyj polecenia cmdlet [az backup job list.](/cli/azure/backup/job#az_backup_job_list) Interfejs wiersza polecenia umożliwia również [wstrzymanie aktualnie uruchomionego zadania lub](/cli/azure/backup/job#az_backup_job_stop) zaczekaj [na ukończenie zadania.](/cli/azure/backup/job#az_backup_job_wait)

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

## <a name="change-policy"></a>Zmienianie zasad

Aby zmienić zasady bazowe konfiguracji SAP HANA kopii zapasowej, użyj polecenia cmdlet [az backup policy set.](/cli/azure/backup/policy#az_backup_policy_set) Parametr name w tym polecenia cmdlet odwołuje się do elementu kopii zapasowej, którego zasady chcemy zmienić. W tym samouczku zastąpimy zasady bazy danych SAP HANA *saphanadatabase;hxe;hxe* nowymi zasadami *newsaphanaPolicy.* Nowe zasady można tworzyć za pomocą [polecenia cmdlet az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

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

## <a name="create-incremental-backup-policy"></a>Tworzenie zasad tworzenia przyrostowych kopii zapasowych

Aby utworzyć zasady przyrostowej kopii zapasowej, wykonaj [polecenie az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) z następującymi parametrami:

* **--backup-management-type** — obciążenie platformy Azure
* **--workload-type** — SAPHana
* **--name** — nazwa zasad
* **--policy** — plik JSON z odpowiednimi szczegółami harmonogramu i przechowywania
* **--resource-group** - Grupa zasobów magazynu
* **--vault-name** — nazwa magazynu

Przykład:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Przykładowy kod JSON (sappolicy.jssię):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

Po pomyślnym utworzeniu zasad dane wyjściowe polecenia będą wyświetlać kod JSON zasad przekazany jako parametr podczas wykonywania polecenia.

Możesz zmodyfikować następującą sekcję zasad, aby określić żądaną częstotliwość tworzenia kopii zapasowych i przechowywanie przyrostowych kopii zapasowych.

Na przykład:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Przykład:

Jeśli chcesz mieć przyrostowe kopie zapasowe tylko w sobotę i przechowywać je przez 60 dni, w zasadach należy wprowadzić następujące zmiany:

* Liczba **okresów przechowywania** aktualizacji do 60 dni
* Określ wartość **ScheduleRunDays** tylko w sobotę

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Ochrona nowych baz danych dodanych do SAP HANA danych

[Zarejestrowanie wystąpienia SAP HANA w magazynie usługi Recovery Services](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) automatycznie odnajduje wszystkie bazy danych w tym wystąpieniu.

Jednak w przypadkach, gdy nowe bazy danych zostaną później dodane do SAP HANA, użyj polecenia cmdlet [az backup protectable-item.](/cli/azure/backup/protectable-item#az_backup_protectable_item_initialize) To polecenie cmdlet odnajduje dodane nowe bazy danych.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Następnie użyj polecenia [cmdlet az backup protectable-item list,](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) aby wyświetlić listę wszystkich baz danych, które zostały odnalezione w SAP HANA wystąpieniu. Ta lista nie obejmuje jednak baz danych, na których skonfigurowano już tworzenie kopii zapasowych. Po odnalezioniu bazy danych do tworzenia kopii zapasowej zapoznaj się z tematem Enable backup on SAP HANA database (Włączanie tworzenia kopii zapasowej [w SAP HANA bazy danych).](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Nowa baza danych, której kopię zapasową chcesz wrócić, zostanie wyświetlona na tej liście. Będzie ona wyglądać następująco:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Zatrzymywanie ochrony bazy danych SAP HANA danych

Ochronę bazy danych SAP HANA można zatrzymać na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się opuścić punkty odzyskiwania, pamiętaj o tych szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone na zawsze, a całe przycinanie zostanie zatrzymane po zatrzymaniu ochrony z zachowaniem danych.
* Zostanie naliczona opłata za wystąpienie chronione i zużyty magazyn.
* Jeśli usuniesz źródło danych bez zatrzymywania tworzenia kopii zapasowych, tworzenie nowych kopii zapasowych nie powiedzie się.

Przyjrzyjmy się każdemu ze sposobów zatrzymania ochrony bardziej szczegółowo.

### <a name="stop-protection-with-retain-data"></a>Zatrzymanie ochrony z zachowaniem danych

Aby zatrzymać ochronę z zachowaniem danych, użyj [polecenia cmdlet az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

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

Aby sprawdzić stan tej operacji, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="stop-protection-without-retain-data"></a>Zatrzymywanie ochrony bez zachowania danych

Aby zatrzymać ochronę bez zachowania danych, użyj [polecenia cmdlet az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

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

Aby sprawdzić stan tej operacji, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="resume-protection"></a>Wznawianie ochrony

Po zatrzymaniu ochrony bazy danych SAP HANA z zachowaniem danych można później wznowić ochronę. Jeśli nie zachowasz danych kopii zapasowej, nie będzie można wznowić ochrony.

Aby wznowić ochronę, użyj [polecenia cmdlet az backup protection resume.](/cli/azure/backup/protection#az_backup_protection_resume)

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

Aby sprawdzić stan tej operacji, użyj [polecenia cmdlet az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć kopię zapasową bazy danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure przy użyciu maszyny Azure Portal, zobacz Backup [SAP HANA databases on Azure VMs](./backup-azure-sap-hana-database.md) (Tworzenie kopii zapasowych baz danych na maszynach wirtualnych platformy Azure)

* Aby dowiedzieć się, jak zarządzać kopią zapasową bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure przy użyciu programu Azure Portal, zobacz Manage [Backed up SAP HANA databases on Azure VM](./sap-hana-db-manage.md) (Zarządzanie kopiami zapasową baz danych na maszynie wirtualnej platformy Azure)