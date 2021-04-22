---
title: 'Szybki start: tworzenie Azure Database for MySQL pomocą az mysql up'
description: Przewodnik Szybki start dotyczący tworzenia serwera Azure Database for MySQL użyciu polecenia up interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d753061c6141dd0ca75415cab5502e7fa350cd90
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873545"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Szybki start: tworzenie aplikacji Azure Database for MySQL pomocą prostego polecenia interfejsu wiersza polecenia platformy Azure — az mysql up (wersja zapoznawcza)

> [!IMPORTANT]
> Polecenie [az mysql up interfejsu](/cli/azure/mysql#az_mysql_up) wiersza polecenia platformy Azure jest w wersji zapoznawczej.

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub w skryptach. W tym przewodniku Szybki start pokazano, jak za pomocą polecenia [az mysql up](/cli/azure/mysql#az_mysql_up) utworzyć serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz utworzenia serwera polecenie tworzy przykładową bazę danych, użytkownika głównego w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla `az mysql up` komputera klienckiego. Pomaga to przyspieszyć proces tworzenia oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/authenticate-azure-cli) Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **subscription ID** z danych **wyjściowych az login** subskrypcji symbolem zastępczym identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Aby użyć poleceń , zainstaluj [rozszerzenie db-up.](/cli/azure/) Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Utwórz serwer Azure Database for MySQL przy użyciu następującego polecenia:

```azurecli
az mysql up
```

Serwer zostanie utworzony z następującymi wartościami domyślnymi (chyba że zostaną ręcznie przesłonięcia):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowany przez system | Unikatowa nazwa, która identyfikuje serwer usługi Azure Database for MySQL.
resource-group | Wygenerowany przez system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to serwer Ogólnego przeznaczenia Gen5 z 2 rdzeniami wirtualnych. Zobacz naszą [stronę cennika,](https://azure.microsoft.com/pricing/details/mysql/) aby uzyskać więcej informacji o warstwach.
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni.
geo-redundant-backup | Disabled | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Enabled (Włączony) | Określa, czy protokół SSL powinien być włączony dla tego serwera.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
Wersja | 5.7 | Wersja główna MySQL.
admin-user | Wygenerowane przez system | Nazwa użytkownika w przypadku logowania administratora.
admin-password | Wygenerowane przez system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji na temat `az mysql up` polecenia i jego dodatkowych parametrów, zobacz dokumentację interfejsu wiersza polecenia platformy [Azure](/cli/azure/mysql#az_mysql_up).

Po utworzeniu serwera zostaną w nim ustawienia następujące:

- Tworzona jest reguła zapory o nazwie "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP maszyny, z których jest uruchamiane `az mysql up` polecenie, i zezwala na ten adres IP.
- Ustawienie "Zezwalaj na dostęp do usług platformy Azure" ma wartość WŁ. To ustawienie konfiguruje zaporę serwera tak, aby akceptowała połączenia ze wszystkich zasobów platformy Azure, w tym zasobów poza subskrypcją.
- Parametr `wait_timeout` jest ustawiony na 8 godzin
- Tworzona jest pusta baza danych o nazwie "sampledb"
- Zostanie utworzony nowy użytkownik o nazwie "root" z uprawnieniami do "sampledb"

> [!NOTE]
> Azure Database for MySQL się za pośrednictwem portu 3306. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 3306 może być dozwolony przez zaporę sieciową. Aby nawiązać połączenie z serwerem, dział IT otwórz port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po zakończeniu polecenia zostanie zwrócona lista ciągów połączenia dla popularnych języków `az mysql up` programowania. Te parametry połączenia są wstępnie skonfigurowane przy użyciu określonych atrybutów nowo utworzonego Azure Database for MySQL serwera.

Możesz użyć polecenia [az mysql show-connection-string,](/cli/azure/mysql#az_mysql_show_connection_string) aby ponownie wyświetlić listę tych parametrów połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wszystkie zasoby utworzone w przewodniku Szybki start za pomocą następującego polecenia. To polecenie usuwa Azure Database for MySQL i grupę zasobów.

```azurecli
az mysql down --delete-group
```

Jeśli chcesz usunąć nowo utworzony serwer, możesz uruchomić [polecenie az mysql down.](/cli/azure/mysql#az_mysql_down)

```azurecli
az mysql down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-design-database-using-cli.md)
