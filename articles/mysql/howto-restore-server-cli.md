---
title: Tworzenie kopii zapasowych i przywracanie — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee06eed1b8f54877d01a8b316c015938038879cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535405"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure

Kopie zapasowe serwerów Azure Database for MySQL są podejmowane okresowo w celu włączenia funkcji przywracania. Korzystając z tej funkcji, można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- Potrzebny jest [serwer Azure Database for MySQL i baza danych](quickstart-create-mysql-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Wybór między konfiguracją serwera na potrzeby lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera. 

> [!NOTE]
> Po utworzeniu serwera, jego rodzaju nadmiarowości, czyli geograficznie nadmiarowej a lokalnie nadmiarowy, nie można przełączyć.
>

Podczas tworzenia serwera za pomocą `az mysql server create` polecenia `--geo-redundant-backup` parametr decyduje o opcji nadmiarowości kopii zapasowej. Jeśli `Enabled` są tworzone geograficznie nadmiarowe kopie zapasowe. Lub jeśli `Disabled` są tworzone lokalnie nadmiarowe kopie zapasowe. 

Okres przechowywania kopii zapasowych jest ustawiany przez parametr `--backup-retention` . 

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-cli.md).

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Poprzedni przykład zmienia okres przechowywania kopii zapasowych z mydemoserver do 10 dni.

Okres przechowywania kopii zapasowej decyduje o tym, jak daleko w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie do określonego momentu zostało opisane w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Przywracanie do punktu w czasie serwera
Serwer można przywrócić do wcześniejszego punktu w czasie. Przywrócone dane zostaną skopiowane na nowy serwer, a istniejący serwer jest pozostawiony jako. Na przykład jeśli tabela jest przypadkowo porzucana dzisiaj, można przywrócić ją do godziny tuż przed południem. Następnie można pobrać brakującą tabelę i dane z przywróconej kopii serwera. 

Aby przywrócić serwer, użyj interfejsu wiersza polecenia platformy Azure [AZ MySQL Server Restore](/cli/azure/mysql/server#az-mysql-server-restore) .

### <a name="run-the-restore-command"></a>Uruchom polecenie Restore

Aby przywrócić serwer, w wierszu polecenia platformy Azure wpisz następujące polecenie:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore`Polecenie wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której znajduje się serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018 R-03-13T13:59:00Z | Wybierz punkt w czasie, do którego chcesz przywrócić. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu daty i godziny ISO8601. Na przykład możesz użyć własnej lokalnej strefy czasowej, takiej jak `2018-03-13T05:59:00-08:00` . Można również użyć formatu UTC Zulu, na przykład `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Po przywróceniu serwera do wcześniejszego punktu w czasie zostanie utworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej dla przywróconego serwera pozostają takie same, jak oryginalny serwer. 

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Ponadto po zakończeniu operacji przywracania istnieją dwa parametry serwera, które są resetowane do wartości domyślnych (i nie są kopiowane z serwera podstawowego) po operacji przywracania
*   time_zone — ta wartość jest ustawiana na domyślny **system** wartości
*   event_scheduler — event_scheduler jest **wyłączona** na przywróconym serwerze

Należy skopiować wartość z serwera podstawowego i ustawić ją na przywróconym serwerze przez ponowne skonfigurowanie [parametru serwera](howto-server-parameters.md)

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="geo-restore"></a>Przywracanie geograficzne
Jeśli serwer został skonfigurowany pod kątem kopii zapasowych geograficznie nadmiarowych, można utworzyć nowy serwer z kopii zapasowej istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który Azure Database for MySQL jest dostępny.  

Aby utworzyć serwer przy użyciu geograficznie nadmiarowej kopii zapasowej, użyj polecenia platformy Azure `az mysql server georestore` .

> [!NOTE]
> Gdy serwer jest tworzony po raz pierwszy, może nie być od razu dostępny do przywracania geograficznego. Wypełnienie wymaganych metadanych może potrwać kilka godzin.
>

Aby przywrócić lokalizację geograficzną serwera, w wierszu polecenia platformy Azure wpisz następujące polecenie:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
To polecenie tworzy nowy serwer o nazwie *mydemoserver-georestore* w regionie Wschodnie stany USA, który będzie należeć do *zasobu*. Jest to Ogólnego przeznaczenia, serwer generacji 5 z 8 rdzeni wirtualnych. Serwer jest tworzony na podstawie geograficznie nadmiarowej kopii zapasowej *mydemoserver*, która również znajduje się w *grupie zasobów* .

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów z istniejącego serwera, w `--source-server` parametrze należy zakwalifikować nazwę serwera, tak jak w poniższym przykładzie:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mysql server georestore`Polecenie wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów, do której należy nowy serwer.|
|name | mydemoserver-georestore | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego są używane geograficznie nadmiarowe kopie zapasowe. |
|location | eastus | Lokalizacja nowego serwera. |
|sku-name| GP_Gen5_8 | Ten parametr ustawia warstwę cenową, generowanie obliczeń i liczbę rdzeni wirtualnych nowego serwera. GP_Gen5_8 Maps do Ogólnego przeznaczenia, generacji 5 Server z 8 rdzeni wirtualnych.|

Podczas tworzenia nowego serwera przy użyciu przywracania geograficznego program dziedziczy ten sam rozmiar magazynu i warstwę cenową co serwer źródłowy. Nie można zmienić tych wartości podczas tworzenia. Po utworzeniu nowego serwera jego rozmiar magazynu może być skalowany w górę.

Po zakończeniu procesu przywracania Znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych](concepts-backup.md) usługi
- Informacje o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)
