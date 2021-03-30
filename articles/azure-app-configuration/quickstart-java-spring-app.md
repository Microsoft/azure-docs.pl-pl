---
title: Przewodnik Szybki Start, aby dowiedzieć się, jak korzystać z usługi Azure App Configuration
description: W tym przewodniku szybki start utworzysz aplikację ze sprężyną Java z konfiguracją aplikacji platformy Azure w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932034"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji ze sprężyną Java przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji ze sprężyną Java w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Obsługiwany [zestaw Java Development Kit (JDK)](/java/azure/jdk) w wersji 8.
- System [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3,0 lub nowszej.

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz kolejno pozycje **Eksplorator konfiguracji**  >  **+ Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | /application/config.message | Hello |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

8. Wybierz przycisk **Zastosuj**.

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Użyj [sprężyny Initializr](https://start.spring.io/) , aby utworzyć nowy projekt z rozruchem sprężyny.

1. Przejdź na stronę <https://start.spring.io/>.

1. Określ następujące opcje:

   - Wygeneruj projekt **Maven** z użyciem języka **Java**.
   - Określ wersję **rozruchu sprężynowego** , która jest równa lub większa niż 2,0.
   - Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   - Dodaj zależność **sieci Web sprężyny** .

1. Po określeniu poprzednich opcji wybierz pozycję **Generuj projekt**. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Po wyodrębnieniu plików w systemie lokalnym, prosta aplikacja rozruchowa sprężyny jest gotowa do edycji. Znajdź plik *pom.xml* w katalogu głównym swojej aplikacji.

1. Otwórz plik *pom.xml* w edytorze tekstu, a następnie dodaj starter Spring Cloud Azure Config do listy `<dependencies>`:

    **Chmura Wiosenna 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Chmura Wiosenna 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
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

1. Utwórz nowy plik o nazwie `bootstrap.properties` w katalogu zasobów aplikacji, a następnie Dodaj do pliku następujące wiersze. Zastąp przykładowe wartości odpowiednimi właściwościami magazynu konfiguracji aplikacji.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Ustaw zmienną środowiskową o nazwie **APP_CONFIGURATION_CONNECTION_STRING** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. W wierszu polecenia Uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację do rozruchu ze sprężyną przy użyciu Maven i uruchom ją, na przykład:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Po uruchomieniu aplikacji należy użyć *zazwinięcie* do przetestowania aplikacji, na przykład:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Zobaczysz komunikat wprowadzony w magazynie konfiguracji aplikacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i użył go w aplikacji ze sprężyną Java. Aby uzyskać więcej informacji, zobacz [Sprężyna na platformie Azure](/java/azure/spring-framework/). Aby dowiedzieć się, jak włączyć aplikację ze sprężyną Java do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-java-spring-app.md)