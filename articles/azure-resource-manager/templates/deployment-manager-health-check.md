---
title: Wdrażanie integracji z kondycją — Deployment Manager platformy Azure
description: Opisuje sposób wdrażania usługi w wielu regionach za pomocą usługi Azure Deployment Manager. Przedstawiono w nim bezpieczne praktyki wdrażania, aby zweryfikować stabilność wdrożenia przed wdrożeniem we wszystkich regionach.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536971"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Wprowadzanie wdrożenia integracji z kondycją na platformie Azure Deployment Manager (publiczna wersja zapoznawcza)

[Usługa Azure Deployment Manager](./deployment-manager-overview.md) umożliwia wykonywanie etapowych rozmieszczenia zasobów Azure Resource Manager. Zasoby są wdrażane region według regionów w uporządkowany sposób. Zintegrowane Sprawdzanie kondycji Deployment Manager platformy Azure może monitorować wdrożenia i automatycznie zatrzymywać problemy, dzięki czemu można rozwiązywać problemy i zmniejszać skalę wpływu. Ta funkcja może zmniejszyć dostępność usługi spowodowaną przez regresje w aktualizacjach.

## <a name="health-monitoring-providers"></a>Dostawcy monitorowania kondycji

Aby zapewnić integrację z kondycją tak jak to możliwe, firma Microsoft współpracuje z niektórymi firmami monitorowania kondycji usług w celu zapewnienia prostego rozwiązania do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Jeśli monitor kondycji nie jest jeszcze używany, są to doskonałe rozwiązania, które można zacząć od:

