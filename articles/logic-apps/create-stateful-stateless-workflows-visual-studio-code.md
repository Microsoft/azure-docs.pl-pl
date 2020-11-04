---
title: Tworzenie przepływów pracy automatyzacji (wersja zapoznawcza) w Visual Studio Code
description: Twórz bezstanowe lub stanowe przepływy pracy automatyzacji za pomocą rozszerzenia Azure Logic Apps (wersja zapoznawcza) w Visual Studio Code do integrowania aplikacji, danych, usług w chmurze i systemów lokalnych
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 51fd8b8427dd8214e22fa59e50b26bb9db237946
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322061"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Tworzenie stanowych lub bezstanowych przepływów pracy w programie Visual Studio Code za pomocą rozszerzenia usługi Azure Logic Apps (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby utworzyć przepływy pracy aplikacji logiki, które integrują się między aplikacjami, danymi, usługami w chmurze i systemami, można użyć rozszerzenia Visual Studio Code i Azure Logic Apps (wersja zapoznawcza) do kompilowania i uruchamiania [przepływów pracy aplikacji logiki *stanowych* i *bezstanowych*](#stateful-stateless) w środowisku deweloperskim.

![Zrzut ekranu pokazujący Visual Studio Code i przepływ pracy aplikacji logiki.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Aplikacje logiki tworzone za pomocą rozszerzenia publicznej wersji zapoznawczej używają nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** i są obsługiwane przez środowisko uruchomieniowe [Azure Functions](../azure-functions/functions-overview.md) w środowisku lokalnym. Ten nowy typ zasobu może obejmować wiele przepływów pracy i jest podobny na kilka sposobów do **aplikacja funkcji** typu zasobu, który może zawierać wiele funkcji.

W tym czasie oryginalny typ zasobu **Logic Apps** nadal istnieje do utworzenia i użycia w Visual Studio Code i w Azure Portal. Jednak środowiska dla oryginalnego typu zasobu są oddzielone od nowego typu zasobu. W tej chwili oba typy zasobów **Logic Apps** i **Logic App (wersja zapoznawcza)** mogą znajdować się w tym samym czasie w Visual Studio Code i w Azure Portal. Możesz wyświetlać i uzyskiwać dostęp do wszystkich wdrożonych aplikacji logiki w ramach subskrypcji platformy Azure, ale są one wyświetlane i są przechowywane oddzielnie w swoich własnych kategoriach i sekcjach.

Ten artykuł zawiera [Ogólne omówienie tej publicznej wersji zapoznawczej](#whats-new), opis różnych aspektów typu zasobu **aplikacja logiki (wersja zapoznawcza)** oraz sposób tworzenia tego zasobu przy użyciu Visual Studio Code:

* Jak aplikacje logiki [stanowe i bezstanowe](#stateful-stateless) różnią się od siebie.

* Jak spełnić [wymagania dotyczące instalacji](#prerequisites) i [skonfigurować Visual Studio Code](#set-up) dla rozszerzenia publicznej wersji zapoznawczej.

* Jak utworzyć nowe przepływy pracy **aplikacji logiki (wersja zapoznawcza)** , [tworząc projekt i wybierając szablon przepływu pracy](#create-project).

* Jak lokalnie uruchamiać i debugować nowe aplikacje logiki w Visual Studio Code.

* Jak opublikować te nowe aplikacje logiki bezpośrednio z Visual Studio Code [na platformie Azure](#publish-azure) lub [kontenera Docker](#deploy-docker) , który można uruchomić w dowolnym miejscu. Aby uzyskać więcej informacji na temat platformy Docker, zobacz [co to jest platforma Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Co znajduje się w tej publicznej wersji zapoznawczej?

Rozszerzenie Azure Logic Apps (wersja zapoznawcza) udostępnia wiele bieżących i dodatkowych funkcji Logic Apps do lokalnego środowiska programistycznego w Visual Studio Code, na przykład:

* Twórz aplikacje logiki do obsługi przepływów pracy integracji i automatyzacji z programu [390 i łączników](/connectors/connector-reference/connector-reference-logicapps-connectors) w przypadku aplikacji typu "oprogramowanie jako usługa" (SaaS) i "platforma jako usługa" (PaaS) oraz łączników dla systemów lokalnych.

  * Niektóre zarządzane łączniki, takie jak Azure Service Bus, Azure Event Hubs i SQL Server działają podobnie jak wbudowane wyzwalacze natywne i akcje, takie jak akcja HTTP.

  * Twórz i wdrażaj aplikacje logiki, które można uruchamiać w dowolnym miejscu, ponieważ usługa Azure Logic Apps generuje parametry połączenia sygnatury dostępu współdzielonego, które mogą być używane przez aplikacje logiki do wysyłania żądań do punktu końcowego środowiska uruchomieniowego połączenia w chmurze. Usługa Logic Apps zapisuje te parametry połączenia przy użyciu innych ustawień aplikacji, dzięki czemu można łatwo przechowywać te wartości w Azure Key Vault podczas wdrażania na platformie Azure.

    > [!NOTE]
    > Domyślnie dla zasobu **aplikacji logiki (wersja zapoznawcza)** jest automatycznie włączona [tożsamość zarządzana przypisana przez system](../logic-apps/create-managed-service-identity.md) w celu uwierzytelniania połączeń w czasie wykonywania. Ta tożsamość różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę tożsamość, połączenia nie będą działały w czasie wykonywania.

* Twórz bezstanowe Aplikacje logiki, które są uruchamiane tylko w pamięci, aby kończyły się szybciej, reagować na szybsze, mieć wyższą przepływność i tańsze do uruchomienia, ponieważ w przypadku uruchamiania historii i danych między akcjami nie są zachowywane w magazynie zewnętrznym. Opcjonalnie można włączyć historię uruchamiania, aby ułatwić debugowanie. Aby uzyskać więcej informacji, zobacz [stanowe i bezstanowe Aplikacje logiki](#stateful-stateless).

* Uruchamiaj i Debuguj aplikacje logiki lokalnie w Visual Studio Code środowisku deweloperskim.

* Publikowanie i wdrażanie aplikacji logiki z Visual Studio Code bezpośrednio do różnych środowisk hostingu, takich jak [Azure App Service](../app-service/environment/intro.md) i [kontenerów platformy Docker](/dotnet/core/docker/introduction).

> [!NOTE]
> Aby uzyskać informacje o aktualnych znanych problemach, przejrzyj [stronę z znanymi problemami](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)dotyczącymi rozszerzenia podglądu.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Stanowe i bezstanowe Aplikacje logiki

* *Stanowych*

  Twórz aplikacje stanowe, gdy chcesz zachować, przejrzeć lub odwołać dane z poprzednich zdarzeń. Te aplikacje logiki przechowują dane wejściowe i wyjściowe dla każdej akcji i ich stanów przepływu pracy w magazynie zewnętrznym, co pozwala przeglądać szczegóły przebiegu i historię po zakończeniu każdego przebiegu. Aplikacje logiki stanowej zapewniają wysoką odporność w przypadku wystąpienia awarii. Po przywróceniu usług i systemów można odtworzyć przerwane uruchomienia aplikacji logiki z zapisanego stanu i ponownie uruchamiać aplikacje logiki w celu ukończenia. Stanowe przepływy pracy mogą być nadal wykonywane przez maksymalnie rok.

* *Bezstanowe*

  Twórz bezstanowe Aplikacje logiki, gdy nie musisz zapisywać, przeglądać ani odwoływać danych z poprzednich zdarzeń w magazynie zewnętrznym do późniejszego przeglądu. Te aplikacje logiki przechowują dane wejściowe i wyjściowe dla każdej akcji i Stany przepływu pracy tylko w pamięci, a nie przesyłają tych informacji do magazynu zewnętrznego. W związku z tym bezstanowe Aplikacje logiki mają krótsze uruchomienia, które zwykle nie są dłuższe niż 5 minut, krótszą wydajność dzięki szybszym czasom reakcji, wyższą przepływność i obniżone koszty działania, ponieważ szczegóły i historia przebiegu nie są przechowywane w magazynie zewnętrznym. Jeśli jednak wystąpi awaria, przerwane uruchomienia nie są automatycznie przywracane, więc wywołujący musi ręcznie ponownie przesłać przerwane uruchomienia. Te aplikacje logiki można uruchamiać synchronicznie, a w celu łatwiejszego debugowania można [włączyć historię uruchamiania](#run-history), która ma wpływ na wydajność.

  Bezstanowe przepływy pracy obsługują obecnie tylko *Akcje* dotyczące [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), które są wdrożone na platformie Azure, a nie są wyzwalane. Aby uruchomić przepływ pracy, wybierz [wbudowane żądanie, Event Hubs lub wyzwalacz Service Bus](../connectors/apis-list.md#built-ins), który działa natywnie w środowisku uruchomieniowym Logic Apps. Aby uzyskać więcej informacji na temat nieobsługiwanych wyzwalaczy, akcji i łączników, zobacz [funkcje nieobsługiwane lub niedostępne](#unsupported).

Aby uzyskać informacje o tym, jak zagnieżdżone Aplikacje logiki działają inaczej niż aplikacje logiki stanowych i bezstanowych, zobacz [zagnieżdżone różnice między aplikacjami logiki stanowych i bezstanowych](#nested-behavior).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Model cen

Podczas wdrażania nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** zostanie wyświetlony monit o wybranie planu hostingu, w tym plan [App Service plan lub Premium](../azure-functions/functions-scale.md) , który będzie używany jako model cen. W przypadku wybrania planu App Service zostanie również wyświetlony monit o wybranie [warstwy cenowej](../app-service/overview-hosting-plans.md). W publicznej wersji zapoznawczej uruchamianie aplikacji logiki na App Service nie wiąże się z *dodatkowymi* opłatami na podstawie wybranego planu.

Aplikacje logiki stanowej korzystają z [magazynu zewnętrznego](../azure-functions/functions-scale.md#storage-account-requirements), więc model cen usługi Azure Storage ma zastosowanie do transakcji magazynu wykonywanych przez środowisko uruchomieniowe Azure Logic Apps. Na przykład kolejki są używane do planowania, natomiast tabele i obiekty blob są używane do przechowywania stanów przepływu pracy.

Aby uzyskać więcej informacji na temat modelu cen, które są stosowane do tego nowego typu zasobu, przejrzyj następujące tematy:

* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)
* [Skalowanie w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions szczegóły cennika](https://azure.microsoft.com/pricing/details/functions/)
* [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Szczegóły cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Niedostępne lub nieobsługiwane możliwości

W przypadku tej publicznej wersji zapoznawczej te funkcje nie są dostępne lub nie są obsługiwane:

* Tworzenie nowego zasobu **aplikacji logiki (wersja zapoznawcza)** jest obecnie niedostępne w macOS.

* Nie wszystkie regiony platformy Azure są jeszcze obsługiwane. W przypadku aktualnie dostępnych regionów Sprawdź [listę regionów](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Aby uruchomić przepływ pracy, użyj [wbudowanego żądania, protokołu HTTP, Event Hubs lub wyzwalacza Service Bus](../connectors/apis-list.md), który działa natywnie w środowisku uruchomieniowym środowiska Logic Apps. Obecnie w tej wersji zapoznawczej nie są obsługiwane [Łączniki przedsiębiorstwa](../connectors/apis-list.md#enterprise-connectors), wyzwalacze [lokalnych bram danych](../connectors/apis-list.md#on-premises-connectors), wyzwalacze oparte na elementach webhook, wyzwalacz okna przesuwania, [Łączniki niestandardowe](../connectors/apis-list.md#custom-apis-and-connectors), konta integracji, ich artefakty i [Łączniki](../connectors/apis-list.md#integration-account-connectors) . Funkcja "wywołaj funkcję platformy Azure" jest niedostępna, dlatego użyj *akcji* http, aby wywołać adres URL żądania dla funkcji platformy Azure.

  Poza określonymi wcześniej wyzwalaczami przepływy pracy *stanowych* mogą używać wyzwalaczy i akcji dla [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), które są wdrażane na platformie Azure, a wbudowane wyzwalacze i akcje, które działają natywnie w środowisku uruchomieniowym Logic Apps. Jednak *bezstanowe* przepływy pracy obsługują obecnie tylko *Akcje* dla łączników zarządzanych, a nie wyzwalacze. Chociaż łączniki na platformie Azure można włączyć dla bezstanowego przepływu pracy, Projektant nie wyświetla żadnych wyzwalaczy łączników zarządzanych, które można wybrać.

* Nowy typ zasobu **aplikacji logiki (wersja zapoznawcza)** można wdrożyć tylko [w planie usług w warstwie Premium lub App Service na platformie Azure](#publish-azure) lub w [kontenerze platformy Docker](#deploy-docker), a nie w [środowiskach usługi integracji (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Plany hostingu **zużycia** nie są obsługiwane ani nie są dostępne do wdrożenia tego typu zasobu.

* W Azure Portal nie można tworzyć nowych aplikacji logiki przy użyciu nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** . Te aplikacje logiki można tworzyć tylko w Visual Studio Code. Jednak po wdrożeniu aplikacji logiki przy użyciu tego typu zasobu z Visual Studio Code na platformie Azure można [dodać do nich Nowe przepływy pracy](#add-workflows).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="access-and-connectivity"></a>Dostęp i łączność

* Dostęp do Internetu w taki sposób, aby można było pobrać wymagania, nawiązać połączenie z Visual Studio Code na konto platformy Azure i publikować z Visual Studio Code na platformie Azure, kontenera Docker lub innego środowiska.

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Aby móc utworzyć tę samą przykładową aplikację logiki w tym artykule, potrzebujesz konta e-mail programu Outlook w usłudze Office 365, które używa konta służbowego firmy Microsoft do logowania.

  Jeśli zdecydujesz się użyć innego [łącznika poczty e-mail, który jest obsługiwany przez Azure Logic Apps](/connectors/), na przykład Outlook.com lub [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), możesz nadal postępować zgodnie z przykładem, a ogólna ogólna procedura jest taka sama, ale interfejs użytkownika i opcje mogą się różnić. Jeśli na przykład używasz łącznika Outlook.com, zamiast tego Zaloguj się przy użyciu osobistego konto Microsoft.

### <a name="storage-requirements"></a>Wymagania dotyczące magazynu

1. Pobierz i zainstaluj [emulator usługi Azure Storage 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Aby uruchomić emulator, musisz mieć lokalną instalację bazy danych SQL, na przykład bezpłatną [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Przed otwarciem projektanta aplikacji logiki w celu utworzenia przepływu pracy aplikacji logiki upewnij się, że uruchomiono emulator. W przeciwnym razie zostanie wyświetlony komunikat `Workflow design time could not be started` .
   >
   > ![Zrzut ekranu pokazujący emulator usługi Azure Storage z systemem.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>narzędzia

* [Visual Studio Code 1.30.1 (styczeń 2019) lub nowszy](https://code.visualstudio.com/), który jest bezpłatny. Ponadto Pobierz i zainstaluj te dodatkowe narzędzia dla Visual Studio Code, jeśli nie są jeszcze:

  * [Rozszerzenie konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), które zapewnia jedno wspólne środowisko rejestrowania w usłudze Azure i obsługę filtrowania subskrypcji dla wszystkich innych rozszerzeń platformy azure w Visual Studio Code.

  * Język [C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), który umożliwia uruchamianie aplikacji logiki w funkcji F5.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), w wersji [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) lub [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), za pomocą Instalatora Microsoft (msi). Narzędzia te zawierają wersję tego samego środowiska uruchomieniowego, która umożliwia Azure Functions środowisko uruchomieniowe działające w programie Visual Studio Code.

    > [!IMPORTANT]
    > Jeśli masz instalację wcześniejszą niż te wersje, najpierw Odinstaluj tę wersję lub upewnij się, że zmienna środowiskowa PATH wskazuje na wersję pobieraną i zainstalowaną.
    >
    > Jeśli chcesz użyć [akcji **kodu wbudowanego**](../logic-apps/logic-apps-add-run-inline-code.md) do uruchamiania kodu JavaScript, musisz użyć środowiska uruchomieniowego Azure Functions w wersji 3, ponieważ akcja nie obsługuje wersji 2. Ponadto ta akcja nie jest obecnie obsługiwana w systemach operacyjnych Linux.

  * [Rozszerzenie Azure Logic Apps (wersja zapoznawcza) Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). To rozszerzenie publicznej wersji zapoznawczej zapewnia możliwość tworzenia stanowych i bezstanowych aplikacji logiki oraz uruchamiania ich lokalnie w Visual Studio Code.

    Obecnie można mieć zainstalowane zarówno oryginalne rozszerzenie **Azure Logic Apps** , jak i nowe rozszerzenie **Azure Logic Apps (wersja zapoznawcza)** w Visual Studio Code. Po wybraniu ikony platformy Azure na pasku narzędzi Visual Studio Code można wyświetlić wszystkie aplikacje logiki wdrożone na platformie Azure, ale każdy typ zasobu pojawia się we własnych sekcjach rozszerzenia, **Logic Apps** i **Azure Logic Apps (wersja zapoznawcza)**.

    > [!IMPORTANT]
    > Jeśli aplikacje logiki zostały utworzone przy użyciu rozszerzenia **Azure Logic Apps (Private Preview)** , te aplikacje logiki nie będą działały z rozszerzeniem publicznej wersji zapoznawczej. Można jednak migrować te aplikacje logiki, odinstalując rozszerzenie prywatnej wersji zapoznawczej, wykonując wymagane czyszczenie i instalując rozszerzenie publicznej wersji zapoznawczej. Następnie możesz utworzyć nowy projekt w Visual Studio Code i skopiować utworzony wcześniej plik **Workflow. Definition** aplikacji logiki do nowego projektu.
    >
    > Dlatego przed zainstalowaniem rozszerzenia publicznej wersji zapoznawczej upewnij się, że wszystkie wcześniejsze wersje zostały odinstalowane, i Usuń następujące artefakty:
    >
    > * Folder **Microsoft. Azure. Functions. ExtensionBundle.** workflows, który zawiera poprzednie zbiory rozszerzeń i znajduje się w ścieżce w tym miejscu:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * Folder **Microsoft. Azure. Workflows. WebJobs. Extension** , który jest pamięcią podręczną [NuGet](/nuget/what-is-nuget) dla rozszerzenia prywatnej wersji zapoznawczej i znajduje się w tej ścieżce:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Aby zainstalować rozszerzenie **Azure Logic Apps (wersja zapoznawcza)** , wykonaj następujące kroki:

    1. W Visual Studio Code na pasku narzędzi po lewej stronie wybierz pozycję **rozszerzenia**.

    1. W polu wyszukiwania rozszerzeń wprowadź `azure logic apps preview` . Z listy wyników wybierz pozycję Zainstaluj **Azure Logic Apps (wersja zapoznawcza)** **>** **Install**.

       Po zakończeniu instalacji, rozszerzenie publicznej wersji zapoznawczej zostanie wyświetlone na liście **rozszerzenia: zainstalowane** .

       ![Zrzut ekranu przedstawiający listę rozszerzeń zainstalowanych Visual Studio Code z podkreśleniem rozszerzenia "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Aby przetestować przykładową aplikację logiki utworzoną w tym artykule, potrzebne jest narzędzie, które może wysyłać wywołania do wyzwalacza żądania, który jest pierwszym krokiem w przykładowej aplikacji logiki. Jeśli nie masz takiego narzędzia, możesz pobrać, zainstalować i użyć programu [Poster](https://www.postman.com/downloads/).

* Aby ułatwić rejestrowanie i śledzenie diagnostyki, można dodać zasób [Application Insights](../azure-monitor/app/app-insights-overview.md) i użyć go. Ten zasób można utworzyć podczas wdrażania aplikacji logiki lub w Azure Portal po wdrożeniu aplikacji logiki.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

1. Aby upewnić się, że wszystkie rozszerzenia są prawidłowo zainstalowane, Załaduj ponownie lub Uruchom ponownie Visual Studio Code.

1. Włącz lub sprawdź, czy Visual Studio Code automatycznie wyszukuje i instaluje aktualizacje rozszerzeń, aby Twoje rozszerzenie publicznej wersji zapoznawczej pobiera najnowsze aktualizacje.

   Aby sprawdzić to ustawienie, wykonaj następujące kroki:

   1. W menu **plik** przejdź do pozycji **Preferencje** **>** **Ustawienia**.

   1. Na karcie **użytkownik** przejdź do pozycji **funkcje** **>** **rozszerzenia**.

   1. Upewnij się, że zaznaczone są **aktualizacje AutoCheck** i **Autoaktualizacja** .

1. Włącz lub sprawdź, czy te ustawienia rozszerzenia **Azure Logic Apps (wersja zapoznawcza)** zostały skonfigurowane w Visual Studio Code:

   * **Azure Logic Apps v2: Tryb panelu**
   * **Azure Logic Apps v2: środowisko uruchomieniowe projektu**

   1. W menu **plik** przejdź do pozycji **Preferencje** **>** **Ustawienia**.

   1. Na karcie **użytkownik** przejdź do pozycji **>** **rozszerzenia** **>** **Azure Logic Apps (wersja zapoznawcza)**.

   1. W obszarze **Azure Logic Apps v2: Tryb panelu** upewnij się, że wybrano **tryb włączania panelu** . W obszarze **Azure Logic Apps v2: środowisko uruchomieniowe projektu** Ustaw wersję na **~ 3** lub **~ 2** na podstawie zainstalowanej wcześniej [wersji Azure Functions Core Tools](#prerequisites) .

      > [!IMPORTANT]
      > Jeśli chcesz użyć [akcji **kodu wbudowanego**](../logic-apps/logic-apps-add-run-inline-code.md) do uruchamiania kodu JavaScript, upewnij się, że używasz środowiska uruchomieniowego projektu w wersji 3, ponieważ akcja nie obsługuje wersji 2. Ponadto ta akcja nie jest obecnie obsługiwana w systemach operacyjnych Linux.

      ![Zrzut ekranu pokazujący ustawienia Visual Studio Code dla rozszerzenia "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

1. Na pasku narzędzi Visual Studio Code wybierz ikonę platformy Azure.

   ![Zrzut ekranu, który pokazuje Visual Studio Code pasek narzędzi i wybraną ikonę platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. W okienku Azure w obszarze **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Zaloguj się do platformy Azure**. Gdy zostanie wyświetlona strona uwierzytelnianie Visual Studio Code, zaloguj się przy użyciu konta platformy Azure.

   ![Zrzut ekranu przedstawiający okienko platformy Azure i wybrany link do logowania do platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Po zalogowaniu się w okienku Azure wyświetlane są subskrypcje na koncie platformy Azure. Jeśli masz publicznie wydane rozszerzenie Logic Apps, możesz znaleźć wszystkie oryginalne zasoby Logic Apps, które zostały utworzone przy użyciu oryginalnego rozszerzenia w sekcji **Logic Apps** wydanego rozszerzenia, a nie sekcję **Logic Apps (wersja zapoznawcza)** rozszerzenia podglądu.
   
   Jeśli oczekiwane subskrypcje nie są wyświetlane lub chcesz, aby okienko pokazywało tylko określone subskrypcje, wykonaj następujące kroki:

   1. Na liście subskrypcje Przenieś wskaźnik obok pierwszej subskrypcji, aż zostanie wyświetlony przycisk **wybierz subskrypcje** (ikona filtru). Wybierz ikonę filtru.

      ![Zrzut ekranu pokazujący okienko platformy Azure i wybraną ikonę filtru.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Lub na pasku stanu Visual Studio Code wybierz swoje konto platformy Azure. 

   1. Gdy zostanie wyświetlona inna Lista subskrypcji, wybierz odpowiednie subskrypcje, a następnie upewnij się, że wybrano **przycisk OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Utwórz projekt lokalny

Przed utworzeniem aplikacji logiki Utwórz projekt lokalny, aby móc zarządzać i wdrażać aplikację logiki z poziomu Visual Studio Code. Projekt źródłowy jest podobny do projektu Azure Functions, zwanego także projektem aplikacji funkcji. Jednak te typy projektów są niezależne od siebie, więc przepływy pracy i funkcje logiki aplikacji nie mogą istnieć w tym samym projekcie.

1. Na komputerze Utwórz *pusty* folder lokalny do użycia w projekcie, który zostanie później utworzony w Visual Studio Code.

1. W Visual Studio Code Zamknij wszystkie otwarte foldery i wszystkie.

1. W okienku platformy Azure obok pozycji **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Utwórz nowy projekt** (ikona, która pokazuje folder i Piorun).

   ![Zrzut ekranu pokazujący pasek narzędzi okienka platformy Azure z wybranym elementem "Utwórz nowy projekt".](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Jeśli Zapora Windows Defender poprosi o przyznanie dostępu do sieci `Code.exe` , który jest Visual Studio Code, a w przypadku `func.exe` , czyli Azure Functions Core Tools, wybierz opcję **sieci prywatne, na przykład sieć domowa lub służbowa** **>** **zezwala na dostęp**.

1. Przejdź do lokalizacji, w której został utworzony folder projektu, wybierz ten folder i Kontynuuj.

   ![Zrzut ekranu pokazujący okno dialogowe Wybieranie folderu z nowo utworzonym folderem projektu i wybranym przyciskiem "Select".](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Z wyświetlonej listy szablonów wybierz opcję **stanowy przepływ pracy** lub **bezstanowy przepływ pracy**. Ten przykład wybiera **stanowy przepływ pracy**.

   ![Zrzut ekranu przedstawiający listę szablonów przepływu pracy z wybranym "stanem" przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Podaj nazwę dla przepływu pracy aplikacji logiki, a następnie naciśnij klawisz ENTER. Ten przykład używa `example-workflow` jako nazwy.

   ![Zrzut ekranu pokazujący "Tworzenie nowego stanu przepływu pracy (3/4)" i "przykładowy przepływ pracy" jako nazwę przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Z następnej wyświetlonej listy wybierz pozycję **Otwórz w bieżącym oknie**.

   ![Zrzut ekranu pokazujący listę z wybranym elementem "Otwórz w bieżącym oknie".](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code ponowne ładowanie, otwarcie okienka Eksploratora i wyświetlenie projektu, który zawiera teraz automatycznie generowane pliki projektu. Na przykład projekt zawiera folder, który zawiera nazwę przepływu pracy aplikacji logiki. W tym folderze **workflow.jsw** pliku zawiera podstawową definicję JSON przepływu pracy aplikacji logiki.

   ![Zrzut ekranu pokazujący okno Eksploratora z folderem projektu, folderem przepływu pracy i plikiem "workflow.json".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Otwórz plik definicji przepływu pracy w Projektancie aplikacji logiki

1. Sprawdź wersje zainstalowane na komputerze, uruchamiając następujące polecenie:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Jeśli masz zestaw .NET Core SDK 5. x, ta wersja może uniemożliwić otwieranie podstawowej definicji przepływu pracy aplikacji logiki w projektancie. Zamiast odinstalowywać tę wersję, w lokalizacji głównej projektu Utwórz **global.jsw** pliku, który odwołuje się do wersji środowiska uruchomieniowego .NET Core Runtime 3. x, która jest nowsza niż 3.1.201, na przykład:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Upewnij się, że jawnie dodawaj **global.jsw** pliku do projektu w lokalizacji głównej z poziomu Visual Studio Code. W przeciwnym razie Projektant nie zostanie otwarty.

1. Jeśli Visual Studio Code jest uruchomiony w systemie Windows lub Linux, upewnij się, że emulator usługi Azure Storage jest uruchomiony. Aby uzyskać więcej informacji, zapoznaj się z [wymaganiami wstępnymi](#prerequisites).

1. Rozwiń folder projektu dla przepływu pracy. Otwórz menu skrótów pliku **workflow.js** i wybierz polecenie **Otwórz w projektancie**.

   ![Zrzut ekranu pokazujący okienko Eksploratora i okno skrótów dla workflow.jsw pliku z wybranym elementem "Otwórz w projektancie".](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Jeśli zostanie wyświetlony komunikat o błędzie `Workflow design time could not be started` , upewnij się, że emulator usługi Azure Storage jest uruchomiony. W przeciwnym razie wypróbuj inne sugestie dotyczące rozwiązywania problemów:

   W Visual Studio Code Sprawdź dane wyjściowe rozszerzenia podglądu.

   1. Z menu **Widok** wybierz pozycję **dane wyjściowe**.

   1. Z listy na pasku tytułu **danych wyjściowych** wybierz pozycję **Azure Logic Apps** , aby można było wyświetlić dane wyjściowe dla rozszerzenia podglądu, na przykład:

      ![Zrzut ekranu pokazujący okno danych wyjściowych Visual Studio Code z wybranym "Azure Logic Apps".](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Przejrzyj dane wyjściowe i sprawdź, czy ten komunikat o błędzie jest wyświetlany:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Ten błąd może wystąpić, jeśli wcześniej podjęto próbę otwarcia projektanta, a następnie wycofanie lub usunięcie projektu. Aby rozwiązać ten problem, Usuń folder **ExtensionBundles** w tej lokalizacji **. ..\Users \\ {Nazwa użytkownika} \AppData\Local\Temp\Functions\ExtensionBundles** , a następnie ponów próbę otwarcia **workflow.jsw** pliku w projektancie.

1. Na liście **Włącz łączniki na platformie Azure** wybierz pozycję **Użyj łączników z platformy Azure** , która ma zastosowanie do wszystkich łączników zarządzanych dostępnych i wdrożonych na platformie Azure, a nie tylko łączników dla usług platformy Azure.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z wybraną listą "Włącz łączniki na platformie Azure" i "użyj łączników z platformy Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Bezstanowe przepływy pracy obsługują obecnie tylko *Akcje* dotyczące [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), które są wdrożone na platformie Azure, a nie są wyzwalane. Chociaż istnieje możliwość włączenia łączników na platformie Azure dla bezstanowego przepływu pracy, Projektant nie wyświetla żadnych wyzwalaczy łączników zarządzanych, które można wybrać.

1. Z listy grupy zasobów wybierz pozycję **Utwórz nową grupę zasobów**.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z listą grupy zasobów i wybraną opcją "Utwórz nową grupę zasobów"](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Podaj nazwę grupy zasobów, a następnie naciśnij klawisz ENTER. Ten przykład używa `example-logic-app-preview-rg` .

   ![Zrzut ekranu przedstawiający okienko Eksploratora i pole nazwy grupy zasobów.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Z listy lokalizacje Znajdź i wybierz [obsługiwany region platformy Azure](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) , który ma być używany do tworzenia grupy zasobów i zasobów. W tym przykładzie używane jest **zachodnie środkowe stany USA**.

   > [!IMPORTANT]
   > Nie wszystkie regiony są obecnie obsługiwane, ale trwają aktualizacje dodawania większej liczby regionów. Wybranie nieobsługiwanego regionu może spowodować problemy, takie jak tworzenie połączeń. W przypadku aktualnie obsługiwanych regionów zapoznaj się ze stroną usługi [GitHub znane problemy](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)z rozszerzeniem podglądu.

   ![Zrzut ekranu pokazujący okienko Eksploratora z listą lokalizacji i wybraną pozycją "zachodnio-środkowe stany USA".](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Po wykonaniu tego kroku Visual Studio Code otwiera projektanta aplikacji logiki.

   > [!NOTE]
   > Gdy Visual Studio Code uruchamia interfejs API czasu projektowania przepływu pracy, pojawi się komunikat, że uruchomienie może potrwać kilka sekund. Możesz zignorować ten komunikat lub wybrać **przycisk OK**.

   Po wyświetleniu projektanta aplikacji logiki zostanie wyświetlony monit **Wybierz operację** w Projektancie i jest domyślnie zaznaczony, który pokazuje okienko **Dodawanie akcji** .

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Następnie [Dodaj wyzwalacz i akcje](#add-trigger-actions) do przepływu pracy.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Dodawanie wyzwalacza i akcji

Po otwarciu projektanta aplikacji logiki z **workflow.jsw** menu skrótów do pliku, w projektancie zostanie wyświetlony monit **Wybieranie operacji** , który jest domyślnie wybrany. Teraz możesz zacząć tworzyć przepływy pracy, dodając wyzwalacz i akcje.

W tym przykładzie przepływ pracy aplikacji logiki używa tego wyzwalacza i następujących akcji:

* Wbudowany [wyzwalacz żądania](../connectors/connectors-native-reqres.md), **gdy odebrane zostanie żądanie HTTP** , które odbiera wywołania przychodzące lub żądania i tworzy punkt końcowy, do którego mogą być wywoływane inne usługi lub Aplikacje logiki.

* [Akcja programu Outlook pakietu Office 365](../connectors/connectors-create-api-office365-outlook.md), **Wyślij wiadomość e-mail**.

* Wbudowana [Akcja odpowiedzi](../connectors/connectors-native-reqres.md)służąca do wysyłania odpowiedzi i powrotu danych z powrotem do obiektu wywołującego.

### <a name="add-the-request-trigger"></a>Dodawanie wyzwalacza żądania

1. Obok pola projektant w okienku **Dodawanie wyzwalacza** , w obszarze **Wybierz operację** wyszukiwania, upewnij się, że jest zaznaczona opcja **wbudowane** , aby można było wybrać wyzwalacz, który działa natywnie.

1. W polu wyszukiwania **Wybierz operację** wprowadź `when a http request` , a następnie wybierz wbudowany wyzwalacz żądania o nazwie, **gdy odebrane zostanie żądanie HTTP**.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki i * * Dodaj okienko wyzwalacz * * z wybranym wyzwalaczem "Kiedy Odebrano żądanie HTTP".](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Gdy wyzwalacz pojawi się w projektancie, zostanie otwarte okienko Szczegóły wyzwalacza, aby wyświetlić właściwości wyzwalacza, ustawienia i inne akcje.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki z wybranym wyzwalaczem "po odebraniu żądania HTTP" i otwartym okienku szczegółów wyzwalacza.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Jeśli okienko szczegółów nie jest wyświetlane, upewnij się, że wyzwalacz został wybrany w projektancie.

1. Jeśli musisz usunąć element z projektanta, wykonaj następujące kroki:

   1. W Projektancie wybierz element, który powoduje otwarcie okienka szczegółów elementu po prawej stronie.

   1. Rozwiń okno Visual Studio Code wystarczająco często, aby obok wyzwalacza lub nazwy akcji w prawym górnym rogu pojawił się przycisk wielokropka ( **...** ). 

   1. Otwórz menu wielokropka ( **...** ), a następnie wybierz pozycję **Usuń**. Aby potwierdzić usunięcie, wybierz **przycisk OK**.

      ![Zrzut ekranu pokazujący wybrany element w Projektancie z otwartym okienkiem szczegółów i z wybranym przyciskiem wielokropka i opcją "Usuń".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Dodawanie akcji programu Outlook pakietu Office 365

1. W projektancie w obszarze dodany wyzwalacz wybierz pozycję **nowy krok**.

   Zostanie wyświetlony monit **Wybierz operację** w projektancie, a **okienko Dodawanie akcji** zostanie otwarte ponownie, aby można było wybrać następną akcję.

1. W okienku **Dodawanie akcji** w polu wyszukiwania **Wybierz operację** wybierz pozycję **Azure** , aby można było znaleźć i wybrać akcję dla łącznika zarządzanego wdrożonego na platformie Azure.

   Ten przykład wybiera i używa akcji programu Outlook pakietu Office 365, **Wyślij wiadomość e-mail (wersja 2)**.

   ![Zrzut ekranu, który pokazuje projektanta aplikacji logiki i * * Dodaj akcję * * okienko z pakietem Office 365 Outlook "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. W okienku szczegółów akcji wybierz pozycję **Zaloguj się** , aby można było utworzyć połączenie z kontem e-mail.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki i * * Wyślij wiadomość e-mail (v2) * * z wybraną opcją "Zaloguj się".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Jeśli wystąpi błąd, `Failed to create connection...` możesz wybrać aktualnie nieobsługiwany region dla aplikacji logiki. Trwa aktualizacja dodawania większej liczby regionów. W tym przypadku w przypadku aktualnie obsługiwanych regionów Sprawdź [stronę znane problemy dotyczące](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)rozszerzenia podglądu w witrynie GitHub.

1. Gdy Visual Studio Code zostanie wyświetlony komunikat z prośbą o zgodę na dostęp do Twojego konta e-mail, wybierz pozycję **Otwórz**.

   ![Zrzut ekranu pokazujący monit Visual Studio Code, aby zezwolić na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Aby zapobiec wyświetlaniu w przyszłości, wybierz opcję **Konfiguruj zaufane domeny** , aby można było dodać stronę uwierzytelniania jako domenę zaufaną.

1. Postępuj zgodnie z kolejnymi monitami, aby się zalogować, Zezwól na dostęp i Zezwalaj na powrót do Visual Studio Code.

   > [!NOTE]
   > Jeśli zbyt dużo czasu upłynie przed ukończeniem wyświetlania, przekroczenie limitu czasu procesu uwierzytelniania i zakończy się niepowodzeniem. W takim przypadku Wróć do projektanta i ponów próbę zalogowania się, aby utworzyć połączenie.

1. Gdy rozszerzenie Azure Logic Apps Preview wyświetli prośbę o zgodę na dostęp do Twojego konta e-mail, wybierz pozycję **Otwórz**. Postępuj zgodnie z kolejnym monitem, aby zezwolić na dostęp.

   ![Zrzut ekranu pokazujący monit o rozszerzenie podglądu, aby zezwolić na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Aby zapobiec wyświetlaniu w przyszłości, wybierz opcję **nie Monituj ponownie dla tego rozszerzenia**.

   Po Visual Studio Code utworzeniu połączenia niektóre łączniki pokazują komunikat informujący o `The connection will be valid for {n} days only.` tym, że ten limit czasu dotyczy tylko czasu trwania podczas tworzenia aplikacji logiki w programie Visual Studio Code. Po wdrożeniu ten limit nie obowiązuje, ponieważ aplikacja logiki może uwierzytelnić się w czasie wykonywania przy użyciu automatycznie włączonej [tożsamości zarządzanej przypisanej przez system](../logic-apps/create-managed-service-identity.md). Ta tożsamość zarządzana różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę tożsamość zarządzaną przypisaną przez system, połączenia nie będą działały w czasie wykonywania.

1. W projektancie, jeśli Akcja **Wyślij wiadomość e-mail** nie jest zaznaczona, wybierz tę akcję.

1. W okienku Szczegóły akcji na karcie **Parametry** podaj wymagane informacje dotyczące akcji, na przykład:

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki ze szczegółowymi informacjami dotyczącymi pakietu Office 365 Outlook "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*adres e-mail użytkownika*> | Odbiorca wiadomości e-mail, który może być Twoim adresem e-mail do celów testowych. Ten przykład używa fikcyjnej poczty e-mail `sophiaowen@fabrikam.com` . |
   | **Temat** | Tak | `An email from your example workflow` | Temat wiadomości e-mail |
   | **Treść** | Tak | `Hello from your example workflow!` | Zawartość wiadomości e-mail |
   ||||

   > [!NOTE]
   > Jeśli chcesz wprowadzić zmiany w okienku szczegółów na karcie **Ustawienia** , **Uruchom po** lub **statyczny wynik** , upewnij się, że wybrano pozycję **gotowe** , aby zatwierdzić te zmiany przed przełączeniem kart lub zmianę fokusu projektanta. W przeciwnym razie Visual Studio Code nie będą zachować zmian. Aby uzyskać więcej informacji, zapoznaj się ze [stroną usługi GitHub znane problemy](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)z rozszerzeniem podglądu.

1. W Projektancie wybierz pozycję **Zapisz**.

Następnie uruchom i Debuguj przepływ pracy lokalnie w Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Uruchamianie i debugowanie lokalnie

Aby przetestować aplikację logiki, wykonaj następujące kroki, aby rozpocząć sesję debugowania i znaleźć adres URL dla punktu końcowego, który został utworzony przez wyzwalacz żądania. Ten adres URL będzie potrzebny do późniejszego wysłania żądania do tego punktu końcowego.

1. W celu łatwiejszego debugowania przepływu pracy aplikacji logiki bezstanowej można [włączyć historię uruchamiania dla tego przepływu pracy](#run-history).

1. Na pasku narzędzi Visual Studio Code Otwórz menu **Uruchom** , a następnie wybierz polecenie **Rozpocznij debugowanie** (F5).

   Zostanie otwarte okno **terminalu** umożliwiające przejrzenie sesji debugowania.

1. Teraz Znajdź adres URL wywołania zwrotnego dla punktu końcowego w wyzwalaczu żądania.

   1. Ponownie otwórz okienko Eksploratora, aby można było wyświetlić projekt.

   1. Z menu skrótów **workflow.jsw** pliku wybierz pozycję **Przegląd**.

      ![Zrzut ekranu pokazujący okienko Eksploratora i okno skrótów dla workflow.jsw pliku z wybranym "przeglądem".](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Znajdź wartość **adresu URL wywołania zwrotnego** , która będzie wyglądać podobnie do tego adresu URL dla przykładowego wyzwalacza żądania:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Zrzut ekranu przedstawiający stronę omówienia przepływu pracy z adresem URL wywołania zwrotnego](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Aby przetestować adres URL wywołania zwrotnego, wyzwalając przepływ pracy aplikacji logiki, Otwórz program [Poster](https://www.postman.com/downloads/) lub preferowane narzędzie do tworzenia i wysyłania żądań.

   Ten przykład jest kontynuowany przy użyciu programu Poster. Aby uzyskać więcej informacji, zobacz temat [poster wprowadzenie](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na pasku narzędzi programu Poster wybierz pozycję **Nowy**.

      ![Zrzut ekranu pokazujący, że wybrano nowy przycisk.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. W okienku **Utwórz nowe** w obszarze **bloki konstrukcyjne** wybierz pozycję **żądanie**.

   1. W oknie **Zapisywanie żądania** w obszarze **Nazwa żądania** Podaj nazwę żądania, na przykład `Test workflow trigger` .

   1. W obszarze **Wybierz kolekcję lub folder, w którym chcesz zapisać** , wybierz pozycję **Utwórz kolekcję**.

   1. W obszarze **wszystkie kolekcje** Podaj nazwę kolekcji, która ma zostać utworzona do organizowania żądań, naciśnij klawisz ENTER, a następnie wybierz pozycję **zapisz, aby < *nazwę* > kolekcji**. Ten przykład używa `Logic Apps requests` jako nazwy kolekcji.

      Zostanie otwarte okienko żądania programu post, dzięki któremu można wysłać żądanie do adresu URL wywołania zwrotnego dla wyzwalacza żądania.

      ![Zrzut ekranu przedstawiający program ogłaszający przy otwartym okienku żądania](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Wróć do Visual Studio Code. na stronie Przegląd przepływu pracy skopiuj wartość właściwości **adres URL wywołania zwrotnego** .

   1. Wróć do programu Poster. W okienku żądanie kliknij następną listę metod, która obecnie **jest wyświetlana jako** domyślna metoda żądania, wklej adres URL wywołania zwrotnego, który został wcześniej skopiowany w polu adres, a następnie wybierz pozycję **Wyślij**.

      ![Zrzut ekranu przedstawiający adres URL wpisu i zwrotny w polu adres z wybranym przyciskiem Wyślij](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Przykładowy przepływ pracy aplikacji logiki wysyła wiadomość e-mail, która wygląda podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający wiadomości e-mail w programie Outlook zgodnie z opisem w przykładzie](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. W Visual Studio Code Wróć do strony przegląd przepływu pracy.

   Jeśli utworzono stanowy przepływ pracy, po wysłaniu żądania wyzwala przepływ pracy, na stronie Przegląd zostanie wyświetlony stan przebiegu przepływu pracy i jego historia.

   > [!TIP]
   > Jeśli stan uruchomienia nie zostanie wyświetlony, spróbuj odświeżyć stronę przeglądu, wybierając pozycję **Odśwież**. Nie ma żadnego przebiegu dla wyzwalacza, który został pominięty z powodu niewypełnienia kryteriów lub wyszukiwania brakujących danych.

   ![Zrzut ekranu przedstawiający stronę przeglądu przepływu pracy z stanem uruchamiania i historią](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Stan uruchomienia | Opis |
   |------------|-------------|
   | **Zostało przerwane** | Przebieg został zatrzymany lub nie został zakończony z powodu problemów zewnętrznych, na przykład awarii systemu lub subskrypcji platformy Azure. |
   | **Zerwan** | Uruchomienie zostało wyzwolone i uruchomione, ale Odebrano żądanie anulowania. |
   | **Niepowodzenie** | Co najmniej jedna akcja w przebiegu nie powiodła się. Nie skonfigurowano żadnych kolejnych akcji w przepływie pracy w celu obsłużenia błędu. |
   | **Uruchomienie** | Uruchomienie zostało wyzwolone i jest w toku, ale ten stan może również pojawić się dla przebiegu, który jest ograniczany ze względu na [limity akcji](logic-apps-limits-and-config.md) lub [bieżący plan cenowy](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Porada** : w przypadku skonfigurowania [rejestrowania diagnostycznego](monitor-logic-apps-log-analytics.md)można uzyskać informacje o wszelkich zdarzeniach związanych z ograniczaniem. |
   | **Powiodło się** | Przebieg zakończył się pomyślnie. Jeśli jakakolwiek akcja zakończyła się niepowodzeniem, kolejna Akcja w przepływie pracy obsłuży ten błąd. |
   | **Przekroczono limit czasu** | Przekroczono limit czasu uruchamiania, ponieważ bieżący czas trwania przekracza limit czasu trwania przebiegu, który jest kontrolowany przez [ustawienie **przechowywania historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits). Czas trwania przebiegu jest obliczany przy użyciu limitu czasu rozpoczęcia i czas trwania uruchomienia w tym czasie. <p><p>**Uwaga** : Jeśli czas trwania przebiegu przekracza *Limit przechowywania bieżącej historii przebiegów* , który jest również kontrolowany przez ustawienie trwa [ **przechowywanie historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits), uruchomienie jest usuwane z historii uruchamiania przez codzienne zadanie oczyszczania. Bez względu na to, czy czas trwania lub zakończeniu przebiegu, okres przechowywania jest zawsze obliczany przy użyciu czasu rozpoczęcia i *bieżącego* limitu przechowywania. W związku z tym, jeśli skracasz limit czasu trwania dla uruchomienia w locie, przekroczenie limitu czasu przebiegu. Jednak przebieg jest wyświetlany lub zostaje usunięty z historii uruchamiania w zależności od tego, czy czas trwania przebiegu przekroczył limit przechowywania. |
   | **Oczekiwanie** | Uruchomienie nie zostało uruchomione lub zostało wstrzymane, na przykład z powodu wcześniejszego wystąpienia przepływu pracy, które nadal działa. |
   |||

1. Aby sprawdzić stan każdego kroku w określonym przebiegu oraz dane wejściowe i wyjściowe kroku, wybierz przycisk wielokropka ( **...** ) dla tego przebiegu, a następnie wybierz polecenie **Pokaż uruchomienie**.

   ![Zrzut ekranu pokazujący wiersz historii przebiegu przepływu pracy z wielokropkiem i wybraną opcją "Pokaż przebieg"](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code otwiera widok Monitorowanie i pokazuje stan każdego kroku w przebiegu.

   ![Zrzut ekranu pokazujący każdy krok w przebiegu przepływu pracy i ich stan](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   Oto możliwe stany, które każdy krok w przepływie pracy może:

   | Stan akcji | Ikona | Opis |
   |---------------|------|-------------|
   | Zostało przerwane | ![Ikona stanu akcji "przerwane"][aborted-icon] | Akcja została zatrzymana lub nie została zakończona z powodu problemów zewnętrznych, na przykład awarii systemu lub subskrypcji platformy Azure. |
   | Anulowano | ![Ikona stanu akcji "anulowana"][cancelled-icon] | Akcja była uruchomiona, ale odebrała żądanie anulowania. |
   | Niepowodzenie | ![Ikona stanu akcji "Niepowodzenie"][failed-icon] | Akcja nie powiodła się. |
   | Uruchomienie | ![Ikona stanu akcji "uruchomiona"][running-icon] | Akcja jest obecnie uruchomiona. |
   | Pominięto | ![Ikona stanu akcji "pominięto"][skipped-icon] | Akcja została pominięta, ponieważ Poprzednia akcja nie powiodła się. Akcja ma `runAfter` warunek, który wymaga, aby poprzednia akcja została zakończona pomyślnie, zanim będzie można uruchomić bieżącą akcję. |
   | Sukces | ![Ikona stanu akcji "powodzenie"][succeeded-icon] | Akcja zakończyła się pomyślnie. |
   | Powodzenie z ponownymi próbami | ![Ikona stanu akcji "powodzenie z ponownymi próbami"][succeeded-with-retries-icon] | Akcja zakończyła się powodzeniem, ale tylko po wykonaniu jednej lub kilku ponownych prób. Aby przejrzeć historię ponownych prób, w widoku szczegółów historii uruchamiania wybierz tę akcję, aby wyświetlić dane wejściowe i wyjściowe. |
   | Przekroczono limit czasu | ![Ikona stanu akcji "Przekroczono limit czasu"][timed-out-icon] | Akcja została zatrzymana z powodu przekroczenia limitu czasu określonego przez ustawienia tej akcji. |
   | Oczekiwanie | ![Ikona stanu akcji "oczekiwanie"][waiting-icon] | Dotyczy akcji elementu webhook, która oczekuje na żądanie przychodzące od wywołującego. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Aby sprawdzić dane wejściowe i wyjściowe dla każdego kroku, wybierz krok, który chcesz sprawdzić.

   ![Zrzut ekranu pokazujący stan każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe w rozwiniętej akcji "Wyślij wiadomość e-mail"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Aby dodatkowo przejrzeć nieprzetworzone dane wejściowe i wyjściowe dla tego kroku, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe** lub **Pokaż nieprzetworzone wyjścia**.

1. Aby zatrzymać sesję debugowania, w menu **Uruchom** wybierz polecenie **Zatrzymaj debugowanie** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Zwróć odpowiedź do obiektu wywołującego

Aby zwrócić odpowiedź z powrotem do obiektu wywołującego, który wysłał żądanie do aplikacji logiki, można użyć wbudowanej [akcji odpowiedzi](../connectors/connectors-native-reqres.md) dla przepływu pracy rozpoczynającego się od wyzwalacza żądania.

1. W Projektancie aplikacji logiki w obszarze **Wyślij wiadomość e-mail** wybierz pozycję **nowy krok**.

   Zostanie wyświetlony monit **Wybierz operację** w projektancie, a **okienko Dodawanie akcji** zostanie otwarte ponownie, aby można było wybrać następną akcję.

1. W okienku **Dodaj akcję** , w polu wyszukiwania **Wybierz akcję** upewnij się, że jest zaznaczona opcja **wbudowane** . W polu wyszukiwania wpisz `response` , a następnie wybierz akcję **odpowiedź** .

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki z wybraną akcją odpowiedź.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Gdy akcja **odpowiedzi** pojawi się w projektancie, zostanie automatycznie otwarta okienko Szczegóły akcji.

   ![Zrzut ekranu, który pokazuje projektanta aplikacji logiki z otwartym okienkiem akcji "odpowiedź", i właściwość "treść" ma ustawioną wartość właściwości "treść" wysyłania wiadomości e-mail.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Na karcie **Parametry** podaj wymagane informacje dotyczące funkcji, którą chcesz wywołać.

   Ten przykład zwraca wartość właściwości **treści** , która jest wyprowadzana z akcji **Wyślij wiadomość e-mail** .

   1. Kliknij wewnątrz pola właściwości **treść** , aby wyświetlić listę zawartości dynamicznej i wyświetlić dostępne wartości wyjściowe z poprzedniego wyzwalacza i akcji w przepływie pracy.

      ![Zrzut ekranu przedstawiający okienko szczegółów akcji "odpowiedź" ze wskaźnikiem myszy wewnątrz właściwości "Body", dzięki czemu zostanie wyświetlona lista zawartości dynamicznej.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Z listy zawartość dynamiczna w obszarze **Wyślij wiadomość e-mail** wybierz pozycję **treść**.

      ![Zrzut ekranu pokazujący otwartą listę zawartości dynamicznej. Na liście w obszarze "Wyślij wiadomość e-mail" jest zaznaczona wartość wyjściowa "treść".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Gdy skończysz, właściwość **treść** akcji odpowiedź zostanie teraz ustawiona na wartość wyjściową **treści** akcji **wysłania wiadomości e-mail** .

      ![Zrzut ekranu pokazujący stan każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe w rozwiniętej akcji "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. W Projektancie wybierz pozycję **Zapisz**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Przetestowanie aplikacji logiki

Po wprowadzeniu aktualizacji aplikacji logiki można uruchomić inny test, ponownie uruchamiając debuger w programie Visual Studio i wysyłając kolejne żądanie wyzwalające zaktualizowaną aplikację logiki, podobną do kroków w sekcji [debugowanie i testowanie aplikacji logiki](#debug-test-locally).

1. Na pasku narzędzi Visual Studio Code Otwórz menu **Uruchom** , a następnie wybierz polecenie **Rozpocznij debugowanie** (F5).

1. Wyślij kolejne żądanie, aby wyzwolić przepływ pracy w programie Poster lub w narzędziu do tworzenia i wysyłania żądań.

1. Jeśli utworzono stanowy przepływ pracy, na stronie Przegląd przepływu pracy Sprawdź stan ostatniego uruchomienia. Aby wyświetlić stan, dane wejściowe i wyjściowe dla każdego kroku w tym przebiegu, wybierz przycisk wielokropka ( **...** ) dla tego przebiegu, a następnie wybierz polecenie **Pokaż uruchomienie**.

   Na przykład poniżej przedstawiono stan krok po kroku dla uruchomienia po zaktualizowaniu przykładowego przepływu pracy przy użyciu akcji odpowiedzi.

   ![Zrzut ekranu pokazujący stan każdego kroku w zaktualizowanym przepływie pracy oraz dane wejściowe i wyjściowe w rozwiniętej akcji "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Aby zatrzymać sesję debugowania, w menu **Uruchom** wybierz polecenie **Zatrzymaj debugowanie** (Shift + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

W Visual Studio Code można wdrożyć projekt bezpośrednio na platformie Azure, który publikuje aplikację logiki przy użyciu nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** . Podobnie jak w przypadku zasobu aplikacji funkcji w Azure Functions, wdrożenie dla tego nowego typu zasobu wymaga wybrania [planu hostingu i warstwy cenowej](../app-service/overview-hosting-plans.md), którą można skonfigurować podczas wdrażania. Aby uzyskać więcej informacji o planach hostingu i cenach, zapoznaj się z następującymi tematami:

* [Skalowanie w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)

Aplikację logiki można opublikować jako nowy zasób, co spowoduje automatyczne utworzenie wszelkich dodatkowych zasobów, takich jak [konto usługi Azure Storage, podobnie jak w przypadku wymagań aplikacji funkcji](../azure-functions/storage-considerations.md). Możesz również opublikować aplikację logiki w wcześniej wdrożonym zasobie **aplikacji logiki (wersja zapoznawcza)** , która zastępuje tę aplikację logiki.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publikuj jako nowy zasób aplikacji logiki (wersja zapoznawcza)

1. Na pasku narzędzi Visual Studio Code wybierz ikonę platformy Azure.

1. Na pasku narzędzi okienka **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Wdróż do aplikacji logiki**.

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i pasek narzędzi okienka z wybraną opcją "wdróż w aplikacji logiki".](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Z wyświetlonej Visual Studio Code listy wybierz jedną z następujących opcji:

   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure** (szybka)
   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure Advanced**
   * Wcześniej wdrożony zasób **aplikacji logiki (wersja zapoznawcza)** , jeśli istnieje

   Ten przykład kontynuuje **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure Advanced**.

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" z listą z wybraną opcją "Utwórz nową aplikację logiki (wersja zapoznawcza) na platformie Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Aby utworzyć nowy zasób **aplikacji logiki (wersja zapoznawcza)** , wykonaj następujące kroki:

   1. Podaj globalnie unikatową nazwę nowej aplikacji logiki, która jest nazwą używaną dla zasobu **aplikacji logiki (wersja zapoznawcza)** . Ten przykład używa `example-logic-app-preview` .

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o podanie nazwy nowej aplikacji logiki do utworzenia.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Wybierz plan hostingu dla nowej aplikacji logiki, [**App Service plan**](../azure-functions/functions-scale.md#app-service-plan) lub [**Premium**](../azure-functions/functions-scale.md#premium-plan). Ten przykład wybiera **App Service plan**.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o wybranie opcji "App Service plan" lub "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Utwórz nowy plan App Service lub wybierz istniejący plan. W tym przykładzie wybrano opcję **Utwórz nowy Plan App Service**.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o utworzenie nowego planu App Service lub wybór istniejącego planu App Service.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Podaj nazwę planu App Service, a następnie wybierz [warstwę cenową](../app-service/overview-hosting-plans.md) dla planu. W tym przykładzie wybierany jest **bezpłatny plan F1** .

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o wybranie warstwy cenowej.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. W celu uzyskania optymalnej wydajności Znajdź i wybierz tę samą grupę zasobów co projekt wdrożenia.

      > [!NOTE]
      > Chociaż można utworzyć lub użyć innej grupy zasobów, może to mieć wpływ na wydajność. Jeśli utworzysz lub wybierzesz inną grupę zasobów, ale zostanie anulowana po wyświetleniu monitu o potwierdzenie, wdrożenie zostanie również anulowane.

   1. W przypadku przepływów stanowych, wybierz pozycję **Utwórz nowe konto magazynu** lub istniejące konto magazynu.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o utworzenie lub wybranie konta magazynu.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Aby ułatwić rejestrowanie i śledzenie diagnostyki, można wybrać istniejący zasób Application Insights. W przeciwnym razie możesz wybrać opcję **Utwórz nowy zasób Application Insights** lub skonfigurować Application Insights w Azure Portal po wdrożeniu aplikacji.

      Przed wdrożeniem upewnij się, że obiekt został dodany `logLevel` do `logging` obiektu w **host.js** w pliku, który istnieje na poziomie głównym projektu, a następnie ustaw wartość `Host.Triggers.Workflow` na na `Information` przykład:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Oto jak **host.jsw** pliku może wyglądać:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Gdy skończysz, Visual Studio Code rozpocznie tworzenie i wdrażanie zasobów niezbędnych do opublikowania aplikacji logiki.

1. Aby przejrzeć i monitorować proces wdrażania, w menu **Widok** wybierz pozycję **dane wyjściowe**. Z listy pasków narzędzi okna dane wyjściowe wybierz pozycję **Azure Logic Apps**.

   ![Zrzut ekranu pokazujący okno danych wyjściowych z opcją "Azure Logic Apps" wybraną na liście pasków narzędzi wraz z postępem i Stanami wdrożenia.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Po zakończeniu Visual Studio Code wdrożenia przepływu pracy aplikacji logiki na platformie Azure zostanie wyświetlony następujący komunikat:

   ![Zrzut ekranu pokazujący komunikat, że pomyślnie ukończono wdrażanie wdrożenia na platformie Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulacje, Twoja aplikacja logiki jest teraz aktywna na platformie Azure i domyślnie włączona.

Następnie można dowiedzieć się, jak wykonywać następujące zadania:

* [Znajdź wdrożoną aplikację logiki w Azure Portal](#find-manage-deployed-workflows-portal) lub [Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Włącz historię uruchamiania w przepływach pracy aplikacji logiki bezstanowej](#run-history).

* [Włącz monitorowanie dla zasobu wdrożonej **aplikacji logiki (wersja zapoznawcza)**](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Znajdowanie wdrożonych aplikacji logiki i zarządzanie nimi w programie Visual Studio Code

W Visual Studio Code można wyświetlić wszystkie wdrożone Aplikacje logiki w ramach subskrypcji platformy Azure, niezależnie od tego, czy są to oryginalne **Logic Apps** , typ zasobu **aplikacji logiki (wersja zapoznawcza)** , a także wybrać zadania, które ułatwiają zarządzanie tymi aplikacjami logiki. Aby jednak uzyskać dostęp do obu typów zasobów, potrzebne są zarówno **Azure Logic Apps** , jak i **Azure Logic Apps (wersja zapoznawcza)** rozszerzeń Visual Studio Code.

1. Na pasku narzędzi po lewej stronie wybierz ikonę platformy Azure. W okienku **Azure: Logic Apps (wersja zapoznawcza)** Rozwiń swoją subskrypcję, która zawiera wszystkie wdrożone Aplikacje logiki dla tej subskrypcji.

1. Znajdź i wybierz aplikację logiki, którą chcesz zarządzać. Otwórz menu skrótów aplikacji logiki i wybierz zadanie, które chcesz wykonać.

   Można na przykład wybrać zadania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie lub usuwanie wdrożonej aplikacji logiki.

   ![Zrzut ekranu, który pokazuje Visual Studio Code z otwartym okienkiem rozszerzenia "Azure Logic Apps (wersja zapoznawcza)" i wdrożonym przepływem pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Aby wyświetlić wszystkie przepływy pracy w aplikacji logiki, rozwiń aplikację logiki, a następnie rozwiń węzeł **przepływy pracy** .

1. Aby wyświetlić konkretny przepływ pracy, otwórz menu skrótów przepływu pracy i wybierz polecenie **Otwórz w projektancie** , co spowoduje otwarcie przepływu pracy w trybie tylko do odczytu.

   Aby edytować przepływ pracy, dostępne są następujące opcje:

   * W Visual Studio Code Otwórz **workflow.jsprojektu na** pliku w Projektancie aplikacji logiki, wprowadź zmiany i Wdróż ponownie aplikację logiki na platformie Azure.

   * W Azure Portal [Znajdź i Otwórz aplikację logiki](#find-manage-deployed-workflows-portal). Znajdź, Edytuj i Zapisz przepływ pracy.

1. Aby otworzyć wdrożoną aplikację logiki w Azure Portal, otwórz menu skrótów aplikacji logiki i wybierz polecenie **Otwórz w portalu**.

   Azure Portal zostanie otwarta w przeglądarce, program automatycznie zaloguje się do portalu, jeśli zalogujesz się do Visual Studio Code i zobaczysz aplikację logiki.

   ![Zrzut ekranu przedstawiający stronę Azure Portal aplikacji logiki w programie Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Możesz również zalogować się niezależnie do Azure Portal, użyj pola wyszukiwania portalu, aby znaleźć aplikację logiki, a następnie wybierz aplikację logiki z listy wyników.

   ![Zrzut ekranu pokazujący Azure Portal i pasek wyszukiwania z wynikami wyszukiwania wdrożonej aplikacji logiki, która zostanie wyświetlona.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Znajdowanie wdrożonych aplikacji logiki i zarządzanie nimi w portalu

W Azure Portal można wyświetlić wszystkie wdrożone Aplikacje logiki, które znajdują się w subskrypcji platformy Azure, niezależnie od tego, czy są one oryginalnym typem zasobu **Logic Apps** czy typu zasobu **aplikacja logiki (wersja zapoznawcza)** . Obecnie każdy typ zasobu jest zorganizowany i zarządzany jako osobne kategorie na platformie Azure.

> [!NOTE]
> W publicznej wersji zapoznawczej można wyświetlić tylko zasoby wdrożonych **aplikacji logiki (wersja zapoznawcza)** w Azure Portal nie tworzyć nowych zasobów **aplikacji logiki (wersja zapoznawcza)** . Te aplikacje logiki można tworzyć tylko w Visual Studio Code. Można jednak [dodać przepływy pracy](#add-workflows) do wdrożonych aplikacji logiki przy użyciu tego typu zasobu.

Aby znaleźć aplikacje logiki, które mają typ zasobu **aplikacja logiki (wersja zapoznawcza)** , wykonaj następujące kroki:

1. W polu wyszukiwania Azure Portal wprowadź wartość `logic app preview` . Gdy zostanie wyświetlona lista wyników, w obszarze **usługi** wybierz pozycję **aplikacja logiki (wersja zapoznawcza)**.

   ![Zrzut ekranu przedstawiający pole wyszukiwania Azure Portal z tekstem wyszukiwania "aplikacja logiki" w wersji zapoznawczej.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. W okienku **aplikacja logiki (wersja zapoznawcza)** Znajdź i wybierz aplikację logiki wdrożoną na podstawie Visual Studio Code.

   ![Zrzut ekranu przedstawiający Azure Portal i zasoby aplikacji logiki (wersja zapoznawcza) wdrożone na platformie Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal otwiera indywidualną stronę zasobów dla wybranej aplikacji logiki.

   ![Zrzut ekranu przedstawiający stronę zasobów przepływu pracy aplikacji logiki w Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Aby wyświetlić przepływy pracy w tej aplikacji logiki, w menu aplikacji logiki wybierz pozycję **przepływy pracy**.

   W okienku **przepływy pracy** są wyświetlane wszystkie przepływy pracy w bieżącej aplikacji logiki. Ten przykład pokazuje przepływ pracy utworzony w Visual Studio Code.

   ![Zrzut ekranu, na którym jest wyświetlana strona zasobów "aplikacja logiki (wersja zapoznawcza)" z otwartym okienkiem "przepływy pracy" i wdrożony przepływ pracy](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Aby wyświetlić przepływ pracy, w okienku **przepływy** pracy wybierz ten przepływ pracy.

   Zostanie otwarte okienko przepływu pracy zawierające więcej informacji i zadań, które można wykonać w tym przepływie pracy.

   Aby na przykład wyświetlić kroki w przepływie pracy, wybierz pozycję **Projektant**.

   ![Zrzut ekranu pokazujący okienko "przegląd" wybranego przepływu pracy, podczas gdy menu przepływ pracy zawiera wybrane polecenie "Projektant".](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Zostanie otwarty projektant aplikacji logiki i zostanie wyświetlony przepływ pracy utworzony w Visual Studio Code. Teraz można wprowadzać zmiany w tym przepływie pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki i przepływ pracy wdrożony z Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Dodawanie przepływu pracy do wdrożonych aplikacji logiki

Za pomocą Azure Portal można dodać puste przepływy pracy do zasobu **aplikacji logiki (wersja zapoznawcza)** , który został wdrożony z Visual Studio Code i utworzyć te przepływy pracy w Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób wdrożonej **aplikacji logiki (wersja zapoznawcza)** .

1. W menu aplikacji logiki wybierz pozycję **przepływy pracy**. W okienku **przepływy pracy** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu pokazujący okienko "przepływy pracy" i pasek narzędzi wybranych aplikacji logiki z wybranym poleceniem "Dodaj".](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. W okienku **Nowy przepływ pracy** Podaj nazwę dla przepływu pracy. Wybierz opcję **stanowe** lub **bezstanowe** **>** **Create**.

   Po wdrożeniu nowego przepływu pracy przez platformę Azure, który jest wyświetlany w okienku **przepływy** pracy, wybierz ten przepływ pracy, aby wykonać zarządzanie i inne zadania, na przykład otwierając projektanta aplikacji logiki lub widok kodu.

   ![Zrzut ekranu pokazujący wybrany przepływ pracy z opcjami zarządzania i przeglądu.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Na przykład otwarcie projektanta dla nowego przepływu pracy pokazuje pustą kanwę. Teraz można utworzyć ten przepływ pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki i pusty przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Historia uruchamiania przepływów pracy aplikacji logiki bezstanowej

Aby łatwiej debugować przepływ pracy bezstanowej aplikacji logiki, można włączyć historię uruchamiania dla tego przepływu pracy w Visual Studio Code lub w Azure Portal, a następnie wyłączyć historię uruchamiania po zakończeniu.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Dla przepływu pracy aplikacji logiki bezstanowej w Visual Studio Code

Jeśli pracujesz lokalnie w Visual Studio Code przepływ pracy aplikacji logiki bezstanowej, wykonaj następujące czynności:

1. W projekcie Znajdź i rozwiń folder **Workflow-DesignTime** . Znajdź i Otwórz **local.settings.js** pliku.

1. Dodaj `Workflows.{yourWorkflowName}.operationOptions` Właściwość i ustaw wartość na, na `WithStatelessRunHistory` przykład:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Aby wyłączyć historię uruchamiania po zakończeniu, Usuń `Workflows.{yourWorkflowName}.OperationOptions` Właściwość i jej wartość lub ustaw właściwość na `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Dla przepływu pracy aplikacji logiki bezstanowej w Azure Portal

Jeśli projekt został już wdrożony na Azure Portal, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz zasób **aplikacji logiki (wersja zapoznawcza)** .

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Konfiguracja**.

1. Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.

1. W okienku **Dodawanie/Edytowanie ustawienia aplikacji** w polu **Nazwa** wprowadź tę nazwę opcji operacji: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. W polu **wartość** Wprowadź następującą wartość: `WithStatelessRunHistory`

   Przykład:

   ![Zrzut ekranu pokazujący zasób Azure Portal i Logic App (wersja zapoznawcza) z otwartym okienkiem "Konfiguracja" > "nowe ustawienie aplikacji" < "Dodaj/Edytuj ustawienie aplikacji" i "przepływy pracy". {yourWorkflowName}. Opcja "OperationOptions" ma wartość "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Gdy skończysz, wybierz opcję **OK**. W okienku **Konfiguracja** wybierz pozycję **Zapisz**.

Aby włączyć monitorowanie wdrożonego zasobu aplikacji logiki (wersja zapoznawcza), przejdź do następnej sekcji.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Włącz monitorowanie dla zasobów wdrożonej aplikacji logiki (wersja zapoznawcza)

Aby włączyć monitorowanie dla wdrożonego zasobu **aplikacji logiki (wersja zapoznawcza)** , wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób wdrożonej **aplikacji logiki (wersja zapoznawcza)** .

1. W menu tego zasobu w obszarze **interfejs API** wybierz pozycję **CORS**.

1. W okienku **CORS** w obszarze **dozwolone źródła** Dodaj symbol wieloznaczny (*).

1. Gdy skończysz, na pasku narzędzi **CORS** wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający Azure Portal ze wdrożonym zasobem Logic Apps (wersja zapoznawcza). W menu zasób jest zaznaczone polecenie "CORS" z nowym wpisem "dozwolone źródła" ustawione na symbol wieloznaczny "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Wdróż w kontenerze platformy Docker

Za pomocą [Narzędzia .NET Core Command Line Interface (CLI)](/dotnet/core/tools/)można skompilować projekt, a następnie opublikować kompilację. Następnie można skompilować [kontener platformy Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) i użyć go jako miejsca docelowego do wdrożenia przepływu pracy aplikacji logiki. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

* [Wprowadzenie do kontenerów i platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Wprowadzenie do platform .NET i Docker](/dotnet/core/docker/introduction)
* [Terminologia platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Samouczek: wprowadzenie do platformy Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Aby skompilować projekt, Otwórz wiersz polecenia i uruchom następujące polecenie:

   `dotnet build -c release`

   Aby uzyskać więcej informacji, zobacz stronę referencyjną [kompilacji dotnet](/dotnet/core/tools/dotnet-build/) .

1. Opublikuj kompilację, uruchamiając następujące polecenie:

   `dotnet publish -c release`

   Aby uzyskać więcej informacji, zobacz stronę referencyjną [dotnet Publish](/dotnet/core/tools/dotnet-publish/) .

1. Utwórz kontener platformy Docker przy użyciu pliku platformy Docker dla przepływu pracy programu .NET i uruchamiając to polecenie:

   `docker build --tag local/workflowcontainer .`

   Na przykład poniżej znajduje się przykładowy plik platformy Docker, który wdraża aplikację logiki stanowej i określa parametry połączenia dla konta usługi Azure Storage, które zostało użyte do opublikowania aplikacji logiki w Azure Portal. Aby znaleźć i skopiować parametry połączenia konta magazynu w Azure Portal, przejrzyj temat [Zarządzanie kluczami konta magazynu](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Zrzut ekranu przedstawiający Azure Portal z kluczami dostępu do konta magazynu i skopiowanymi parametrami połączenia.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Parametry połączenia wyglądają podobnie jak w przypadku tego przykładu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Oto format pliku platformy Docker:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Aby uzyskać więcej informacji, zobacz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Zapisz ciąg w bezpiecznym miejscu, aby później można było dodać ciąg do **local.settings.jsw** plikach w projekcie używanym do tworzenia aplikacji logiki w programie Visual Studio Code.

1. Uruchom kontener lokalnie za pomocą tego polecenia:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Aby uzyskać więcej informacji, zobacz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. Aby uzyskać adres URL wywołania zwrotnego dla wyzwalacza żądania, Wyślij to żądanie:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Wartość> *głównego klucza* <jest definiowana na koncie usługi Azure Storage ustawionym dla programu `AzureWebJobsStorage` w ramach pliku, **Azure-WebJobs-Secret/{Deployment-Name}/host.jsna** , gdzie można znaleźć wartość w tej sekcji:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Różnice w zachowaniu zagnieżdżonych aplikacji logiki stanowej i bezstanowej

[Przepływ pracy aplikacji logiki można wywołać](../logic-apps/logic-apps-http-endpoint.md) z innych przepływów pracy aplikacji logiki, które istnieją w tym samym zasobie **logiki (wersja zapoznawcza)** przy użyciu wyzwalacza [żądania](../connectors/connectors-native-reqres.md) , wyzwalacza [elementu webhook protokołu HTTP](../connectors/connectors-native-webhook.md) lub wyzwalaczy łączników zarządzanych, które mają [Typ ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać żądania HTTPS.

Poniżej przedstawiono wzorce zachowania, które mogą występować w zagnieżdżonych przepływach pracy aplikacji logiki po wystąpieniu nadrzędnego przepływu pracy wywołującego podrzędny przepływ pracy:

* Wzorzec sondowania asynchronicznego

  Element nadrzędny nie czeka na odpowiedź na początkowe wywołanie, ale stale sprawdza historię uruchamiania dziecka do momentu zakończenia działania elementu podrzędnego. Domyślne przepływy pracy są zgodne z tym wzorcem, co jest idealne dla długotrwałych przepływów pracy, które mogą przekroczyć [limity limitu czasu żądania](../logic-apps/logic-apps-limits-and-config.md).

* Wzorzec synchroniczny ("pożar i zapomnij")

  Element podrzędny potwierdza wywołanie przez natychmiastowe zwrócenie `202 ACCEPTED` odpowiedzi, a element nadrzędny kontynuuje następną akcję bez czekania na wyniki z elementu podrzędnego. Zamiast tego obiekt nadrzędny otrzymuje wyniki po zakończeniu działania elementu podrzędnego. Podrzędne bezstanowe przepływy pracy, które nie zawierają akcji odpowiedzi, zawsze podążają za wzorcem synchronicznym. Dla podrzędnych przepływów pracy, historia przebiegów jest dostępna do przejrzenia.

  Aby włączyć to zachowanie, w definicji JSON przepływu pracy Ustaw `operationOptions` Właściwość na `DisableAsyncPattern` . Aby uzyskać więcej informacji, zobacz [typy wyzwalaczy i akcji — opcje operacji](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Wyzwalaj i czekaj

  W przypadku podrzędnego bezstanowego przepływu pracy element nadrzędny czeka na odpowiedź, która zwraca wyniki z elementu podrzędnego. Ten wzorzec działa podobnie do użycia wbudowanego [wyzwalacza http lub akcji](../connectors/connectors-native-http.md) w celu wywołania podrzędnego przepływu pracy. Podrzędne bezstanowe przepływy pracy, które nie zawierają akcji odpowiedzi natychmiast zwracają `202 ACCEPTED` odpowiedź, ale element nadrzędny czeka na zakończenie działania elementu podrzędnego przed przejściem do następnej akcji. Te zachowania mają zastosowanie tylko do podrzędnych bezstanowych przepływów pracy.

Ta tabela określa zachowanie podrzędnego przepływu pracy w zależności od tego, czy nadrzędne i podrzędne są typy stanowe, bezstanowe czy mieszane przepływy pracy:

| Nadrzędny przepływ pracy | Podrzędny przepływ pracy | Zachowanie elementu podrzędnego |
|-----------------|----------------|----------------|
| Stanowe | Stanowe | Asynchroniczne lub synchroniczne z `"operationOptions": "DisableAsyncPattern"` ustawieniem |
| Stanowe | Bezstanowe | Wyzwalaj i czekaj |
| Bezstanowe | Stanowe | Synchronous |
| Bezstanowe | Bezstanowe | Wyzwalaj i czekaj |
||||

## <a name="limits"></a>Limity

Chociaż liczne [istniejące limity dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) są takie same dla tego typu zasobu, poniżej przedstawiono różnice w tym rozszerzeniu publicznej wersji zapoznawczej:

* Łączniki zarządzane: 50 żądań na minutę za połączenie

* W przypadku [akcji kodu wbudowanego dla akcji JavaScript](../logic-apps/logic-apps-add-run-inline-code.md) te limity zostały zmienione:

  * Limit znaków kodu jest większy od 1 024 znaków do 100 000 znaków.

  * Limit czasu do uruchomienia kodu rośnie z pięciu sekund do 15 sekund.

## <a name="next-steps"></a>Następne kroki

Chcemy wiedzieć o Twoich doświadczeniach z tym publicznym rozszerzeniem w wersji zapoznawczej.

* W przypadku usterek lub problemów [Utwórz swoje problemy w serwisie GitHub](https://github.com/Azure/logicapps/issues).
* Aby uzyskać odpowiedzi na pytania, żądania, komentarze i inne opinie, [Skorzystaj z tej formy opinii](https://aka.ms/lafeedback).
