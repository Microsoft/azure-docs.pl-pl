---
title: Powiązywanie usługi Azure cache for Redis z Twoją aplikacją w chmurze platformy Azure
description: Dowiedz się, jak powiązać usługę Azure cache for Redis z aplikacją w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 414f7dc64027b286d8a963452d2b86126b3c5818
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878554"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Powiązywanie usługi Azure cache for Redis z Twoją aplikacją w chmurze platformy Azure 

**Ten artykuł ma zastosowanie do:** ✔️ Java

Zamiast ręcznego konfigurowania aplikacji do rozruchu sprężynowego można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami za pomocą chmury sieci platformy Azure. W tym artykule pokazano, jak powiązać aplikację z usługą Azure cache for Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożone wystąpienie chmury Azure wiosennej
* Pamięć podręczna platformy Azure dla wystąpienia usługi Redis
* Rozszerzenie chmury Azure wiosny dla interfejsu wiersza polecenia platformy Azure

Jeśli nie masz wdrożonego wystąpienia chmury sieci platformy Azure, wykonaj kroki opisane w [przewodniku szybki start dotyczącym wdrażania aplikacji w chmurze Azure wiosennej](spring-cloud-quickstart.md).

## <a name="bind-azure-cache-for-redis"></a>Powiązywanie pamięci podręcznej platformy Azure dla Redis

1. Dodaj następującą zależność do pliku pom.xml projektu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Usuń wszystkie `spring.redis.*` właściwości z `application.properties` pliku

1. Zaktualizuj bieżące wdrożenie przy użyciu programu `az spring-cloud app update` lub Utwórz nowe wdrożenie przy użyciu programu `az spring-cloud app deployment create` .

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Przejdź do **pulpitu nawigacyjnego aplikacji** i wybierz aplikację, która ma zostać powiązana z usługą Azure cache for Redis. Ta aplikacja jest taka sama, która została zaktualizowana lub wdrożona w poprzednim kroku.

1. Wybierz pozycję **powiązanie usługi** i wybierz pozycję **Utwórz powiązanie usługi**. Wypełnij formularz, wybierając wartość **Typ powiązania** **pamięć podręczna platformy Azure dla Redis**, pamięć podręczną platformy Azure dla serwera Redis i opcję klucz **podstawowy** .

1. Uruchom ponownie aplikację. Powiązanie powinno teraz funkcjonować.

1. Aby upewnić się, że powiązanie usługi jest prawidłowe, wybierz nazwę powiązania i sprawdź jej szczegóły. `property`Pole powinno wyglądać następująco:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób powiązania aplikacji chmurowej platformy Azure z usługą Azure cache for Redis. Aby dowiedzieć się więcej na temat powiązań usług dla aplikacji, zobacz [bind z wystąpieniem Azure Database for MySQL](spring-cloud-howto-bind-mysql.md).
