---
title: Bezpieczne wdrażanie między regionami — Menedżer wdrażania platformy Azure
description: Dowiedz się, jak wdrożyć usługę w wielu regionach za pomocą usługi Azure Menedżer wdrażania i zapoznaj się z bezpiecznymi praktykami wdrażania.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8b950fdc36fe3fbea1ce9436bdd7f7372c64c055
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333209"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Włączanie bezpiecznych praktyk wdrażania przy użyciu usługi Azure Menedżer wdrażania (publiczna wersja zapoznawcza)

Aby wdrożyć usługę w wielu regionach i upewnić się, że działa ona zgodnie z oczekiwaniami w każdym regionie, możesz użyć usługi Azure Menedżer wdrażania, aby koordynować etapowe wdrażanie usługi. Podobnie jak w przypadku dowolnego wdrożenia platformy Azure, można zdefiniować zasoby dla usługi w [szablonach Menedżer zasobów](template-syntax.md). Po utworzeniu szablonów należy używać Menedżer wdrażania do opisywania topologii usługi i sposobu jej wdrożenia.

Menedżer wdrażania jest funkcją Menedżer zasobów. Rozszerza możliwości podczas wdrażania. Menedżer wdrażania należy używać w przypadku złożonej usługi, która musi zostać wdrożona w kilku regionach. Dzięki etapowemu wdrażaniu Twojej usługi możesz znaleźć potencjalne problemy zanim zostanie ona wdrożona we wszystkich regionach. Jeśli nie są potrzebne dodatkowe środki ostrożności w przypadku wdrożenia przemieszczanego, użyj standardowych [opcji wdrażania](deploy-portal.md) dla Menedżer zasobów. Menedżer wdrażania bezproblemowo integruje się ze wszystkimi istniejącymi narzędziami innych firm, które obsługują wdrożenia Menedżer zasobów, takie jak oferty ciągłej integracji i ciągłego dostarczania (CI/CD).

