---
title: Uruchom ponownie serwer-Azure PowerShell-Azure Database for PostgreSQL
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for PostgreSQL przy użyciu programu PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5037975bcfbf9b19de5cc203d26e1caf0f85893f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710145"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>Ponowne uruchamianie Azure Database for PostgreSQL serwera przy użyciu programu PowerShell

W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for PostgreSQL. Może być konieczne ponowne uruchomienie serwera ze względów konserwacyjnych, co powoduje krótkie przestoje podczas operacji.

Ponowne uruchomienie serwera jest blokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania PostgreSQL. Aby skrócić czas ponownego uruchomienia, zalecamy zminimalizowanie liczby działań występujących na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer przy użyciu następującego polecenia:

```azurepowershell-interactive
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie serwera Azure Database for PostgreSQL przy użyciu programu PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)
