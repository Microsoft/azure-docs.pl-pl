---
title: Zarządzanie replikami odczytu — interfejs wiersza polecenia interfejsu wiersza polecenia platformy Azure, interfejs API REST — usługa Azure Database dla bazy danych MariaDB
description: W tym artykule opisano sposób konfigurowania replik odczytu i zarządzania nimi w usłudze Azure Database dla mariadb przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu API REST.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: c5062bce572fbeda4143902ae6a04b31b9a89754
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025054"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database dla mariadb przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu API REST

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for MariaDB przy użyciu interfejsu API platformy Azure i interfejsu API REST.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Repliki odczytu i zarządzanie nimi można tworzyć przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Usługa Azure Database dla serwera MariaDB,](quickstart-create-mariadb-server-database-using-azure-portal.md) który będzie używany jako serwer główny. 

> [!IMPORTANT]
> Funkcja repliki odczytu jest dostępna tylko dla usługi Azure Database dla serwerów MariaDB w warstwach cenowych ogólnego przeznaczenia lub zoptymalizowanej pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

Serwer replik odczytu można utworzyć za pomocą następującego polecenia:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Polecenie `az mariadb server replica create` wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzony serwer repliki.  |
| name | serwer mydemoreplicaserver | Nazwa nowego serwera replik, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator istniejącego serwera głównego do replikacji. |

Aby utworzyć replikę odczytu `--location` międzyregionami, należy użyć parametru. 

Poniższy przykład interfejsu wiersza polecenia tworzy replikę w zachodnie stany USA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

> [!NOTE]
> Repliki odczytu są tworzone przy tej samej konfiguracji serwera co wzorzec. Konfigurację serwera repliki można zmienić po jej utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana na równych lub większych wartościach niż wzorca, aby zapewnić, że replika jest w stanie nadążyć za wzorcem.

### <a name="list-replicas-for-a-master-server"></a>Lista replik serwera głównego

Aby wyświetlić wszystkie repliki dla danego serwera głównego, uruchom następujące polecenie: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Polecenie `az mariadb server replica list` wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzony serwer repliki.  |
| nazwa-serwera | mydemoserver | Nazwa lub identyfikator serwera głównego. |

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywać replikację na serwerze repliki

> [!IMPORTANT]
> Zatrzymanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między wzorcem a repliką nie można jej cofnąć. Serwer repliki staje się następnie serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Ten serwer nie może być ponownie przekształcony w replikę.

Replikację na serwer repliki odczytu można zatrzymać za pomocą następującego polecenia:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Polecenie `az mariadb server replica stop` wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer repliki.  |
| name | serwer mydemoreplicaserver | Nazwa serwera repliki, na który ma być zatrzymana replikacja. |

### <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usunięcie odczytu serwera replik można wykonać, uruchamiając polecenie **[usuwania serwera az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Usuwanie serwera głównego

> [!IMPORTANT]
> Usunięcie serwera głównego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera głównego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer główny, można uruchomić polecenie **[delete serwera az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>Interfejs API REST
Repliki odczytu można tworzyć i zarządzać nimi za pomocą [interfejsu API usługi Azure REST](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu
Replikę odczytu można utworzyć przy użyciu [interfejsu API tworzenia:](/rest/api/mariadb/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

Jeśli `azure.replication_support` parametr nie został ustawiony na **REPLIKA** na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiony serwer, zostanie wyświetlony błąd. Wykonaj te dwa kroki przed utworzeniem repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu, co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowej. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.


> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadążyć za wszelkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Lista replik
Listę replik serwera głównego można wyświetlić za pomocą [interfejsu API listy replik:](/rest/api/mariadb/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywać replikację na serwerze repliki
Replikację między serwerem głównym a repliką odczytu można zatrzymać przy użyciu [interfejsu API aktualizacji](/rest/api/mariadb/servers/update).

Po zatrzymaniu replikacji na serwerze głównym i repliki odczytu nie można jej cofnąć. Replika odczytu staje się autonomicznym serwerem, który obsługuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera wzorca lub repliki
Aby usunąć serwer wzorca lub repliki, należy użyć [interfejsu API usuwania:](/rest/api/mariadb/servers/delete)

Po usunięciu serwera głównego replikacja wszystkich replik odczytu jest zatrzymywana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują zarówno odczyty, jak i zapisy.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [replikach odczytu](concepts-read-replicas.md)
