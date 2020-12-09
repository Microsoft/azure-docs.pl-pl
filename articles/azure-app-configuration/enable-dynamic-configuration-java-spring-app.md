---
title: Używanie konfiguracji dynamicznej w aplikacji do rozruchu sprężynowego
titleSuffix: Azure App Configuration
description: Dowiedz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji do rozruchu sprężynowego
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929161"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji ze sprężyną Java

Biblioteka klienta sieci komputerowej ze sprężyną konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Biblioteka klienta buforuje każde ustawienie, aby uniknąć zbyt wielu wywołań magazynu konfiguracji. Operacja odświeżania nie aktualizuje wartości do momentu wygaśnięcia wartości w pamięci podręcznej, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund. W razie potrzeby można go zastąpić.

Możesz sprawdzić dostępność zaktualizowanych ustawień na żądanie, wywołując `AppConfigurationRefresh` `refreshConfigurations()` metodę.

Alternatywnie można użyć `spring-cloud-azure-appconfiguration-config-web` pakietu, który ma zależność od `spring-web` do obsługi zautomatyzowanego odświeżania.

## <a name="use-automated-refresh"></a>Użyj automatycznego odświeżania

Aby użyć automatycznego odświeżania, Zacznij od aplikacji z rozruchem sprężyny korzystającej z konfiguracji aplikacji, takiej jak utworzona przez Ciebie aplikacja, postępując zgodnie z [przewodnikiem Szybki Start dla konfiguracji aplikacji](quickstart-java-spring-app.md).

Następnie otwórz plik *pom.xml* w edytorze tekstów i Dodaj `<dependency>` do `spring-cloud-azure-appconfiguration-config-web` .

**Chmura Wiosenna 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Chmura Wiosenna 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Uruchamianie i testowanie aplikacji lokalnie

1. Skompiluj aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Otwórz okno przeglądarki i przejdź do adresu URL: `http://localhost:8080` .  Zobaczysz komunikat skojarzony z kluczem. 

    Można również użyć *zwinięcie* do testowania aplikacji, na przykład: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Aby przetestować konfigurację dynamiczną, Otwórz Portal konfiguracji aplikacji platformy Azure skojarzony z Twoją aplikacją. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartość wyświetlanego klucza, na przykład:
    | Klucz | Wartość |
    |---|---|
    | Aplikacja/Konfiguracja. komunikat | Witaj — Zaktualizowano |

1. Odśwież stronę przeglądarki, aby wyświetlić wyświetlony nowy komunikat.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację rozruchu sprężynowego do dynamicznego odświeżania ustawień konfiguracji z poziomu konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
