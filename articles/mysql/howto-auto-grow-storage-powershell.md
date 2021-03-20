---
title: Azure PowerShell Azure Database for MySQL magazynu
description: W tym artykule opisano sposób włączania magazynu przy użyciu programu PowerShell w Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8c9f6f66e484fbd9ebe5c15934936d6e5c59073
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542035"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Powiększ magazyn na serwerze Azure Database for MySQL przy użyciu programu PowerShell

W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MySQL do wzrostu bez wpływu na obciążenie.

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi [osiągnięcie limitu magazynu](./concepts-pricing-tiers.md#reaching-the-storage-limit) i staje się tylko do odczytu. W przypadku serwerów z 100 GB lub mniejszą ilością zasobów magazynowych rozmiar jest zwiększany o 5 GB, gdy ilość wolnego miejsca jest mniejsza niż 10%. W przypadku serwerów mających więcej niż 100 GB miejsca w magazynie, rozmiar jest zwiększany o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB. Maksymalne limity magazynu są stosowane zgodnie z opisem w sekcji magazyn [warstw cenowych Azure Database for MySQL](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MySql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MySql PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera MySQL

Włącz magazyn na serwerze z funkcją autowzrostu na istniejącym serwerze przy użyciu następującego polecenia:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Włącz magazyn do autouzupełniania serwera podczas tworzenia nowego serwera przy użyciu następującego polecenia:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL przy użyciu programu PowerShell](howto-read-replicas-powershell.md).