---
title: Uruchom ponownie serwer — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ccbd0790692f065cc313b5f95d5944ad3ff716b
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515425"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Ponowne uruchamianie Azure Database for MariaDB serwera przy użyciu interfejsu wiersza polecenia platformy Azure
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MariaDB. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- Potrzebny jest [serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.


## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer przy użyciu następującego polecenia:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat sposobu ustawiania parametrów w Azure Database for MariaDB](howto-configure-server-parameters-cli.md)