| ![Monitor platformy Azure dla dostawcy monitorowania kondycji programu Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Dostawca monitora kondycji programu Azure Deployment Manager usługi Datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Dostawca monitora kondycji programu Azure Deployment Manager Site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Dostawca monitora kondycji programu Azure Deployment Manager plik Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor w pełni zaobserwowanie platformy firmy Microsoft dla & natywnego monitorowania i analizy hybrydowej w chmurze. |Usługi Datadog, wiodąca platforma do monitorowania i analizowania dla nowoczesnych środowisk chmurowych. Zobacz, [jak usługi Datadog integruje się z usługą Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7 — rozwiązanie do monitorowania usług w chmurze prywatnej i publicznej. Zobacz, [jak Site24x7 integruje się z usługą Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Plik Wavefront, platforma monitorowania i analizy dla środowisk aplikacji wielochmurowych. Zobacz, [jak plik Wavefront integruje się z usługą Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Sposób określania kondycji usługi

[Dostawcy monitorowania kondycji](#health-monitoring-providers) oferują kilka mechanizmów monitorowania usług i powiadamiania o wszelkich problemach z kondycją usługi. [Azure monitor](../../azure-monitor/overview.md) to przykład jednej oferty. Azure Monitor może służyć do tworzenia alertów w przypadku przekroczenia określonych progów. Na przykład użycie pamięci i procesora CPU przekracza oczekiwane poziomy podczas wdrażania nowej aktualizacji do usługi. Po powiadomieniu można wykonać akcje naprawcze.

Ci dostawcy kondycji zazwyczaj oferują interfejsy API REST, aby można było programowo sprawdzić stan monitorów usługi. Interfejsy API REST mogą wrócić do prostej kondycji w dobrej kondycji (określonej przez kod odpowiedzi HTTP) i/lub ze szczegółowymi informacjami na temat sygnałów, które otrzymuje.

Nowy `healthCheck` krok na platformie Azure Deployment Manager umożliwia deklarowanie kodów http, które wskazują na dobrą kondycję. W przypadku złożonych wyników REST można określić wyrażenia regularne, które po dopasowaniu wskazują na dobrą odpowiedź.

Przepływ, aby skonfigurować usługi Azure Deployment Manager Health checks:

1. Utwórz monitory kondycji za pomocą wybranego przez siebie dostawcy usług kondycji.
1. Utwórz co najmniej jeden `healthCheck` etap w ramach wdrożenia Deployment Manager platformy Azure. Wypełnij kroki, `healthCheck` podając następujące informacje:

    1. Identyfikator URI interfejsu API REST dla monitorów kondycji (zgodnie z definicją dostawcy usługi kondycji).
    1. Informacje o uwierzytelnianiu. Obecnie obsługiwane jest tylko uwierzytelnianie w stylu kluczowym interfejsu API. W przypadku Azure Monitor typ uwierzytelniania powinien być ustawiony tak, jak `RolloutIdentity` tożsamość zarządzana przez użytkownika używana na potrzeby wdrożenia Deployment Manager platformy Azure rozciąga się na Azure monitor.
    1. [Kody stanu HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) lub wyrażenia regularne, które definiują dobrą odpowiedź. Możesz dostarczyć wyrażenia regularne, które muszą być zgodne, aby odpowiedź była uznawana za zdrową, lub można podać wyrażenia, które muszą być zgodne, aby odpowiedź była uznawana za prawidłową. Obsługiwane są obie metody.

    Poniższy kod JSON jest przykładem integracji Azure Monitor z usługą Azure Deployment Manager. W przykładzie `RolloutIdentity` są stosowane i ustanawiane jest sprawdzanie kondycji, w którym wdrożenie jest przeprowadzane, jeśli nie ma żadnych alertów. Jedyne obsługiwane Azure Monitor API: [alerty — Pobierz wszystko](/rest/api/monitor/alertsmanagement/alerts/getall).

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

1. Wywołaj `healthCheck` kroki w odpowiednim czasie we wdrożeniu Deployment Manager platformy Azure. W poniższym przykładzie `healthCheck` krok jest wywoływany w programie `postDeploymentSteps` `stepGroup2` .

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

Aby zapoznać się z przykładem, zobacz [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Fazy kontroli kondycji

W tym momencie usługa Azure Deployment Manager wie, jak wykonywać zapytania dotyczące kondycji usługi i etapów wdrożenia. Jednak usługa Azure Deployment Manager umożliwia również dokładne skonfigurowanie czasu tych sprawdzeń. `healthCheck`Krok jest wykonywany w trzech fazach sekwencyjnych, z których wszystkie mają konfigurowalne czasy trwania:

1. Oczekiwanie

    1. Po zakończeniu operacji wdrażania maszyny wirtualne mogą być ponownie uruchamiane, ponownie konfigurowane na podstawie nowych danych, a nawet uruchamiane po raz pierwszy. Trwa także czas, aby usługa mogła rozpocząć emitowanie sygnałów kondycji, które mają być agregowane przez dostawcę monitorowania kondycji, aby coś użyteczne. W trakcie tego procesu tumultuous może nie mieć sensu Sprawdzenie kondycji usługi, ponieważ aktualizacja jeszcze nie osiągnęła stałego stanu. W rzeczywistości usługa może napadać na zbyt dobre i niezdrowe Stany w miarę ich rozliczania.
    1. W fazie oczekiwania kondycja usługi nie jest monitorowana. Służy to do zezwalania na tworzenie wdrożonych zasobów przed rozpoczęciem procesu kontroli kondycji.

1. Kształcenia

    1. Ponieważ nie jest możliwe, aby nie wiedzieć, jak długo potrwa, zanim zasoby staną się stabilne, faza elastyczna pozwala na elastyczny czas między zasobami, gdy zasoby są potencjalnie niestabilne i gdy są wymagane do utrzymania zdrowego stanu.
    1. Po rozpoczęciu fazy elastycznej usługa Azure Deployment Manager rozpocznie okresowe sondowanie podanego punktu końcowego REST w celu zapewnienia kondycji usługi. Interwał sondowania można skonfigurować.
    1. Jeśli monitor kondycji powróci do sygnałów wskazujących, że usługa jest w złej kondycji, te sygnały są ignorowane, faza elastyczna jest kontynuowana i sondowanie jest kontynuowane.
    1. Gdy monitor kondycji zwróci sygnały wskazujące, że usługa jest w dobrej kondycji, zostanie zakończona faza elastyczna i rozpocznie się faza HealthyState.
    1. W ten sposób czas określony dla fazy elastycznej jest maksymalną ilością czasu, którą można wypróbować w przypadku kondycji usługi, zanim zostanie uznana za obowiązkową.

1. HealthyState

    1. W fazie HealthyState kondycja usługi jest ciągle sondowana w tym samym interwale co w przypadku fazy elastycznej.
    1. Oczekuje się, że usługa utrzymuje sygnały w dobrej kondycji od dostawcy monitorowania kondycji przez cały określony czas.
    1. Jeśli w dowolnym momencie zostanie wykryta odpowiedź w złej kondycji, usługa Azure Deployment Manager przestanie całe wdrożenie i zwróci odpowiedź REST przenoszącą sygnały usługi w złej kondycji.
    1. Po zakończeniu HealthyState czas trwania `healthCheck` zostanie zakończony, a wdrożenie przejdzie do następnego kroku.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób integrowania monitorowania kondycji w usłudze Azure Deployment Manager. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć program przy użyciu Deployment Manager.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)
