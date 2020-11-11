---
title: Skalowanie ręczne — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób włączania magazynu z możliwością skalowania przy użyciu interfejsu wiersza polecenia platformy Azure w Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3c7fc5165766ff5b492f05047000814475235280
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516428"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure Database for MariaDB magazynowanie przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MariaDB do wzrostu bez wpływu na obciążenie.

Serwer [osiągnięty limit magazynu](concepts-pricing-tiers.md#reaching-the-storage-limit)jest ustawiony na tylko do odczytu. Jeśli funkcja autozwiększania rozmiaru magazynu jest włączona, w przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn zostanie zwiększony o 5 GB, gdy tylko ilość wolnego miejsca w magazynie będzie mniejsza niż 1 GB lub 10% magazynu zasobów. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](concepts-pricing-tiers.md#storage) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- Potrzebny jest [serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera MariaDB

Włącz na istniejącym serwerze magazyn na serwerze, który jest powiększany, przy użyciu następującego polecenia:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Po utworzeniu nowego serwera przy użyciu następującego polecenia Włącz magazyn do ponownego zwiększania serwera:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-metric.md).
