---
title: 'Szybki Start: Tworzenie serwera-AZ Postgres up-Azure Database for PostgreSQL-Single Server'
description: Przewodnik Szybki Start dotyczący tworzenia Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia platformy Azure
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: c11a98c04f81e7c111faf4aa449ab2bf9a4890bb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485226"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Szybki Start: korzystanie z interfejsu wiersza polecenia platformy Azure, AZ Postgres up (wersja zapoznawcza) w celu utworzenia Azure Database for PostgreSQL-pojedynczego serwera

> [!IMPORTANT]
> Polecenie [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI jest w wersji zapoznawczej.

Azure Database for PostgreSQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku szybki start pokazano, jak za pomocą polecenia [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) utworzyć serwer Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz tworzenia serwera, `az postgres up` polecenie tworzy przykładową bazę danych, użytkownika root w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla komputera klienckiego. Te ustawienia domyślne ułatwiają przyspieszenie procesu tworzenia oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/authenticate-azure-cli) . Zanotuj Właściwość **ID** z danych wyjściowych polecenia dla odpowiadającej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp wartość właściwości **Identyfikator subskrypcji** w polu **AZ login** Output for the Subscription do symbolu zastępczego identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Aby użyć poleceń, zainstaluj rozszerzenie [bazy danych](/cli/azure/ext/db-up) . W przypadku zwrócenia błędu upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Utwórz serwer Azure Database for PostgreSQL przy użyciu następującego polecenia:

```azurecli
az postgres up
```

Serwer jest tworzony z następującymi wartościami domyślnymi (chyba że zostaną ręcznie przesłonięte):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowano system | Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL.
resource-group | Wygenerowano system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to Ogólnego przeznaczenia serwer 5 rdzeń z 2 rdzeni wirtualnych. Aby uzyskać więcej informacji o warstwach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/) .
backup-retention | 7 | Czas przechowywania kopii zapasowej. Jednostka to dni.
geo-redundant-backup | Disabled | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | zachodnie_stany_USA_2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled | Czy na tym serwerze powinien być włączony protokół TLS/SSL.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
Wersja | 10 | Wersja główna PostgreSQL.
admin-user | Wygenerowano system | Nazwa użytkownika administratora.
admin-password | Wygenerowano system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji o `az postgres up` poleceniu i jego dodatkowych parametrach, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Po utworzeniu serwera dostępne są następujące ustawienia:

- Utworzono regułę zapory "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP komputera, `az postgres up` z którego jest uruchamiane polecenie, i zezwala na ten adres IP.
- "Zezwalaj na dostęp do usług platformy Azure" jest ustawiony na wartość włączone. To ustawienie umożliwia skonfigurowanie zapory serwera do akceptowania połączeń ze wszystkich zasobów platformy Azure, w tym zasobów spoza subskrypcji.
- Utworzono pustą bazę danych o nazwie "SampleDB"
- Utworzono nowego użytkownika o nazwie "root" z uprawnieniami do "SampleDB"

> [!NOTE]
> Azure Database for PostgreSQL komunikuje się przez port 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Aby nawiązać połączenie z serwerem, dział IT otworzy port 5432.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po zakończeniu działania `az postgres up` polecenia zostanie wykazana lista parametrów połączenia dla popularnych języków programowania. Te parametry połączenia są wstępnie skonfigurowane z określonymi atrybutami nowo utworzonego serwera Azure Database for PostgreSQL.

Aby ponownie wyświetlić te parametry połączenia, można użyć polecenia [AZ Postgres show-Connection-String](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wszystkie zasoby utworzone w ramach szybkiego startu przy użyciu następującego polecenia. To polecenie usuwa serwer Azure Database for PostgreSQL i grupę zasobów.

```azurecli
az postgres down --delete-group
```

Jeśli chcesz usunąć nowo utworzony serwer, możesz uruchomić polecenie [AZ Postgres Down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) .

```azurecli
az postgres down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
