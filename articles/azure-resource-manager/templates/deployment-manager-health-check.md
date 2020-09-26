---
title: Wdrażanie integracji z kondycją — Menedżer wdrażania platformy Azure
description: Opisuje sposób wdrażania usługi w wielu regionach za pomocą usługi Azure Menedżer wdrażania. Przedstawiono w nim bezpieczne praktyki wdrażania, aby zweryfikować stabilność wdrożenia przed wdrożeniem we wszystkich regionach.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: 63879350eba897cfe5a793309e5129323fe8bbde
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372378"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Wprowadzanie wdrożenia integracji z kondycją na platformie Azure Menedżer wdrażania (publiczna wersja zapoznawcza)

[Usługa Azure Menedżer wdrażania](./deployment-manager-overview.md) umożliwia wykonywanie etapowych rozmieszczenia zasobów Azure Resource Manager. Zasoby są wdrażane region według regionów w uporządkowany sposób. Zintegrowane Sprawdzanie kondycji Menedżer wdrażania platformy Azure może monitorować wdrożenia i automatycznie zatrzymywać problemy, dzięki czemu można rozwiązywać problemy i zmniejszać skalę wpływu. Ta funkcja może zmniejszyć dostępność usługi spowodowaną przez regresje w aktualizacjach.

## <a name="health-monitoring-providers"></a>Dostawcy monitorowania kondycji

Aby zapewnić integrację z kondycją tak jak to możliwe, firma Microsoft współpracuje z niektórymi firmami monitorowania kondycji usług w celu zapewnienia prostego rozwiązania do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Jeśli monitor kondycji nie jest jeszcze używany, są to doskonałe rozwiązania, które można zacząć od:

