---
title: Szybki start, aby dowiedzieć się, jak korzystać z konfiguracji aplikacji platformy Azure
description: Przewodnik Szybki start dotyczący korzystania z usługi Azure App Configuration z aplikacjami Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: 85b966a9423fa6c8b2dc9e97cab1e24d9756caa4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687352"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Szybki start: tworzenie aplikacji Java Spring za pomocą konfiguracji aplikacji azure

W tym przewodniku Szybki start można włączyć konfigurację aplikacji platformy Azure do aplikacji Java Spring, aby scentralizować przechowywanie i zarządzanie ustawieniami aplikacji oddzielnie od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- Obsługiwany zestaw [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) w wersji 8.
- [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3.0 lub wyższej.

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **Eksplorator** > konfiguracji **+ Utwórz** > **wartość klucza,** aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | /application/config.message | Hello |

    Pozostaw **etykietę** i **typ zawartości** puste na razie.

7. Wybierz przycisk **Zastosuj**.

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Użyj [spring initializr,](https://start.spring.io/) aby utworzyć nowy projekt Spring Boot.

1. Przejdź do <https://start.spring.io/>.

1. Określ następujące opcje:

   - Wygeneruj projekt **Maven** z użyciem języka **Java**.
   - Określ wersję **rozruchu sprężyny,** która jest równa lub większa niż 2.0.
   - Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   - Dodaj **wiosenną** zależność sieci Web.

1. Po określeniu poprzednich opcji wybierz pozycję **Generuj projekt**. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Po wyodrębnieniu plików w systemie lokalnym prosta aplikacja Spring Boot jest gotowa do edycji. Znajdź plik *pom.xml* w katalogu głównym swojej aplikacji.

1. Otwórz plik *pom.xml* w edytorze tekstu, a następnie dodaj starter Spring Cloud Azure Config do listy `<dependencies>`:

    **Wiosenna chmura 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Wiosenna chmura 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Utwórz nowy plik Java o nazwie *MessageProperties.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Utwórz nowy plik Java o nazwie *HelloController.java* w katalogu pakietów swojej aplikacji. Dodaj następujące wiersze:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Otwórz główny plik Java aplikacji i dodaj element `@EnableConfigurationProperties`, aby włączyć tę funkcję.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Utwórz nowy `bootstrap.properties` plik o nazwie w katalogu zasobów aplikacji i dodaj do pliku następujące wiersze. Zastąp przykładowe wartości odpowiednimi właściwościami dla sklepu konfiguracji aplikacji.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Ustaw zmienną środowiskową o nazwie **APP_CONFIGURATION_CONNECTION_STRING**i ustaw ją na klucz dostępu do sklepu konfiguracji aplikacji. W wierszu polecenia uruchom następujące polecenie i uruchom ponownie wiersz polecenia, aby umożliwić zmianę:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz systemu macOS lub Linux, uruchom następujące polecenie:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Zbuduj aplikację Spring Boot za pomocą Maven i uruchom ją, na przykład:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Po uruchomieniu aplikacji użyj *curl,* aby przetestować aplikację, na przykład:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Zostanie wyświetlony komunikat wprowadzony w sklepie Konfiguracja aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono nowy sklep konfiguracji aplikacji i użyto go z aplikacją Java Spring. Aby uzyskać więcej informacji, zobacz [Wiosna na platformie Azure](https://docs.microsoft.com/java/azure/spring-framework/). Aby dowiedzieć się, jak włączyć aplikację Java Spring dynamicznie odświeżać ustawienia konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-java-spring-app.md)
