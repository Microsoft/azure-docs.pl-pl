---
title: Utwórz & Wdróż projekty grupy zasobów programu Visual Studio
description: Tworzenie projektu grupy zasobów platformy Azure przy użyciu programu Visual Studio i wdrażanie zasobów na platformie Azure.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 4c0e19042506e0cf1772398b0a02b81f0245b1c7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306980"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio

Program Visual Studio umożliwia utworzenie projektu służącego do wdrażania infrastruktury i kodu na platformie Azure. Można na przykład wdrożyć hosta sieci Web, witrynę internetową i kod witryny internetowej. Program Visual Studio zapewnia wiele różnych szablonów początkowych do wdrażania typowych scenariuszy. W tym artykule opisano wdrażanie aplikacji sieci Web.

W tym artykule pokazano, jak używać [programu Visual Studio 2019 lub nowszego z zainstalowanymi obciążeniami deweloperskimi i ASP.NET platformy Azure](/visualstudio/install/install-visual-studio). W przypadku korzystania z programu Visual Studio 2017 środowisko jest w dużym stopniu takie samo.

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

W tej sekcji utworzysz projekt grupy zasobów platformy Azure z szablonem **aplikacji sieci Web** .

1. W programie Visual Studio wybierz pozycję **plik** > **Nowy** > **projekt**.
1. Wybierz szablon projektu **grupy zasobów platformy Azure** i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia okno Utwórz nowy projekt z grupą zasobów platformy Azure, a następnie wyróżnione przycisk Dalej.](./media/create-visual-studio-deployment-project/create-project.png)

1. Nadaj projektowi nazwę. Inne ustawienia domyślne są prawdopodobnie odpowiednie, ale zapoznaj się z nimi, aby działały w danym środowisku. Po zakończeniu wybierz pozycję **Utwórz**.

    ![Tworzenie projektu](./media/create-visual-studio-deployment-project/name-project.png)

1. Wybierz szablon, który ma zostać wdrożony w usłudze Azure Resource Manager. Należy zauważyć, że dostępnych jest wiele różnych opcji, zależnie od typu projektu, który chcesz wdrożyć. W tym artykule wybierz szablon **aplikacji sieci Web** i **przycisk OK**.

    ![Wybieranie szablonu](./media/create-visual-studio-deployment-project/select-project.png)

    Wybrany szablon jest tylko punktem wyjściowym. Możesz dodać lub usunąć zasoby, aby wykonać dany scenariusz.

1. Program Visual Studio tworzy projekt wdrożenia grupy zasobów dla aplikacji sieci Web. Aby wyświetlić pliki dla projektu, przyjrzyj się węzłowi w projekcie wdrożenia.

    ![Pokaż węzły](./media/create-visual-studio-deployment-project/show-items.png)

    Po wybraniu szablonu aplikacji sieci Web zobaczysz następujące pliki:

   | Nazwa pliku | Opis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skrypt programu PowerShell, który uruchamia polecenia w celu wdrożenia do usługi Azure Resource Manager. Program Visual Studio używa tego skryptu programu PowerShell do wdrożenia szablonu. |
   | WebSite.jsna |Szablon usługi Resource Manager określający infrastrukturę, którą chcesz wdrożyć na platformie Azure, oraz parametry, które można podać podczas wdrażania. Umożliwia on również definiowanie zależności między zasobami, tak aby usługa Resource Manager wdrożyła je w odpowiedniej kolejności. |
   | WebSite.parameters.jsna |Plik parametrów zawierający wartości wymagane przez szablon. Poszczególne wdrożenia są dostosowywane za pomocą przekazywanych wartości parametrów. Zwróć uwagę, że **Akcja kompilacji** jest ustawiona na **zawartość**. Jeśli dodasz więcej plików parametrów, upewnij się, że akcja kompilacja jest ustawiona na **zawartość**. |

    Wszystkie projekty wdrażania grup zasobów zawierają te podstawowe pliki. Inne projekty mogą zawierać więcej plików do obsługi innych funkcji.

## <a name="customize-resource-manager-template"></a>Dostosowywanie szablonu Menedżer zasobów

