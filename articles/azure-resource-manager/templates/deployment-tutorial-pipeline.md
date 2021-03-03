---
title: Ciągła integracja z użyciem usługi Azure Pipelines
description: Dowiedz się, jak ciągle kompilować, testować i wdrażać szablony Azure Resource Manager (szablony ARM).
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3ff98c1c033c6da4b6bdf40c3b8ecb3347601741
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722818"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>Samouczek: Ciągła integracja szablonów ARM z Azure Pipelines

W [poprzednim samouczku](./deployment-tutorial-linked-template.md)zostanie wdrożony połączony szablon.  W ramach tego samouczka nauczysz się używać Azure Pipelines do ciągłego kompilowania i wdrażania projektów Azure Resource Manager szablon (szablon ARM).

Usługa Azure DevOps zapewnia usługi deweloperskie do obsługi zespołów do planowania pracy, współpracy nad programowaniem kodu oraz kompilowania i wdrażania aplikacji. Deweloperzy mogą korzystać z chmury, korzystając z Azure DevOps Services. Usługa Azure DevOps udostępnia zintegrowany zestaw funkcji, do których można uzyskać dostęp za pomocą przeglądarki sieci Web lub klienta IDE. Potok platformy Azure jest jedną z tych funkcji. Azure Pipelines to w pełni oferowana usługa ciągłej integracji (CI) i ciągłe dostarczanie (CD). Współpracuje z preferowanym dostawcą usługi git i można wdrożyć w większości najważniejszych usług w chmurze. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

