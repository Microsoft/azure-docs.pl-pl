---
title: Samouczek — Konfigurowanie automatycznego skalowania dla aplikacji mikrousług
description: W tym artykule opisano sposób konfigurowania ustawień automatycznego skalowania aplikacji przy użyciu Microsoft Azure Portal lub interfejsu wiersza polecenia platformy Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ca0c498e9fd03ce6397824465f9e1e006eeccc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905396"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>Samouczek: Konfigurowanie automatycznego skalowania dla aplikacji mikrousług

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Skalowanie automatyczne to wbudowana funkcja chmurowej platformy Azure, która ułatwia aplikacjom mikrousługom optymalne wykonywanie w przypadku zmiany zapotrzebowania. Obejmuje to modyfikowanie liczby wystąpień wirtualnych procesorów CPU, pamięci i aplikacji. W tym artykule opisano sposób konfigurowania ustawień automatycznego skalowania aplikacji przy użyciu Microsoft Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te procedury, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Wdrożone wystąpienie usługi w chmurze Azure wiosną. Aby rozpocząć pracę, postępuj zgodnie z [przewodnikiem Szybki Start dotyczącym wdrażania aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) .
* Co najmniej jedna aplikacja została już utworzona w wystąpieniu usługi.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Przejdź do strony automatycznego skalowania w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony **omówienia** chmury Azure wiosennej.
3. Wybierz grupę zasobów, która zawiera tę usługę.
4. Wybierz kartę **aplikacje** w obszarze **Ustawienia** w menu w okienku nawigacji po lewej stronie.
5. Wybierz aplikację, dla której chcesz skonfigurować automatyczne skalowanie. W tym przykładzie wybierz aplikację o nazwie **Demonstracja**. Powinna zostać wyświetlona strona **Przegląd** aplikacji.
6. Przejdź do karty **skalowanie w poziomie** w obszarze **Ustawienia** w menu w okienku nawigacji po lewej stronie.
7. Wybierz wdrożenie, dla którego chcesz skonfigurować automatyczne skalowanie. W poniższej sekcji znajdują się opcje skalowania automatycznego.


![Menu skalowania automatycznego](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Skonfiguruj ustawienia automatycznego skalowania aplikacji w Azure Portal

Dostępne są dwie opcje zarządzania popytem automatycznego skalowania:

* Skalowanie ręczne: utrzymuje liczbę stałych wystąpień. W warstwie Standardowa można skalować w poziomie do maksymalnie 500 wystąpień. Ta wartość zmienia liczbę oddzielnych uruchomionych wystąpień aplikacji mikrousług.
* Niestandardowe automatyczne skalowanie: skaluje się według dowolnych harmonogramów w oparciu o wszystkie metryki.

W Azure Portal wybierz sposób skalowania.  Na poniższej ilustracji przedstawiono **niestandardowe opcje automatycznego skalowania** i ustawienia trybu.

![Niestandardowe Skalowanie automatyczne](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Konfigurowanie ustawień automatycznego skalowania aplikacji w interfejsie wiersza polecenia platformy Azure
Można również skonfigurować tryby skalowania automatycznego przy użyciu interfejsu wiersza polecenia platformy Azure.  Poniższe polecenia tworzą ustawienia automatycznego skalowania i regułę automatycznego skalowania.

* Tworzenie ustawienia automatycznego skalowania
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Utwórz regułę automatycznego skalowania
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Zmiana warstwy na wyższą warstwę Standardowa

Jeśli korzystasz z warstwy Podstawowa i ograniczono jeden lub więcej z tych limitów, możesz przeprowadzić uaktualnienie do warstwy Standardowa. W tym celu przejdź do menu Warstwa **cenowa** , wybierając najpierw kolumnę warstwy *standardowa* i klikając przycisk **Uaktualnij** .

## <a name="next-steps"></a>Następne kroki

* [Omówienie automatycznego skalowania w Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/autoscale-overview)
* [Skalowanie automatyczne monitorowania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)