Projekt wdrożenia można dostosować, modyfikując szablon Menedżer zasobów, który opisuje zasoby, które mają zostać wdrożone. Aby uzyskać informacje na temat elementów szablonu usługi Resource Manager, zobacz [Authoring Azure Resource Manager templates](template-syntax.md) (Tworzenie szablonów usługi Azure Resource Manager).

1. Aby móc korzystać z szablonu, Otwórz **WebSite.json**.

1. Edytor programu Visual Studio udostępnia narzędzia pomocne podczas edytowania szablonu usługi Resource Manager. Okno **Konspekt pliku JSON** ułatwia wyświetlanie elementów zdefiniowanych w szablonie.

   ![Pokaż konspekt JSON](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Wybierz element w konspekcie, aby przejść do tej części szablonu.

   ![Nawigacja w formacie JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Nowy zasób można dodać, wybierając przycisk **Dodaj zasób** u góry okna Konspekt pliku JSON lub klikając prawym przyciskiem myszy węzeł **zasoby** i wybierając polecenie **Dodaj nowy zasób**.

   ![Zrzut ekranu przedstawia okno konspektu JSON z wyróżnioną opcją Dodaj nowy zasób.](./media/create-visual-studio-deployment-project/add-resource.png)

1. Wybierz pozycję **konto magazynu** i nadaj jej nazwę. Podaj nazwę nie dłuższą niż 11 znaków, zawierającą tylko cyfry i małe litery.

   ![Dodawanie magazynu](./media/create-visual-studio-deployment-project/add-storage.png)

1. Należy zauważyć, że dodany został nie tylko zasób, ale również parametr typu konta magazynu oraz zmienna nazwy konta magazynu.

   ![Pokaż konspekt](./media/create-visual-studio-deployment-project/show-new-items.png)

1. Parametr dla typu konta magazynu jest wstępnie zdefiniowany z dozwolonymi typami i typem domyślnym. Możesz pozostawić te wartości bez zmian lub edytować je dla danego scenariusza. Jeśli nie chcesz, aby inne osoby wdrażały konta magazynu **Premium_LRS** przy użyciu tego szablonu, usuń go z dozwolonych typów.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. Program Visual Studio oferuje również funkcję IntelliSense ułatwiającą zrozumienie dostępnych właściwości podczas edytowania szablonu. Aby na przykład edytować właściwości planu usługi App Service, przejdź do zasobu **HostingPlan** i dodaj wartość dla obiektu **properties**. Należy zauważyć, że funkcja IntelliSense wyświetla dostępne wartości oraz ich opisy.

   ![Pokaż funkcję IntelliSense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Można ustawić **numberOfWorkers** na 1 i zapisać plik.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otwórz **WebSite.parameters.js** pliku. Plik parametrów służy do przekazywania wartości podczas wdrażania, który dostosowuje wdrażany zasób. Nadaj nazwę planu hostingu i Zapisz plik.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Wdrażanie projektu na platformie Azure

Teraz możesz przystąpić do wdrażania projektu w grupie zasobów.

Domyślnie skrypt programu PowerShell (Deploy-AzureResourceGroup.ps1) w projekcie używa modułu AzureRM. Jeśli nadal masz zainstalowany moduł AzureRM i chcesz kontynuować korzystanie z niego, możesz użyć tego domyślnego skryptu. Za pomocą tego skryptu można wdrożyć rozwiązanie przy użyciu interfejsu programu Visual Studio.

Jeśli jednak przeprowadzono migrację do nowego elementu [AZ module](/powershell/azure/new-azureps-module-az), musisz dodać nowy skrypt do projektu. Aby dodać skrypt, który używa AZ module, skopiuj skrypt [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) i dodaj go do projektu. Aby użyć tego skryptu do wdrożenia, należy uruchomić go z konsoli programu PowerShell, zamiast korzystać z interfejsu wdrażania programu Visual Studio.

W tym artykule przedstawiono oba podejścia. Ten artykuł odnosi się do domyślnego skryptu jako skryptu modułu AzureRM oraz nowego skryptu jako skryptu AZ module.

### <a name="az-module-script"></a>AZ module Script

Dla skryptu AZ module Otwórz konsolę programu PowerShell i uruchom polecenie:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM Użyj programu Visual Studio:

1. W menu skrótów węzła projektu wdrażania wybierz pozycję **Wdróż**  >  **nowe**.

    ![Nowy element menu wdrożenia](./media/create-visual-studio-deployment-project/deploy.png)

1. Zostanie wyświetlone okno dialogowe **Wdrażanie w grupie zasobów**. W polu listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Wybierz pozycję **Wdróż**.

    ![Okno dialogowe wdrażanie w grupie zasobów](./media/create-visual-studio-deployment-project/show-deployment.png)

1. W oknach **Dane wyjściowe** jest wyświetlany stan wdrożenia. Po zakończeniu wdrażania ostatni komunikat wskazuje pomyślne wdrożenie i jest podobny do następującego:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Wyświetl wdrożone zasoby

Sprawdźmy wyniki.

1. W przeglądarce Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się na swoim koncie. Aby wyświetlić grupę zasobów wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę, do której wykonano wdrożenie.

1. Zostaną wyświetlone wszystkie wdrożone zasoby. Należy zauważyć, że nazwa konta magazynu nie jest identyczna z określoną podczas dodawania tego zasobu. Konto magazynu musi być unikatowe. Szablon automatycznie dodaje ciąg znaków do podanej nazwy, aby utworzyć unikatową nazwę.

    ![Pokaż zasoby](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Dodawanie kodu do projektu

Wdrożono już infrastrukturę aplikacji, ale z projektem nie został wdrożony rzeczywisty kod.

1. Dodaj projekt do rozwiązania programu Visual Studio. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Dodaj**  >  **Nowy projekt**.

    ![Dodaj projekt](./media/create-visual-studio-deployment-project/add-project.png)

1. Dodaj **ASP.NET Core aplikację sieci Web**.

    ![Dodaj aplikację sieci Web](./media/create-visual-studio-deployment-project/add-app.png)

1. Nadaj nazwę aplikacji sieci Web, a następnie wybierz pozycję **Utwórz**.

    ![Nazwa aplikacji sieci Web](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Wybierz pozycję **aplikacja sieci Web** i **Utwórz**.

    ![Wybierz aplikację sieci Web](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Po utworzeniu aplikacji internetowej przez program Visual Studio oba projekty są wyświetlane w rozwiązaniu.

    ![Pokaż projekty](./media/create-visual-studio-deployment-project/show-projects.png)

1. Musisz teraz upewnić się, że projekt grupy zasobów wie o nowym projekcie. Wróć do projektu grupy zasobów (ExampleAppDeploy). Kliknij prawym przyciskiem myszy pozycję **Odwołania** i wybierz polecenie **Dodaj odwołanie**.

    ![Zrzut ekranu przedstawia menu ExampleAppDeploy z wyróżnioną opcją Dodaj odwołanie.](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Wybierz projekt aplikacji internetowej, który został utworzony.

   ![Dodawanie odwołania](./media/create-visual-studio-deployment-project/add-reference.png)

   Dodanie odwołania powoduje połączenie projektu aplikacji sieci Web z projektem grupy zasobów i automatyczne ustawienie niektórych właściwości. Te właściwości będą wyświetlane w oknie **Właściwości** odwołania. Właściwość **Include File Path** zawiera ścieżkę, w której zostanie utworzony pakiet. Zanotuj folder (ExampleApp) i plik (package.zip). Musisz znać te wartości, ponieważ są one podawane jako parametry podczas wdrażania aplikacji.

   ![Zobacz odwołanie](./media/create-visual-studio-deployment-project/see-reference.png)

1. Wróć do szablonu (WebSite.json) i Dodaj zasób do szablonu.

    ![Dodaj zasób](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Tym razem wybierz pozycję **Web Deploy dla usługi Web Apps**.

    ![Dodawanie narzędzia Web Deploy](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Zapisz szablon.

1. Szablon zawiera nowe parametry. Dodano je w poprzednim kroku. Nie musisz podawać wartości **_artifactsLocation** ani **_artifactsLocationSasToken** , ponieważ te wartości są generowane automatycznie. Należy jednak ustawić folder i nazwę pliku na ścieżkę zawierającą pakiet wdrożeniowy. Nazwy tych parametrów kończą się z **PackageFolder** i **PackageFileName**. Pierwsza część nazwy jest nazwą dodanego zasobu Web Deploy. W tym artykule są one nazwane **ExampleAppPackageFolder** i **ExampleAppPackageFileName**.

   Otwórz **Website.parameters.jsna** i ustaw te parametry na wartości, które zostały podane we właściwościach odwołania. Ustaw **ExampleAppPackageFolder** na nazwę folderu. Ustaw **ExampleAppPackageFileName** na nazwę pliku zip.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Wdrażanie kodu za pomocą infrastruktury

Ponieważ kod został dodany do projektu, wdrożenie jest nieco inne. Podczas wdrażania można przemieszczać artefakty dla projektu do miejsca, do którego Menedżer zasobów może uzyskać dostęp. Artefakty są przemieszczane do konta magazynu.

### <a name="az-module-script"></a>AZ module Script

Jeśli używasz skryptu AZ module, musisz wprowadzić jedną małą zmianę w szablonie. Ten skrypt dodaje ukośnik do lokalizacji artefaktów, ale szablon nie oczekuje tego ukośnika. Otwórz WebSite.jsna i Znajdź właściwości rozszerzenia MSDeploy. Ma właściwość o nazwie **packageUri**. Usuń ukośnik między lokalizacją artefaktów a folderem pakietu.

Powinno to wyglądać następująco:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Zwróć uwagę, że w poprzednim przykładzie nie ma `'/',` między **parametrami ("_artifactsLocation")** i **parametrami ("ExampleAppPackageFolder")**.

Ponownie skompiluj projekt. Kompilowanie projektu pozwala upewnić się, że pliki, które należy wdrożyć, są dodawane do folderu przemieszczania.

Teraz otwórz konsolę programu PowerShell i uruchom następujące polecenie:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM Użyj programu Visual Studio:

1. Aby przeprowadzić ponowne wdrożenie, wybierz pozycję **Wdróż** i wdrożoną wcześniej grupę zasobów.

    ![Wdróż ponownie projekt](./media/create-visual-studio-deployment-project/redeploy.png)

1. Wybierz konto magazynu wdrożone za pomocą tej grupy zasobów dla **konta magazynu artefaktów**.

   ![Wdróż ponownie Web Deploy](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Wyświetl aplikację sieci Web

1. Po zakończeniu wdrażania wybierz aplikację internetową w witrynie portalu. Wybierz adres URL, aby przejść do lokacji.

   ![Przeglądaj witrynę](./media/create-visual-studio-deployment-project/browse-site.png)

1. Zobaczysz, że domyślna aplikacja ASP.NET została pomyślnie wdrożona.

   ![Pokaż wdrożoną aplikację](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Dodaj pulpit nawigacyjny operacji

Możesz korzystać nie tylko z zasobów dostępnych z poziomu interfejsu programu Visual Studio. Można dostosować wdrożenie, dodając do szablonu zasoby niestandardowe. Aby zobaczyć, jak działa dodawanie zasobu, dodasz operacyjny pulpit nawigacyjny w celu zarządzania wdrożonymi zasobami.

1. Otwórz WebSite.jsw pliku i Dodaj następujący kod JSON po zasobie konta magazynu, ale przed zamknięciem `]` sekcji Resources.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Wdróż ponownie projekt.

1. Po zakończeniu wdrażania Wyświetl pulpit nawigacyjny w portalu. Wybierz pozycję **pulpit nawigacyjny** i wybierz wdrożony.

   ![Zrzut ekranu przedstawia stronę pulpitu nawigacyjnego z wyróżnionym przykładowym pulpitem nawigacyjnym.](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Zobaczysz dostosowany pulpit nawigacyjny.

   ![Niestandardowy pulpit nawigacyjny](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Dostęp do pulpitu nawigacyjnego można zarządzać przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Po wdrożeniu pulpitu nawigacyjnego możesz też dostosowywać jego wygląd. Jeśli jednak ponownie wdrożysz grupę zasobów, pulpit nawigacyjny zostanie zresetowany do domyślnego stanu określonego w szablonie. Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz [Programmatically create Azure Dashboards (Programowe tworzenie pulpitów nawigacyjnych platformy Azure)](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz nazwę grupy zasobów.

1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia i wdrażania szablonów przy użyciu programu Visual Studio. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
