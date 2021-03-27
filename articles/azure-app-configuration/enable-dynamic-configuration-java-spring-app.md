---
title: Używanie konfiguracji dynamicznej w aplikacji do rozruchu sprężynowego
titleSuffix: Azure App Configuration
description: Dowiedz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji do rozruchu sprężynowego
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625521"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji ze sprężyną Java

Konfiguracja aplikacji ma dwie biblioteki do sprężyny. `spring-cloud-azure-appconfiguration-config` wymaga sprężyny rozruchu i bierze zależność od `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` wymaga sprężyny sieci Web, a także rozruchu sprężynowego. Obie biblioteki obsługują Ręczne wyzwalanie w celu sprawdzenia odświeżonych wartości konfiguracyjnych. `spring-cloud-azure-appconfiguration-config-web` dodaje również obsługę automatycznego sprawdzania odświeżania konfiguracji.

Odświeżanie umożliwia odświeżenie wartości konfiguracyjnych bez konieczności ponownego uruchamiania aplikacji, ale spowoduje to ponowne utworzenie wszystkich ziaren w programie `@RefreshScope` . Biblioteka klienta buforuje identyfikator skrótu aktualnie załadowanych konfiguracji, aby uniknąć zbyt wielu wywołań magazynu konfiguracji. Operacja odświeżania nie aktualizuje wartości do momentu wygaśnięcia wartości w pamięci podręcznej, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund. W razie potrzeby można go zastąpić.

`spring-cloud-azure-appconfiguration-config-web`zautomatyzowane odświeżanie jest wyzwalane w oparciu o aktywność, w odróżnieniu od sieci Web `ServletRequestHandledEvent` . Jeśli `ServletRequestHandledEvent` nie jest wyzwalane, `spring-cloud-azure-appconfiguration-config-web` zautomatyzowane odświeżanie nie spowoduje wyzwolenia odświeżenia, nawet jeśli upłynął limit czasu wygaśnięcia pamięci podręcznej.

## <a name="use-manual-refresh"></a>Użyj ręcznego odświeżania

Konfiguracja aplikacji `AppConfigurationRefresh` , która może służyć do sprawdzenia, czy pamięć podręczna wygasła, a jeśli wygasła, wyzwala odświeżanie.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`zwraca wartość `Future` , która ma wartość true, jeśli zostało wyzwolone odświeżenie, i wartość false, jeśli nie. Wartość false oznacza, że upłynął limit czasu wygaśnięcia pamięci podręcznej, nie wprowadzono zmian lub inny wątek aktualnie sprawdza odświeżanie.

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
