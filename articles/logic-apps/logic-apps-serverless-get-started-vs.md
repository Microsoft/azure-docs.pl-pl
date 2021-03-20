---
title: Tworzenie pierwszej aplikacji bezserwerowej w programie Visual Studio
description: Kompilowanie i wdrażanie aplikacji bezserwerowej oraz zarządzanie nią przy użyciu Azure Logic Apps i Azure Functions w programie Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96749151"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Tworzenie pierwszej aplikacji bezserwerowej przy użyciu Azure Logic Apps i Azure Functions w programie Visual Studio

Możesz szybko opracowywać i wdrażać aplikacje w chmurze, korzystając z narzędzi i funkcji bezserwerowych na platformie Azure, takich jak [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Azure Functions](../azure-functions/functions-overview.md). W tym artykule pokazano, jak rozpocząć tworzenie aplikacji bezserwerowej, która używa aplikacji logiki, która wywołuje funkcję platformy Azure w programie Visual Studio. Aby dowiedzieć się więcej na temat rozwiązań bezserwerowych na platformie Azure, zobacz [bezserwerowe usługi Azure Functions i Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do utworzenia aplikacji bezserwerowej w programie Visual Studio potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Poniższe narzędzia. Jeśli ich nie masz, pobierz je i zainstaluj.

  * [Visual Studio 2019, 2017 lub 2015 (społeczność lub inne wersje)](https://aka.ms/download-visual-studio). 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017 upewnij się, że wybrano obciążenie **Programowanie na platformie Azure** .

  * [Zestaw Microsoft Azure SDK dla platformy .NET (wersja 2.9.1 lub nowsza)](https://azure.microsoft.com/downloads/). 
  Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](/dotnet/azure/intro).

  * Zainstalowanie programu [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps narzędzia dla wersji programu Visual Studio, którą chcesz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) dla funkcji debugowania lokalnego.

* Dostęp do sieci Web przy użyciu osadzonego projektanta aplikacji logiki.

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

## <a name="create-a-resource-group-project"></a>Tworzenie projektu grupy zasobów

Aby rozpocząć, Utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dla aplikacji bezserwerowej. Na platformie Azure Możesz tworzyć zasoby w *grupie zasobów*, która jest kolekcją logiczną używaną do organizowania, zarządzania i wdrażania zasobów dla całej aplikacji jako pojedynczego zasobu. W przypadku aplikacji bezserwerowej na platformie Azure Grupa zasobów zawiera zasoby dla Azure Logic Apps i Azure Functions. Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Uruchom program Visual Studio i zaloguj się przy użyciu konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**.

   ![Utwórz nowy projekt w programie Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Następnie wybierz pozycję   >  **Grupa zasobów** w chmurze Azure.

   > [!NOTE]
   > Jeśli nie istnieje kategoria **chmury** lub projekt **grupy zasobów platformy Azure** , upewnij się, że zainstalowano zestaw Azure SDK dla programu Visual Studio.

   Jeśli używasz programu Visual Studio 2019, wykonaj następujące kroki:

   1. W polu **Utwórz nowy projekt** wybierz szablon projektu **Grupa zasobów platformy Azure** dla języka Visual C# lub Visual Basic, a następnie wybierz przycisk **dalej**.

   1. Podaj nazwę i inne informacje o projekcie, które mają być używane dla grupy zasobów platformy Azure. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

1. Nadaj projektowi nazwę i lokalizację, a następnie wybierz przycisk **OK**.

   Program Visual Studio wyświetli prośbę o wybranie szablonu z listy szablonów. 
   W tym przykładzie zastosowano szablon szybkiego startu platformy Azure, dzięki czemu można utworzyć aplikację bezserwerową, która zawiera aplikację logiki i wywołanie funkcji platformy Azure.

   > [!TIP]
   > W scenariuszach, w których nie chcesz prewdrażać rozwiązania w grupie zasobów platformy Azure, możesz użyć szablonu pustej **aplikacji logiki** , który po prostu tworzy pustą aplikację logiki.

1. Z listy **Pokaż szablony z tej lokalizacji** wybierz pozycję **Azure — szybki start (GitHub.com/Azure/Azure-QuickStart-templates)**.

1. W polu wyszukiwania wprowadź ciąg "Logic-App" jako filtr. Na podstawie wyników wybierz szablon **101-Logic-App-and-Function-App** .

   ![Wybierz szablon szybkiego startu platformy Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Program Visual Studio tworzy i otwiera rozwiązanie dla projektu grupy zasobów. 
   Wybrany szablon szybkiego startu platformy Azure tworzy szablon wdrożenia o nazwie azuredeploy.jsw obrębie projektu grupy zasobów. Ten szablon wdrożenia obejmuje definicję prostej aplikacji logiki, która jest wyzwalana przez żądanie HTTP, wywołuje funkcję platformy Azure i zwraca wynik jako odpowiedź HTTP.

   ![Nowe rozwiązanie bezserwerowe](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Następnie wdróż swoje rozwiązanie na platformie Azure. Należy to zrobić przed otwarciem szablonu wdrożenia i przejrzeniem zasobów dla aplikacji bezserwerowej.

## <a name="deploy-your-solution"></a>Wdrażanie rozwiązania

Aby można było otworzyć aplikację logiki w Projektancie aplikacji logiki w programie Visual Studio, musisz mieć grupę zasobów platformy Azure, która została już wdrożona na platformie Azure. Projektant może następnie utworzyć połączenia z zasobami i usługami w aplikacji logiki. W przypadku tego zadania wykonaj następujące kroki, aby wdrożyć rozwiązanie z programu Visual Studio w Azure Portal:

1. W Eksplorator rozwiązań z menu skrótów projektu zasobów wybierz pozycję **Wdróż**  >  **nowe**.

   ![Utwórz nowe wdrożenie dla grupy zasobów](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Jeśli nie są one jeszcze wybrane, wybierz subskrypcję platformy Azure i grupę zasobów, w której chcesz wdrożyć. Następnie wybierz pozycję **Wdróż**.

   ![Ustawienia wdrożenia](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Jeśli zostanie wyświetlone okno **Edytuj parametry** , Podaj nazwy zasobów, które mają być używane dla aplikacji logiki i aplikacji funkcji platformy Azure podczas wdrażania, a następnie Zapisz ustawienia. Upewnij się, że używasz unikatowej nazwy globalnej dla aplikacji funkcji.

   ![Podaj nazwy aplikacji logiki i aplikacji funkcji](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Gdy program Visual Studio rozpocznie wdrażanie do określonej grupy zasobów, stan wdrożenia rozwiązania pojawi się w oknie **danych wyjściowych** programu Visual Studio. 
   Po zakończeniu wdrażania aplikacja logiki będzie aktywna w Azure Portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Edytowanie aplikacji logiki w programie Visual Studio

Aby edytować aplikację logiki po wdrożeniu, Otwórz aplikację logiki przy użyciu projektanta aplikacji logiki w programie Visual Studio.

1. W Eksplorator rozwiązań z menu skrótów okna azuredeploy.jsw pliku wybierz pozycję **Otwórz za pomocą projektanta aplikacji logiki**.

   ![Otwórz azuredeploy.jsw programie Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

1. Gdy zostanie wyświetlone okno **właściwości aplikacji logiki** , w obszarze **subskrypcja** wybierz subskrypcję platformy Azure, jeśli nie została jeszcze wybrana. W obszarze **Grupa zasobów** wybierz grupę zasobów i lokalizację, w której wdrożono rozwiązanie, a następnie wybierz przycisk **OK**.

   ![Właściwości aplikacji logiki](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otwarciu projektanta aplikacji logiki można kontynuować dodawanie kroków lub zmienić przepływ pracy oraz zapisać aktualizacje.

   ![Otwarto aplikację logiki w Projektancie aplikacji logiki](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Tworzenie projektu Azure Functions

Aby utworzyć projekt funkcji i funkcję przy użyciu języka JavaScript, Python, F #, programu PowerShell, usługi Batch lub bash, wykonaj kroki opisane w temacie [Work with Azure Functions Core Tools](../azure-functions/functions-run-local.md). Aby opracować funkcję platformy Azure przy użyciu języka C# w rozwiązaniu, użyj biblioteki klas języka C#, wykonując kroki opisane w temacie [Publikowanie biblioteki klas .NET jako aplikacja funkcji](https://azure.microsoft.com/blog/).

## <a name="deploy-functions-from-visual-studio"></a>Wdrażanie funkcji z programu Visual Studio

Szablon wdrożenia wdraża wszystkie funkcje platformy Azure, które znajdują się w rozwiązaniu, z repozytorium git, które jest określone przez zmienne w azuredeploy.jspliku. Jeśli tworzysz i utworzysz projekt funkcji w rozwiązaniu, możesz sprawdzić ten projekt w kontroli źródła git (na przykład GitHub lub Azure DevOps), a następnie zaktualizować zmienną, tak aby `repo` szablon wdrażał funkcję platformy Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Zarządzaj usługą Logic Apps i Wyświetl historię uruchamiania

W przypadku aplikacji logiki już wdrożonych na platformie Azure można nadal edytować, zarządzać, wyświetlać historię uruchamiania i wyłączać te aplikacje z programu Visual Studio.

1. W menu **Widok** w programie Visual Studio Otwórz program **Cloud Explorer**.

1. W obszarze **wszystkie subskrypcje** wybierz subskrypcję platformy Azure skojarzoną z aplikacjami logiki, którymi chcesz zarządzać, a następnie wybierz pozycję **Zastosuj**.

1. W obszarze **Logic Apps** wybierz aplikację logiki. Z menu skrótów tej aplikacji wybierz pozycję **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

Teraz można pobrać już opublikowaną aplikację logiki do projektu grupy zasobów. Mimo że można uruchomić aplikację logiki w Azure Portal, można nadal importować tę aplikację i zarządzać nią w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md)
