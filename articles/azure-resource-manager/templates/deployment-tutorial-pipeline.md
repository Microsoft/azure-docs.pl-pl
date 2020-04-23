---
title: Ciągła integracja z użyciem usługi Azure Pipelines
description: Dowiedz się, jak stale tworzyć, testować i wdrażać szablony usługi Azure Resource Manager.
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1c56ce913a1b63bab90f5dd5aaada382abbf493
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084332"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Samouczek: Ciągła integracja szablonów usługi Azure Resource Manager z potokami platformy Azure

W [poprzednim samouczku](./deployment-tutorial-linked-template.md)wdrożono szablon połączony.  W tym samouczku dowiesz się, jak używać potoków platformy Azure do ciągłego tworzenia i wdrażania projektów szablonów usługi Azure Resource Manager.

Usługa Azure DevOps zapewnia usługi dla deweloperów, aby wspierać zespoły w planowaniu pracy, współpracy nad tworzeniem kodu oraz tworzeniu i wdrażaniu aplikacji. Deweloperzy mogą pracować w chmurze przy użyciu usług Azure DevOps. Usługa Azure DevOps zapewnia zintegrowany zestaw funkcji, do których można uzyskać dostęp za pośrednictwem przeglądarki sieci Web lub klienta IDE. Usługa Azure Pipeline jest jedną z tych funkcji. Usługa Azure Pipelines to w pełni funkcjonalna usługa ciągłej integracji (CI) i ciągłego dostarczania (CD). Współpracuje z preferowanym dostawcą git i można wdrożyć w większości głównych usług w chmurze. Następnie możesz zautomatyzować kompilację, testowanie i wdrażanie kodu na platformie Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

