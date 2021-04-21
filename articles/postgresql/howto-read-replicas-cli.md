---
title: Zarządzanie replikami do odczytu — interfejs wiersza polecenia platformy Azure, interfejs API REST — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak zarządzać replikami do odczytu w usłudze Azure Database for PostgreSQL — pojedynczy serwer z interfejsu wiersza polecenia platformy Azure i interfejsu API REST
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764697"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Tworzenie replik do odczytu i zarządzanie nimi z interfejsu wiersza polecenia platformy Azure, interfejsu API REST

Z tego artykułu dowiesz się, jak tworzyć repliki do odczytu i zarządzać nimi w usłudze Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby dowiedzieć się więcej na temat replik do odczytu, zobacz [omówienie](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Obsługa replikacji na platformie Azure
[Repliki do odczytu i](concepts-read-replicas.md) [dekodowanie logiczne](concepts-logical.md) zależą od dziennika zapisu z wyprzedzeniem (WAL) bazy danych Postgres, aby uzyskać informacje. Te dwie funkcje wymagają różnych poziomów rejestrowania z poziomu programu Postgres. Dekodowanie logiczne wymaga wyższego poziomu rejestrowania niż repliki do odczytu.

Aby skonfigurować odpowiedni poziom rejestrowania, użyj parametru obsługi replikacji platformy Azure. Obsługa replikacji platformy Azure ma trzy opcje ustawień:

* **Wył.** — umieszcza najmniej informacji w węzłem WAL. To ustawienie nie jest dostępne na większości Azure Database for PostgreSQL serwerów.  
* **Replika** — bardziej szczegółowe informacje niż **w przypadku opcji Wyłączone.** Jest to minimalny poziom rejestrowania wymagany do działania [replik](concepts-read-replicas.md) do odczytu. To ustawienie jest ustawieniem domyślnym na większości serwerów.
* **Wartość logiczna** — bardziej szczegółowa niż w **przypadku repliki**. Jest to minimalny poziom rejestrowania do działania dekodowania logicznego. Repliki do odczytu działają również w przypadku tego ustawienia.


> [!NOTE]
> Podczas wdrażania replik do odczytu dla trwałych obciążeń podstawowych intensywnie intensywnie korzystających z zapisu opóźnienie replikacji może nadal rosnąć i nigdy nie nadążają one za podstawowymi obciążeniami. Może to również zwiększyć użycie magazynu w podstawowej, ponieważ pliki WAL nie zostaną usunięte, dopóki nie zostaną odebrane w replice.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Repliki do odczytu można tworzyć i zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli)
- Serwer [Azure Database for PostgreSQL jako](quickstart-create-server-up-azure-cli.md) serwer podstawowy.


### <a name="prepare-the-primary-server"></a>Przygotowywanie serwera podstawowego

