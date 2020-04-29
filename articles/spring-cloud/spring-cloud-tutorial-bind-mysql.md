---
title: Samouczek — jak powiązać wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure
description: W tym samouczku pokazano, jak powiązać wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277542"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Samouczek: Powiąż wystąpienie Azure Database for MySQL z aplikacją w chmurze platformy Azure 

Dzięki chmurze platformy Azure można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami, a nie trzeba ręcznie konfigurować aplikacji rozruchowej ze sprężyną. W tym samouczku pokazano, jak powiązać aplikację z wystąpieniem Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Konto Azure Database for MySQL
* Interfejs wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury Azure wiosennej, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie aplikacji w chmurze platformy Azure przy użyciu Azure Portal](spring-cloud-quickstart-launch-app-portal.md) do wdrożenia pierwszej aplikacji w chmurze.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Powiązywanie aplikacji z wystąpieniem Azure Database for MySQL

1. Zanotuj nazwę użytkownika i hasło administratora konta Azure Database for MySQL. 

1. Połącz się z serwerem, Utwórz bazę danych o nazwie **TestDB** z klienta MySQL, a następnie utwórz nowe konto niebędące administratorami.

1. W pliku *pliku pom. XML* projektu Dodaj następującą zależność:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. W pliku *Application. Properties* Usuń wszystkie `spring.datasource.*` właściwości.

1. Zaktualizuj bieżące wdrożenie przez uruchomienie `az spring-cloud app update`lub Utwórz nowe wdrożenie dla tej zmiany, uruchamiając `az spring-cloud app deployment create`polecenie.  Te polecenia służą do aktualizowania lub tworzenia aplikacji z nową zależnością.

1. W Azure Portal na stronie usługi **Azure wiosennej w chmurze** Znajdź **pulpit nawigacyjny aplikacji**, a następnie wybierz aplikację, która ma zostać powiązana z wystąpieniem Azure Database for MySQL.  Jest to ta sama aplikacja, która została zaktualizowana lub wdrożona w poprzednim kroku. 

1. Wybierz pozycję **powiązanie usługi**, a następnie wybierz przycisk **Utwórz powiązanie usługi** . 

1. Wypełnij formularz, wybierając pozycję **Azure MySQL** jako **Typ powiązania**, używając tej samej nazwy bazy danych, która została użyta wcześniej, i korzystając z tej samej nazwa użytkownika i hasło zanotowane w pierwszym kroku.

1. Uruchom ponownie aplikację, a to powiązanie powinno teraz funkcjonować.

1. Aby upewnić się, że powiązanie usługi jest poprawne, wybierz nazwę powiązania, a następnie sprawdź jej szczegóły. `property` Pole powinno wyglądać następująco:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób powiązania aplikacji w chmurze platformy Azure z wystąpieniem Azure Database for MySQL.  Aby dowiedzieć się więcej o zarządzaniu usługą Azure wiosennej w chmurze, zapoznaj się z artykułem dotyczącym odnajdywania i rejestracji usług.

> [!div class="nextstepaction"]
> [Włączanie odnajdowania i rejestracji usług przy użyciu rejestru wiosennej usługi w chmurze](spring-cloud-service-registration.md)
