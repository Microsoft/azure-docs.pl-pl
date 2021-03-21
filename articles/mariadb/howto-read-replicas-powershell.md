---
title: Zarządzanie replikami odczytu — Azure Database for MariaDB Azure PowerShell
description: Informacje na temat konfigurowania replik odczytu i zarządzania nimi w programie Azure Database for MariaDB przy użyciu programu PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 713f99f67706caded0694d88ece220373920e555
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664991"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB przy użyciu programu PowerShell

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for MariaDB przy użyciu programu PowerShell. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Można tworzyć repliki odczytu i zarządzać nimi za pomocą programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

### <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

> [!IMPORTANT]
> Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której jest tworzony serwer repliki.  |
| Nazwa | mydemoreplicaserver | Nazwa nowego serwera repliki, który został utworzony. |

Aby utworzyć replikę odczytu między regionami, użyj parametru **Location** . Poniższy przykład tworzy replikę w regionie **zachodnie stany USA** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md).

Domyślnie repliki odczytu są tworzone z tą samą konfiguracją serwera co źródło, chyba że określono parametr **jednostki SKU** .

> [!NOTE]
> Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

### <a name="list-replicas-for-a-source-server"></a>Wyświetlanie listy replik dla serwera źródłowego

Aby wyświetlić wszystkie repliki dla danego serwera źródłowego, uruchom następujące polecenie:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| ServerName | mydemoserver | Nazwa lub identyfikator serwera źródłowego. |

### <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu można wykonać, uruchamiając `Remove-AzMariaDbServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Usuwanie serwera źródłowego

> [!IMPORTANT]
> Usunięcie serwera źródłowego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera źródłowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer źródłowy, można uruchomić `Remove-AzMariaDbServer` polecenie cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ponowne uruchamianie Azure Database for MariaDB serwera przy użyciu programu PowerShell](howto-restart-server-powershell.md)