| ![Monitor platformy Azure dla dostawcy monitorowania kondycji programu Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Dostawca monitora kondycji programu Azure Deployment Manager usługi Datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Dostawca monitora kondycji programu Azure Deployment Manager Site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Dostawca monitora kondycji programu Azure Deployment Manager plik Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor w pełni zaobserwowanie platformy firmy Microsoft dla & natywnego monitorowania i analizy hybrydowej w chmurze. |Usługi Datadog, wiodąca platforma do monitorowania i analizowania dla nowoczesnych środowisk chmurowych. Zobacz, [jak usługi Datadog integruje się z usługą Azure Menedżer wdrażania](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7 — rozwiązanie do monitorowania usług w chmurze prywatnej i publicznej. Zobacz, [jak Site24x7 integruje się z usługą Azure Menedżer wdrażania](https://www.site24x7.com/azure/adm.html).| Plik Wavefront, platforma monitorowania i analizy dla środowisk aplikacji wielochmurowych. Zobacz, [jak plik Wavefront integruje się z usługą Azure Menedżer wdrażania](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Sposób określania kondycji usługi

[Dostawcy monitorowania kondycji](#health-monitoring-providers) oferują kilka mechanizmów monitorowania usług i powiadamiania o wszelkich problemach z kondycją usługi. [Azure monitor](../../azure-monitor/overview.md) to przykład jednej oferty. Azure Monitor może służyć do tworzenia alertów w przypadku przekroczenia określonych progów. Na przykład użycie pamięci i procesora CPU przekracza oczekiwane poziomy podczas wdrażania nowej aktualizacji do usługi. Po powiadomieniu można wykonać akcje naprawcze.

Ci dostawcy kondycji zazwyczaj oferują interfejsy API REST, aby można było programowo sprawdzić stan monitorów usługi. Interfejsy API REST mogą wrócić do prostej kondycji w dobrej kondycji (określonej przez kod odpowiedzi HTTP) i/lub ze szczegółowymi informacjami na temat sygnałów, które otrzymuje.

Nowy krok *healthCheck* na platformie Azure Menedżer wdrażania umożliwia deklarowanie kodów http wskazujących usługę zdrowej lub, w przypadku bardziej złożonych wyników REST, można nawet określić wyrażenia regularne, które, jeśli są zgodne, wskazują na dobrą odpowiedź.

Przepływ pobierania Instalatora przy użyciu usługi Azure Menedżer wdrażania Health checks:

1. Utwórz monitory kondycji za pomocą wybranego przez siebie dostawcy usług kondycji.
1. Utwórz co najmniej jedno healthCheck kroki w ramach wdrożenia Menedżer wdrażania platformy Azure. Wypełnij kroki healthCheck, podając następujące informacje:

    1. Identyfikator URI interfejsu API REST dla monitorów kondycji (zgodnie z definicją dostawcy usługi kondycji).
    1. Informacje o uwierzytelnianiu. Obecnie obsługiwane jest tylko uwierzytelnianie w stylu kluczowym interfejsu API. W przypadku Azure Monitor typ uwierzytelniania powinien być ustawiony jako "RolloutIdentity", ponieważ tożsamość zarządzana przypisana przez użytkownika używana na potrzeby wdrożenia Menedżer wdrażania platformy Azure rozszerza się dla Azure Monitor.
    1. [Kody stanu HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) lub wyrażenia regularne, które definiują dobrą odpowiedź. Należy zauważyć, że można podać wyrażenia regularne, które wszystkie muszą być zgodne, aby można je było traktować w dobrej kondycji, lub podać wyrażenia, które muszą być zgodne, aby odpowiedź została uznana za prawidłową. Obsługiwane są obie metody.

    Poniższy kod JSON umożliwia integrację Azure Monitor z usługą Azure Menedżer wdrażania, która korzysta z RolloutIdentity i ustanawia kontrolę kondycji, w której wdrożenie jest przeprowadzane, gdy nie ma żadnych alertów. Jedyne obsługiwane Azure Monitor API: [alerty — Pobierz wszystko](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    Poniższy kod JSON jest przykładem dla wszystkich innych dostawców monitorowania kondycji:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Wywołaj kroki healthCheck w odpowiednim czasie we wdrożeniu Menedżer wdrażania platformy Azure. W poniższym przykładzie krok sprawdzania kondycji jest wywoływany w **postDeploymentSteps** of **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Aby zapoznać się z przykładem, zobacz [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Menedżer wdrażania](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fazy kontroli kondycji

W tym momencie usługa Azure Menedżer wdrażania wie, jak wykonywać zapytania dotyczące kondycji usługi i etapów wdrożenia, aby to zrobić. Jednak usługa Azure Menedżer wdrażania umożliwia również dokładne skonfigurowanie czasu tych sprawdzeń. Krok healthCheck jest wykonywany w 3 fazach sekwencyjnych, z których wszystkie mają konfigurowalne czasy trwania:

1. Oczekiwanie

    1. Po zakończeniu operacji wdrażania maszyny wirtualne mogą być ponownie uruchamiane, ponownie konfigurowane na podstawie nowych danych, a nawet uruchamiane po raz pierwszy. Trwa także czas, aby usługa mogła rozpocząć emitowanie sygnałów kondycji, które mają być agregowane przez dostawcę monitorowania kondycji, aby coś użyteczne. Podczas tego procesu tumultuous może nie mieć sensu Sprawdzenie kondycji usługi, ponieważ aktualizacja nie osiągnęła jeszcze stałego stanu. W rzeczywistości usługa może napadać na zbyt dobre i niezdrowe Stany w miarę ich rozliczania.
    1. W fazie oczekiwania usługa Service Health nie jest monitorowana. Służy to do zezwalania na tworzenie wdrożonych zasobów przed rozpoczęciem procesu kontroli kondycji.
1. Kształcenia

    1. Ponieważ nie można było wiedzieć, jak długo zasoby zajmieją się tworzenie zanim staną się stabilne, faza elastyczna umożliwia elastyczne przedziały czasu między zasobami, gdy zasoby są potencjalnie niestabilne i muszą być utrzymywane w dobrej kondycji.
    1. Po rozpoczęciu fazy elastycznej usługa Azure Menedżer wdrażania rozpocznie okresowe sondowanie podanego punktu końcowego REST w celu zapewnienia kondycji usługi. Interwał sondowania można skonfigurować.
    1. Jeśli monitor kondycji powróci do sygnałów wskazujących, że usługa jest w złej kondycji, te sygnały są ignorowane, faza elastyczna jest kontynuowana i sondowanie jest kontynuowane.
    1. Gdy tylko monitor kondycji powróci do sygnałów wskazujących, że usługa jest w dobrej kondycji, zostanie zakończona faza elastyczna i rozpocznie się faza HealthyState.
    1. W ten sposób czas określony dla fazy elastycznej jest maksymalną ilością czasu, którą można wypróbować w przypadku kondycji usługi, zanim zostanie uznana za obowiązkową.
1. HealthyState

    1. W fazie HealthyState kondycja usługi jest ciągle sondowana w tym samym interwale co w przypadku fazy elastycznej.
    1. Oczekuje się, że usługa utrzymuje sygnały w dobrej kondycji od dostawcy monitorowania kondycji przez cały określony czas.
    1. Jeśli w dowolnym momencie zostanie wykryta odpowiedź w złej kondycji, usługa Azure Menedżer wdrażania przestanie całe wdrożenie i zwróci odpowiedź REST przenoszącą sygnały usługi w złej kondycji.
    1. Po zakończeniu HealthyState czas healthCheck zostanie ukończony, a wdrożenie przejdzie do następnego kroku.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób integrowania monitorowania kondycji w usłudze Azure Menedżer wdrażania. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć program przy użyciu Menedżer wdrażania.

> [!div class="nextstepaction"]
> [Samouczek: Integracja kontroli kondycji w usłudze Azure Menedżer wdrażania](./deployment-manager-tutorial-health-check.md)