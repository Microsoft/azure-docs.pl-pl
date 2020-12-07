---
title: Automatyzowanie zadań i przepływów pracy za pomocą programu Visual Studio
description: Tworzenie, planowanie i uruchamianie zautomatyzowanych przepływów pracy dla integracji przedsiębiorstwa przy użyciu Azure Logic Apps i programu Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/27/2020
ms.openlocfilehash: ff195f7a0071c06d5309f95f77e32ae75f584f82
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749168"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Szybki start: Tworzenie automatycznych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps — Visual Studio

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i programu Visual Studio można tworzyć przepływy pracy służące do automatyzowania zadań i procesów, które integrują aplikacje, dane, systemy i usługi między przedsiębiorstwami i organizacjami. W tym przewodniku Szybki start przedstawiono sposób projektowania i kompilowania tych przepływów pracy przez tworzenie aplikacji logiki w programie Visual Studio i wdrażanie ich na platformie Azure. Chociaż można wykonać te zadania w Azure Portal, program Visual Studio umożliwia dodawanie aplikacji logiki do kontroli źródła, publikowanie różnych wersji i tworzenie Azure Resource Manager szablonów dla różnych środowisk wdrażania.

Jeśli dopiero zaczynasz Azure Logic Apps i chcesz tylko uzyskać podstawowe koncepcje, wypróbuj [Przewodnik Szybki Start dotyczący tworzenia aplikacji logiki w Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Projektant aplikacji logiki działa podobnie do Azure Portal i programu Visual Studio.

W tym przewodniku szybki start utworzysz tę samą aplikację logiki przy użyciu programu Visual Studio, co Azure Portal przewodniku Szybki Start. Możesz również dowiedzieć się, jak [utworzyć przykładową aplikację w Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)i [utworzyć Aplikacje logiki i zarządzać nimi za pomocą interfejsu Command-Line platformy Azure (interfejs wiersza polecenia platformy Azure)](quickstart-logic-apps-azure-cli.md). Ta aplikacja logiki monitoruje kanał informacyjny RSS witryny sieci Web i wysyła wiadomość e-mail dla każdego nowego elementu w tym źródle danych. Gotowa aplikacja logiki wygląda jak ten przepływ pracy wysokiego poziomu:

![Zrzut ekranu pokazujący przepływ pracy wysokiego poziomu gotowej aplikacji logiki.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Jeśli masz subskrypcję Azure Government, postępuj zgodnie z tymi dodatkowymi krokami, aby [skonfigurować program Visual Studio dla Azure Government Cloud](#azure-government).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio 2019, 2017 lub 2015 — wersja Community lub nowsza](https://aka.ms/download-visual-studio). Ten przewodnik Szybki Start używa programu Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017 upewnij się, że wybrano obciążenie **Programowanie na platformie Azure** .

  * [Zestaw Microsoft Azure SDK dla platformy .NET (2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/). Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Najnowsze Azure Logic Apps narzędzia dla rozszerzenia programu Visual Studio dla używanej wersji:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do Internetu podczas korzystania z osadzonego Projektanta aplikacji logiki

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure i odczytywania właściwości i danych z łączników w aplikacji logiki.

* Konto e-mail obsługiwane przez Logic Apps, takie jak Outlook dla Microsoft 365, Outlook.com lub gmail. W przypadku innych dostawców zapoznaj się z [listą łączników tutaj](/connectors/). W tym przykładzie zastosowano pakiet Office 365 Outlook. Jeśli używasz innego dostawcy, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Set up Visual Studio for Azure Government (Konfigurowanie programu Visual Studio na potrzeby usługi Azure Government)

### <a name="visual-studio-2017"></a>Visual Studio 2017

Można użyć [rozszerzenia środowiska Azure Environment Selector programu Visual Studio](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), które można pobrać i zainstalować z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

### <a name="visual-studio-2019"></a>Visual Studio 2019

Aby obejść Azure Government subskrypcje w Azure Logic Apps, musisz [dodać punkt końcowy odnajdywania dla chmury Azure Government do programu Visual Studio](../azure-government/documentation-government-connect-vs.md). Jednak *przed zalogowaniem się do programu Visual Studio przy użyciu konta Azure Government* należy zmienić nazwę pliku JSON, który jest generowany po dodaniu punktu końcowego odnajdywania, wykonując następujące czynności:

1. Zamknij program Visual Studio.

1. Znajdź wygenerowany plik JSON o nazwie `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` w tej lokalizacji:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Zmień nazwę pliku JSON na `AadProvider.Configuration.json` .

1. Uruchom ponownie program Visual Studio.

1. Wykonaj kroki, aby zalogować się za pomocą konta Azure Government.

Aby przywrócić tę konfigurację, usuń plik JSON w następującej lokalizacji i ponownie uruchom program Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

Aby rozpocząć, utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Uruchom program Visual Studio. Zaloguj się przy użyciu konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**. (Klawiatura: Ctrl + Shift + N)

   ![W menu „File” wybierz pozycję „Nowy” > „Projekt”](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Wybierz **Cloud**  >  **grupę zasobów** w chmurze Azure. Nazwij projekt, na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Nazwy grup zasobów mogą zawierać tylko litery, cyfry, kropki ( `.` ), podkreślenia ( `_` ), łączniki ( `-` ) i nawiasy ( `(` , `)` ), ale nie mogą *kończyć* się kropką ( `.` ).
   >
   > Jeśli nie zostanie wyświetlona usługa **Cloud** lub **Grupa zasobów platformy Azure** , upewnij się, że zainstalowano zestaw Azure SDK dla programu Visual Studio.

   Jeśli używasz programu Visual Studio 2019, wykonaj następujące kroki:

   1. W polu **Utwórz nowy projekt** wybierz projekt **Grupa zasobów platformy Azure** dla programu Visual C# lub Visual Basic. Wybierz pozycję **Dalej**.

   1. Podaj nazwę grupy zasobów platformy Azure, której chcesz użyć, i inne informacje o projekcie. Wybierz przycisk **Utwórz**.

1. Z listy szablon wybierz szablon **aplikacji logiki** . Wybierz przycisk **OK**.

   ![Wybieranie szablonu aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Po utworzeniu projektu w programie Visual Studio Eksplorator rozwiązań otwiera i pokazuje rozwiązanie. W rozwiązaniu **LogicApp.jsw** pliku nie tylko przechowuje definicję aplikacji logiki, ale również szablon Azure Resource Manager, którego można użyć do wdrożenia.

   ![Eksplorator rozwiązań pokazuje nowe rozwiązanie aplikacji logiki i pliku wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

Jeśli masz projekt grupy zasobów platformy Azure, Utwórz aplikację logiki przy użyciu szablonu **pustej aplikacji logiki** .

1. W Eksplorator rozwiązań otwórz menu skrótów pliku **LogicApp.js** . Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwieranie pliku JSON aplikacji logiki za pomocą Projektanta aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Program Visual Studio poprosi o subskrypcję platformy Azure i grupę zasobów platformy Azure na potrzeby tworzenia i wdrażania zasobów dla aplikacji logiki i połączeń.

1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć kolejną grupę zasobów platformy Azure.

   ![Wybieranie subskrypcji platformy Azure, grupy zasobów i lokalizacji zasobu](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Ustawienie | Przykładowa wartość | Opis |
   | ------- | ------------- | ----------- |
   | Konto użytkownika | Fabrikam <br> sophia-owen@fabrikam.com | Konto używane podczas logowania do programu Visual Studio |
   | **Subskrypcja** | Płatność zgodnie z rzeczywistym użyciem <br> (sophia-owen@fabrikam.com) | Nazwa Twojej subskrypcji platformy Azure i skojarzone konto |
   | **Grupa zasobów** | MyLogicApp-RG <br> (Zachodnie stany USA) | Grupa zasobów platformy Azure i lokalizacja służąca do przechowywania i wdrażania zasobów aplikacji logiki |
   | **Lokalizacja** | **Taka sama jak grupa zasobów** | Typ lokalizacji i określona lokalizacja wdrażania aplikacji logiki. Typ lokalizacji to region platformy Azure lub istniejące [środowisko usługi integracji (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>W tym przewodniku szybki start Zachowaj typ lokalizacji ustawiony na **region** i lokalizację ustawioną na **taką samą jak grupa zasobów**. <p>**Uwaga**: po utworzeniu projektu grupy zasobów można [zmienić typ lokalizacji i lokalizację](manage-logic-apps-with-visual-studio.md#change-location), ale różne typy lokalizacji mają wpływ na aplikację logiki na różne sposoby. |
   ||||

1. W projektancie Logic Apps zostanie otwarta strona z wprowadzeniem wideo i często używanymi wyzwalaczami. Przewiń w dół wideo i wyzwalacze do **szablonów**, a następnie wybierz pozycję **pusta aplikacja logiki**.

   ![Wybieranie pozycji „Pusta aplikacja logiki”](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Kompilowanie przepływu pracy aplikacji logiki

Następnie Dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS, który jest uruchamiany, gdy pojawi się nowy element źródła danych. Każda aplikacja logiki rozpoczyna się od wyzwalacza, który jest uruchamiany po spełnieniu określonych kryteriów. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej przepływ pracy.

1. W Projektancie aplikacji logiki, w polu wyszukiwania zaznacz **wszystko**. W polu wyszukiwania wprowadź ciąg "RSS". Z listy Wyzwalacze wybierz ten wyzwalacz: **po opublikowaniu elementu kanału informacyjnego**

   ![Kompilowanie aplikacji logiki przez dodanie wyzwalacza i akcji](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po wyświetleniu wyzwalacza w projektancie Zakończ tworzenie aplikacji logiki, wykonując kroki przepływu pracy w [Azure Portal szybki start](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), a następnie wróć do tego artykułu. Gdy wszystko będzie gotowe, aplikacja logiki będzie wyglądać następująco:

   ![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Zapisz rozwiązanie programu Visual Studio. (Klawiatura: Ctrl+S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Wdrażanie aplikacji logiki na platformie Azure

Aby można było uruchomić i przetestować aplikację logiki, należy wdrożyć aplikację na platformie Azure z poziomu programu Visual Studio.

1. W Eksplorator rozwiązań w menu skrótów projektu wybierz pozycję **Wdróż**  >  **nowe**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

   ![Tworzenie wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. W przypadku tego wdrożenia Zachowaj domyślną subskrypcję platformy Azure, grupę zasobów i inne ustawienia. Wybierz pozycję **Wdróż**.

   ![Wdrażanie aplikacji logiki do grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie wyświetlone okno **Edytuj parametry** , podaj nazwę zasobu dla aplikacji logiki. Zapisz ustawienia.

   ![Podawanie nazwy wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure.

   ![Dane wyjściowe stanu wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Jeśli wybrane łączniki wymagają wprowadzenia danych, zostanie otwarte okno programu PowerShell w tle i zostanie wyświetlony monit o podanie wszelkich niezbędnych haseł lub kluczy tajnych. Po wprowadzeniu tych informacji wdrażanie będzie kontynuowane.

   ![Okno programu PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po zakończeniu wdrażania aplikacja logiki będzie aktywna w Azure Portal i będzie działać zgodnie z określonym harmonogramem (co minutę). Jeśli wyzwalacz znajdzie nowe elementy kanału informacyjnego, wyzwala wyzwalacz, który tworzy wystąpienie przepływu pracy, które uruchamia akcje aplikacji logiki. Aplikacja logiki wysyła wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie, jeśli wyzwalacz nie znajdzie nowych elementów, wyzwalacz nie uruchomi się i "pomija" Tworzenie wystąpienia przepływu pracy. Aplikacja logiki czeka do następnego interwału przed sprawdzeniem.

   Poniżej przedstawiono przykładowe wiadomości e-mail wysyłane przez tę aplikację logiki. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

   ![Program Outlook wysyła wiadomość e-mail dla każdego nowego elementu danych RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulacje, Twoja aplikacja logiki została pomyślnie skompilowana i wdrożona za pomocą programu Visual Studio. Aby zarządzać aplikacją logiki i przeglądać historię jej uruchomień, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Dodaj nową aplikację logiki

Jeśli masz istniejący projekt grupy zasobów platformy Azure, możesz dodać nową pustą aplikację logiki do tego projektu przy użyciu okna konspektu JSON.

1. W Eksplorator rozwiązań Otwórz `<logic-app-name>.json` plik.

1. Z menu **Widok** wybierz **inny**  >  **Konspekt JSON** systemu Windows.

1. Aby dodać zasób do pliku szablonu, wybierz pozycję **Dodaj zasób** u góry okna konspektu JSON. Lub w oknie konspektu JSON Otwórz menu skrótów **zasoby** , a następnie wybierz pozycję **Dodaj nowy zasób**.

   ![Okno konspektu JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. W oknie dialogowym **Dodawanie zasobu** w polu wyszukiwania Znajdź `logic app` i wybierz pozycję **aplikacja logiki**. Nadaj nazwę aplikacji logiki, a następnie wybierz pozycję **Dodaj**.

   ![Dodaj zasób](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z aplikacją logiki Usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) za pomocą konta używanego do tworzenia aplikacji logiki.

1. W menu Azure Portal wybierz pozycję **grupy zasobów** lub Wyszukaj i wybierz pozycję **grupy zasobów** z dowolnej strony. Wybierz grupę zasobów aplikacji logiki.

1. Na stronie **Przegląd** wybierz pozycję **Usuń grupę zasobów**. Wprowadź nazwę grupy zasobów jako potwierdzenie, a następnie wybierz pozycję **Usuń**.

   ![„Grupy zasobów” > „Przegląd” > „Usuń grupę zasobów”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Usuń rozwiązanie programu Visual Studio z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

W tym artykule kompilowano, wdrożono i uruchomiono aplikację logiki przy użyciu programu Visual Studio. Aby dowiedzieć się więcej o zarządzaniu i wykonywaniu zaawansowanego wdrażania aplikacji logiki za pomocą programu Visual Studio, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
