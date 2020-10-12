---
title: Magazyn z autoprzyrostem — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak skonfigurować funkcję autozwiększania magazynu przy użyciu interfejsu wiersza polecenia platformy Azure w ramach Azure Database for PostgreSQL-jednego serwera.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4d4d9c6ac3eb4e9b0642f1ecb714b19a5029a314
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87493792"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL magazynowanie — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for PostgreSQL do wzrostu bez wpływu na obciążenie.

Serwer [osiągnięty limit magazynu](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)jest ustawiony na tylko do odczytu. Jeśli funkcja autozwiększania rozmiaru magazynu jest włączona, w przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn zostanie zwiększony o 5 GB, gdy tylko ilość wolnego miejsca w magazynie będzie mniejsza niż 1 GB lub 10% magazynu zasobów. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik zawiera informacje wymagające użycia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby potwierdzić wersję, w wierszu polecenia platformy Azure wpisz polecenie `az --version` . Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera PostgreSQL

Włącz na istniejącym serwerze magazyn na serwerze, który jest powiększany, przy użyciu następującego polecenia:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Po utworzeniu nowego serwera przy użyciu następującego polecenia Włącz magazyn do ponownego zwiększania serwera:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).
