---
title: Jak powiązać wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure
description: Dowiedz się, jak powiązać wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878368"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Powiąż wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure 

**Ten artykuł ma zastosowanie do:** ✔️ Java

Dzięki chmurze platformy Azure można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami, a nie trzeba ręcznie konfigurować aplikacji rozruchowej ze sprężyną. W tym artykule pokazano, jak powiązać aplikację z wystąpieniem Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Konto Azure Database for MySQL
* Interfejs wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury Azure wiosennej, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie aplikacji w chmurze platformy Azure przy użyciu Azure Portal](spring-cloud-quickstart.md) do wdrożenia pierwszej aplikacji w chmurze.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Powiązywanie aplikacji z wystąpieniem Azure Database for MySQL

1. Zanotuj nazwę użytkownika i hasło administratora konta Azure Database for MySQL. 

1. Połącz się z serwerem, Utwórz bazę danych o nazwie **TestDB** z klienta MySQL, a następnie utwórz nowe konto niebędące administratorami.

1. W pliku *pom.xml* projektu Dodaj następującą zależność:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. W pliku *Application. Properties* Usuń wszystkie `spring.datasource.*` właściwości.

1. Zaktualizuj bieżące wdrożenie przez uruchomienie `az spring-cloud app update` lub Utwórz nowe wdrożenie dla tej zmiany, uruchamiając polecenie `az spring-cloud app deployment create` .  Te polecenia służą do aktualizowania lub tworzenia aplikacji z nową zależnością.

1. W Azure Portal na stronie usługi **Azure wiosennej w chmurze** Znajdź **pulpit nawigacyjny aplikacji**, a następnie wybierz aplikację, która ma zostać powiązana z wystąpieniem Azure Database for MySQL.  Jest to ta sama aplikacja, która została zaktualizowana lub wdrożona w poprzednim kroku. 

1. Wybierz pozycję **powiązanie usługi**, a następnie wybierz przycisk **Utwórz powiązanie usługi** . 

1. Wypełnij formularz, wybierając pozycję **Azure MySQL** jako **Typ powiązania**, używając tej samej nazwy bazy danych, która została użyta wcześniej, i korzystając z tej samej nazwa użytkownika i hasło zanotowane w pierwszym kroku.

1. Uruchom ponownie aplikację, a to powiązanie powinno teraz funkcjonować.

1. Aby upewnić się, że powiązanie usługi jest poprawne, wybierz nazwę powiązania, a następnie sprawdź jej szczegóły. `property`Pole powinno wyglądać następująco:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób powiązania aplikacji chmurowej platformy Azure z wystąpieniem Azure Database for MySQL. Aby dowiedzieć się więcej na temat powiązań usług w aplikacji, zobacz temat [BIND a Azure Cosmos DB Database w aplikacji w chmurze platformy Azure](spring-cloud-howto-bind-cosmos.md).