Usługa Azure Menedżer wdrażania jest w wersji zapoznawczej. Pomóż nam ulepszyć funkcję, przekazując [Opinie](https://aka.ms/admfeedback).

Aby użyć Menedżer wdrażania, należy utworzyć cztery pliki:

* Szablon topologii
* Wdrażanie szablonu
* Plik parametrów dla topologii
* Plik parametrów do wdrożenia

Szablon topologii jest wdrażany przed wdrożeniem szablonu wdrożenia.

Dodatkowe zasoby:

- [Dokumentacja interfejsu API REST usługi Azure Menedżer wdrażania](/rest/api/deploymentmanager/).
- [Samouczek: korzystanie z usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).
- [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Menedżer wdrażania](./deployment-manager-tutorial-health-check.md).
- [Przykład Menedżer wdrażania platformy Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Tożsamość i dostęp

W przypadku Menedżer wdrażania [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md) wykonuje akcje wdrożenia. Tę tożsamość tworzysz przed rozpoczęciem wdrażania. Musi mieć dostęp do subskrypcji, w której jest wdrażana usługa, oraz wystarczających uprawnień do ukończenia wdrożenia. Informacje o akcjach przyznanych za pomocą ról można znaleźć [w temacie Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Tożsamość musi znajdować się w tej samej lokalizacji co wdrożenie.

## <a name="topology-template"></a>Szablon topologii

Szablon topologii zawiera opis zasobów platformy Azure, które składają się na usługę i gdzie należy je wdrożyć. Na poniższej ilustracji przedstawiono topologię przykładowej usługi:

![Hierarchia z topologii usługi do usług do jednostek usługi](./media/deployment-manager-overview/service-topology.png)

Szablon topologii obejmuje następujące zasoby:

* Źródło artefaktu — w którym są przechowywane Menedżer zasobów szablony i parametry
* Topologia usługi — wskazuje Źródło artefaktu
  * Usługi — określa lokalizację i Identyfikator subskrypcji platformy Azure
    * Jednostki usługi — określa grupę zasobów, tryb wdrażania i ścieżkę do pliku szablonu i parametru

Aby zrozumieć, co się dzieje na każdym poziomie, warto sprawdzić, jakie wartości są podane przez użytkownika.

![Wartości dla każdego poziomu](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Źródło artefaktów dla szablonów

W szablonie topologii utworzysz Źródło artefaktu, w którym znajdują się pliki szablonów i parametrów. Źródłem artefaktów jest metoda ściągania plików do wdrożenia. W dalszej części tego artykułu zobaczysz inne źródło artefaktów dla plików binarnych.

W poniższym przykładzie przedstawiono ogólny format źródła artefaktu.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

Aby uzyskać więcej informacji, zobacz [artifactSources Template Reference](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia usługi

Poniższy przykład przedstawia ogólny format zasobu topologii usług. Podaj identyfikator zasobu źródła artefaktu, który zawiera szablony i pliki parametrów. Topologia usługi zawiera wszystkie zasoby usługi. Aby upewnić się, że źródło artefaktów jest dostępne, topologia usługi jest zależna od niej.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu Servicetopologie](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Usługi

Poniższy przykład przedstawia ogólny format zasobu usług. W każdej usłudze Podaj lokalizację i Identyfikator subskrypcji platformy Azure, które mają być używane do wdrażania usługi. Aby wdrożyć w kilku regionach, należy zdefiniować usługę dla każdego regionu. Usługa zależy od topologii usługi.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu usług](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednostki usług

W poniższym przykładzie przedstawiono ogólny format zasobu jednostki usługi. W każdej jednostce usługi należy określić grupę zasobów, [Tryb wdrożenia](deployment-modes.md) , który ma zostać użyty do wdrożenia, oraz ścieżkę do pliku szablonu i parametru. W przypadku określenia ścieżki względnej dla szablonu i parametrów pełna ścieżka jest tworzona z folderu głównego w źródle artefaktów. Możesz określić ścieżkę bezwzględną dla szablonu i parametrów, ale utracisz możliwość łatwego wydania wersji. Jednostka usługi jest zależna od usługi.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Każdy szablon powinien zawierać powiązane zasoby, które mają zostać wdrożone w jednym kroku. Na przykład jednostka usługi może mieć szablon, który wdraża wszystkie zasoby dla frontonu usługi.

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu Serviceunits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Wdrażanie szablonu

Szablon wdrożenia zawiera opis czynności, które należy wykonać podczas wdrażania usługi. Należy określić topologię usługi, która ma być używana, i zdefiniować kolejność wdrażania jednostek usługi. Zawiera źródło artefaktu do przechowywania plików binarnych wdrożenia. W szablonie wdrożenia należy zdefiniować następującą hierarchię:

* Źródło artefaktu
* Krok
* Mieszczenie
  * Grupy kroków
    * Operacje wdrażania

Na poniższej ilustracji przedstawiono hierarchię szablonu wdrożenia:

![Hierarchia od wdrożenia do kroków](./media/deployment-manager-overview/Rollout.png)

Każde wdrożenie może mieć wiele grup kroków. Każda grupa kroków ma jedną operację wdrażania, która wskazuje jednostkę usługi w topologii usługi.

### <a name="artifact-source-for-binaries"></a>Źródło artefaktów dla plików binarnych

W szablonie wdrożenia utworzysz Źródło artefaktów dla plików binarnych, które są potrzebne do wdrożenia w usłudze. To źródło artefaktu jest podobne do [źródła artefaktu dla szablonów](#artifact-source-for-templates), z tą różnicą, że zawiera skrypty, strony sieci Web, skompilowany kod lub inne pliki, które są niezbędne przez usługę.

### <a name="steps"></a>Kroki

Można zdefiniować krok do wykonania przed operacją wdrożenia lub po niej. Obecnie dostępny jest tylko `wait` krok i krok "healthCheck".

Krok oczekiwania wstrzymuje wdrożenie przed kontynuowaniem. Umożliwia sprawdzenie, czy usługa działa zgodnie z oczekiwaniami przed wdrożeniem następnej jednostki usługi. W poniższym przykładzie przedstawiono ogólny format kroku oczekiwania.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Właściwość Duration używa [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Poprzedni przykład określa czas oczekiwania na minutę.

Aby uzyskać więcej informacji o kroku sprawdzania kondycji, zobacz [wprowadzenie do wdrożenia integracji kondycji na platformie azure Menedżer wdrażania](./deployment-manager-health-check.md) i [Samouczek: Używanie kontroli kondycji w usłudze Azure Menedżer wdrażania](./deployment-manager-tutorial-health-check.md).

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu kroków](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Wprowadzanie

Aby upewnić się, że źródło artefaktu jest dostępne, jego wdrożenie zależy od niego. Wdrażanie definiuje grupy kroków dla każdej wdrożonej jednostki usługi. Można zdefiniować akcje przed wdrożeniem lub po nim. Można na przykład określić, że wdrożenie zaczeka po wdrożeniu jednostki usługi. Można zdefiniować kolejność grup kroków.

Obiekt Identity określa [tożsamość zarządzaną przypisaną przez użytkownika](#identity-and-access) , która wykonuje akcje wdrożenia.

W poniższym przykładzie przedstawiono ogólny format wdrożenia.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

Aby uzyskać więcej informacji, zobacz temat [Wdrażanie szablonu dokumentacja](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Plik parametrów

Tworzysz dwa pliki parametrów. Podczas wdrażania topologii usługi używany jest jeden plik parametrów, a drugi jest używany we wdrożeniu wdrożenia. Istnieją pewne wartości, które należy wykonać, aby upewnić się, że są one takie same w obu plikach parametrów.

## <a name="containerroot-variable"></a>Zmienna containerRoot

W przypadku wdrożeń z wersjami ścieżki do artefaktów zmieniają się wraz z każdą nową wersją. Przy pierwszym uruchomieniu wdrożenia może to być ścieżka `https://<base-uri-blob-container>/binaries/1.0.0.0` . Druga godzina może być `https://<base-uri-blob-container>/binaries/1.0.0.1` . Menedżer wdrażania upraszcza pobieranie prawidłowej ścieżki głównej dla bieżącego wdrożenia przy użyciu `$containerRoot` zmiennej. Ta wartość zmienia się w przypadku każdej wersji i nie jest znana przed wdrożeniem.

Użyj `$containerRoot` zmiennej w pliku parametrów dla szablonu do wdrożenia zasobów platformy Azure. W czasie wdrażania ta zmienna jest zastępowana rzeczywistymi wartościami z wdrożenia.

Na przykład podczas wprowadzania utworzysz Źródło artefaktu dla artefaktów binarnych.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Zwróć uwagę `artifactRoot` na `sasUri` właściwości i. Element główny artefaktu może być ustawiony na wartość taką jak `binaries/1.0.0.0` . Identyfikator URI sygnatury dostępu współdzielonego jest identyfikatorem URI dla kontenera magazynu z tokenem SAS na potrzeby programu Access. Menedżer wdrażania automatycznie konstruuje wartość `$containerRoot` zmiennej. Łączy te wartości w formacie `<container>/<artifactRoot>` .

Szablon i plik parametrów muszą znać poprawną ścieżkę do pobierania plików binarnych z wersjami. Na przykład, aby wdrożyć pliki dla aplikacji sieci Web, należy utworzyć następujący plik parametrów z zmienną $containerRoot. Musisz użyć dwóch ukośników odwrotnych ( `\\` ) dla ścieżki, ponieważ pierwszy jest znakiem ucieczki.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Następnie użyj tego parametru w szablonie:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

Wdrożenia z wersjami można zarządzać przez utworzenie nowych folderów i przekazanie ich w katalogu głównym podczas wdrażania. Ścieżka przechodzi przez szablon, który wdraża zasoby.

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono Menedżer wdrażania. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć program przy użyciu Menedżer wdrażania.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md)
>
> [Szybki Start: Wypróbuj usługę Azure Menedżer wdrażania w ciągu zaledwie kilku minut](https://github.com/Azure-Samples/adm-quickstart)
