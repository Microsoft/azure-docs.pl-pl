---
title: Samouczek — Powiązywanie usługi Azure cache for Redis z aplikacją w chmurze sieci Azure
description: W tym samouczku pokazano, jak powiązać usługę Azure cache for Redis z aplikacją w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277520"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Powiązywanie usługi Azure cache for Redis z Twoją aplikacją w chmurze platformy Azure 

Zamiast ręcznego konfigurowania aplikacji do rozruchu sprężynowego można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami za pomocą chmury sieci platformy Azure. W tym artykule pokazano, jak powiązać aplikację z usługą Azure cache for Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Pamięć podręczna platformy Azure dla wystąpienia usługi Redis
* Rozszerzenie chmury Azure wiosny dla interfejsu wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury sieci platformy Azure, wykonaj kroki opisane w [przewodniku szybki start dotyczącym wdrażania aplikacji w chmurze Azure wiosennej](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Powiązywanie pamięci podręcznej platformy Azure dla Redis

1. Dodaj następującą zależność do pliku pliku pom. XML projektu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Usuń wszystkie `spring.redis.*` właściwości z `application.properties` pliku

1. Zaktualizuj bieżące wdrożenie przy użyciu `az spring-cloud app update` programu lub Utwórz nowe wdrożenie przy `az spring-cloud app deployment create`użyciu programu.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Przejdź do **pulpitu nawigacyjnego aplikacji** i wybierz aplikację, która ma zostać powiązana z usługą Azure cache for Redis. Ta aplikacja jest taka sama, która została zaktualizowana lub wdrożona w poprzednim kroku.

1. Wybierz pozycję **powiązanie usługi** i wybierz pozycję **Utwórz powiązanie usługi**. Wypełnij formularz, wybierając wartość **Typ powiązania** **pamięć podręczna platformy Azure dla Redis**, pamięć podręczną platformy Azure dla serwera Redis i opcję klucz **podstawowy** .

1. Uruchom ponownie aplikację. Powiązanie powinno teraz funkcjonować.

1. Aby upewnić się, że powiązanie usługi jest prawidłowe, wybierz nazwę powiązania i sprawdź jej szczegóły. `property` Pole powinno wyglądać następująco:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób powiązania aplikacji w chmurze platformy Azure z usługą Azure cache for Redis. Aby dowiedzieć się więcej na temat powiązań usług z aplikacją, przejdź do samouczka dotyczącego powiązania aplikacji z wystąpieniem Azure Database for MySQL.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać z wystąpieniem Azure Database for MySQL](spring-cloud-tutorial-bind-mysql.md)
