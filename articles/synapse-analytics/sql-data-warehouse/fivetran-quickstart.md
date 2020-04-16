---
title: 'Szybki start: Fivetran i hurtownia danych'
description: Wprowadzenie do fivetran i magazynu danych usługi Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8f164232a3b1782511758f93a9e9b8d17d3714d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414268"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Szybki start: Fivetran z magazynem danych 

W tym przewodniku Szybki start opisano sposób konfigurowania nowego użytkownika fivetran do pracy z magazynem danych usługi Azure Synapse Analytics aprowizowanym z pulą SQL. W tym artykule przyjęto założenie, że masz istniejący magazyn danych.

## <a name="set-up-a-connection"></a>Konfigurowanie połączenia

1. Znajdź w pełni kwalifikowaną nazwę serwera i nazwę bazy danych, których używasz do łączenia się z magazynem danych.
    
    Jeśli potrzebujesz pomocy w znalezieniu tych informacji, zobacz [Łączenie się z magazynem danych](../sql/connect-overview.md).

2. W kreatorze instalacji wybierz, czy baza danych ma być łączona bezpośrednio, czy za pomocą tunelu SSH.

   Jeśli zdecydujesz się połączyć bezpośrednio z bazą danych, należy utworzyć regułę zapory, aby zezwolić na dostęp. Ta metoda jest najprostszą i najbezpieczniejszą metodą.

   Jeśli zdecydujesz się połączyć za pomocą tunelu SSH, Fivetran łączy się z oddzielnym serwerem w sieci. Serwer udostępnia tunel SSH do bazy danych. Tej metody należy użyć, jeśli baza danych znajduje się w niedostępnej podsieci w sieci wirtualnej.

3. Dodaj adres IP **52.0.2.4** do zapory na poziomie serwera, aby zezwolić na połączenia przychodzące do wystąpienia magazynu danych z Fivetran.

   Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Konfigurowanie poświadczeń użytkownika

1. Połącz się z magazynem danych przy użyciu programu SQL Server Management Studio (SSMS) lub preferowanego narzędzia. Zaloguj się jako administrator serwera. Następnie uruchom następujące polecenia SQL, aby utworzyć użytkownika dla Fivetran:

    - W głównej bazie danych: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - W bazie danych magazynu danych:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Udziel użytkownikowi Fivetran następujących uprawnień do magazynu danych:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Uprawnienie CONTROL jest wymagane do tworzenia poświadczeń o zakresie bazy danych, które są używane, gdy użytkownik ładuje pliki z magazynu obiektów Blob platformy Azure przy użyciu polybase.

3. Dodaj odpowiednią klasę zasobów do użytkownika Fivetran. Klasa zasobów, której używasz, zależy od pamięci, która jest wymagana do utworzenia indeksu magazynu kolumn. Na przykład integracje z produktami, takimi jak Marketo i Salesforce, wymagają wyższej klasy zasobów ze względu na dużą liczbę kolumn i większą ilość danych używanych przez produkty. Wyższa klasa zasobów wymaga więcej pamięci do tworzenia indeksów magazynu kolumn.

    Zaleca się używanie klas zasobów statycznych. Można rozpocząć od `staticrc20` klasy zasobów. Klasa `staticrc20` zasobów przydziela 200 MB dla każdego użytkownika, niezależnie od poziomu wydajności, którego używasz. Jeśli indeksowanie magazynu kolumn nie powiedzie się na poziomie początkowej klasy zasobu, zwiększ klasę zasobu.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Aby uzyskać więcej informacji, przeczytaj o [limitach pamięci i współbieżności](memory-concurrency-limits.md) oraz [klasach zasobów](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Połącz się z Fivetran

Aby połączyć się z magazynem danych z konta Fivetran, wprowadź poświadczenia używane do uzyskiwania dostępu do magazynu danych: 

* Host (nazwa serwera).
* Portu.
* Bazy danych.
* Użytkownik (nazwa użytkownika powinna być **fivetran\@server_name,** gdzie *server_name* jest częścią uri hosta platformy Azure: ** _nazwa serwera\__.database.windows.net**).
* Hasło.
