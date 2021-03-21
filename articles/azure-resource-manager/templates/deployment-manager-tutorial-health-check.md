---
title: Korzystanie z usługi Azure Deployment Manager Health Check
description: Za pomocą funkcji Kontrola kondycji bezpiecznie Wdrażaj zasoby platformy Azure za pomocą usługi Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 12d246a493ff9ee9e20868da32d633d51939e66c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626630"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Samouczek: korzystanie z kontroli kondycji w usłudze Azure Deployment Manager (publiczna wersja zapoznawcza)

Dowiedz się, jak zintegrować kontrolę kondycji w [usłudze Azure Deployment Manager](./deployment-manager-overview.md). Ten samouczek jest oparty na samouczku [Korzystanie z Deployment Manager platformy Azure z szablonami Menedżer zasobów](./deployment-manager-tutorial.md) . Przed przeprowadzeniem tej czynności należy wykonać ten samouczek.

W szablonie wdrożenia używanym w [usłudze Azure Deployment Manager z szablonami Menedżer zasobów](./deployment-manager-tutorial.md)użyto kroku oczekiwania. W tym samouczku zastąpisz krok oczekiwania z etapem sprawdzania kondycji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja jest oznaczona jako przeznaczona do testowania nowych funkcji platformy Azure, możesz użyć usługi Azure Deployment Manager tylko do wdrożenia w regionach Kanaryjskich.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie symulatora usługi sprawdzania kondycji
> * Popraw szablon wdrożenia
> * Wdrażanie topologii
> * Wdrażanie wdrożenia ze stanem złej kondycji
> * Weryfikowanie wdrożenia wdrażania
> * Wdrażanie wdrożenia ze stanem kondycji
> * Weryfikowanie wdrożenia wdrażania
> * Czyszczenie zasobów

Dodatkowe zasoby:

