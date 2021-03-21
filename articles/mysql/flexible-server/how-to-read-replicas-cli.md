---
title: Zarządzanie replikami odczytu w Azure Database for MySQL elastycznym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure.
description: Dowiedz się, jak skonfigurować repliki odczytu i zarządzać nimi w Azure Database for MySQL elastycznym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7d63fd76a88430495c9f55200308f63b11c89d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96494311"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL elastycznym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Odczytaj repliki w Azure Database for MySQL-elastycznym serwerze jest w wersji zapoznawczej.

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi na Azure Database for MySQL elastycznym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

> [!Note]
> Replika nie jest obsługiwana na serwerze z włączonym wysoką dostępnością. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Można tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2,0](/cli/azure/install-azure-cli)
- [Azure Database for MySQL elastyczny serwer](quickstart-create-server-cli.md) , który będzie używany jako serwer źródłowy.

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

> [!IMPORTANT]
> Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć przy użyciu następującego polecenia:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co źródło. Konfigurację serwera repliki można zmienić po jego utworzeniu. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów, w tej samej lokalizacji i tej samej subskrypcji co serwer źródłowy. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](../../azure-resource-manager/management/move-resource-group-and-subscription.md) po utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie zachować ze źródłem.


### <a name="list-replicas-for-a-source-server"></a>Wyświetlanie listy replik dla serwera źródłowego

Aby wyświetlić wszystkie repliki dla danego serwera źródłowego, uruchom następujące polecenie: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między źródłem a repliką nie można jej cofnąć. Serwer repliki stał się serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Nie można ponownie wykonać tego serwera w replice.

Replikację na serwer repliki odczytu można zatrzymać przy użyciu następującego polecenia:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu można wykonać, uruchamiając polecenie **[AZ MySQL Server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Usuwanie serwera źródłowego

> [!IMPORTANT]
> Usunięcie serwera źródłowego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera źródłowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer źródłowy, możesz uruchomić polecenie **[AZ MySQL elastyczny-Server Delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)