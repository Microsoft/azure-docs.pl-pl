---
title: Zarządzanie replikami odczytu — Azure Database for MySQL Azure PowerShell
description: Informacje na temat konfigurowania replik odczytu i zarządzania nimi w programie Azure Database for MySQL przy użyciu programu PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c85af0f4078010fa5b6a1d116b3bfda942c0490c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816936"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL przy użyciu programu PowerShell

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for MySQL przy użyciu programu PowerShell. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Można tworzyć repliki odczytu i zarządzać nimi za pomocą programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MySql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MySql PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MySQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

> [!IMPORTANT]
> Gdy tworzysz replikę dla wzorca, który nie ma istniejących replik, wzorzec zostanie najpierw uruchomiony ponownie w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której jest tworzony serwer repliki.  |
| Nazwa | mydemoreplicaserver | Nazwa nowego serwera repliki, który został utworzony. |

Aby utworzyć replikę odczytu między regionami, użyj parametru **Location** . Poniższy przykład tworzy replikę w regionie **zachodnie stany USA** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md).

Domyślnie repliki odczytu są tworzone z tą samą konfiguracją serwera co wzorzec, chyba że określono parametr **SKU** .

> [!NOTE]
> Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż wzorzec, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

### <a name="list-replicas-for-a-master-server"></a>Wyświetlanie listy replik dla serwera głównego

Aby wyświetlić wszystkie repliki dla danego serwera głównego, uruchom następujące polecenie:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| ServerName | mydemoserver | Nazwa lub identyfikator serwera głównego. |

### <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu można wykonać, uruchamiając `Remove-AzMySqlServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Usuwanie serwera głównego

> [!IMPORTANT]
> Usunięcie serwera głównego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera głównego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer główny, można uruchomić `Remove-AzMySqlServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ponowne uruchamianie Azure Database for MySQL serwera przy użyciu programu PowerShell](howto-restart-server-powershell.md)
