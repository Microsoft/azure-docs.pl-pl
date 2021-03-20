---
title: Edytowanie aplikacji logiki i zarządzanie nimi przy użyciu programu Visual Studio z programem Cloud Explorer
description: Edytowanie, aktualizowanie, zarządzanie, dodawanie do kontroli źródła i wdrażanie aplikacji logiki przy użyciu programu Visual Studio z programem Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: cde8db5310e3ede2721ba327b28c789ccd0b7dd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87280770"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Zarządzanie aplikacjami logiki w programie Visual Studio

Chociaż można tworzyć, edytować i wdrażać aplikacje logiki w [Azure Portal](https://portal.azure.com), można także użyć programu Visual Studio, jeśli chcesz dodać aplikacje logiki do kontroli źródła, opublikować różne wersje i utworzyć [Azure Resource Manager](../azure-resource-manager/management/overview.md) szablony dla różnych środowisk wdrażania. Za pomocą programu Visual Studio Cloud Explorer można znajdować aplikacje logiki i zarządzać nimi wraz z innymi zasobami platformy Azure. Można na przykład otwierać, pobierać, edytować, uruchamiać, wyświetlać historię uruchamiania, wyłączać i włączać Aplikacje logiki, które są już wdrożone w Azure Portal. Jeśli dopiero zaczynasz pracę z Azure Logic Apps w programie Visual Studio, Dowiedz się, [jak tworzyć aplikacje logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Możesz również [zarządzać aplikacjami logiki w Azure Portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Wdrożenie lub opublikowanie aplikacji logiki z programu Visual Studio zastępuje wersję tej aplikacji w Azure Portal. Dlatego jeśli wprowadzisz zmiany w Azure Portal, które chcesz zachować, upewnij się, że [aplikacja logiki została odświeżona w programie Visual Studio](#refresh) z Azure Portal przed następnym wdrożeniem lub opublikowaniem w programie Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio 2019, 2017 lub 2015 — wersja Community lub nowsza](https://aka.ms/download-visual-studio). Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017 upewnij się, że wybrano obciążenie **Programowanie na platformie Azure** .
    > Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w programie Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    Aby zainstalować program Cloud Explorer dla programu Visual Studio 2015, [Pobierz program Cloud Explorer z Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w programie Visual Studio Cloud Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Zestaw Azure SDK (2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Najnowsze Azure Logic Apps narzędzia dla rozszerzenia programu Visual Studio dla używanej wersji:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

  * Aby skorzystać z subskrypcji Azure Government w programie Visual Studio, zobacz następujące tematy, aby uzyskać dodatkową konfigurację:

    * Visual Studio 2019: [Szybki Start: łączenie się z Azure Government za pomocą programu Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [wprowadzenie do rozszerzenia programu Visual Studio selektora środowiska platformy Azure](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), które można pobrać i zainstalować z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Dostęp do sieci Web przy użyciu osadzonego projektanta Logic Apps

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Znajdowanie aplikacji logiki

W programie Visual Studio można znaleźć wszystkie aplikacje logiki skojarzone z subskrypcją platformy Azure i wdrożone w Azure Portal przy użyciu programu Cloud Explorer.

1. Otwórz program Visual Studio. W menu **Widok** wybierz opcję **Eksplorator chmury**.

1. W programie Cloud Explorer wybierz ikonę **Zarządzanie kontem** . Wybierz subskrypcję platformy Azure skojarzoną z aplikacjami logiki, a następnie wybierz pozycję **Zastosuj**. Na przykład:

   ![Wybierz pozycję "Zarządzanie kontami"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Obok ikony **Zarządzanie kontem** wybierz pozycję **typy zasobów**. W ramach subskrypcji platformy Azure rozwiń węzeł **Logic Apps** , aby wyświetlić wszystkie wdrożone Aplikacje logiki, które są skojarzone z Twoją subskrypcją.

Następnie otwórz aplikację logiki w edytorze aplikacji logiki.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Otwieranie aplikacji logiki w programie Visual Studio

W programie Visual Studio można otwierać aplikacje logiki wcześniej utworzone i wdrożone bezpośrednio za pomocą Azure Portal lub projektów grupy zasobów platformy Azure z programem Visual Studio.

1. [Otwórz program Cloud Explorer i Znajdź aplikację logiki](#find-logic-apps-vs).

1. Z menu skrótów aplikacji logiki wybierz pozycję **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   ![Otwórz wdrożoną aplikację logiki z Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otwarciu aplikacji logiki w programie Logic Apps Designer u dołu projektanta można wybrać **Widok kod** , aby można było przejrzeć podstawową strukturę definicji aplikacji logiki. Jeśli chcesz utworzyć szablon wdrożenia dla aplikacji logiki, Dowiedz się, [jak pobrać szablon Azure Resource Manager](#download-logic-app) dla tej aplikacji logiki. Dowiedz się więcej o [szablonach Menedżer zasobów](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Pobierz z platformy Azure

Aplikacje logiki można [pobrać](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) z [Azure Portal](https://portal.azure.com) i zapisać je jako szablony [Azure Resource Manager](../azure-resource-manager/management/overview.md) . Następnie można edytować szablony lokalnie za pomocą programu Visual Studio i dostosowywać Aplikacje logiki do różnych środowisk wdrażania.  Pobieranie aplikacji logiki automatycznie *parameterizes* ich definicje w [szablonach Menedżer zasobów](../azure-resource-manager/templates/overview.md), które również używają JavaScript Object Notation (JSON).

1. W programie Visual Studio za pomocą programu Cloud Explorer [Otwórz aplikację logiki, którą chcesz pobrać z platformy Azure](#open-designer).

1. Z menu skrótów aplikacji logiki wybierz pozycję **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Aplikacja logiki zostanie otwarta w Projektancie aplikacji logiki.

1. Na pasku narzędzi projektanta wybierz pozycję **Pobierz**.

   ![Pobierz aplikację logiki z Azure Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Po wyświetleniu monitu o lokalizację przejdź do tej lokalizacji i Zapisz szablon Menedżer zasobów dla definicji aplikacji logiki w formacie pliku JSON (JSON).

   Definicja aplikacji logiki zostanie wyświetlona w `resources` podsekcji wewnątrz szablonu Menedżer zasobów. Teraz można edytować definicję aplikacji logiki i szablon Menedżer zasobów za pomocą programu Visual Studio. Możesz również dodać szablon jako [projekt grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do rozwiązania programu Visual Studio. Dowiedz się więcej o [projektach grup zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Link do konta integracji

Aby tworzyć aplikacje logiki dla scenariuszy integracji z przedsiębiorstwem B2B (Business-to-Business), możesz połączyć aplikację logiki z wcześniej utworzonym [kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , które istnieje w tym samym regionie, w którym znajduje się aplikacja logiki. Konto integracji zawiera artefakty B2B, takie jak partnerzy handlowi, umowy, schematy i mapy, a także umożliwia aplikacji logiki używanie łączników B2B do sprawdzania poprawności kodu XML i prostego kodowania plików lub dekodowania. Chociaż można [utworzyć ten link za pomocą Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), można również użyć programu Visual Studio po spełnieniu [wymagań wstępnych](#requirements), a aplikacja LOGIKI istnieje jako plik JSON (JSON) w [projekcie grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Dowiedz się więcej o [projektach grup zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. W programie Visual Studio Otwórz projekt grupy zasobów platformy Azure, który zawiera aplikację logiki.

1. W Eksplorator rozwiązań otwórz menu skrótów **<logiki-App-name C1.js#** , a następnie wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwórz plik JSON aplikacji logiki przy użyciu projektanta aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio i rozszerzenie narzędzi Azure Logic Apps.

1. Upewnij się, że projektant aplikacji logiki ma fokus, wybierając kartę lub powierzchnię projektanta, tak aby okno Właściwości pokazywała Właściwość **konta integracji** dla aplikacji logiki.

   ![Okno Właściwości — Właściwość "konto integracji"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Jeśli okno Właściwości nie jest jeszcze otwarty, w menu **Widok** wybierz polecenie **okno właściwości**. (Klawiatura: naciśnij klawisz F4)

1. Otwórz listę właściwości **konta integracji** i wybierz konto integracji, które chcesz połączyć z aplikacją logiki, na przykład:

   ![Otwórz listę właściwości "konto integracji"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Gdy skończysz, pamiętaj, aby zapisać rozwiązanie programu Visual Studio.

Po ustawieniu właściwości **konto integracji** w programie Visual Studio i zapisaniu aplikacji logiki jako szablonu Azure Resource Manager ten szablon zawiera również deklarację parametru dla wybranego konta integracji. Aby uzyskać więcej informacji na temat parametrów szablonu i usługi Logic Apps, zobacz [Omówienie: Automatyzowanie wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Zmień lokalizację wdrożenia

W programie Visual Studio, jeśli aplikacja logiki istnieje jako plik JSON (JSON) w [projekcie grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) , który jest używany do automatyzowania wdrożenia, ta aplikacja logiki jest ustawiona na typ lokalizacji i określoną lokalizację. Ta lokalizacja jest regionem platformy Azure lub istniejącym [środowiskiem usługi integracji (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Aby zmienić typ lokalizacji lub lokalizację aplikacji logiki, należy otworzyć plik definicji przepływu pracy aplikacji logiki (JSON) z Eksplorator rozwiązań przy użyciu projektanta aplikacji logiki. Nie można zmienić tych właściwości przy użyciu programu Cloud Explorer.

> [!IMPORTANT]
> Zmiana typu lokalizacji z **regionu** na [**środowisko usługi integracji**](connect-virtual-network-vnet-isolated-environment-overview.md) ma wpływ na [model cenowy](logic-apps-pricing.md#fixed-pricing) aplikacji logiki używany na potrzeby rozliczeń, [limitów](logic-apps-limits-and-config.md#integration-account-limits), [obsługi kont integracji](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)i tak dalej. Przed wybraniem innego typu lokalizacji upewnij się, że rozumiesz Wynikowy wpływ na aplikację logiki.

1. W programie Visual Studio Otwórz projekt grupy zasobów platformy Azure, który zawiera aplikację logiki.

1. W Eksplorator rozwiązań Otwórz `<logic-app-name>.json` menu skrótów pliku i wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**. (Klawiatura: Ctrl + L)

   ![Otwórz plik JSON aplikacji logiki przy użyciu projektanta aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio i rozszerzenie narzędzi Azure Logic Apps.

1. Upewnij się, że projektant aplikacji logiki ma fokus, wybierając kartę lub powierzchnię projektanta, aby okno Właściwości pokazywała właściwości **Wybierz typ lokalizacji** i **lokalizację** dla aplikacji logiki. Typ lokalizacji projektu jest ustawiony na **region** lub **środowisko usługi integracji**.

   ![Okno Właściwości — "Wybieranie typu lokalizacji" & właściwości lokalizacji "](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Jeśli okno Właściwości nie jest jeszcze otwarty, w menu **Widok** wybierz polecenie **okno właściwości**. (Klawiatura: naciśnij klawisz F4)

1. Aby zmienić typ lokalizacji, Otwórz listę **Wybierz typ lokalizacji** i wybierz odpowiedni typ lokalizacji.

   Na przykład, jeśli typ lokalizacji to **środowisko usługi integracji**, można wybrać **region**.

   !["Wybieranie typu lokalizacji" właściwości — Zmień typ lokalizacji](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Aby zmienić określoną lokalizację, Otwórz listę właściwości **lokalizacji** . Na podstawie typu lokalizacji wybierz żądaną lokalizację, na przykład:

   * Wybierz inny region świadczenia usługi Azure:

     ![Otwórz listę właściwości "lokalizacja", wybierz inny region platformy Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Wybierz inny ISE:

     ![Otwórz listę właściwości "Location", wybierz inną ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Gdy skończysz, pamiętaj, aby zapisać rozwiązanie programu Visual Studio.

W przypadku zmiany typu lokalizacji lub lokalizacji w programie Visual Studio i zapisania aplikacji logiki jako szablonu Azure Resource Manager ten szablon zawiera również deklaracje parametrów dla tego typu lokalizacji i lokalizacji. Aby uzyskać więcej informacji na temat parametrów szablonu i usługi Logic Apps, zobacz [Omówienie: Automatyzowanie wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Odświeżanie z platformy Azure

Jeśli edytujesz aplikację logiki w Azure Portal i chcesz zachować te zmiany, pamiętaj, aby odświeżyć wersję tej aplikacji w programie Visual Studio przy użyciu tych zmian.

* W programie Visual Studio na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Odśwież**.

  -lub-

* W programie Visual Studio Cloud Explorer otwórz menu skrótów aplikacji logiki i wybierz polecenie **Odśwież**.

![Odśwież aplikację logiki przy użyciu aktualizacji](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publikowanie aktualizacji aplikacji logiki

Gdy wszystko będzie gotowe do wdrożenia aktualizacji aplikacji logiki z programu Visual Studio na platformie Azure, na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Publikuj**.

![Opublikuj zaktualizowaną aplikację logiki do Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Ręczne uruchamianie aplikacji logiki

Możesz ręcznie wyzwolić aplikację logiki wdrożoną na platformie Azure z programu Visual Studio. Na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Uruchom wyzwalacz**.

![Ręczne uruchamianie wyzwalacza dla aplikacji logiki](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Aby sprawdzić stan i zdiagnozować problemy z uruchomieniami aplikacji logiki, można przejrzeć szczegóły, takie jak dane wejściowe i wyjściowe, dla tych przebiegów w programie Visual Studio.

1. W programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Otwórz historię uruchamiania**.

   ![Otwórz historię uruchamiania dla aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Aby wyświetlić szczegóły określonego uruchomienia, kliknij dwukrotnie przebieg. Na przykład:

   ![Wyświetl informacje o określonym przebiegu](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Aby posortować tabelę według właściwości, wybierz nagłówek kolumny dla tej właściwości.

1. Rozwiń kroki, których dane wejściowe i wyjściowe chcesz przejrzeć, na przykład:

   ![Wyświetl dane wejściowe i wyjściowe dla każdego kroku](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Bez usuwania aplikacji logiki można zatrzymać wywoływanie wyzwalacza przy następnym uruchomieniu warunku wyzwalacza. Wyłączenie aplikacji logiki uniemożliwia aparatowi Logic Apps tworzenie i uruchamianie przyszłych wystąpień przepływu pracy dla aplikacji logiki. W programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Wyłącz**.

![Wyłączanie aplikacji logiki w programie Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Po wyłączeniu aplikacji logiki nie są tworzone żadne nowe uruchomienia. Wszystkie w toku i oczekujące przebiegi będą kontynuowane do czasu zakończenia, co może zająć trochę czasu.

Aby ponownie uaktywnić aplikację logiki, w programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Włącz**.

![Włączanie aplikacji logiki w programie Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Usuwanie aplikacji logiki

Aby usunąć aplikację logiki z Azure Portal, w programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Usuń**.

![Usuń aplikację logiki z Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

> [!NOTE]
> Jeśli usuniesz i utworzysz ponownie podrzędną aplikację logiki, musisz ponownie zapisać nadrzędną aplikację logiki. Ponownie utworzona aplikacja podrzędna będzie miała inne metadane.
> Jeśli nie zapiszesz ponownie nadrzędnej aplikacji logiki po ponownym utworzeniu elementu podrzędnego, Twoje wywołania podrzędnej aplikacji logiki zakończą się niepowodzeniem z błędem "Brak autoryzacji". To zachowanie dotyczy aplikacji logiki nadrzędny-podrzędny, na przykład tych, które używają artefaktów na kontach integracji lub wywołują usługi Azure Functions.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po otwarciu projektu aplikacji logiki w projektancie Logic Apps można nie uzyskać opcji wyboru subskrypcji platformy Azure. Zamiast tego aplikacja logiki zostanie otwarta z subskrypcją platformy Azure, która nie jest tą, której chcesz użyć. Takie zachowanie występuje, ponieważ po otwarciu pliku JSON aplikacji logiki program Visual Studio buforuje pierwszą wybraną subskrypcję do użytku w przyszłości. Aby rozwiązać ten problem, spróbuj wykonać jedną z następujących czynności:

* Zmień nazwę pliku JSON aplikacji logiki. Pamięć podręczna subskrypcji zależy od nazwy pliku.

* Aby usunąć wcześniej wybrane subskrypcje dla *wszystkich* aplikacji logiki w rozwiązaniu, Usuń ukryty folder ustawień programu Visual Studio (. vs) w katalogu rozwiązania. Ta lokalizacja przechowuje informacje o subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zarządzania wdrożonymi aplikacjami logiki w programie Visual Studio. Następnie Dowiedz się więcej na temat dostosowywania definicji aplikacji logiki na potrzeby wdrożenia:

> [!div class="nextstepaction"]
> [Tworzenie definicji aplikacji logiki w formacie JSON](../logic-apps/logic-apps-author-definitions.md)
