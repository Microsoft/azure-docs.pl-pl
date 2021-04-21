---
title: Tworzenie i przywracanie kopii zapasowych — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8c8b0f37729ea20a62838d736dbed59f05c584c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780429"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak tworzyć i przywracać kopię zapasową serwera w usłudze Azure Database for MySQL użyciu interfejsu wiersza polecenia platformy Azure

Azure Database for MySQL są okresowo tworzyć kopię zapasową w celu włączenia funkcji przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- Potrzebujesz serwera [Azure Database for MySQL bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Podczas tworzenia serwera można dokonać wyboru między skonfigurowaniem serwera dla lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych. 

> [!NOTE]
> Po utworzeniu serwera nie można przełączyć rodzaju jego nadmiarowości geograficznie nadmiarowej lub lokalnie nadmiarowej.
>

Podczas tworzenia serwera za pomocą `az mysql server create` polecenia parametr decyduje o opcji `--geo-redundant-backup` nadmiarowości kopii zapasowej. W `Enabled` przypadku programu są podejmowane geograficznie nadmiarowe kopie zapasowe. Lub jeśli `Disabled` są podejmowane lokalnie nadmiarowe kopie zapasowe. 

Okres przechowywania kopii zapasowej jest ustawiany za pomocą parametru `--backup-retention` . 

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [przewodnik Szybki start](quickstart-create-mysql-server-database-using-azure-cli.md)Azure Database for MySQL wiersza polecenia serwera .

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Poprzedni przykład zmienia okres przechowywania kopii zapasowej serwera mydemoserver na 10 dni.

Okres przechowywania kopii zapasowych określa, jak daleko do tyłu w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie do punktu w czasie jest opisane bardziej szczegółowo w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Przywracanie do punktu w czasie serwera
Serwer można przywrócić do wcześniejszego punktu w czasie. Przywrócone dane są kopiowane na nowy serwer, a istniejący serwer pozostaje bez danych. Jeśli na przykład tabela zostanie przypadkowo porzucona w południe dzisiaj, możesz przywrócić ją do godziny tuż przed południem. Następnie możesz pobrać brakującą tabelę i dane z przywróconej kopii serwera. 

Aby przywrócić serwer, użyj polecenia [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) interfejsu wiersza polecenia platformy Azure.

### <a name="run-the-restore-command"></a>Uruchamianie polecenia przywracania

Aby przywrócić serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Polecenie `az mysql server restore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wybierz punkt w czasie, do którym chcesz przywrócić dane. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu daty i godzin ISO8601. Możesz na przykład użyć własnej lokalnej strefy czasowej, takiej jak `2018-03-13T05:59:00-08:00` . Możesz również użyć formatu UTC Zulu, na przykład `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Po przywróceniu serwera do wcześniejszego punktu w czasie jest tworzony nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości lokalizacji i warstwy cenowej przywróconego serwera pozostają takie same jak w przypadku oryginalnego serwera. 

Po zakończeniu procesu przywracania znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie Przegląd **nowego** serwera.

Ponadto po zakończeniu operacji przywracania istnieją dwa parametry serwera, które są resetowane do wartości domyślnych (i nie są kopiowane z serwera podstawowego) po operacji przywracania
*   time_zone — ta wartość do ustawienia na WARTOŚĆ **DOMYŚLNA SYSTEM**
*   event_scheduler — event_scheduler jest wyłączona **na** przywróconym serwerze

Należy skopiować wartość z serwera podstawowego i ustawić ją na przywróconym serwerze przez ponowne skonfigurowanie [parametru serwera](howto-server-parameters.md)

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera zostaną przywrócone.

## <a name="geo-restore"></a>Przywracanie geograficzne
Jeśli skonfigurowano serwer dla geograficznie nadmiarowych kopii zapasowych, można utworzyć nowy serwer na podstawie kopii zapasowej istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, Azure Database for MySQL jest dostępny.  

Aby utworzyć serwer przy użyciu geograficznie nadmiarowej kopii zapasowej, użyj polecenia interfejsu wiersza `az mysql server georestore` polecenia platformy Azure.

> [!NOTE]
> Przy pierwszym utworzeniu serwera może on nie być natychmiast dostępny do przywracania geograficznego. Wypełnienie niezbędnych metadanych może potrwać kilka godzin.
>

Aby przywrócić geograficznie serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
To polecenie tworzy nowy serwer o nazwie *mydemoserver-georestored* w wschodniego usa, który będzie należeć *do myresourcegroup*. Jest to serwer Ogólnego przeznaczenia 5. generacji z 8 rdzeniami wirtualnych. Serwer jest tworzony na podstawie geograficznie nadmiarowej kopii zapasowej serwera *mydemoserver,* który znajduje się również w grupie zasobów *myresourcegroup*

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów niż istniejący serwer, w parametrze należy zakwalifikować nazwę serwera, jak w `--source-server` poniższym przykładzie:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Polecenie `az mysql server georestore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów, do której będzie należeć nowy serwer.|
|name | mydemoserver-georestored | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego geograficznie nadmiarowe kopie zapasowe są używane. |
|location | eastus | Lokalizacja nowego serwera. |
|sku-name| GP_Gen5_8 | Ten parametr określa warstwę cenową, generację zasobów obliczeniowych i liczbę rdzeni wirtualnych nowego serwera. GP_Gen5_8 mapuje na serwer Ogólnego przeznaczenia 5. generacji z 8 rdzeniami wirtualnych.|

Podczas tworzenia nowego serwera przez przywracanie geograficzne dziedziczy on ten sam rozmiar magazynu i warstwę cenową co serwer źródłowy. Tych wartości nie można zmienić podczas tworzenia. Po utworzeniu nowego serwera można skalować jego rozmiar magazynu w górę.

Po zakończeniu procesu przywracania znajdź nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami. Nowy serwer ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie Przegląd **nowego** serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Reguły zapory z oryginalnego serwera zostaną przywrócone.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o kopiach zapasowych [usługi](concepts-backup.md)
- Dowiedz się więcej o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o [opcjach ciągłości działania](concepts-business-continuity.md)
