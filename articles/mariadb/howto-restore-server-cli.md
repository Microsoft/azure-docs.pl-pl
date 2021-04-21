---
title: Tworzenie kopii zapasowej i przywracanie — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: af197f2dd144e7541f669fdd67b58c38d91b4400
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777135"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Jak tworzyć i przywracać kopię zapasową serwera w usłudze Azure Database for MariaDB użyciu interfejsu wiersza polecenia platformy Azure

Azure Database for MariaDB serwerów są okresowo tworzyć kopię zapasową w celu włączenia funkcji przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie na nowym serwerze.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Potrzebujesz serwera [Azure Database for MariaDB bazy danych](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten przewodnik z instrukcji wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Możesz dokonać wyboru między skonfigurowaniem serwera dla lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera.

> [!NOTE]
> Po utworzeniu serwera nie można przełączyć rodzaju nadmiarowości, geograficznie nadmiarowej lub lokalnie nadmiarowej.
>

Podczas tworzenia serwera za pomocą `az mariadb server create` polecenia parametr decyduje o opcji `--geo-redundant-backup` nadmiarowości kopii zapasowej. W `Enabled` przypadku programu są podejmowane geograficznie nadmiarowe kopie zapasowe. Lub jeśli `Disabled` są podejmowane lokalnie nadmiarowe kopie zapasowe.

Okres przechowywania kopii zapasowej jest ustawiany za pomocą parametru `--backup-retention` .

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz przewodnik Szybki start Azure Database for MariaDB [cli (Interfejs wiersza polecenia serwera).](quickstart-create-mariadb-server-database-using-azure-cli.md)

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Poprzedni przykład zmienia okres przechowywania kopii zapasowej serwera mydemoserver na 10 dni.

Okres przechowywania kopii zapasowych określa, jak daleko wstecz w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie do punktu w czasie jest opisane bardziej szczegółowo w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Przywracanie do punktu w czasie serwera

Serwer można przywrócić do wcześniejszego punktu w czasie. Przywrócone dane są kopiowane na nowy serwer, a istniejący serwer pozostaje w stanie, w którym się znajduje. Jeśli na przykład tabela zostanie przypadkowo porzucona w południe dzisiaj, możesz przywrócić ją do godziny tuż przed południem. Następnie możesz pobrać brakującą tabelę i dane z przywróconej kopii serwera.

Aby przywrócić serwer, użyj polecenia [az mariadb server restore interfejsu](/cli/azure/mariadb/server#az_mariadb_server_restore) wiersza polecenia platformy Azure.

### <a name="run-the-restore-command"></a>Uruchamianie polecenia przywracania

Aby przywrócić serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Polecenie `az mariadb server restore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wybierz punkt w czasie, do którym chcesz przywrócić dane. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu daty i godzin ISO8601. Możesz na przykład użyć własnej lokalnej strefy czasowej, takiej jak `2018-03-13T05:59:00-08:00` . Można również użyć formatu UTC Zulu, na przykład `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Po przywróceniu serwera do wcześniejszego punktu w czasie jest tworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej przywróconego serwera pozostają takie same jak w przypadku oryginalnego serwera. 

Po zakończeniu procesu przywracania znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie Przegląd **nowego** serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera zostaną przywrócone.

## <a name="geo-restore"></a>Przywracanie geograficzne

Jeśli skonfigurowano serwer dla geograficznie nadmiarowych kopii zapasowych, można utworzyć nowy serwer z kopii zapasowej istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, Azure Database for MariaDB jest dostępny.  

Aby utworzyć serwer przy użyciu geograficznie nadmiarowej kopii zapasowej, użyj polecenia interfejsu wiersza `az mariadb server georestore` polecenia platformy Azure.

> [!NOTE]
> Po utworzeniu serwera może on nie być natychmiast dostępny do przywracania geograficznego. Wypełnienie niezbędnych metadanych może potrwać kilka godzin.
>

Aby przywrócić geograficznie serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

To polecenie tworzy nowy serwer o nazwie *mydemoserver-georestored* w wschodnich usa, który będzie należeć *do grupy myresourcegroup.* Jest to serwer Ogólnego przeznaczenia 5. generacji z 8 rdzeniami wirtualnych. Serwer jest tworzony na podstawie geograficznie nadmiarowej kopii zapasowej serwera *mydemoserver,* który znajduje się również w grupie zasobów *myresourcegroup*

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów niż istniejący serwer, w parametrze należy zakwalifikować nazwę serwera, jak w `--source-server` poniższym przykładzie:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Polecenie `az mariadb server georestore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów, do której będzie należeć nowy serwer.|
|name | mydemoserver-georestored | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego geograficznie nadmiarowe kopie zapasowe są używane. |
|location | eastus | Lokalizacja nowego serwera. |
|sku-name| GP_Gen5_8 | Ten parametr określa warstwę cenową, generację obliczeń i liczbę rdzeni wirtualnych nowego serwera. GP_Gen5_8 mapuje na serwer Ogólnego przeznaczenia Gen 5 z 8 rdzeniami wirtualnych.|

Podczas tworzenia nowego serwera przez przywracanie geograficzne dziedziczy on ten sam rozmiar magazynu i warstwę cenową co serwer źródłowy. Tych wartości nie można zmienić podczas tworzenia. Po utworzeniu nowego serwera można skalować jego rozmiar magazynu w górę.

Po zakończeniu procesu przywracania znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie Przegląd **nowego** serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera zostaną przywrócone.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o kopiach zapasowych [usługi](concepts-backup.md)
- Dowiedz się więcej o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o [opcjach ciągłości działania](concepts-business-continuity.md)
