---
title: 'Szybki Start: Tworzenie Azure Database for MySQL przy użyciu polecenia AZ MySQL up'
description: Przewodnik Szybki Start dotyczący tworzenia Azure Database for MySQL serwera przy użyciu interfejsu wiersza polecenia platformy Azure — polecenie.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542171"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Szybki Start: Tworzenie Azure Database for MySQL przy użyciu prostego polecenia platformy Azure — polecenie AZ MySQL up (wersja zapoznawcza)

> [!IMPORTANT]
> Polecenie [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI jest w wersji zapoznawczej.

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure jest używany do tworzenia zasobów platformy Azure i zarządzania nimi w wierszu poleceń lub w skryptach. W tym przewodniku szybki start pokazano, jak za pomocą polecenia [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) utworzyć serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz tworzenia serwera, `az mysql up` polecenie tworzy przykładową bazę danych, użytkownika root w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla komputera klienckiego. Pomaga to przyspieszyć proces tworzenia oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/authenticate-azure-cli) . Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp wartość właściwości **Identyfikator subskrypcji** w polu **AZ login** Output for the Subscription do symbolu zastępczego identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Aby użyć poleceń, zainstaluj rozszerzenie [bazy danych](/cli/azure/ext/db-up) . W przypadku zwrócenia błędu upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Utwórz serwer Azure Database for MySQL przy użyciu następującego polecenia:

```azurecli
az mysql up
```

Serwer jest tworzony z następującymi wartościami domyślnymi (chyba że zostaną ręcznie przesłonięte):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowano system | Unikatowa nazwa, która identyfikuje serwer usługi Azure Database for MySQL.
resource-group | Wygenerowano system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to Ogólnego przeznaczenia serwer 5 rdzeń z 2 rdzeni wirtualnych. Aby uzyskać więcej informacji o warstwach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/mysql/) .
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni.
geo-redundant-backup | Disabled | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Enabled (Włączony) | Określa, czy protokół SSL ma być włączony na tym serwerze, czy nie.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
Wersja | 5.7 | Wersja główna MySQL.
admin-user | Wygenerowano system | Nazwa użytkownika w przypadku logowania administratora.
admin-password | Wygenerowano system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji o `az mysql up` poleceniu i jego dodatkowych parametrach, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Po utworzeniu serwera dostępne są następujące ustawienia:

- Utworzono regułę zapory "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP komputera, `az mysql up` z którego jest uruchamiane polecenie, i zezwala na ten adres IP.
- "Zezwalaj na dostęp do usług platformy Azure" jest ustawiony na wartość włączone. To ustawienie umożliwia skonfigurowanie zapory serwera do akceptowania połączeń ze wszystkich zasobów platformy Azure, w tym zasobów spoza subskrypcji.
- `wait_timeout`Parametr jest ustawiony na 8 godzin
- Utworzono pustą bazę danych o nazwie "SampleDB"
- Utworzono nowego użytkownika o nazwie "root" z uprawnieniami do "SampleDB"

> [!NOTE]
> Azure Database for MySQL komunikuje się przez port 3306. W przypadku nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 3306 może nie być dozwolony przez zaporę sieci. Aby nawiązać połączenie z serwerem, dział IT otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po zakończeniu działania `az mysql up` polecenia zostanie wykazana lista parametrów połączenia dla popularnych języków programowania. Te parametry połączenia są wstępnie skonfigurowane z określonymi atrybutami nowo utworzonego serwera Azure Database for MySQL.

Aby ponownie wyświetlić te parametry połączenia, można użyć polecenia [AZ mysql show-Connection-String](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wszystkie zasoby utworzone w ramach szybkiego startu przy użyciu następującego polecenia. To polecenie usuwa serwer Azure Database for MySQL i grupę zasobów.

```azurecli
az mysql down --delete-group
```

Jeśli chcesz usunąć nowo utworzony serwer, możesz uruchomić polecenie [AZ MySQL Down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) .

```azurecli
az mysql down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-design-database-using-cli.md)
