---
title: Konfiguruj wystąpienie zarządzane SQL z obsługą usługi Azure Arc
description: Konfiguruj wystąpienie zarządzane SQL z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940634"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfiguruj wystąpienie zarządzane SQL z obsługą usługi Azure Arc

W tym artykule wyjaśniono, jak skonfigurować wystąpienie zarządzane SQL z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Konfigurowanie zasobów dla wystąpienia zarządzanego SQL z obsługą usługi Azure Arc

### <a name="configure-using-azdata"></a>Konfigurowanie przy użyciu azdata

Można edytować konfigurację wystąpień zarządzanych przez usługę Azure ARC przy użyciu `azdata` interfejsu wiersza polecenia. Uruchom następujące polecenie, aby wyświetlić opcje konfiguracji. 

```
azdata arc sql mi edit --help
```

W poniższym przykładzie ustawiono procesor CPU i żądania pamięci oraz limity.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Aby wyświetlić zmiany wprowadzone w wystąpieniu zarządzanym SQL, można użyć następujących poleceń w celu wyświetlenia pliku konfiguracji YAML:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Konfigurowanie opcji serwera

Można skonfigurować ustawienia konfiguracji serwera dla usługi Azure ARC z włączonym wystąpieniem zarządzanym SQL po utworzeniu. W tym artykule opisano sposób konfigurowania ustawień, takich jak Włączanie lub wyłączanie agenta MSSQL, Włączanie określonych flag śledzenia na potrzeby scenariuszy rozwiązywania problemów.

Aby zmienić dowolne z tych ustawień, wykonaj następujące kroki:

1. Utwórz plik niestandardowy `mssql-custom.conf` zawierający ustawienia ustawień strategicznych. Poniższy przykład włącza agenta SQL i włącza flagę śledzenia 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopiuj `mssql-custom.conf` plik do `/var/opt/mssql` w `mssql-miaa` kontenerze w obszarze `master-0` . Zamień `<namespaceName>` na nazwę klastra danych Big Data.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Uruchom ponownie wystąpienie SQL Server.  Zamień `<namespaceName>` na nazwę klastra danych Big Data.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Znane ograniczenia**
- Powyższe kroki wymagają uprawnień administratora klastra Kubernetes
- Jest to uzależnione od zmiany w wersji zapoznawczej
