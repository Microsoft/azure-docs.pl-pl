---
title: Szybki Start — Konfigurowanie serwera konfiguracji chmury z platformą Azure
description: W tym artykule opisano konfigurowanie serwera konfiguracji chmury ze sprężyną na platformie Azure na potrzeby wdrażania aplikacji.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: b23b5d516479cb28948a601c6bd71309500e73f1
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825783"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Szybki Start: Konfigurowanie serwera konfiguracji chmury z platformą Azure

Serwer konfiguracji chmurowej platformy Azure to scentralizowana usługa konfiguracji dla systemów rozproszonych. Używa warstwy repozytorium podłączanej, która obecnie obsługuje magazyn lokalny, Git i Subversion. W tym przewodniku szybki start skonfigurujesz serwer konfiguracji w taki sposób, aby pobierał dane z repozytorium git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj poprzedni Przewodnik Szybki Start w tej serii: [zastrzeganie usługi w chmurze Azure wiosennej](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedury serwera konfiguracji chmury ze sprężyną Azure

Skonfiguruj konfigurację serwera z lokalizacją repozytorium git dla projektu, uruchamiając następujące polecenie. Zamień `<service instance name>` na nazwę usługi, która została utworzona wcześniej. Wartość domyślna nazwy wystąpienia usługi ustawiona w poprzednim przewodniku Szybki Start nie działa z tym poleceniem.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

To polecenie informuje serwer konfiguracji, aby znaleźć dane konfiguracji w folderze [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) przykładowego repozytorium aplikacji. Ponieważ nazwa aplikacji, która będzie pobierać dane konfiguracji, to plik, `planet-weather-provider` który będzie używany, to [Planet-Weather-Provider. yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Serwer konfiguracji chmurowej platformy Azure to scentralizowana usługa konfiguracji dla systemów rozproszonych. Używa warstwy repozytorium podłączanej, która obecnie obsługuje magazyn lokalny, Git i Subversion.  Skonfiguruj serwer konfiguracji, aby wdrażać aplikacje mikrousług w chmurze Azure wiosennej.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedury serwera konfiguracji chmury ze sprężyną Azure

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Poniższa procedura umożliwia skonfigurowanie serwera konfiguracji przy użyciu Azure Portal do wdrożenia [przykładu Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Przejdź do strony **Przegląd** usługi i wybierz pozycję **serwer konfiguracji**.

2. W sekcji **repozytorium domyślne** Ustaw **Identyfikator URI** na " https://github.com/Azure-Samples/piggymetrics-config ".

3. Kliknij przycisk **Weryfikuj**.

    ![Przejdź do serwera konfiguracji](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Po zakończeniu walidacji kliknij pozycję **Zastosuj** , aby zapisać zmiany.

    ![Sprawdzanie poprawności serwera konfiguracji](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. Aktualizacja konfiguracji może potrwać kilka minut.
 
    ![Aktualizowanie serwera konfiguracji](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. Po zakończeniu konfiguracji należy otrzymać powiadomienie.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Poniższa procedura używa interfejsu wiersza polecenia platformy Azure w celu skonfigurowania serwera konfiguracji do wdrożenia [przykładu Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Skonfiguruj konfigurację serwera z lokalizacją repozytorium git dla projektu:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Jeśli używasz prywatnego repozytorium dla serwera konfiguracji, zapoznaj się z naszym [samouczkiem dotyczącym konfigurowania uwierzytelniania](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server).

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Rozwiązywanie problemów z serwerem konfiguracji chmury ze sprężyną Azure

Poniższa procedura wyjaśnia, jak rozwiązywać problemy z ustawieniami serwera konfiguracji.

1. W Azure Portal przejdź do strony **Przegląd** usługi i wybierz pozycję **dzienniki**. 
1. Wybierz pozycję **zapytania** i **Pokaż dzienniki aplikacji, które zawierają warunki "Error" lub "Exception**". 
1. Kliknij przycisk **Uruchom**. 
1. Jeśli w dziennikach znajdziesz błąd **Java. lang. illegalStateException** , oznacza to, że usługa w chmurze sprężynowej nie może zlokalizować właściwości z serwera konfiguracji.

    [![Zapytanie ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) przebiegu portalu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Przejdź do strony **Przegląd** usługi.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**. 
1. Wybierz opcję detektor **serwera konfiguracji** .

    [![Diagnozuj problemy z ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) portalem ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Kliknij pozycję **Konfiguracja kondycji serwera konfiguracji**.

    [![Genie ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) portalu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Kliknij pozycję **Konfiguracja stan serwera** , aby wyświetlić więcej szczegółów dotyczących wykrywania.

    [![Stan ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) kondycji portalu w ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, jeśli pozostaną w ramach subskrypcji. Jeśli nie planujesz kontynuować pracy z następnym przewodnikiem Szybki Start, zobacz [Oczyszczanie zasobów](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). W przeciwnym razie przejdź do następnego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Kompilowanie i wdrażanie aplikacji](spring-cloud-quickstart-deploy-apps.md)
