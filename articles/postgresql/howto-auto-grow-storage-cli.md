---
title: Automatyczne powiększanie magazynu — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania automatycznego powiększania magazynu przy użyciu interfejsu wiersza polecenia platformy Azure w Azure Database for PostgreSQL — pojedynczy serwer.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d16fe5ef6654ee29c3e345ff0532ed91206d86d3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366171"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Automatyczne powiększanie magazynu Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano sposób konfigurowania magazynu Azure Database for PostgreSQL serwera w celu wzrostu bez wpływu na obciążenie.

Serwer, [który osiąga limit magazynu,](./concepts-pricing-tiers.md#reaching-the-storage-limit)jest ustawiony na wartość tylko do odczytu. Jeśli automatyczne zwiększenie rozmiaru magazynu jest włączone, to w przypadku serwerów z aprowizowany magazynem o rozmiarze mniejszym niż 100 GB aprowizowany rozmiar magazynu jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie będzie niższa niż 1 GB lub 10% aprowizowanych magazynów. W przypadku serwerów z ponad 100 GB aprowizowanych magazynów aprowizowany rozmiar magazynu zwiększa się o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB rozmiaru aprowizowanych magazynów. Obowiązują maksymalne limity magazynu określone [w tym miejscu.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Wymagania wstępne

- Potrzebny jest [Azure Database for PostgreSQL serwera .](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Włączanie automatycznego wzrostu magazynu serwera PostgreSQL

Włącz automatyczne powiększanie magazynu serwera na istniejącym serwerze za pomocą następującego polecenia:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Włącz automatyczne powiększanie magazynu serwera podczas tworzenia nowego serwera za pomocą następującego polecenia:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące metryk.](howto-alert-on-metric.md)
