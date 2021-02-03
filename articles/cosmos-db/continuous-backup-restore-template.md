---
title: Użyj szablonu ARM, aby skonfigurować ciągłą kopię zapasową i przywracanie do punktu w czasie w Azure Cosmos DB.
description: Dowiedz się, jak udostępnić konto z ciągłymi kopiami zapasowymi i przywracać dane przy użyciu szablonów Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527554"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>Konfigurowanie i zarządzanie ciągłymi kopiami zapasowymi oraz przywracaniem do punktu w czasie za pomocą szablonów Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Funkcja przywracania do punktu w czasie Azure Cosmos DBa ułatwia odzyskanie od przypadkowej zmiany w kontenerze, przywrócenie usuniętego konta, bazy danych lub kontenera lub przywrócenie do dowolnego regionu (w którym istnieją kopie zapasowe). Tryb ciągłej kopii zapasowej umożliwia przywracanie w dowolnym momencie w ciągu ostatnich 30 dni.

W tym artykule opisano sposób aprowizacji konta z ciągłymi kopiami zapasowymi i przywracania danych przy użyciu szablonów Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Inicjowanie obsługi administracyjnej konta przy użyciu ciągłej kopii zapasowej

Za pomocą szablonów Azure Resource Manager można wdrożyć konto Azure Cosmos DB z trybem ciągłym. Podczas definiowania szablonu w celu aprowizacji konta należy uwzględnić parametr "backupPolicy", jak pokazano w następującym przykładzie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Następnie wdróż szablon przy użyciu Azure PowerShell lub interfejsu wiersza polecenia. W poniższym przykładzie pokazano, jak wdrożyć szablon za pomocą polecenia CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Przywracanie przy użyciu szablonu Menedżer zasobów

Konto można również przywrócić przy użyciu szablonu Menedżer zasobów. Podczas definiowania szablonu należy uwzględnić następujące parametry:

* Ustaw parametr "createmode" na "Restore"
* Zdefiniuj "restoreParameters", Zauważ, że wartość "restoreSource" jest wyodrębniana z danych wyjściowych `az cosmosdb restorable-database-account list` polecenia dla konta źródłowego. Atrybut identyfikatora wystąpienia dla nazwy konta jest używany do przywracania.
* Ustaw parametr "RestoreMode" na "PointInTime" i skonfiguruj wartość "restoreTimestampInUtc".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Następnie wdróż szablon przy użyciu Azure PowerShell lub interfejsu wiersza polecenia. W poniższym przykładzie pokazano, jak wdrożyć szablon za pomocą polecenia CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md), [programu PowerShell](continuous-backup-restore-command-line.md)lub [Azure Portal](continuous-backup-restore-portal.md).
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.