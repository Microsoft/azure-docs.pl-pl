---
title: Azure PowerShell Azure Database for PostgreSQL magazynu
description: W tym artykule opisano sposób włączania magazynu przy użyciu programu PowerShell w Azure Database for PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4655ce6cd572183cd92e1c8b2ac10e613ebd8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489969"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Powiększ magazyn na serwerze Azure Database for PostgreSQL przy użyciu programu PowerShell

W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for PostgreSQL do wzrostu bez wpływu na obciążenie.

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi [osiągnięcie limitu magazynu](./concepts-pricing-tiers.md#reaching-the-storage-limit) i staje się tylko do odczytu. W przypadku serwerów z 100 GB lub mniejszą ilością zasobów magazynowych rozmiar jest zwiększany o 5 GB, gdy ilość wolnego miejsca jest mniejsza niż 10%. W przypadku serwerów mających więcej niż 100 GB miejsca w magazynie, rozmiar jest zwiększany o 5%, gdy ilość wolnego miejsca jest mniejsza niż 10 GB. Maksymalne limity magazynu są stosowane zgodnie z opisem w sekcji magazyn [warstw cenowych Azure Database for PostgreSQL](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu serwera PostgreSQL

Włącz magazyn na serwerze z funkcją autowzrostu na istniejącym serwerze przy użyciu następującego polecenia:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Włącz magazyn do autouzupełniania serwera podczas tworzenia nowego serwera przy użyciu następującego polecenia:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie replik odczytu i zarządzanie nimi w Azure Database for PostgreSQL przy użyciu programu PowerShell](howto-read-replicas-powershell.md).