> [!NOTE]
> Wybierz nazwę projektu. Po przejściu przez samouczek, zastąpić dowolną z **AzureRmPipeline** z nazwą projektu.
> Ta nazwa projektu jest używana do generowania nazw zasobów.  Jednym z zasobów jest konto magazynu. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i mniejszych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu jest nazwą projektu z dołączenym "magazynem", a nazwa projektu musi zawierać od 3 do 11 znaków. Dlatego nazwa projektu musi spełniać wymagania dotyczące nazwy konta magazynu i ma mniej niż 11 znaków.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie repozytorium GitHub
> * Tworzenie projektu DevOps platformy Azure
> * Tworzenie potoku platformy Azure
> * Weryfikowanie wdrożenia potoku
> * Aktualizowanie szablonu i ponowne wdrożenie
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* **Konto GitHub**, gdzie używasz go do utworzenia repozytorium szablonów. Jeśli go nie masz, możesz [go utworzyć za darmo.](https://github.com) Aby uzyskać więcej informacji na temat korzystania z repozytoriów GitHub, zobacz [Tworzenie repozytoriów GitHub](/azure/devops/pipelines/repos/github).
* **Zainstaluj Git**. Ta instrukcja samouczka używa *Git Bash* lub *Git Shell*. Aby uzyskać instrukcje, zobacz [Instalowanie gita]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizacja Azure DevOps**. Jeśli go nie masz, możesz go utworzyć za darmo. Zobacz [Tworzenie kolekcji organizacji lub projektu]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* (opcjonalnie) **Rozszerzenie programu Visual Studio Code with Resource Manager Tools**. Zobacz [Tworzenie szablonów usługi Azure Resource Manager za pomocą kodu programu Visual Studio.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>Przygotowywanie repozytorium GitHub

GitHub służy do przechowywania kodu źródłowego projektu, w tym szablonów Menedżera zasobów. Aby uzyskać inne obsługiwane repozytoria, zobacz [repozytoria obsługiwane przez usługę Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Tworzenie repozytorium GitHub

Jeśli nie masz konta GitHub, zobacz [Wymagania wstępne](#prerequisites).

1. Zaloguj się do [GitHub](https://github.com).
1. Wybierz obraz konta w prawym górnym rogu, a następnie wybierz **pozycję Twoje repozytoria**.

    ![Potoki azure resource manager azure devops azure tworzą repozytorium GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wybierz **pozycję Nowy**, zielony przycisk.
1. W **nazwie repozytorium**wprowadź nazwę repozytorium.  Na przykład **AzureRmPipeline-repo**. Pamiętaj, aby zastąpić dowolną usługę **AzureRmPipeline** nazwą projektu. Możesz wybrać **publiczne** lub **prywatne,** aby przejść przez ten samouczek. A następnie wybierz **pozycję Utwórz repozytorium**.
1. Zapisz adres URL. Adres URL repozytorium jest następującym formatem - ** https://github.com/[YourAccountName]/[YourRepositoryName]**.

To repozytorium jest określane jako *zdalne repozytorium*. Każdy z deweloperów tego samego projektu może sklonować własne *repozytorium lokalne*i scalić zmiany w zdalnym repozytorium.

### <a name="clone-the-remote-repository"></a>Klonowanie zdalnego repozytorium

1. Otwórz Git Shell lub Git Bash.  Zobacz [Wymagania wstępne](#prerequisites).
1. Sprawdź, czy twoim bieżącym folderem jest **GitHub**.
1. Uruchom następujące polecenie:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Zastąp **[YourAccountName]** swoją nazwą konta GitHub i zastąp **[YourGitHubRepositoryName]** nazwą repozytorium utworzoną w poprzedniej procedurze.

Folder **CreateWebApp** jest folderem, w którym szablon jest przechowywany. Polecenie **pwd** pokazuje ścieżkę folderu. Ścieżka jest miejscem, w którym można zapisać szablon w poniższej procedurze.

### <a name="download-a-quickstart-template"></a>Pobieranie szablonu przewodnika Szybki start

Zamiast tworzyć szablony, można pobrać szablony i zapisać je w folderze **CreateWebApp.**

* Główny szablon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Połączony szablon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Zarówno nazwa folderu, jak i nazwy plików są używane, ponieważ są one w potoku.  Jeśli zmienisz te nazwy, należy zaktualizować nazwy używane w potoku.

### <a name="push-the-template-to-the-remote-repository"></a>Wypychanie szablonu do zdalnego repozytorium

Azuredeploy.json został dodany do lokalnego repozytorium. Następnie należy przekazać szablon do zdalnego repozytorium.

1. Otwórz *Git Shell* lub *Git Bash*, jeśli nie jest otwarty.
1. Zmień katalog na folder CreateWebApp w lokalnym repozytorium.
1. Sprawdź, czy plik **azuredeploy.json** znajduje się w folderze.
1. Uruchom następujące polecenie:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Możesz otrzymać ostrzeżenie o LF. Ostrzeżenie można zignorować. **master** jest gałęzią główną.  Zazwyczaj tworzysz gałąź dla każdej aktualizacji. Aby uprościć samouczek, należy użyć gałęzi głównej bezpośrednio.
1. Przejdź do repozytorium GitHub z przeglądarki.  Adres URL to ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Zobaczysz folder **CreateWebApp** i trzy pliki wewnątrz folderu.
1. Wybierz **opcję linkedStorageAccount.json,** aby otworzyć szablon.
1. Wybierz przycisk **Raw.** Adres URL jest uruchamiany od **raw.githubusercontent.com**.
1. Utwórz kopię adresu URL.  Należy podać tę wartość podczas konfigurowania potoku w dalszej części samouczka.

Do tej pory utworzono repozytorium GitHub i przesłano szablony do repozytorium.

## <a name="create-a-devops-project"></a>Tworzenie projektu DevOps

Organizacja DevOps jest potrzebna, zanim będzie można przejść do następnej procedury.  Jeśli go nie masz, zobacz [Wymagania wstępne](#prerequisites).

1. Zaloguj się do [usługi Azure DevOps](https://dev.azure.com).
1. Wybierz organizację DevOps od lewej.

    ![Potoki azure resource manager azure devops azure create Azure DevOps project Azure Resource Manager Azure DevOps create Azure DevOps project Azure Resource Manager Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wybierz pozycję **New project** (Nowy projekt). Jeśli nie masz żadnych projektów, strona tworzenia projektu jest otwierana automatycznie.
1. Wprowadź następujące wartości:

    * **Nazwa projektu**: wprowadź nazwę projektu. Można użyć nazwy projektu, który został wybrany na samym początku samouczka.
    * **Kontrola wersji**: Wybierz **Git**. Może być konieczne rozwinięcie **zaawansowanego,** aby wyświetlić **kontrolę wersji**.

    Użyj wartości domyślnej dla innych właściwości.
1. Wybierz pozycję **Utwórz**.

Utwórz połączenie usługi, które jest używane do wdrażania projektów na platformie Azure.

1. Wybierz **pozycję Ustawienia projektu** u dołu lewego menu.
1. Wybierz **pozycję Połączenia usługi w** obszarze **Potoki**.
1. Wybierz **pozycję Nowe połączenie usługi**, wybierz pozycję Azure Resource **Manager**, a następnie wybierz pozycję **Dalej**.
1. Wybierz **pozycję Główny serwis**, a następnie wybierz pozycję **Dalej**.
1. Wprowadź następujące wartości:

    * **Poziom zakresu**: wybierz **pozycję Subskrypcja**.
    * **Subskrypcja:** wybierz subskrypcję.
    * **Grupa zasobów**: Pozostaw ją pustą.
    * **Nazwa połączenia**: wprowadź nazwę połączenia. Na przykład **AzureRmPipeline-conn**. Zapisz tę nazwę, potrzebujesz nazwy podczas tworzenia potoku.
    * **Udziel uprawnień dostępu do wszystkich potoków**. (wybrano)
1. Wybierz pozycję **Zapisz**.

## <a name="create-a-pipeline"></a>Tworzenie potoku

Do tej pory wykonaliśmy następujące zadania.  Jeśli pominąć poprzednie sekcje, ponieważ są zaznajomieni z GitHub i DevOps, należy wykonać zadania przed kontynuowaniem.

* Utwórz repozytorium GitHub i zapisz szablony w folderze **CreateWebApp** w repozytorium.
* Utwórz projekt DevOps i utwórz połączenie usługi Azure Resource Manager.

Aby utworzyć potok z krokiem do wdrożenia szablonu:

1. Z menu po lewej stronie **wybierz polecenie Potoki.**
1. Wybierz **nowy potok**.
1. Na karcie **Connect** (Połączenie) wybierz pozycję **GitHub**. Jeśli zostaniesz poproszony, wprowadź poświadczenia usługi GitHub, a następnie postępuj zgodnie z instrukcjami. Jeśli widzisz następujący ekran, wybierz **pozycję Wybierz tylko repozytoria**i sprawdź, czy repozytorium znajduje się na liście przed wybraniem opcji **Zatwierdź & Zainstaluj**.

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines tylko wybrać repozytoria](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na karcie **Wybierz** wybierz repozytorium.  Domyślna nazwa to **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Na karcie **Konfigurowanie** wybierz pozycję **Potok startowy**. Pokazuje plik potoku **azure-pipelines.yml** z dwoma krokami skryptu.
1. Usuń dwa kroki skryptu z pliku yml.
1. Przenieś kursor do wiersza po **krokach:**.
1. Wybierz **pozycję Pokaż asystenta** po prawej stronie ekranu, aby otworzyć okienko **Zadania.**
1. Wybierz **wdrożenie szablonu ARM**.
1. Wprowadź następujące wartości:

    * **deploymentScope**: Wybierz **grupę zasobów**.. Aby dowiedzieć się więcej o zakresach, zobacz [Zakresy wdrażania](deploy-rest.md#deployment-scope).
    * **Połączenie usługi Azure Resource Manager:** Wybierz nazwę połączenia usługi, która została utworzona wcześniej.
    * **Subskrypcja**: Określ identyfikator subskrypcji docelowej.
    * **Akcja**: Wybierz akcję **Utwórz lub aktualizuj grupę zasobów** wykonuje 2 akcje - 1. utworzyć grupę zasobów, jeśli podana jest nowa nazwa grupy zasobów; 2. wdrożyć określony szablon.
    * **Grupa zasobów**: Wprowadź nową nazwę grupy zasobów. Na przykład **AzureRmPipeline-rg**.
    * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów, na przykład **środkowe stany USA**.
    * **Lokalizacja szablonu**: Wybierz **połączony artefakt**, co oznacza, że zadanie wyszukuje plik szablonu bezpośrednio z połączonego repozytorium.
    * **Szablon**: Wprowadź **CreateWebApp/azuredeploy.json**. Jeśli zmieniono nazwę folderu i nazwę pliku, należy zmienić tę wartość.
    * **Parametry szablonu**: Pozostaw to pole puste. Wartości parametrów zostaną określone w parametrach szablonu **Zastądeń.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Zastąp nazwę projektu i połączony adres URL szablonu. Połączony adres URL szablonu jest tym, co zapisałeś na końcu [tworzenia repozytorium GitHub](#create-a-github-repository).
    * **Tryb wdrażania**: Wybierz **opcję Przyrostowa**.
    * **Nazwa wdrożenia**: Enter **DeployPipelineTemplate**. Wybierz **pozycję Zaawansowane,** zanim będzie można wyświetlić **nazwę wdrożenia**.

    ![Krok Potoki usługi Azure Resource Manager Azure DevOps](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Wybierz pozycję **Dodaj**.

    Aby uzyskać więcej informacji na temat tego zadania, zobacz [Zadanie wdrażania grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)i zadanie wdrażania [szablonu usługi Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    Akta yml są podobne do:

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Wybierz polecenie **Zapisz i uruchom**.
1. W okienku **Zapisz i uruchom** wybierz pozycję Zapisz i uruchom **ponownie.** Kopia pliku YAML jest zapisywana w podłączonym repozytorium. Możesz zobaczyć plik YAML, przejdź do repozytorium.
1. Sprawdź, czy potok został pomyślnie wykonany.

    ![Usługi Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Nazwa jest to, co zostało określone w pliku YAML potoku.  Zobaczysz jedno konto magazynu utworzone.  Nazwa konta magazynu zaczyna się od **magazynu**.
1. Wybierz nazwę konta magazynu, aby ją otworzyć.
1. Wybierz **pozycję Właściwości**. Zwróć uwagę, **że replikacja** to **magazyn lokalnie nadmiarowy (LRS).**

## <a name="update-and-redeploy"></a>Aktualizacja i ponowne wdrożenie

Po zaktualizowaniu szablonu i wypchnięciu zmian do repozytorium zdalnego potok automatycznie aktualizuje zasoby, konto magazynu w tym przypadku.

1. Otwórz **linkedStorageAccount.json** z lokalnego repozytorium w programie Visual Studio Code lub dowolnego edytora tekstu.
1. Zaktualizuj **domyślnąValue** **storageAccountType** **do Standard_GRS**. Zobacz poniższy zrzut ekranu:

    ![Usługa Azure Resource Manager Azure DevOps Azure Pipelines aktualizuje yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Zapisz zmiany.
1. Wypchnij zmiany do zdalnego repozytorium, uruchamiając następujące polecenia z Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    Pierwsze polecenie (ściąganie) synchronizuje repozytorium lokalne ze zdalnym repozytorium. Plik YAML potoku został dodany tylko do zdalnego repozytorium. Uruchomienie polecenia ściągania pobiera kopię pliku YAML do gałęzi lokalnej.

    Czwarte polecenie (wypychanie) przesyła poprawiony plik linkedStorageAccount.json do zdalnego repozytorium. Po zaktualizowaniu gałęzi głównej repozytorium zdalnego potok jest uruchamiany ponownie.

Aby zweryfikować zmiany, można sprawdzić właściwość Replikacja konta magazynu.  Zobacz [Zweryfikowanie wdrożenia](#verify-the-deployment).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

Można również usunąć repozytorium GitHub i projektu Azure DevOps.

## <a name="next-steps"></a>Następne kroki

Gratulacje, ukończono ten samouczek wdrażania szablonu Menedżera zasobów. Daj nam znać, jeśli masz jakieś uwagi i sugestie w sekcji opinii. Dziękujemy.
Możesz przejść do bardziej zaawansowanych koncepcji szablonów. Następny samouczek przechodzi do bardziej szczegółowych informacji na temat korzystania z dokumentacji odwołania do szablonu, aby pomóc w definiowaniu zasobów do wdrożenia.

> [!div class="nextstepaction"]
> [Dokumentacja dotycząca korzystania z szablonów](./template-tutorial-use-template-reference.md)
