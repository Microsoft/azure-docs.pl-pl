---
title: Azure PowerShell Azure Database for MariaDB magazynu
description: W tym artykule opisano sposób włączania magazynu przy użyciu programu PowerShell w Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87438c406ea2b31942003d9211cf5e285628f15d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665161"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Powiększ magazyn na serwerze Azure Database for MariaDB przy użyciu programu PowerShell

W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MariaDB do wzrostu bez wpływu na obciążenie.

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi [osiągnięcie limitu magazynu](concepts-pricing-tiers.md#reaching-the-storage-limit) i staje się tylko do odczytu. W przypadku serwerów z 100 GB lub mniejszą ilością zasobów magazynowych rozmiar jest zwiększany o 5 GB, gdy ilość wolnego miejsca jest mniejsza niż 10%. W przypadku serwerów mających więcej niż 100 GB miejsca w magazynie, rozmiar jest zwiększany o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB. Maksymalne limity magazynu są stosowane zgodnie z opisem w sekcji magazyn [warstw cenowych Azure Database for MariaDB](concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-mariadb-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera MariaDB

Włącz magazyn na serwerze z funkcją autowzrostu na istniejącym serwerze przy użyciu następującego polecenia:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Włącz magazyn do autouzupełniania serwera podczas tworzenia nowego serwera przy użyciu następującego polecenia:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB przy użyciu programu PowerShell](howto-read-replicas-powershell.md).
