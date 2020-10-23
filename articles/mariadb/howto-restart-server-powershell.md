---
title: Uruchom ponownie serwer-Azure PowerShell-Azure Database for MariaDB
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB przy użyciu programu PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ad8ea20e26c71a4775e98d3457204ff2181ee493
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425814"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Ponowne uruchamianie Azure Database for MariaDB serwera przy użyciu programu PowerShell

W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MariaDB. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje podczas operacji.

Ponowne uruchomienie serwera jest blokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MariaDB. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer przy użyciu następującego polecenia:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie serwera Azure Database for MariaDB przy użyciu programu PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)