1. Sprawdź wartość serwera `azure.replication_support` podstawowego. Aby repliki do odczytu działały, powinna być co najmniej REPLIKA.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Jeśli `azure.replication_support` nie jest co najmniej REPLICA, ustaw go. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Uruchom ponownie serwer, aby zastosować zmianę.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Polecenie [az postgres server replica create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| name | mydemoserver-replica | Nazwa nowo utworzonego serwera repliki. |
| source-server | mydemoserver | Nazwa lub identyfikator zasobu istniejącego serwera podstawowego do replikacji. Użyj identyfikatora zasobu, jeśli chcesz, aby grupy zasobów repliki i wzorca różniły się. |

W poniższym przykładzie interfejsu wiersza polecenia replika jest tworzona w tym samym regionie co region główny.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Aby utworzyć replikę odczytu między regionami, użyj `--location` parametru . Poniższy przykład interfejsu wiersza polecenia tworzy replikę w zachodnich usa.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, zapoznaj się z [artykułem read replica concepts (Pojęcia dotyczące repliki do odczytu).](concepts-read-replicas.md) 

Jeśli nie ustawiono parametru REPLICA na serwerze podstawowym zoptymalizowanym pod kątem Ogólnego przeznaczenia pamięci i serwer został uruchomiony ponownie, zostanie `azure.replication_support` wyświetlony błąd.  Wykonaj te dwa kroki przed utworzeniem repliki.

> [!IMPORTANT]
> Zapoznaj się [z sekcją zagadnieniami o omówieniem repliki do odczytu.](concepts-read-replicas.md#considerations)
>
> Zanim ustawienie serwera podstawowego zostanie zaktualizowane do nowej wartości, zaktualizuj ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadązyć za zmianami wprowadzonymi w wzorcu.

### <a name="list-replicas"></a>Lista replik
Listę replik serwera podstawowego można wyświetlić za pomocą polecenia [az postgres server replica list.](/cli/azure/postgres/server/replica#az_postgres_server_replica_list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwerze repliki
Replikację między serwerem podstawowym i repliką do odczytu można zatrzymać za pomocą [polecenia az postgres server replica stop.](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop)

Po zatrzymaniu replikacji na serwerze podstawowym i replice do odczytu nie można jej cofnąć. Replika do odczytu staje się serwerem autonomicznym, który obsługuje zarówno odczyty, jak i zapis. Nie można ponownie utworzyć serwera autonomicznego w replice.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Usuwanie serwera podstawowego lub serwera repliki
Aby usunąć serwer podstawowy lub serwer repliki, użyj [polecenia az postgres server delete.](/cli/azure/postgres/server#az_postgres_server_delete)

Po usunięciu serwera podstawowego replikacja do wszystkich replik do odczytu jest zatrzymywana. Repliki do odczytu stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>Interfejs API REST
Repliki do odczytu można tworzyć i zarządzać nimi przy użyciu interfejsu [API REST platformy Azure.](/rest/api/azure/)

### <a name="prepare-the-primary-server"></a>Przygotowywanie serwera podstawowego

1. Sprawdź wartość serwera `azure.replication_support` podstawowego. Aby repliki do odczytu działały, powinna być co najmniej repliką REPLICA.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Jeśli `azure.replication_support` nie jest co najmniej REPLICA, ustaw go.

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

2. [Uruchom ponownie serwer,](/rest/api/postgresql/servers/restart) aby zastosować zmianę.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Replikę do odczytu można utworzyć przy użyciu interfejsu [API tworzenia:](/rest/api/postgresql/servers/create)

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
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, zapoznaj się z [artykułem read replica concepts (Pojęcia dotyczące repliki do odczytu).](concepts-read-replicas.md) 

Jeśli nie ustawiono parametru REPLICA na serwerze podstawowym zoptymalizowanym pod kątem Ogólnego przeznaczenia pamięci i serwer został uruchomiony ponownie, zostanie `azure.replication_support` wyświetlony błąd.  Wykonaj te dwa kroki przed utworzeniem repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i ustawień magazynu co replika główna. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera podstawowego: generowanie obliczeń, rdzenie wirtualne, magazyn i okres przechowywania kopii zapasowej. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z tej warstwy.


> [!IMPORTANT]
> Zanim ustawienie serwera podstawowego zostanie zaktualizowane do nowej wartości, zaktualizuj ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadązyć za zmianami wprowadzonymi w wzorcu.

### <a name="list-replicas"></a>Lista replik
Listę replik serwera podstawowego można wyświetlić przy użyciu interfejsu [API listy replik:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwerze repliki
Replikację między serwerem podstawowym i repliką do odczytu można zatrzymać przy użyciu interfejsu [API aktualizacji](/rest/api/postgresql/servers/update).

Po zatrzymaniu replikacji na serwerze podstawowym i repliki do odczytu nie można jej cofnąć. Replika do odczytu staje się autonomicznym serwerem, który obsługuje zarówno odczyty, jak i zapis. Nie można ponownie utworzyć serwera autonomicznego w replice.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Usuwanie serwera podstawowego lub serwera repliki
Aby usunąć serwer podstawowy lub serwer repliki, użyj interfejsu [API usuwania:](/rest/api/postgresql/servers/delete)

Po usunięciu serwera podstawowego replikacja do wszystkich replik do odczytu jest zatrzymywana. Repliki do odczytu stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [replikach do odczytu w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, [jak tworzyć repliki do odczytu i zarządzać](howto-read-replicas-portal.md)nimi w Azure Portal.