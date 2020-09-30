---
title: Zarządzanie replikami odczytu — interfejs Azure Database for PostgreSQL wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać replikami odczytów w Azure Database for PostgreSQL-pojedynczym serwerze z interfejsu wiersza polecenia platformy Azure i interfejsem API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 732db7fb9eaebb437dea60f98d6c85c855d1b109
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541598"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Tworzenie replik odczytu i zarządzanie nimi w interfejsie wiersza polecenia platformy Azure, interfejs API REST

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Obsługa replikacji platformy Azure
[Odczytywanie replik](concepts-read-replicas.md) i [dekodowanie logiczne](concepts-logical.md) są zależne od dziennika Postgres zapisu (WAL) w celu uzyskania informacji. Te dwie funkcje wymagają różnych poziomów rejestrowania z Postgres. Dekodowanie logiczne wymaga wyższego poziomu rejestrowania niż repliki odczytu.

Aby skonfigurować odpowiedni poziom rejestrowania, użyj parametru Obsługa replikacji platformy Azure. Obsługa replikacji platformy Azure ma trzy opcje ustawień:

* **Wyłączone** — umieszcza najniższe informacje w Wal. To ustawienie nie jest dostępne na większości serwerów Azure Database for PostgreSQL.  
* **Replika** — większa niż **wyłączona**. Jest to minimalny poziom rejestrowania, który jest wymagany do działania [replik odczytu](concepts-read-replicas.md) . To ustawienie jest domyślne na większości serwerów.
* **Logiczne** — więcej informacji niż **replika**. Jest to minimalny poziom rejestrowania kodu logicznego do pracy. Odczytaj repliki również działają w tym ustawieniu.

Po zmianie tego parametru należy ponownie uruchomić serwer. Wewnętrznie, ten parametr ustawia parametry Postgres `wal_level` , `max_replication_slots` i `max_wal_senders` .

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Można tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md) być serwerem podstawowym.


### <a name="prepare-the-primary-server"></a>Przygotuj serwer podstawowy

1. Sprawdź wartość serwera podstawowego `azure.replication_support` . Aby repliki odczytu działały, należy mieć co najmniej replikę.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Jeśli `azure.replication_support` nie jest równa co najmniej REplika, ustaw ją. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Uruchom ponownie serwer, aby zastosować zmianę.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Polecenie [AZ Postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| name | mydemoserver — replika | Nazwa nowego serwera repliki, który został utworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator zasobu istniejącego serwera podstawowego do replikacji. Użyj identyfikatora zasobu, jeśli chcesz, aby replika i grupy zasobów głównego były różne. |

W poniższym przykładzie interfejsu wiersza polecenia replika jest tworzona w tym samym regionie co główny.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Aby utworzyć replikę odczytu między regionami, użyj `--location` parametru. Poniższy przykład interfejsu wiersza polecenia tworzy replikę w regionie zachodnie stany USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

Jeśli nie ustawisz `azure.replication_support` parametru na **replikę** na serwerze podstawowym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiono serwer, zostanie wyświetlony komunikat o błędzie. Przed utworzeniem repliki wykonaj te dwa kroki.

> [!IMPORTANT]
> Zapoznaj się z [sekcją zagadnienia w temacie Omówienie repliki odczytu](concepts-read-replicas.md#considerations).
>
> Przed zaktualizowaniem ustawienia serwera podstawowego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Wyświetlanie listy replik
Listę replik serwera podstawowego można wyświetlić za pomocą polecenia [AZ Postgres Server Replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki
Można zatrzymać replikację między serwerem podstawowym a repliką odczytu przy użyciu polecenia [AZ Postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Po zatrzymaniu replikacji na serwer podstawowy i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Usuwanie serwera podstawowego lub repliki
Aby usunąć serwer podstawowy lub replikę, użyj polecenia [AZ Postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Po usunięciu serwera podstawowego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>Interfejs API REST
Można tworzyć repliki odczytu i zarządzać nimi za pomocą [interfejsu API REST platformy Azure](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Przygotuj serwer podstawowy

1. Sprawdź wartość serwera podstawowego `azure.replication_support` . Aby repliki odczytu działały, należy mieć co najmniej replikę.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Jeśli `azure.replication_support` nie jest równa co najmniej REplika, ustaw ją.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Uruchom ponownie serwer](/rest/api/postgresql/servers/restart) , aby zastosować zmianę.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Replikę odczytu można utworzyć przy użyciu [interfejsu API tworzenia](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

Jeśli nie ustawisz `azure.replication_support` parametru na **replikę** na serwerze podstawowym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiono serwer, zostanie wyświetlony komunikat o błędzie. Przed utworzeniem repliki wykonaj te dwa kroki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki kilka ustawień można zmienić niezależnie od serwera podstawowego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania zapasowego. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.


> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera podstawowego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Wyświetlanie listy replik
Listę replik serwera podstawowego można wyświetlić za pomocą [interfejsu API listy replik](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki
Można zatrzymać replikację między serwerem podstawowym a repliką odczytu przy użyciu [interfejsu API aktualizacji](/rest/api/postgresql/servers/update).

Po zatrzymaniu replikacji na serwer podstawowy i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Usuwanie serwera podstawowego lub repliki
Aby usunąć serwer podstawowy lub z serwerem repliki, należy użyć [interfejsu API usuwania](/rest/api/postgresql/servers/delete):

Po usunięciu serwera podstawowego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytaniu replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-read-replicas-portal.md).
