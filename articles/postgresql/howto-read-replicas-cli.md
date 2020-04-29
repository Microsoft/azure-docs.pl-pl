---
title: Zarządzanie replikami odczytu — interfejs Azure Database for PostgreSQL wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać replikami odczytów w Azure Database for PostgreSQL-pojedynczym serwerze z interfejsu wiersza polecenia platformy Azure i interfejsem API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774804"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Tworzenie replik odczytu i zarządzanie nimi w interfejsie wiersza polecenia platformy Azure, interfejs API REST

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Można tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md) być serwerem głównym.


### <a name="prepare-the-master-server"></a>Przygotowywanie serwera głównego
Te kroki muszą być używane do przygotowywania serwera głównego w warstwach zoptymalizowanych pod kątem Ogólnego przeznaczenia lub pamięci.

`azure.replication_support` Parametr musi być ustawiony na **replikę** na serwerze głównym. Po zmianie tego parametru statycznego ponowne uruchomienie serwera jest wymagane, aby zmiany zaczęły obowiązywać.

1. Ustaw `azure.replication_support` na replikę.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Jeśli zostanie wyświetlony błąd "podano nieprawidłową wartość" podczas próby ustawienia platformy Azure. replication_support z poziomu interfejsu wiersza polecenia platformy Azure, prawdopodobnie serwer ma już ustawioną replikę. Usterka uniemożliwia poprawne odzwierciedlenie tego ustawienia na nowszych serwerach, na których replika jest domyślnie wewnętrzna. <br><br>
> Możesz pominąć kroki przygotowywania wzorca i przejść do tworzenia repliki. <br><br>
> Jeśli chcesz potwierdzić, że serwer jest w tej kategorii, odwiedź stronę replikacji serwera w Azure Portal. Wartość "Wyłącz replikację" zostanie wyszarzona, a na pasku narzędzi zostanie uaktywniona wartość "Dodaj replikę".

2. Uruchom ponownie serwer, aby zastosować zmianę.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

Polecenie [AZ Postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| name | mydemoserver — replika | Nazwa nowego serwera repliki, który został utworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator zasobu istniejącego serwera głównego, z którego ma być wykonywana replikacja. |

W poniższym przykładzie interfejsu wiersza polecenia replika jest tworzona w tym samym regionie co główny.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Aby utworzyć replikę odczytu między regionami, `--location` Użyj parametru. Poniższy przykład interfejsu wiersza polecenia tworzy replikę w regionie zachodnie stany USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

Jeśli nie ustawisz `azure.replication_support` parametru na **replikę** na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiono serwer, zostanie wyświetlony komunikat o błędzie. Przed utworzeniem repliki wykonaj te dwa kroki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania zapasowego. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Wyświetlanie listy replik
Listę replik serwera głównego można wyświetlić za pomocą polecenia [AZ Postgres Server Replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki
Można zatrzymać replikację między serwerem głównym a repliką odczytu przy użyciu polecenia [AZ Postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Po zatrzymaniu replikacji na serwer główny i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera głównego lub repliki
Aby usunąć serwer główny lub serwera repliki, użyj polecenia [AZ Postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Po usunięciu serwera głównego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>Interfejs API REST
Można tworzyć repliki odczytu i zarządzać nimi za pomocą [interfejsu API REST platformy Azure](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Przygotowywanie serwera głównego
Te kroki muszą być używane do przygotowywania serwera głównego w warstwach zoptymalizowanych pod kątem Ogólnego przeznaczenia lub pamięci.

`azure.replication_support` Parametr musi być ustawiony na **replikę** na serwerze głównym. Po zmianie tego parametru statycznego ponowne uruchomienie serwera jest wymagane, aby zmiany zaczęły obowiązywać.

1. Ustaw `azure.replication_support` na replikę.

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

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu
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

Jeśli nie ustawisz `azure.replication_support` parametru na **replikę** na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiono serwer, zostanie wyświetlony komunikat o błędzie. Przed utworzeniem repliki wykonaj te dwa kroki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania zapasowego. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.


> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Wyświetlanie listy replik
Listę replik serwera głównego można wyświetlić za pomocą [interfejsu API listy replik](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki
Można zatrzymać replikację między serwerem głównym a repliką odczytu przy użyciu [interfejsu API aktualizacji](/rest/api/postgresql/servers/update).

Po zatrzymaniu replikacji na serwer główny i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

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

### <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera głównego lub repliki
Aby usunąć serwer główny lub serwera repliki, należy użyć [interfejsu API usuwania](/rest/api/postgresql/servers/delete):

Po usunięciu serwera głównego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytaniu replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-read-replicas-portal.md).