> [!NOTE]
> Wybierz nazwę projektu. Podczas przechodzenia przez samouczek Zastąp dowolną **AzureRmPipeline** nazwą projektu.
> Ta nazwa projektu służy do generowania nazw zasobów.  Jednym z zasobów jest konto magazynu. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i małych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu to nazwa projektu z dołączonym **magazynem** , a nazwa projektu musi zawierać od 3 do 11 znaków. Nazwa projektu musi być zgodna z wymaganiami dotyczącymi nazwy konta magazynu i ma mniej niż 11 znaków.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie repozytorium GitHub
> * Tworzenie projektu usługi Azure DevOps
> * Tworzenie potoku platformy Azure
> * Weryfikowanie wdrożenia potoku
> * Zaktualizuj szablon i Wdróż ponownie
> * Czyszczenie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* **Konto usługi GitHub**, za pomocą którego można utworzyć repozytorium dla szablonów. Jeśli nie masz takiego konta, możesz [je utworzyć bezpłatnie](https://github.com). Aby uzyskać więcej informacji o korzystaniu z repozytoriów usługi GitHub, zobacz [Tworzenie repozytoriów GitHub](/azure/devops/pipelines/repos/github).
* **Zainstaluj narzędzie git**. W tej instrukcji samouczka jest stosowana funkcja *git bash* lub *powłoka git*. Aby uzyskać instrukcje, zobacz [Instalowanie usługi git](https://www.atlassian.com/git/tutorials/install-git).
* **Organizacja usługi Azure DevOps**. Jeśli nie masz takiego konta, możesz je utworzyć bezpłatnie. Zobacz [Tworzenie organizacji lub kolekcji projektów](/azure/devops/organizations/accounts/create-organization).
* obowiązkowe **Visual Studio Code z rozszerzeniem Menedżer zasobów Tools**. Zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub

GitHub służy do przechowywania kodu źródłowego projektu, w tym Menedżer zasobów szablonów. W przypadku innych obsługiwanych repozytoriów zobacz [repozytoria obsługiwane przez usługę Azure DevOps](/azure/devops/pipelines/repos/).

### <a name="create-a-github-repository"></a>Tworzenie repozytorium GitHub

Jeśli nie masz konta usługi GitHub, zapoznaj się z tematem [wymagania wstępne](#prerequisites).

1. Zaloguj się do usługi [GitHub](https://github.com).
1. Wybierz swój obraz konta w prawym górnym rogu, a następnie wybierz **swoje repozytoria**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines Tworzenie repozytorium GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wybierz pozycję **Nowy**, zielony przycisk.
1. W polu **Nazwa repozytorium** wprowadź nazwę repozytorium.  Na przykład **AzureRmPipeline-repozytorium**. Pamiętaj, aby zastąpić dowolną **AzureRmPipeline** nazwą projektu. Możesz wybrać opcję **publiczny** lub **prywatny** do przechodzenia przez ten samouczek. A następnie wybierz pozycję **Utwórz repozytorium**.
1. Zapisz adres URL. Adres URL repozytorium ma następujący format: `https://github.com/[YourAccountName]/[YourRepositoryName]` .

To repozytorium jest określane jako *zdalne repozytorium*. Każdy deweloper tego samego projektu może sklonować własne *repozytorium lokalne* i scalić zmiany w repozytorium zdalnym.

### <a name="clone-the-remote-repository"></a>Klonowanie repozytorium zdalnego

1. Otwórz powłokę Git lub narzędzie git bash. Zobacz [Wymagania wstępne](#prerequisites).
1. Sprawdź, czy bieżący folder jest w serwisie **GitHub**.
1. Uruchom następujące polecenie:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Zastąp ciąg `[YourAccountName]` nazwą konta usługi GitHub i Zastąp `[YourGitHubRepositoryName]` nazwą swojego repozytorium utworzonego w poprzedniej procedurze.

Folder _CreateWebApp_ jest folderem, w którym przechowywany jest szablon. `pwd`Polecenie pokazuje ścieżkę folderu. Ścieżka służy do zapisywania szablonu w poniższej procedurze.

### <a name="download-a-quickstart-template"></a>Pobierz szablon szybkiego startu

Zamiast tworzyć szablony, można pobrać szablony i zapisać je w folderze _CreateWebApp_ .

* Główny szablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Połączony szablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Nazwa folderu i nazwy plików są używane, ponieważ znajdują się w potoku. W przypadku zmiany tych nazw należy zaktualizować nazwy używane w potoku.

### <a name="push-the-template-to-the-remote-repository"></a>Wypychanie szablonu do repozytorium zdalnego

_azuredeploy.js_ została dodana do repozytorium lokalnego. Następnie Przekaż szablon do zdalnego repozytorium.

1. Otwórz *powłokę git* lub *git bash*, jeśli nie jest ona otwarta.
1. Zmień katalog na folder _CreateWebApp_ w lokalnym repozytorium.
1. Sprawdź, czy _azuredeploy.jsw_ pliku znajduje się w folderze.
1. Uruchom następujące polecenie:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin main
    ```

    Może pojawić się ostrzeżenie dotyczące LF. Możesz zignorować to ostrzeżenie. **główny** jest gałęzią główną.  Tworzona jest zwykle gałąź dla każdej aktualizacji. Aby uprościć samouczek, należy bezpośrednio użyć gałęzi głównej.

1. Przejdź do repozytorium GitHub z przeglądarki. Adres URL to `https://github.com/[YourAccountName]/[YourGitHubRepository]` . Zobaczysz folder _CreateWebApp_ i dwa pliki znajdujące się w folderze.
1. Wybierz pozycję _azuredeploy.js_ , aby otworzyć szablon.
1. Wybierz przycisk **RAW** . Adres URL zaczyna się od `https://raw.githubusercontent.com` .
1. Utwórz kopię adresu URL. Należy podać tę wartość podczas konfigurowania potoku w dalszej części tego samouczka.

Do tej pory utworzono repozytorium GitHub i przekazano szablony do repozytorium.

## <a name="create-a-devops-project"></a>Tworzenie projektu DevOps

Aby można było wykonać następną procedurę, wymagana jest organizacja DevOps. Jeśli go nie masz, zobacz [wymagania wstępne](#prerequisites).

1. Zaloguj się do [usługi Azure DevOps](https://dev.azure.com).
1. Wybierz organizację DevOps z lewej strony.

    ![Azure Resource Manager Azure DevOps Azure Pipelines tworzenia projektu DevOps platformy Azure](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wybierz pozycję **Nowy projekt**. Jeśli nie masz żadnych projektów, Strona Tworzenie projektu zostanie otwarta automatycznie.
1. Podaj następujące wartości:

    * **Nazwa projektu**: Wprowadź nazwę projektu. Możesz użyć nazwy projektu, która została pobrana na początku samouczka.
    * **Kontrola wersji**: wybierz pozycję **git**. Może być konieczne rozszerzenie **Zaawansowane** , aby zobaczyć **kontrolę wersji**.

    Użyj wartości domyślnej dla innych właściwości.
1. Wybierz przycisk **Utwórz**.

Utwórz połączenie usługi używane do wdrażania projektów na platformie Azure.

1. Wybierz pozycję **Ustawienia projektu** w dolnej części menu po lewej stronie.
1. Wybierz pozycję **połączenia usługi** w obszarze **potoki**.
1. Wybierz pozycję **Utwórz połączenie usługi**, wybierz pozycję **Azure Resource Manager**, a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję Nazwa **główna usługi**, a następnie wybierz pozycję **dalej**.
1. Podaj następujące wartości:

    * **Poziom zakresu**: Wybierz **subskrypcję**.
    * **Subskrypcja**: wybierz swoją subskrypcję.
    * **Grupa zasobów**: pozostaw to pole puste.
    * **Nazwa połączenia**: Wprowadź nazwę połączenia. Na przykład **AzureRmPipeline-poł**. Zapisz tę nazwę, podczas tworzenia potoku musisz mieć nazwę.
    * **Udziel uprawnienia dostępu do wszystkich potoków**. niezaznaczone
1. Wybierz pozycję **Zapisz**.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Do tej pory zostały wykonane następujące zadania.  Jeśli pominiesz poprzednie sekcje, ponieważ znasz już usługi GitHub i DevOps, musisz wykonać zadania przed kontynuowaniem.

* Utwórz repozytorium GitHub i Zapisz szablony w folderze _CreateWebApp_ w repozytorium.
* Utwórz projekt DevOps i Utwórz połączenie z usługą Azure Resource Manager.

Aby utworzyć potok z krokiem do wdrożenia szablonu:

1. Wybierz pozycję **potoki** w menu po lewej stronie.
1. Wybierz pozycję **Utwórz potok**.
1. Na karcie **Connect** (Połączenie) wybierz pozycję **GitHub**. Jeśli zostanie wyświetlony monit, wprowadź swoje poświadczenia usługi GitHub, a następnie postępuj zgodnie z instrukcjami. Jeśli widzisz Poniższy ekran, wybierz opcję **tylko wybierz repozytoria** i sprawdź, czy repozytorium znajduje się na liście przed wybraniem opcji **Zatwierdź & Zainstaluj**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines wybierz tylko repozytoria](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na karcie **Wybierz** wybierz repozytorium. Nazwa domyślna to `[YourAccountName]/[YourGitHubRepositoryName]` .
1. Na karcie **Konfiguracja** wybierz pozycję **potok początkowy**. Pokazuje plik potoku _Azure-Pipelines. yml_ z dwoma krokami skryptu.
1. Usuń dwa etapy skryptu z pliku _. yml_ .
1. Przesuń kursor do wiersza po wykonaniu **kroków:**.
1. Wybierz pozycję **Pokaż asystenta** po prawej stronie ekranu, aby otworzyć okienko **zadania** .
1. Wybierz pozycję **wdrożenie szablonu ARM**.
1. Podaj następujące wartości:

    * **deploymentScope**: Wybierz **grupę zasobów**. Aby dowiedzieć się więcej o zakresach, zobacz sekcję [Deployment Scopes](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager połączenie**: wybierz wcześniej utworzoną nazwę połączenia usługi.
    * **Subskrypcja**: Określ Identyfikator subskrypcji docelowej.
    * **Akcja**: Wybierz akcję **Utwórz lub Zaktualizuj grupę zasobów** — 2 akcje — 1. Utwórz grupę zasobów, jeśli podano nową nazwę grupy zasobów; dwóch. Wdróż określony szablon.
    * **Grupa zasobów**: wprowadź nową nazwę grupy zasobów. Na przykład **AzureRmPipeline-RG**.
    * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów, na przykład **środkowe stany USA**.
    * **Lokalizacja szablonu**: Wybierz **adres URL pliku**, co oznacza, że zadanie szuka pliku szablonu przy użyciu adresu URL. Ponieważ _RelativePath_ jest używany w szablonie głównym, a _RelativePath_ jest obsługiwana tylko w przypadku wdrożeń opartych na identyfikatorze URI, należy użyć tutaj adresu URL.
    * **Link szablonu**: wprowadź adres URL uzyskany na końcu sekcji [Przygotowywanie repozytorium GitHub](#prepare-a-github-repository) . Zaczyna się od `https://raw.githubusercontent.com` .
    * **Link parametrów szablonu**: pozostaw to pole puste. Wartości parametrów należy określić w **parametrach szablonu przesłonięć**.
    * **Przesłoń parametry szablonu**: ENTER `-projectName [EnterAProjectName]` .
    * **Tryb wdrożenia**: wybierz pozycję **przyrostowe**.
    * **Nazwa wdrożenia**: wprowadź **DeployPipelineTemplate**. Wybierz pozycję **Zaawansowane** , aby zobaczyć **nazwę wdrożenia**.

    ![Zrzut ekranu przedstawia stronę wdrożenia szablonu ARM z wprowadzonymi wymaganymi wartościami.](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Wybierz pozycję **Dodaj**.

    Aby uzyskać więcej informacji o zadaniu, zobacz zadanie [wdrażania grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)i [zadanie wdrażania Azure Resource Manager szablonu](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    Plik _. yml_ powinien wyglądać podobnie do:

    ![Zrzut ekranu przedstawia stronę przegląd z nowym potokiem zatytułowanym Przejrzyj potok YAML.](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Wybierz polecenie **Zapisz i uruchom**.
1. W okienku **Zapisz i uruchom** wybierz pozycję **Zapisz i uruchom** ponownie. Kopia pliku YAML jest zapisywana w połączonym repozytorium. Plik YAML można zobaczyć, przechodzenie do repozytorium.
1. Sprawdź, czy potok został pomyślnie wykonany.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa jest określona w pliku YAML potoku. Zobaczysz jedno utworzone konto magazynu. Nazwa konta magazynu rozpoczyna się od **zapisania**.
1. Wybierz nazwę konta magazynu, aby go otworzyć.
1. Wybierz pozycję **Właściwości**. Zauważ, że **replikacja** jest **magazynem lokalnie nadmiarowym (LRS)**.

## <a name="update-and-redeploy"></a>Aktualizowanie i ponowne wdrażanie

Gdy aktualizujesz szablon i wypychasz zmiany do repozytorium zdalnego, potok automatycznie zaktualizuje zasoby, konto magazynu w tym przypadku.

1. Otwórz _linkedStorageAccount.jsna_ podstawie lokalnego repozytorium w Visual Studio Code lub dowolnym edytorze tekstu.
1. Zaktualizuj wartość **DefaultValue** elementu **storageAccountType** do **Standard_GRS**. Zobacz poniższy zrzut ekranu:

    ![Azure Resource Manager usługi Azure DevOps Azure Pipelines Update YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Zapisz zmiany.
1. Wypchnij zmiany do repozytorium zdalnego, uruchamiając następujące polecenia z narzędzia Git bash/Shell.

    ```bash
    git pull origin main
    git add .
    git commit -m "Update the storage account type."
    git push origin main
    ```

    Pierwsze polecenie ( `pull` ) synchronizuje repozytorium lokalne ze zdalnym repozytorium. Plik YAML potoku został dodany tylko do zdalnego repozytorium. Uruchomienie `pull` polecenia spowoduje pobranie kopii pliku YAML do lokalnej gałęzi.

    Czwarte polecenie ( `push` ) przekazuje poprawione _linkedStorageAccount.jsw_ pliku do repozytorium zdalnego. Po zaktualizowaniu głównej gałęzi repozytorium zdalnego potok jest uruchamiany ponownie.

Aby sprawdzić zmiany, można sprawdzić Właściwość replikacji konta magazynu. Zobacz [weryfikacja wdrożenia](#verify-the-deployment).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

Możesz również chcieć usunąć repozytorium GitHub i projekt usługi Azure DevOps.

## <a name="next-steps"></a>Następne kroki

Gratulacje, ukończono ten samouczek wdrażania szablonu Menedżer zasobów. Daj nam znać, jeśli masz jakieś komentarze i sugestie w sekcji Opinie. Dziękujemy!
Możesz teraz przejść do bardziej zaawansowanych koncepcji dotyczących szablonów. Następny samouczek zawiera więcej szczegółów dotyczących korzystania z dokumentacji szablonów referencyjnych, aby ułatwić Definiowanie zasobów do wdrożenia.

> [!div class="nextstepaction"]
> [Dokumentacja dotycząca korzystania z szablonów](./template-tutorial-use-template-reference.md)