* [Dokumentacja interfejsu API REST usługi Azure Deployment Manager](/rest/api/deploymentmanager/).
* [Przykład Deployment Manager platformy Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Ukończ [Korzystanie z usługi Azure Deployment Manager z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Instalowanie artefaktów

Jeśli nie pobrano jeszcze przykładów użytych w samouczku wymagań wstępnych, można pobrać [Szablony i artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) i rozpakować je lokalnie. Następnie uruchom skrypt programu PowerShell z sekcji samouczka wymagań wstępnych [przygotowanie artefaktów](./deployment-manager-tutorial.md#prepare-the-artifacts). Skrypt tworzy grupę zasobów, tworzy kontener magazynu, tworzy kontener obiektów blob, przekazuje pobrane pliki, a następnie tworzy token sygnatury dostępu współdzielonego.

* Utwórz kopię adresu URL z tokenem SAS. Ten adres URL jest wymagany do wypełnienia pola w dwóch plikach parametrów: plik parametrów topologii i plik parametrów wdrożenia.
* Otwórz _CreateADMServiceTopology.Parameters.jsna_ i zaktualizuj wartości `projectName` i `artifactSourceSASLocation` .
* Otwórz _CreateADMRollout.Parameters.jsna_ i zaktualizuj wartości `projectName` i `artifactSourceSASLocation` .

## <a name="create-a-health-check-service-simulator"></a>Tworzenie symulatora usługi sprawdzania kondycji

W środowisku produkcyjnym zwykle używany jest jeden lub więcej dostawców monitorowania. Aby zapewnić integrację z kondycją tak jak to możliwe, firma Microsoft współpracuje z niektórymi firmami monitorowania kondycji usług w celu zapewnienia prostego rozwiązania do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Aby zapoznać się z listą tych firm, zobacz [dostawcy monitorowania kondycji](./deployment-manager-health-check.md#health-monitoring-providers). Na potrzeby tego samouczka utworzysz [funkcję platformy Azure](../../azure-functions/index.yml) w celu symulowania usługi monitorowania kondycji. Ta funkcja przyjmuje kod stanu i zwraca ten sam kod. Szablon Deployment Manager platformy Azure używa kodu stanu, aby określić, jak kontynuować wdrażanie.

Poniższe dwa pliki są używane do wdrażania funkcji platformy Azure. Nie musisz pobierać tych plików, aby przejść przez samouczek.

* Szablon Menedżer zasobów znajdujący się pod adresem [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json) . Ten szablon zostanie wdrożony w celu utworzenia funkcji platformy Azure.
* Plik zip kodu źródłowego usługi Azure Functions [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip) . Ten plik zip jest wywoływany przez szablon Menedżer zasobów.

Aby wdrożyć funkcję platformy Azure, wybierz pozycję **Wypróbuj** , aby otworzyć Azure Cloud Shell, a następnie wklej następujący skrypt do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Aby sprawdzić i przetestować funkcję platformy Azure:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa domyślna to nazwa projektu z dołączoną **RG** .
1. Wybierz usługę App Service z grupy zasobów. Domyślną nazwą usługi App Service jest nazwa projektu z dołączoną **webapp** .
1. Rozwiń pozycję **funkcje**, a następnie wybierz pozycję **HttpTrigger1**.

    ![Azure Deployment Manager Health Check — funkcja Azure Function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Wybierz **&lt; /> uzyskać adres URL funkcji**.
1. Wybierz pozycję **Kopiuj** , aby skopiować adres URL do Schowka. Adres URL jest podobny do:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Zastąp `{healthStatus}` adres URL kodem stanu. W tym samouczku użyjesz *złej kondycji* w celu przetestowania scenariusza w złej kondycji i Użyj *zdrowego* lub *ostrzeżenia* , aby przetestować ten scenariusz. Utwórz dwa adresy URL, jeden ze stanem *złej kondycji* , a drugi ze stanem *kondycji* . Na przykład:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Aby ukończyć ten samouczek, musisz dysponować obu adresów URL.

1. Aby przetestować symulator monitorowania kondycji, Otwórz adresy URL utworzone w poprzednim kroku. Wyniki dla stanu złej kondycji będą podobne do:

    ```Output
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Popraw szablon wdrożenia

Ta sekcja zawiera informacje na temat sposobu dołączania kroku sprawdzania kondycji do szablonu wdrożenia.

1. Otwórz _CreateADMRollout.jsw_ programie, który został utworzony za [pomocą usługi Azure Deployment Manager z szablonami Menedżer zasobów](./deployment-manager-tutorial.md). Ten plik JSON jest częścią pobierania.  Zobacz [Wymagania wstępne](#prerequisites).
1. Dodaj dwa dodatkowe parametry:

    ```json
    "healthCheckUrl": {
      "type": "string",
      "metadata": {
        "description": "Specifies the health check URL."
      }
    },
    "healthCheckAuthAPIKey": {
      "type": "string",
      "metadata": {
          "description": "Specifies the health check Azure Function function authorization key."
      }
    }
    ```

1. Zastąp definicję zasobu krok oczekiwania z definicją zasobu kroku sprawdzania kondycji:

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

    Zgodnie z definicją, wdrożenie jest wykonywane, jeśli kondycja jest w *dobrej* kondycji lub *Ostrzeżenie*.

1. Zaktualizuj `dependsOn` definicję wdrożenia, aby uwzględnić nowo zdefiniowany krok sprawdzania kondycji:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizuj, `stepGroups` Aby uwzględnić krok sprawdzania kondycji. `healthCheckStep`Jest wywoływana w `postDeploymentSteps` `stepGroup2` . `stepGroup3` i `stepGroup4` są wdrażane tylko wtedy, gdy kondycja jest w dobrej *kondycji* lub *Ostrzeżenie*.

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

    Jeśli porównasz `stepGroup3` sekcję przed zmianą i po niej, ta sekcja jest teraz zależna od programu `stepGroup2` . Jest to konieczne `stepGroup3` , gdy kolejne grupy kroków są zależne od wyników monitorowania kondycji.

    Poniższy zrzut ekranu ilustruje zmodyfikowane obszary i sposób korzystania z kroku sprawdzania kondycji:

    ![Szablon sprawdzania kondycji usługi Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Wdrażanie topologii

Uruchom Poniższy skrypt programu PowerShell, aby wdrożyć topologię. Te same _CreateADMServiceTopology.js_ i _CreateADMServiceTopology.Parameters.jsna_ tym, które były używane w [usłudze Azure Deployment Manager z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Wdróż wdrożenie ze stanem złej kondycji

Użyj adresu URL stanu złej kondycji utworzonego w temacie [Tworzenie symulatora usługi sprawdzania kondycji](#create-a-health-check-service-simulator). W przypadku [korzystania z usługi Azure Deployment Manager z szablonami Menedżer zasobów](./deployment-manager-tutorial.md)należy skorygować _CreateADMServiceTopology.js_ i tę samą _CreateADMServiceTopology.Parameters.jsę_ .

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` jest wywołaniem asynchronicznym. Komunikat o powodzeniu oznacza, że wdrożenie zostało pomyślnie rozpoczęte. Aby zweryfikować wdrożenie, użyj programu `Get-AZDeploymentManagerRollout` .  Zobacz następną procedurę.

Aby sprawdzić postęp wdrażania, użyj następującego skryptu programu PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Następujące przykładowe dane wyjściowe pokazują, że wdrożenie nie powiodło się z powodu stanu złej kondycji:

```Output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Po zakończeniu wdrażania zobaczysz jedną dodatkową grupę zasobów utworzoną dla regionu zachodnie stany USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Wdrażanie wdrożenia ze stanem kondycji

Powtórz tę sekcję, aby ponownie wdrożyć wdrożenie przy użyciu adresu URL stanu prawidłowości. Po zakończeniu wdrażania zobaczysz jeszcze jedną grupę zasobów utworzoną dla regionu Wschodnie stany USA.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do nowych aplikacji sieci Web w ramach nowych grup zasobów utworzonych przez wdrożenie wdrożenia.
1. Otwórz aplikację internetową w przeglądarce internetowej. Sprawdź lokalizację i wersję pliku _index.html_ .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
1. Użyj pola **Filtruj według nazwy**, aby zawęzić listę grup zasobów utworzonych w tym samouczku.

    * **&lt; projectName>RG**: zawiera zasoby Deployment Manager.
    * **&lt; ProjectName>ServiceWUSrg**: zawiera zasoby zdefiniowane przez ServiceWUS.
    * **&lt; ProjectName>ServiceEUSrg**: zawiera zasoby zdefiniowane przez ServiceEUS.
    * Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
1. Wybierz nazwę grupy zasobów.
1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .
1. Powtórz dwa ostatnie kroki, aby usunąć inne grupy zasobów utworzone w ramach tego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji sprawdzania kondycji w usłudze Azure Deployment Manager. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Resource Manager](../index.yml).
