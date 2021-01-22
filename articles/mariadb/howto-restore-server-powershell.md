---
title: Tworzenie kopii zapasowych i przywracanie Azure PowerShell-Azure Database for MariaDB
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MariaDB przy użyciu Azure PowerShell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 211285a0ae8aaa9b82bca914f3add7fbaa700146
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664855"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Jak utworzyć kopię zapasową i przywrócić serwer Azure Database for MariaDB przy użyciu programu PowerShell

Kopie zapasowe serwerów Azure Database for MariaDB są podejmowane okresowo w celu włączenia funkcji przywracania. Korzystając z tej funkcji, można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Podczas tworzenia serwera można wybrać między skonfigurowaniem serwera na potrzeby lokalnie nadmiarowego lub geograficznie nadmiarowych kopii zapasowych.

> [!NOTE]
> Po utworzeniu serwera należy zmienić jego rodzaj nadmiarowości, Geograficznie nadmiarowy a lokalnie nadmiarowy.

Podczas tworzenia serwera za pomocą `New-AzMariaDbServer` polecenia parametr **GeoRedundantBackup** decyduje o opcji nadmiarowości kopii zapasowej. Jeśli ta **Funkcja jest włączona**, tworzone są kopie zapasowe dublowane. Lub jeśli jest **wyłączone**, tworzone są lokalnie nadmiarowe kopie zapasowe.

Okres przechowywania kopii zapasowych jest ustawiany przez parametr **BackupRetentionDay** .

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia serwera, zobacz [Tworzenie serwera Azure Database for MariaDB przy użyciu programu PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md).

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

Poprzedni przykład zmienia okres przechowywania kopii zapasowych z mydemoserver do 10 dni.

Okres przechowywania kopii zapasowej decyduje o tym, w jaki sposób można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie do określonego momentu zostało opisane w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Przywracanie do punktu w czasie serwera

Serwer można przywrócić do wcześniejszego punktu w czasie. Przywrócone dane są kopiowane na nowy serwer, a istniejący serwer pozostaje niezmieniony. Na przykład jeśli tabela zostanie przypadkowo porzucona, można przywrócić ją do godziny, w której nastąpiło odrzucanie. Następnie można pobrać brakującą tabelę i dane z przywróconej kopii serwera.

Aby przywrócić serwer programu, należy użyć `Restore-AzMariaDbServer` polecenia cmdlet programu PowerShell.

### <a name="run-the-restore-command"></a>Uruchom polecenie Restore

Aby przywrócić serwer, uruchom poniższy przykład z programu PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Zestaw parametrów **PointInTimeRestore** `Restore-AzMariaDbServer` polecenia cmdlet wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupa zasobów, w której znajduje się serwer źródłowy.  |
| Nazwa | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Wybierz punkt w czasie do przywrócenia. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu daty i godziny ISO8601. Na przykład możesz użyć własnej lokalnej strefy czasowej, takiej jak **2020-03-13T05:59:00-08:00**. Można również użyć formatu UTC Zulu, na przykład **2018 r-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Użyj trybu Wskaż w czasie, aby przywrócić. |

Po przywróceniu serwera do wcześniejszego punktu w czasie zostanie utworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej dla przywróconego serwera pozostają takie same, jak oryginalny serwer.

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie uruchomienia przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy osobno skonfigurować dla nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="geo-restore"></a>Przywracanie geograficzne

Jeśli serwer został skonfigurowany pod kątem kopii zapasowych geograficznie nadmiarowych, można utworzyć nowy serwer z kopii zapasowej istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który Azure Database for MariaDB jest dostępny.

Aby utworzyć serwer przy użyciu geograficznie nadmiarowej kopii zapasowej, użyj `Restore-AzMariaDbServer` polecenia z parametrem **UseGeoRestore** .

> [!NOTE]
> Gdy serwer jest tworzony po raz pierwszy, może nie być od razu dostępny do przywracania geograficznego. Wypełnienie wymaganych metadanych może potrwać kilka godzin.

Aby przywrócić lokalizację geograficzną serwera, uruchom następujący przykład z programu PowerShell:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

W tym przykładzie tworzony jest nowy serwer o nazwie **mydemoserver-georestore** w regionie Wschodnie stany USA, który należy do **zasobu**. Jest to Ogólnego przeznaczenia, serwer generacji 5 z 8 rdzeni wirtualnych. Serwer jest tworzony na podstawie geograficznie nadmiarowej kopii zapasowej **mydemoserver**, również **w grupie zasobów**.

Aby utworzyć nowy serwer w innej grupie zasobów z istniejącego serwera, określ nową nazwę grupy zasobów przy użyciu parametru **ResourceGroupName** , jak pokazano w następującym przykładzie:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Zestaw parametrów **Geoprzywracania** dla `Restore-AzMariaDbServer` polecenia cmdlet wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Nazwa grupy zasobów, do której należy nowy serwer.|
|Nazwa | mydemoserver-georestore | Nazwa nowego serwera. |
|Lokalizacja | eastus | Lokalizacja nowego serwera. |
|UseGeoRestore | `<SwitchParameter>` | Aby przywrócić, użyj trybu geograficznego. |

Podczas tworzenia nowego serwera przy użyciu przywracania geograficznego program dziedziczy ten sam rozmiar magazynu i warstwę cenową co serwer źródłowy, chyba że jest określony parametr **SKU** .

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie uruchomienia przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy osobno skonfigurować dla tego nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak wygenerować parametry połączenia Azure Database for MariaDB przy użyciu programu PowerShell](howto-connection-string-powershell.md)