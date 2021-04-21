---
title: Automatyzowanie zadań i przepływów pracy za pomocą Visual Studio
description: Tworzenie, planowanie i uruchamianie zautomatyzowanych przepływów pracy na rzecz integracji dla przedsiębiorstw przy użyciu Azure Logic Apps i Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 5ae67e5708a7298385a4e27d612566008884b972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790063"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Szybki start: tworzenie zautomatyzowanych zadań, procesów i przepływów pracy za Azure Logic Apps — Visual Studio

Dzięki [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i Visual Studio można tworzyć przepływy pracy służące do automatyzowania zadań i procesów, które integrują aplikacje, dane, systemy i usługi w przedsiębiorstwach i organizacjach. W tym przewodniku Szybki start przedstawiono sposób projektowania i kompilowania tych przepływów pracy przez tworzenie aplikacji logiki w programie Visual Studio i wdrażanie ich na platformie Azure. Chociaż te zadania można wykonywać w środowisku Azure Portal, program Visual Studio umożliwia dodawanie aplikacji logiki do kontroli źródła, publikowanie różnych wersji i tworzenie szablonów Azure Resource Manager dla różnych środowisk wdrażania.

Jeśli dopiero zaczynasz tworzyć Azure Logic Apps podstawowych pojęć, wypróbuj przewodnik Szybki start tworzenia aplikacji logiki w [Azure Portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Projektant aplikacji logiki działa podobnie zarówno w Azure Portal, jak i Visual Studio.

W tym przewodniku Szybki start utworzysz tę samą aplikację logiki Visual Studio jako Azure Portal Szybki start. Możesz również dowiedzieć się, jak utworzyć przykładową aplikację w usłudze [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)i tworzyć aplikacje logiki oraz zarządzać nimi za pośrednictwem interfejsu [azure Command-Line Interface (interfejs wiersza polecenia](quickstart-logic-apps-azure-cli.md)platformy Azure). Ta aplikacja logiki monitoruje kanał informacyjny RSS witryny internetowej i wysyła wiadomość e-mail dla każdego nowego elementu w tym kanale informacyjnym. Gotowa aplikacja logiki wygląda następująco:

![Zrzut ekranu przedstawiający przepływ pracy wysokiego poziomu ukończonej aplikacji logiki.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Jeśli masz subskrypcję Azure Government, wykonaj następujące dodatkowe kroki, aby [skonfigurować usługę Visual Studio dla Azure Government Cloud.](#azure-government)

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio 2019, 2017 lub 2015 — Wersja Community lub wyższa.](https://aka.ms/download-visual-studio) W tym przewodniku Szybki start Visual Studio Community 2017 r.

    > [!IMPORTANT]
    > Podczas instalowania programu Visual Studio 2019 lub 2017 upewnij się, że jest wybrane obciążenie **Tworzenie aplikacji na platformie Azure.**

  * [Zestaw Microsoft Azure SDK dla platformy .NET (2.9.1 lub nowszy).](https://azure.microsoft.com/downloads/) Dowiedz się więcej na temat [zestawu Azure SDK dla platformy .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Najnowsze narzędzia Azure Logic Apps dla Visual Studio dla chętnej wersji:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do Internetu podczas korzystania z osadzonego Projektanta aplikacji logiki

  Projektant potrzebuje połączenia internetowego, aby tworzyć zasoby na platformie Azure oraz odczytywać właściwości i dane z łączników w aplikacji logiki.

* Konto e-mail obsługiwane przez usługę Logic Apps, takie jak program Outlook Microsoft 365, Outlook.com lub Gmail. W przypadku innych dostawców przejrzyj listę [łączników tutaj.](/connectors/) W tym przykładzie użyto usługi Office 365 Outlook. Jeśli używasz innego dostawcy, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

  > [!IMPORTANT]
  > Jeśli chcesz używać łącznika usługi Gmail, tylko konta firmowe usługi G Suite mogą używać tego łącznika bez ograniczeń w aplikacjach logiki. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika tylko z określonymi usługami zatwierdzonymi przez Google lub utworzyć aplikację klieniencową Google do uwierzytelniania za pomocą łącznika [usługi Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Aby uzyskać więcej informacji, zobacz [Zasady zabezpieczeń danych i ochrony prywatności dla łączników Google](../connectors/connectors-google-data-security-privacy-policy.md)w Azure Logic Apps .

* Jeśli aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](logic-apps-limits-and-config.md#inbound) do określonych adresów [IP,](logic-apps-limits-and-config.md#outbound) musi zezwalać na dostęp zarówno dla adresów IP dla ruchu przychodzącego, jak i wychodzącego używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp do wszystkich wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md)

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Set up Visual Studio for Azure Government (Konfigurowanie programu Visual Studio na potrzeby usługi Azure Government)

### <a name="visual-studio-2017"></a>Visual Studio 2017

Możesz użyć [selektora środowiska platformy Azure Visual Studio ,](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/)które można pobrać i zainstalować z witryny [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

### <a name="visual-studio-2019"></a>Visual Studio 2019

Aby pracować z Azure Government w usłudze Azure Logic Apps, należy dodać punkt końcowy odnajdywania dla usługi [Azure Government Cloud](../azure-government/documentation-government-connect-vs.md)do usługi Visual Studio . Jednak przed zalogowaniem się do usługi Visual Studio przy użyciu konta *usługi Azure Government* należy zmienić nazwę pliku JSON wygenerowanego po dodaniu punktu końcowego odnajdywania, wykonać następujące kroki:

1. Zamknij program Visual Studio.

1. Znajdź wygenerowany plik JSON o `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` nazwie w tej lokalizacji:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Zmień nazwę pliku JSON na `AadProvider.Configuration.json` .

1. Uruchom ponownie program Visual Studio.

1. Przejdź do kroków, aby zalogować się przy użyciu Azure Government konta.

Aby przywrócić tę konfigurację, usuń plik JSON w następującej lokalizacji i uruchom ponownie Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

Aby rozpocząć, utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Uruchom program Visual Studio. Zaloguj się przy użyciu konta platformy Azure.

1. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**. (Klawiatura: Ctrl + Shift + N)

   ![W menu „File” wybierz pozycję „Nowy” > „Projekt”](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Wybierz **pozycję Grupa zasobów** platformy Azure w  >  **chmurze.** Nazwij projekt, na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Nazwy grup zasobów mogą zawierać tylko litery, cyfry, kropki ( ), podkreślenia ( ), łączniki ( ) i nawiasy (, ), ale nie mogą kończyć się `.` `_` `-` `(` `)` kropkami (  `.` ).
   >
   > Jeśli **chmura** **lub grupa zasobów platformy Azure** nie są wyświetlane, upewnij się, że instalujesz zestaw Azure SDK dla Visual Studio.

   Jeśli używasz programu Visual Studio 2019, wykonaj następujące kroki:

   1. W **polu Create a new project (Tworzenie** nowego projektu) wybierz projekt Grupy zasobów **platformy Azure** dla języka Visual C# lub Visual Basic. Wybierz opcję **Dalej**.

   1. Podaj nazwę grupy zasobów platformy Azure, której chcesz użyć, oraz inne informacje o projekcie. Wybierz przycisk **Utwórz**.

1. Z listy szablonów wybierz szablon **Aplikacja logiki.** Wybierz przycisk **OK**.

   ![Wybieranie szablonu aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Po utworzeniu projektu w programie Visual Studio Eksplorator rozwiązań otwiera i pokazuje rozwiązanie. W rozwiązaniu  plikLogicApp.jsnie tylko przechowuje definicję aplikacji logiki, ale również jest Azure Resource Manager szablonem, który umożliwia wdrożenie.

   ![Eksplorator rozwiązań pokazuje nowe rozwiązanie aplikacji logiki i pliku wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

Jeśli masz projekt grupy zasobów platformy Azure, utwórz aplikację logiki przy użyciu szablonu **Pusta aplikacja logiki.**

1. W Eksplorator rozwiązań otwórzLogicApp.js **w** menu skrótów pliku. Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwieranie pliku JSON aplikacji logiki za pomocą Projektanta aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje dla Visual Studio.

   Visual Studio monituje o subskrypcję platformy Azure i grupę zasobów platformy Azure w celu utworzenia i wdrożenia zasobów dla aplikacji logiki i połączeń.

1. W **przypadku subskrypcji** wybierz swoją subskrypcję platformy Azure. W **przypadku grupy zasobów** wybierz pozycję Utwórz **nową,** aby utworzyć inną grupę zasobów platformy Azure.

   ![Wybieranie subskrypcji platformy Azure, grupy zasobów i lokalizacji zasobu](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Ustawienie | Przykładowa wartość | Opis |
   | ------- | ------------- | ----------- |
   | Konto użytkownika | Fabrikam <br> sophia-owen@fabrikam.com | Konto, które było używane podczas zalogowania się do Visual Studio |
   | **Subskrypcja** | Płatność zgodnie z rzeczywistym użyciem <br> (sophia-owen@fabrikam.com) | Nazwa Twojej subskrypcji platformy Azure i skojarzone konto |
   | **Grupa zasobów** | MyLogicApp-RG <br> (Zachodnie stany USA) | Grupa zasobów platformy Azure i lokalizacja do przechowywania i wdrażania zasobów aplikacji logiki |
   | **Lokalizacja** | **Tak samo jak w przypadku grupy zasobów** | Typ lokalizacji i konkretną lokalizację do wdrożenia aplikacji logiki. Typ lokalizacji to region świadczenia usługi Azure lub istniejące środowisko usługi [integracji (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>W tym przewodniku Szybki start zachowaj typ lokalizacji ustawiony na **Region,** a lokalizację na taką samą jak **grupa zasobów.** <p>**Uwaga:** po utworzeniu projektu grupy zasobów można zmienić typ lokalizacji i lokalizację [,](manage-logic-apps-with-visual-studio.md#change-location)ale inny typ lokalizacji wpływa na aplikację logiki na różne sposoby. |
   ||||

1. Projektant Logic Apps otwiera stronę z wprowadzającym wideo i często używanymi wyzwalaczami. Przewiń w dół po filmie wideo i wybierz pozycję **Szablony,** a następnie wybierz pozycję **Pusta aplikacja logiki.**

   ![Wybieranie pozycji „Pusta aplikacja logiki”](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Kompilowanie przepływu pracy aplikacji logiki

Następnie dodaj wyzwalacz [RSS, który](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest wyzwalany, gdy pojawi się nowy element kanału informacyjnego. Każda aplikacja logiki rozpoczyna się od wyzwalacza, który jest wyzwalany po spełniania określonych kryteriów. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej przepływ pracy.

1. W Projektancie aplikacji logiki w polu wyszukiwania wybierz pozycję **Wszystkie.** W polu wyszukiwania wprowadź "rss". Z listy wyzwalaczy wybierz ten wyzwalacz: **Po opublikowaniu elementu kanału informacyjnego**

   ![Kompilowanie aplikacji logiki przez dodanie wyzwalacza i akcji](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Gdy wyzwalacz pojawi się w projektancie, zakończ tworzenie aplikacji logiki, korzystając z kroków przepływu pracy Azure Portal szybki start, [a](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)następnie wróć do tego artykułu. Gdy wszystko będzie gotowe, aplikacja logiki będzie wyglądać następująco:

   ![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Zapisz swoje Visual Studio rozwiązania. (Klawiatura: Ctrl+S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Wdrażanie aplikacji logiki na platformie Azure

Zanim będzie można uruchomić i przetestować aplikację logiki, wd wdrażaj ją na platformie Azure z Visual Studio.

1. W Eksplorator rozwiązań menu skrótów projektu wybierz pozycję **Wd**  >  **wdrażaj nowy**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

   ![Tworzenie wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. W przypadku tego wdrożenia zachowaj domyślną subskrypcję platformy Azure, grupę zasobów i inne ustawienia. Wybierz pozycję **Wdróż**.

   ![Wdrażanie aplikacji logiki do grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie **wyświetlone pole Edytuj** parametry, podaj nazwę zasobu dla aplikacji logiki. Zapisz ustawienia.

   ![Podawanie nazwy wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure.

   ![Dane wyjściowe stanu wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Jeśli wybrane łączniki wymagają od Ciebie danych wejściowych, w tle zostanie otwarte okno programu PowerShell z monitem o wprowadzenie niezbędnych haseł lub kluczy tajnych. Po wprowadzeniu tych informacji wdrażanie będzie kontynuowane.

   ![Okno programu PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po zakończeniu wdrażania aplikacja logiki będzie żywa w Azure Portal i będzie uruchamiana zgodnie z określonym harmonogramem (co minutę). Jeśli wyzwalacz znajdzie nowe elementy źródła danych, zostanie uruchomiony wyzwalacz, który tworzy wystąpienie przepływu pracy, które uruchamia akcje aplikacji logiki. Aplikacja logiki wysyła wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie, jeśli wyzwalacz nie znajdzie nowych elementów, wyzwalacz nie zostanie uruchomiony i "pominie" jego wystąpienia. Przed sprawdzeniem aplikacja logiki czeka do następnego interwału.

   Oto przykładowe wiadomości e-mail wysyłane przez tę aplikację logiki. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

   ![Program Outlook wysyła wiadomość e-mail dla każdego nowego elementu danych RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulacje, aplikacja logiki została pomyślnie sbudowaną i wdrożoną za pomocą Visual Studio. Aby zarządzać aplikacją logiki i przeglądać historię jej uruchomień, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Dodawanie nowej aplikacji logiki

Jeśli masz istniejący projekt grupy zasobów platformy Azure, możesz dodać do tego projektu nową pustą aplikację logiki przy użyciu okna Konspekt JSON.

1. W Eksplorator rozwiązań otwórz plik `<logic-app-name>.json` .

1. W menu **Widok** wybierz pozycję **Inne konspekt**  >  **JSON systemu** Windows.

1. Aby dodać zasób do pliku szablonu, wybierz pozycję **Dodaj** zasób w górnej części okna Konspekt JSON. Lub w oknie Konspekt  JSON otwórz menu skrótów zasobów i wybierz **pozycję Dodaj nowy zasób**.

   ![Okno Konspekt JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. W **oknie dialogowym** Dodawanie zasobu w polu wyszukiwania znajdź pozycję `logic app` i wybierz pozycję Aplikacja **logiki**. Nadaj nazwę aplikacji logiki, a następnie wybierz **pozycję Dodaj**.

   ![Dodawanie zasobu](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z aplikacją logiki usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) za pomocą konta używanego do tworzenia aplikacji logiki.

1. W menu Azure Portal grupy zasobów lub wyszukaj i wybierz pozycję **Grupy zasobów** na dowolnej stronie. Wybierz grupę zasobów aplikacji logiki.

1. Na stronie **Przegląd** wybierz pozycję **Usuń grupę zasobów.** Wprowadź nazwę grupy zasobów jako potwierdzenie, a następnie wybierz pozycję **Usuń**.

   ![„Grupy zasobów” > „Przegląd” > „Usuń grupę zasobów”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Usuń rozwiązanie programu Visual Studio z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

W tym artykule kompilowano, wdrożono i uruchomiono aplikację logiki przy użyciu programu Visual Studio. Aby dowiedzieć się więcej o zarządzaniu zaawansowanym wdrażaniem aplikacji logiki i wykonywaniu ich za Visual Studio, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
