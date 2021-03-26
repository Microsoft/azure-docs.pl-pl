---
title: Uruchom ponownie serwer — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można ponownie uruchomić pojedynczy serwer Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608395"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Ponowne uruchamianie Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for PostgreSQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje, gdy serwer wykona operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.
 
> [!NOTE] 
> Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania PostgreSQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem. Możesz również zwiększyć częstotliwość punktów kontrolnych. Można również dostrajać wartości parametrów powiązane z punktem kontrolnym, w tym `max_wal_size` . Zalecane jest również uruchomienie `CHECKPOINT` polecenia przed ponownym uruchomieniem serwera.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik:
- Utwórz [serwer Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer przy użyciu następującego polecenia:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat sposobu ustawiania parametrów w Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
