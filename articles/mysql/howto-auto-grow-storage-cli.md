---
title: Skalowanie ręczne — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób włączania magazynu z możliwością skalowania przy użyciu interfejsu wiersza polecenia platformy Azure w Azure Database for MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2e2a2dadfb8bcaedb9a3210ebcab7cce6c30a8a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546929"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure Database for MySQL magazynowanie przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MySQL do wzrostu bez wpływu na obciążenie.

Serwer [osiągnięty limit magazynu](./concepts-pricing-tiers.md#reaching-the-storage-limit)jest ustawiony na tylko do odczytu. Jeśli funkcja autozwiększania rozmiaru magazynu jest włączona, w przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn zostanie zwiększony o 5 GB, gdy tylko ilość wolnego miejsca w magazynie będzie mniejsza niż 1 GB lub 10% magazynu zasobów. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](./concepts-pricing-tiers.md#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik zawiera informacje wymagające użycia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby potwierdzić wersję, w wierszu polecenia platformy Azure wpisz polecenie `az --version` . Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera MySQL

Włącz na istniejącym serwerze magazyn na serwerze, który jest powiększany, przy użyciu następującego polecenia:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Po utworzeniu nowego serwera przy użyciu następującego polecenia Włącz magazyn do ponownego zwiększania serwera:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).