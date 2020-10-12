---
title: Zarządzanie replikami odczytu — Azure Database for PostgreSQL Azure PowerShell
description: Informacje na temat konfigurowania replik odczytu i zarządzania nimi w programie Azure Database for PostgreSQL przy użyciu programu PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 26c6f70f92e4c372c0ff6afbcbb3c0bb284e2f6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704807"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for PostgreSQL przy użyciu programu PowerShell

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for PostgreSQL przy użyciu programu PowerShell. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Można tworzyć repliki odczytu i zarządzać nimi za pomocą programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for PostgreSQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer podstawowy znajduje się w jednej z tych warstw cenowych.

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Serwer repliki odczytu można utworzyć przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której jest tworzony serwer repliki.  |
| Nazwa | mydemoreplicaserver | Nazwa nowego serwera repliki, który został utworzony. |

Aby utworzyć replikę odczytu między regionami, użyj parametru **Location** . Poniższy przykład tworzy replikę w regionie **zachodnie stany USA** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md).

Domyślnie repliki odczytu są tworzone z tą samą konfiguracją serwera co podstawowa, chyba że określono parametr **jednostki SKU** .

> [!NOTE]
> Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż podstawowy, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

### <a name="list-replicas-for-a-primary-server"></a>Wyświetlanie listy replik dla serwera podstawowego

Aby wyświetlić wszystkie repliki dla danego serwera podstawowego, uruchom następujące polecenie:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| ServerName | mydemoserver | Nazwa lub identyfikator serwera podstawowego. |

### <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu można wykonać, uruchamiając `Remove-AzPostgreSqlServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Usuwanie serwera podstawowego

> [!IMPORTANT]
> Usunięcie serwera podstawowego powoduje zatrzymanie replikacji na wszystkich serwerach repliki i usunięcie samego serwera podstawowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer podstawowy, można uruchomić `Remove-AzPostgreSqlServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ponowne uruchamianie Azure Database for PostgreSQL serwera przy użyciu programu PowerShell](howto-restart-server-powershell.md)
