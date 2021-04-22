---
title: 'Szybki start: tworzenie serwera — az postgres up — Azure Database for PostgreSQL — pojedynczy serwer'
description: Przewodnik Szybki start dotyczący tworzenia aplikacji Azure Database for PostgreSQL — pojedynczy serwer przy użyciu polecenia interfejsu wiersza polecenia platformy Azure (interfejs wiersza polecenia).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f0dc56dcaad73ff945d319cf98dc30483ee07e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875093"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Szybki start: używanie polecenia interfejsu wiersza polecenia platformy Azure az postgres up (wersja zapoznawcza) w celu utworzenia Azure Database for PostgreSQL — pojedynczy serwer

> [!IMPORTANT]
> Polecenie [az postgres up interfejsu wiersza](/cli/azure/postgres#az_postgres_up) polecenia platformy Azure jest w wersji zapoznawczej.

Azure Database for PostgreSQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start pokazano, jak za pomocą polecenia [az postgres up](/cli/azure/postgres#az_postgres_up) utworzyć serwer Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz utworzenia serwera polecenie tworzy przykładową bazę danych, czyli użytkownika głównego w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla `az postgres up` komputera klienckiego. Te wartości domyślne pomagają przyspieszyć proces tworzenia oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga lokalnego uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/authenticate-azure-cli) **Zanotuj właściwość ID** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **subscription ID** z danych **wyjściowych az login** subskrypcji symbolem zastępczym identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Aby użyć poleceń , zainstaluj [rozszerzenie db-up.](/cli/azure/) Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Utwórz serwer Azure Database for PostgreSQL za pomocą następującego polecenia:

```azurecli
az postgres up
```

Serwer zostanie utworzony z następującymi wartościami domyślnymi (chyba że zostaną ręcznie przesłonięcia):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowane przez system | Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL.
resource-group | Wygenerowane przez system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to serwer Ogólnego przeznaczenia Gen5 z 2 rdzeniami wirtualnych. Zobacz naszą [stronę cennika,](https://azure.microsoft.com/pricing/details/postgresql/) aby uzyskać więcej informacji o warstwach.
backup-retention | 7 | Czas przechowywania kopii zapasowej. Jednostka to dni.
geo-redundant-backup | Disabled | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled | Określa, czy protokół TLS/SSL powinien być włączony dla tego serwera.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
Wersja | 10 | Wersja główna PostgreSQL.
admin-user | Wygenerowane przez system | Nazwa użytkownika administratora.
admin-password | Wygenerowane przez system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji na temat `az postgres up` polecenia i jego dodatkowych parametrów, zobacz dokumentację interfejsu wiersza polecenia platformy [Azure.](/cli/azure/postgres#az_postgres_up)

Utworzony serwer zawiera następujące ustawienia:

- Tworzona jest reguła zapory o nazwie "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP maszyny, z która uruchamia polecenie, `az postgres up` i zezwala na ten adres IP.
- Ustawienie "Zezwalaj na dostęp do usług platformy Azure" ma wartość WŁ. To ustawienie konfiguruje zaporę serwera tak, aby akceptowała połączenia ze wszystkich zasobów platformy Azure, w tym zasobów poza subskrypcją.
- Tworzona jest pusta baza danych o nazwie "sampledb"
- Tworzony jest nowy użytkownik o nazwie "root" z uprawnieniami do bazy danych "sampledb"

> [!NOTE]
> Azure Database for PostgreSQL się za pośrednictwem portu 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Dział IT musi otworzyć port 5432 w celu nawiązania połączenia z serwerem.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po zakończeniu polecenia zostanie zwrócona lista ciągów połączenia dla popularnych języków `az postgres up` programowania. Te parametry połączenia są wstępnie skonfigurowane przy użyciu określonych atrybutów nowo utworzonego Azure Database for PostgreSQL serwera.

Możesz użyć polecenia [az postgres show-connection-string,](/cli/azure/postgres#az_postgres_show_connection_string) aby ponownie wyświetlić listę tych parametrów połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wszystkie zasoby utworzone w przewodniku Szybki start za pomocą następującego polecenia. To polecenie usuwa Azure Database for PostgreSQL i grupę zasobów.

```azurecli
az postgres down --delete-group
```

Jeśli chcesz usunąć nowo utworzony serwer, możesz uruchomić [polecenie az postgres down.](/cli/azure/postgres#az_postgres_down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
