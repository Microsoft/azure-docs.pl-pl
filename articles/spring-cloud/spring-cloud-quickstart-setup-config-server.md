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
ms.openlocfilehash: 2f6051277f1ddb89e67ce8013c78571a2a7314b7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089132"
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

3. Wybierz pozycję **Zastosuj**, aby zapisać zmiany.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Poniższa procedura używa interfejsu wiersza polecenia platformy Azure w celu skonfigurowania serwera konfiguracji do wdrożenia [przykładu Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Skonfiguruj konfigurację serwera z lokalizacją repozytorium git dla projektu:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, jeśli pozostaną w ramach subskrypcji. Jeśli nie planujesz kontynuować pracy z następnym przewodnikiem Szybki Start, zobacz [Oczyszczanie zasobów](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). W przeciwnym razie przejdź do następnego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Kompilowanie i wdrażanie aplikacji](spring-cloud-quickstart-deploy-apps.md)