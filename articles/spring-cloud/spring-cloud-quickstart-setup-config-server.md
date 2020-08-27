---
title: Szybki Start — Konfigurowanie serwera konfiguracji chmury z platformą Azure
description: W tym artykule opisano konfigurowanie serwera konfiguracji chmury ze sprężyną na platformie Azure na potrzeby wdrażania aplikacji.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951912"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Szybki Start: Konfigurowanie serwera konfiguracji chmury z platformą Azure

Serwer konfiguracji chmurowej platformy Azure to scentralizowana usługa konfiguracji dla systemów rozproszonych. Używa warstwy repozytorium podłączanej, która obecnie obsługuje magazyn lokalny, Git i Subversion.  Skonfiguruj serwer konfiguracji, aby wdrażać aplikacje mikrousług w chmurze Azure wiosennej.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

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

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Kompilowanie i wdrażanie aplikacji](spring-cloud-quickstart-deploy-apps.md)
