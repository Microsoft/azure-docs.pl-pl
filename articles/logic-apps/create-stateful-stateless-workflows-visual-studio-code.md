---
title: Tworzenie Logic Apps w wersji zapoznawczej w Visual Studio Code
description: Kompiluj i uruchamiaj przepływy pracy dla scenariuszy automatyzacji i integracji w Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 941d866fbdea0efc5775bccd08e0235b1629fae0
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440993"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Twórz stanowe i bezstanowe przepływy pracy w Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzięki usłudze [Azure Logic Apps Preview](logic-apps-overview-preview.md)możesz tworzyć rozwiązania do automatyzacji i integracji dla aplikacji, danych, usług w chmurze i systemów przez tworzenie i uruchamianie aplikacji logiki, które zawierają bezstanowe [  i *bezstanowe* przepływy pracy](logic-apps-overview-preview.md#stateful-stateless) w Visual Studio Code przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza). Za pomocą tego nowego typu aplikacji logiki można utworzyć wiele przepływów pracy, które są obsługiwane przez przeprojektowaną Azure Logic Apps środowisko uruchomieniowe podglądu, które zapewnia przenośność, lepszą wydajność i elastyczność w zakresie wdrażania i uruchamiania w różnych środowiskach hostingu, a nie tylko na platformie Azure, ale również kontenerów platformy Docker. Aby dowiedzieć się więcej na temat nowego typu aplikacji logiki, zobacz [omówienie Azure Logic Apps wersji zapoznawczej](logic-apps-overview-preview.md).

![Zrzut ekranu, który pokazuje Visual Studio Code, projekt aplikacji logiki i przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

W Visual Studio Code można rozpocząć od utworzenia projektu, w którym można *lokalnie* kompilować i uruchamiać przepływy pracy aplikacji logiki w środowisku deweloperskim przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza). Możesz również zacząć od [utworzenia nowego zasobu **aplikacji logiki (wersja zapoznawcza)** w Azure Portal](create-stateful-stateless-workflows-azure-portal.md), obie podejścia zapewniają możliwość wdrażania i uruchamiania aplikacji logiki w tym samym rodzaju środowiskach hostingu.

W tym czasie można nadal utworzyć oryginalny typ aplikacji logiki. Chociaż środowiska programistyczne w Visual Studio Code różnią się od oryginalnych i nowych typów aplikacji logiki, Twoja subskrypcja platformy Azure może zawierać oba typy. Możesz wyświetlać i uzyskiwać dostęp do wszystkich wdrożonych aplikacji logiki w ramach subskrypcji platformy Azure, ale aplikacje są zorganizowane w ich własne kategorie i sekcje.

W tym artykule pokazano, jak utworzyć aplikację logiki i przepływ pracy w Visual Studio Code przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza) i wykonując te zadania wysokiego poziomu:

* Utwórz projekt dla aplikacji logiki i przepływu pracy.

* Dodaj wyzwalacz i akcję.

* Uruchom, Testuj, Debuguj i przejrzyj historię uruchamiania lokalnie.

* Wdróż na platformie Azure, w tym opcjonalne Włączanie Application Insights.

* Zarządzaj wdrożoną aplikacją logiki w Visual Studio Code i Azure Portal.

* Włącz historię uruchamiania dla bezstanowych przepływów pracy.

* Włącz lub Otwórz Application Insights po wdrożeniu.

* Wdróż w kontenerze platformy Docker, który można uruchomić w dowolnym miejscu.

> [!NOTE]
> Informacje o bieżących znanych problemach można znaleźć na [stronie Logic Apps znane problemy w wersji zapoznawczej w witrynie GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="access-and-connectivity"></a>Dostęp i łączność

* Dostęp do Internetu w taki sposób, aby można było pobrać wymagania, nawiązać połączenie z Visual Studio Code na konto platformy Azure i publikować z Visual Studio Code na platformie Azure, kontenera Docker lub innego środowiska.

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Aby móc utworzyć tę samą przykładową aplikację logiki w tym artykule, potrzebujesz konta e-mail programu Outlook w usłudze Office 365, które używa konta służbowego firmy Microsoft do logowania.

  Jeśli zdecydujesz się użyć innego [łącznika poczty e-mail, który jest obsługiwany przez Azure Logic Apps](/connectors/), na przykład Outlook.com lub [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), możesz nadal postępować zgodnie z przykładem, a ogólna ogólna procedura jest taka sama, ale interfejs użytkownika i opcje mogą się różnić. Jeśli na przykład używasz łącznika Outlook.com, zamiast tego Zaloguj się przy użyciu osobistego konto Microsoft.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Wymagania dotyczące magazynu

#### <a name="windows"></a>Windows

Aby lokalnie skompilować i uruchomić projekt aplikacji logiki w Visual Studio Code w przypadku korzystania z systemu Windows, wykonaj następujące kroki, aby skonfigurować emulator usługi Azure Storage:

1. Pobierz i zainstaluj [emulator usługi Azure Storage 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Jeśli jeszcze tego nie zrobiono, musisz mieć lokalną instalację bazy danych SQL, na przykład bezpłatną [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658), aby można było uruchomić emulator.

   Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../storage/common/storage-use-emulator.md).

1. Przed uruchomieniem projektu, upewnij się, że uruchomiono emulator.

   ![Zrzut ekranu pokazujący emulator usługi Azure Storage z systemem.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS i Linux

Aby lokalnie skompilować i uruchomić projekt aplikacji logiki w Visual Studio Code w przypadku korzystania z macOS lub Linux, wykonaj następujące kroki, aby utworzyć i skonfigurować konto usługi Azure Storage.

> [!NOTE]
> Obecnie projektant w Visual Studio Code nie działa w systemie operacyjnym Linux, ale nadal można uruchamiać kompilacje, uruchamiać i wdrażać aplikacje logiki, które używają środowiska uruchomieniowego w wersji zapoznawczej Logic Apps do maszyn wirtualnych opartych na systemie Linux. Na razie możesz skompilować Aplikacje logiki w Visual Studio Code w systemie Windows lub macOS, a następnie wdrożyć je na maszynie wirtualnej z systemem Linux.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal), które jest [wymaganiem wstępnym dla Azure Functions](../azure-functions/storage-considerations.md).

1. W menu konto magazynu w obszarze **Ustawienia** wybierz pozycję **klucze dostępu**.

1. W okienku **klucze dostępu** Znajdź i skopiuj parametry połączenia konta magazynu, które wyglądają podobnie do tego przykładu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Zrzut ekranu przedstawiający Azure Portal z kluczami dostępu do konta magazynu i skopiowanymi parametrami połączenia.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Aby uzyskać więcej informacji, zapoznaj się z tematem [Zarządzanie kluczami konta magazynu](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Zapisz parametry połączenia w bezpiecznym miejscu. Po utworzeniu projektu aplikacji logiki w Visual Studio Code należy dodać ciąg do **local.settings.jsw** pliku w folderze poziomu głównego projektu.

   > [!IMPORTANT]
   > Jeśli planujesz wdrożenie do kontenera Docker, musisz również dodać te parametry połączenia do pliku platformy Docker, który ma być używany do wdrożenia.

### <a name="tools"></a>narzędzia

* [Visual Studio Code 1.30.1 (styczeń 2019) lub nowszy](https://code.visualstudio.com/), który jest bezpłatny. Należy również pobrać i zainstalować te narzędzia dla Visual Studio Code, jeśli nie są jeszcze:

  * [Rozszerzenie konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), które zapewnia jedno wspólne środowisko rejestrowania w usłudze Azure i obsługę filtrowania subskrypcji dla wszystkich innych rozszerzeń platformy azure w Visual Studio Code.

  * Język [C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), który umożliwia uruchamianie aplikacji logiki w funkcji F5.

  * [Azure Functions Core Tools 3.0.3245 lub nowszy](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) przy użyciu wersji Instalatora Microsoft (msi), która jest `func-cli-3.0.3245-x*.msi` .

    Te narzędzia obejmują wersję tego samego środowiska uruchomieniowego, która umożliwia środowisko uruchomieniowe Azure Functions, którego rozszerzenie wersji zapoznawczej używa w programie Visual Studio Code.

    > [!IMPORTANT]
    > Jeśli masz instalację wcześniejszą niż te wersje, najpierw Odinstaluj tę wersję lub upewnij się, że zmienna środowiskowa PATH wskazuje na wersję pobieraną i zainstalowaną.

  * [Rozszerzenie Azure Logic Apps (wersja zapoznawcza) Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). To rozszerzenie zapewnia możliwość tworzenia aplikacji logiki, w których można tworzyć stanowe i bezstanowe przepływy pracy uruchamiane lokalnie w Visual Studio Code a następnie wdrażać te aplikacje logiki bezpośrednio na platformie Azure lub w kontenerach platformy Docker.

    Obecnie można mieć zarówno oryginalne rozszerzenie Azure Logic Apps, jak i rozszerzenie publicznej wersji zapoznawczej zainstalowane w programie Visual Studio Code. Chociaż środowiska programistyczne różnią się w zależności od rozszerzeń, subskrypcja platformy Azure może obejmować zarówno typy aplikacji logiki, które tworzysz z rozszerzeniami. Visual Studio Code przedstawia wszystkie wdrożone Aplikacje logiki w ramach subskrypcji platformy Azure, ale organizuje je w różne sekcje według nazw rozszerzeń, **Logic Apps** i **Azure Logic Apps (wersja zapoznawcza)**.

    > [!IMPORTANT]
    > Jeśli utworzono projekty aplikacji logiki przy użyciu wcześniejszego rozszerzenia prywatnej wersji zapoznawczej, te projekty nie będą działały z rozszerzeniem publicznej wersji zapoznawczej. Można jednak migrować te projekty po odinstalowaniu rozszerzenia prywatnej wersji zapoznawczej, usunięciu skojarzonych plików i zainstalowaniu rozszerzenia publicznej wersji zapoznawczej. Następnie utworzysz nowy projekt w Visual Studio Code i skopiujesz poprzednio utworzony plik **definicji przepływu pracy** aplikacji logiki do nowego projektu. Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia prywatnej wersji zapoznawczej](#migrate-private-preview).
    > 
    > Jeśli utworzono projekty aplikacji logiki przy użyciu wcześniejszego rozszerzenia publicznej wersji zapoznawczej, możesz nadal używać tych projektów bez żadnych kroków migracji.

    **Aby zainstalować rozszerzenie **Azure Logic Apps (wersja zapoznawcza)** , wykonaj następujące kroki:**

    1. W Visual Studio Code na pasku narzędzi po lewej stronie wybierz pozycję **rozszerzenia**.

    1. W polu wyszukiwania rozszerzeń wprowadź `azure logic apps preview` . Z listy wyników wybierz pozycję Zainstaluj **Azure Logic Apps (wersja zapoznawcza)** **>** .

       Po zakończeniu instalacji rozszerzenie podglądu zostanie wyświetlone na liście **rozszerzenia: zainstalowane** .

       ![Zrzut ekranu przedstawiający listę rozszerzeń zainstalowanych Visual Studio Code z podkreśleniem rozszerzenia "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Jeśli rozszerzenie nie pojawia się na liście zainstalowanych, spróbuj ponownie uruchomić Visual Studio Code.

* Aby użyć [akcji operacji kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md) , która uruchamia JavaScript, zainstaluj [Node.js wersja 10. x. x, 11. x. x lub 12. x. x](https://nodejs.org/en/download/releases/).

  > [!TIP] 
  > W przypadku systemu Windows Pobierz wersję MSI. Jeśli zamiast tego używasz wersji ZIP, musisz ręcznie udostępnić Node.js za pomocą zmiennej środowiskowej PATH dla danego systemu operacyjnego.

* Aby lokalnie uruchomić wyzwalacze i akcje oparte na elemencie webhook, takie jak [wbudowany wyzwalacz http webhook](../connectors/connectors-native-webhook.md), w Visual Studio Code należy [skonfigurować przekazywanie dla adresu URL wywołania zwrotnego](#webhook-setup).

* Aby przetestować przykładową aplikację logiki utworzoną w tym artykule, potrzebne jest narzędzie, które może wysyłać wywołania do wyzwalacza żądania, który jest pierwszym krokiem w przykładowej aplikacji logiki. Jeśli nie masz takiego narzędzia, możesz pobrać, zainstalować i użyć programu [Poster](https://www.postman.com/downloads/).

* Jeśli utworzysz aplikację logiki i wdrożono ją przy użyciu ustawień, które obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), możesz opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale można utworzyć ten zasób [z wyprzedzeniem](../azure-monitor/app/create-workspace-resource.md), podczas wdrażania aplikacji logiki lub po wdrożeniu.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migruj z prywatnego rozszerzenia podglądu

Wszystkie projekty aplikacji logiki utworzone przy użyciu rozszerzenia **Azure Logic Apps (Private Preview)** nie będą działały z rozszerzeniem publicznej wersji zapoznawczej. Można jednak migrować te projekty do nowych projektów, wykonując następujące czynności:

1. Odinstaluj rozszerzenie prywatnej wersji zapoznawczej.

1. Usuń wszystkie skojarzone pakiety rozszerzeń i foldery pakietów NuGet w następujących lokalizacjach:

   * Folder **Microsoft. Azure. Functions. ExtensionBundle.** workflows, który zawiera poprzednie zbiory rozszerzeń i znajduje się w ścieżce w tym miejscu:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Folder **Microsoft. Azure. Workflows. WebJobs. Extension** , który jest pamięcią podręczną [NuGet](/nuget/what-is-nuget) dla rozszerzenia prywatnej wersji zapoznawczej i znajduje się w tej ścieżce:

     `C:\Users\{userName}\.nuget\packages`

1. Zainstaluj rozszerzenie **Azure Logic Apps (wersja zapoznawcza)** .

1. Utwórz nowy projekt w Visual Studio Code.

1. Skopiuj wcześniej utworzony plik **definicji przepływu pracy** aplikacji logiki do nowego projektu.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

1. Aby upewnić się, że wszystkie rozszerzenia są prawidłowo zainstalowane, Załaduj ponownie lub Uruchom ponownie Visual Studio Code.

1. Upewnij się, że Visual Studio Code automatycznie wyszukuje i instaluje aktualizacje rozszerzeń, aby rozszerzenie wersji zapoznawczej pobiera najnowsze aktualizacje. W przeciwnym razie musisz ręcznie odinstalować nieaktualną wersję i zainstalować najnowszą wersję.

   1. W menu **plik** przejdź do pozycji **Preferencje** **>** **Ustawienia**.

   1. Na karcie **użytkownik** przejdź do pozycji **funkcje** **>** **rozszerzenia**.

   1. Upewnij się, że zaznaczone są **aktualizacje AutoCheck** i **Autoaktualizacja** .

Domyślnie następujące ustawienia są włączone i ustawiane dla rozszerzenia podglądu Logic Apps:

* **Azure Logic Apps v2: środowisko uruchomieniowe projektu**, które jest ustawione na wersję **~ 3**

  > [!NOTE]
  > Ta wersja jest wymagana do korzystania z [akcji operacji kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md).

* **Azure Logic Apps v2: Menedżer widoku doświadczalnego**, który umożliwia najnowszy projektant w programie Visual Studio Code. Jeśli wystąpią problemy z projektantem, na przykład przeciąganie i upuszczanie elementów, wyłącz to ustawienie.

Aby znaleźć i potwierdzić te ustawienia, wykonaj następujące kroki:

1. W menu **plik** przejdź do pozycji **Preferencje** **>** **Ustawienia**.

1. Na karcie **użytkownik** przejdź do pozycji **>** **rozszerzenia** **>** **Azure Logic Apps (wersja zapoznawcza)**.

   Na przykład można znaleźć w tym miejscu ustawienie **Azure Logic Apps v2: Project Runtime** lub użyć pola wyszukiwania, aby znaleźć inne ustawienia:

   ![Zrzut ekranu pokazujący ustawienia Visual Studio Code dla rozszerzenia "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure.

   ![Zrzut ekranu pokazujący pasek działania Visual Studio Code i wybraną ikonę platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. W okienku Azure w obszarze **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Zaloguj się do platformy Azure**. Gdy zostanie wyświetlona strona uwierzytelnianie Visual Studio Code, zaloguj się przy użyciu konta platformy Azure.

   ![Zrzut ekranu przedstawiający okienko platformy Azure i wybrany link do logowania do platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Po zalogowaniu się w okienku Azure wyświetlane są subskrypcje na koncie platformy Azure. Jeśli masz również publicznie wydane rozszerzenie, możesz znaleźć wszystkie aplikacje logiki utworzone za pomocą tego rozszerzenia w sekcji **Logic Apps** , a nie sekcję **Logic Apps (wersja zapoznawcza)** .
   
   Jeśli oczekiwane subskrypcje nie są wyświetlane lub chcesz, aby okienko pokazywało tylko określone subskrypcje, wykonaj następujące kroki:

   1. Na liście subskrypcje Przenieś wskaźnik obok pierwszej subskrypcji, aż zostanie wyświetlony przycisk **wybierz subskrypcje** (ikona filtru). Wybierz ikonę filtru.

      ![Zrzut ekranu pokazujący okienko platformy Azure i wybraną ikonę filtru.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Lub na pasku stanu Visual Studio Code wybierz swoje konto platformy Azure. 

   1. Gdy zostanie wyświetlona inna Lista subskrypcji, wybierz odpowiednie subskrypcje, a następnie upewnij się, że wybrano **przycisk OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Utwórz projekt lokalny

Przed utworzeniem aplikacji logiki Utwórz projekt lokalny, aby móc zarządzać, uruchamiać i wdrażać aplikację logiki z poziomu Visual Studio Code. Projekt źródłowy jest podobny do projektu Azure Functions, zwanego także projektem aplikacji funkcji. Jednak te typy projektów są niezależne od siebie, więc aplikacje logiki i aplikacje funkcji nie mogą znajdować się w tym samym projekcie.

1. Na komputerze Utwórz *pusty* folder lokalny do użycia w projekcie, który zostanie później utworzony w Visual Studio Code.

1. W Visual Studio Code Zamknij wszystkie otwarte foldery i wszystkie.

1. W okienku platformy Azure obok pozycji **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Utwórz nowy projekt** (ikona, która pokazuje folder i Piorun).

   ![Zrzut ekranu pokazujący pasek narzędzi okienka platformy Azure z wybranym elementem "Utwórz nowy projekt".](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Jeśli Zapora Windows Defender poprosi o przyznanie dostępu do sieci `Code.exe` , który jest Visual Studio Code, a w przypadku `func.exe` , czyli Azure Functions Core Tools, wybierz opcję **sieci prywatne, na przykład sieć domowa lub służbowa** **>** **zezwala na dostęp**.

1. Przejdź do lokalizacji, w której został utworzony folder projektu, wybierz ten folder i Kontynuuj.

   ![Zrzut ekranu pokazujący okno dialogowe Wybieranie folderu z nowo utworzonym folderem projektu i wybranym przyciskiem "Select".](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Z wyświetlonej listy szablonów wybierz opcję **stanowy przepływ pracy** lub **bezstanowy przepływ pracy**. Ten przykład wybiera **stanowy przepływ pracy**.

   ![Zrzut ekranu przedstawiający listę szablonów przepływu pracy z wybranym "stanem" przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Podaj nazwę przepływu pracy i naciśnij klawisz ENTER. Ten przykład używa `Fabrikam-Stateful-Workflow` jako nazwy.

   ![Zrzut ekranu przedstawiający pole "Utwórz nowy stanowy przepływ pracy (3/4)" i "Fabrikam-stanowy przepływ pracy" jako nazwę przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code zakończy tworzenie projektu i otwiera **workflow.jsna** pliku dla przepływu pracy w edytorze kodu.

   > [!NOTE]
   > Jeśli zostanie wyświetlony monit o wybranie sposobu otwierania projektu, wybierz pozycję **Otwórz w bieżącym oknie** , jeśli chcesz otworzyć projekt w bieżącym oknie Visual Studio Code. Aby otworzyć nowe wystąpienie Visual Studio Code, wybierz pozycję **Otwórz w nowym oknie**.

1. Na pasku narzędzi programu Visual Studio Otwórz okienko Eksploratora, jeśli nie zostało jeszcze otwarte.

   W okienku Eksploratora zostanie wyświetlony projekt, który zawiera teraz automatycznie generowane pliki projektu. Na przykład projekt zawiera folder, który zawiera nazwę przepływu pracy. W tym folderze **workflow.jsw** pliku zawiera podstawową definicję JSON przepływu pracy.

   ![Zrzut ekranu pokazujący okienko Eksploratora z folderem projektu, folderem przepływu pracy i plikiem "workflow.json".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Jeśli używasz usługi macOS lub Linux, Skonfiguruj dostęp do konta magazynu, wykonując następujące kroki, które są wymagane do lokalnego uruchamiania projektu:

   1. W folderze głównym projektu Otwórz **local.settings.js** pliku.

      ![Zrzut ekranu przedstawiający okienko Eksploratora i plik "local.settings.json" w projekcie.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Zamień `AzureWebJobsStorage` wartość właściwości na zapisane wcześniej parametry połączenia konta magazynu, na przykład:

      Przed:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Po:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

   1. Gdy wszystko będzie gotowe, upewnij się, że Zapisano zmiany.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Włącz funkcję tworzenia łączników wbudowanych

Możesz utworzyć własne wbudowane łączniki dla dowolnej usługi, której potrzebujesz, za pomocą [struktury rozszerzalności wersji zapoznawczej](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Podobnie jak wbudowane łączniki, takie jak Azure Service Bus i SQL Server, te łączniki zapewniają wyższą przepływność, małe opóźnienia, łączność lokalną i działają natywnie w tym samym procesie co środowisko uruchomieniowe w wersji zapoznawczej.

Możliwość tworzenia jest obecnie dostępna tylko w Visual Studio Code, ale nie jest domyślnie włączona. Aby utworzyć te łączniki, należy najpierw przekonwertować projekt z opartego na zbiorze rozszerzeń (Node.js) na oparty na pakiecie NuGet (.NET).

1. W okienku Eksploratora, w katalogu głównym projektu, Przenieś wskaźnik myszy nad pusty obszar poniżej wszystkich innych plików i folderów, otwórz menu skrótów i wybierz polecenie **Konwertuj na projekt aplikacji logiki opartej na NuGet**.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z menu skrótów projektu otwartego w pustym obszarze okna projektu.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Po wyświetleniu monitu potwierdź konwersję projektu.

1. Aby kontynuować, przejrzyj i wykonaj kroki opisane w artykule, [Azure Logic Apps uruchamianie rozszerzalności łączników wbudowanych wszędzie](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Otwórz plik definicji przepływu pracy w projektancie

1. Sprawdź wersje zainstalowane na komputerze, uruchamiając następujące polecenie:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Jeśli masz zestaw .NET Core SDK 5. x, ta wersja może uniemożliwić otwieranie podstawowej definicji przepływu pracy aplikacji logiki w projektancie. Zamiast odinstalowywać tę wersję, w folderze głównym projektu Utwórz **global.jsw** pliku, który odwołuje się do wersji środowiska uruchomieniowego platformy .NET Core 3. x, która jest nowsza niż 3.1.201, na przykład:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Upewnij się, że jawnie dodano **global.js** do pliku w folderze głównym projektu z wewnątrz Visual Studio Code. W przeciwnym razie Projektant nie zostanie otwarty.

1. Rozwiń folder projektu dla przepływu pracy. Otwórz menu skrótów pliku **workflow.js** i wybierz polecenie **Otwórz w projektancie**.

   ![Zrzut ekranu pokazujący okienko Eksploratora i okno skrótów dla workflow.jsw pliku z wybranym elementem "Otwórz w projektancie".](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Na liście **Włącz łączniki na platformie Azure** wybierz pozycję **Użyj łączników z platformy Azure**, która ma zastosowanie do wszystkich łączników zarządzanych dostępnych i wdrożonych na platformie Azure, a nie tylko łączników dla usług platformy Azure.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z wybraną listą "Włącz łączniki na platformie Azure" i "użyj łączników z platformy Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Bezstanowe przepływy pracy obsługują obecnie tylko *Akcje* dotyczące [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), które są wdrożone na platformie Azure, a nie są wyzwalane. Chociaż istnieje możliwość włączenia łączników na platformie Azure dla bezstanowego przepływu pracy, Projektant nie wyświetla żadnych wyzwalaczy łączników zarządzanych, które można wybrać.

1. Z listy **Wybierz subskrypcję** wybierz subskrypcję platformy Azure, która ma być używana dla projektu aplikacji logiki.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z polem "Wybierz subskrypcję" i wybraną subskrypcją.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Z listy grupy zasobów wybierz pozycję **Utwórz nową grupę zasobów**.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z listą grupy zasobów i wybraną opcją "Utwórz nową grupę zasobów".](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Podaj nazwę grupy zasobów, a następnie naciśnij klawisz ENTER. W tym przykładzie użyto wartości `Fabrikam-Workflows-RG`.

   ![Zrzut ekranu przedstawiający okienko Eksploratora i pole nazwy grupy zasobów.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Z listy lokalizacje Znajdź i wybierz region platformy Azure, który ma być używany podczas tworzenia grupy zasobów i zasobów. W tym przykładzie używane jest **zachodnie środkowe stany USA**.

   ![Zrzut ekranu pokazujący okienko Eksploratora z listą lokalizacji i wybraną pozycją "zachodnio-środkowe stany USA".](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Po wykonaniu tego kroku Visual Studio Code otwiera projektanta przepływu pracy.

   > [!NOTE]
   > Gdy Visual Studio Code uruchamia interfejs API czasu projektowania przepływu pracy, może zostać wyświetlony komunikat, że uruchomienie może potrwać kilka sekund. Możesz zignorować ten komunikat lub wybrać **przycisk OK**.
   >
   > Jeśli projektant nie zostanie otwarty, przejrzyj sekcję Rozwiązywanie problemów, [nie można otworzyć projektanta](#designer-fails-to-open).

   Po pojawieniu się projektanta w projektancie zostanie wyświetlony monit **Wybieranie operacji** , który jest domyślnie zaznaczony, co spowoduje wyświetlenie okienka **Dodawanie akcji** .

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Następnie [Dodaj wyzwalacz i akcje](#add-trigger-actions) do przepływu pracy.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Dodawanie wyzwalacza i akcji

Po otwarciu projektanta w projektancie zostanie wyświetlony monit **Wybieranie operacji** , który jest domyślnie wybrany. Teraz możesz zacząć tworzyć przepływy pracy, dodając wyzwalacz i akcje.

Przepływ pracy w tym przykładzie używa tego wyzwalacza i następujących akcji:

* Wbudowany [wyzwalacz żądania](../connectors/connectors-native-reqres.md), **gdy odebrane zostanie żądanie HTTP**, które odbiera wywołania przychodzące lub żądania i tworzy punkt końcowy, do którego mogą być wywoływane inne usługi lub Aplikacje logiki.

* [Akcja programu Outlook pakietu Office 365](../connectors/connectors-create-api-office365-outlook.md), **Wyślij wiadomość e-mail**.

* Wbudowana [Akcja odpowiedzi](../connectors/connectors-native-reqres.md)służąca do wysyłania odpowiedzi i powrotu danych z powrotem do obiektu wywołującego.

### <a name="add-the-request-trigger"></a>Dodawanie wyzwalacza żądania

1. Obok pola projektant w okienku **Dodawanie wyzwalacza** , w obszarze **Wybierz operację** wyszukiwania, upewnij się, że jest zaznaczona opcja **wbudowane** , aby można było wybrać wyzwalacz, który działa natywnie.

1. W polu wyszukiwania **Wybierz operację** wprowadź `when a http request` , a następnie wybierz wbudowany wyzwalacz żądania o nazwie, **gdy odebrane zostanie żądanie HTTP**.

   ![Zrzut ekranu, który pokazuje projektanta przepływu pracy i * * dodaje okienko wyzwalacz * * z wybranym wyzwalaczem "po odebraniu żądania HTTP".](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Gdy wyzwalacz pojawi się w projektancie, zostanie otwarte okienko Szczegóły wyzwalacza, aby wyświetlić właściwości wyzwalacza, ustawienia i inne akcje.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy z wybranym wyzwalaczem "po odebraniu żądania HTTP" i otwartym okienku szczegółów wyzwalacza.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Jeśli okienko szczegółów nie jest wyświetlane, upewnij się, że wyzwalacz został wybrany w projektancie.

1. Jeśli musisz usunąć element z projektanta, [wykonaj następujące kroki w celu usunięcia elementów z projektanta](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Dodawanie akcji programu Outlook pakietu Office 365

1. W projektancie w obszarze dodany wyzwalacz wybierz pozycję **nowy krok**.

   Zostanie wyświetlony monit **Wybierz operację** w projektancie, a okienko **Dodawanie akcji** zostanie otwarte ponownie, aby można było wybrać następną akcję.

1. W okienku **Dodawanie akcji** w polu wyszukiwania **Wybierz operację** wybierz pozycję **Azure** , aby można było znaleźć i wybrać akcję dla łącznika zarządzanego wdrożonego na platformie Azure.

   Ten przykład wybiera i używa akcji programu Outlook pakietu Office 365, **Wyślij wiadomość e-mail (wersja 2)**.

   ![Zrzut ekranu, który pokazuje projektanta przepływu pracy i * * Dodaj akcję * * okienko z pakietem Office 365 Outlook "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. W okienku szczegółów akcji wybierz pozycję **Zaloguj się** , aby można było utworzyć połączenie z kontem e-mail.

   ![Zrzut ekranu pokazujący projektanta przepływu pracy i * * Wyślij wiadomość e-mail (v2) * * z wybraną opcją "Zaloguj się".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Gdy Visual Studio Code zostanie wyświetlony komunikat z prośbą o zgodę na dostęp do Twojego konta e-mail, wybierz pozycję **Otwórz**.

   ![Zrzut ekranu pokazujący monit Visual Studio Code, aby zezwolić na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Aby zapobiec wyświetlaniu w przyszłości, wybierz opcję **Konfiguruj zaufane domeny** , aby można było dodać stronę uwierzytelniania jako domenę zaufaną.

1. Postępuj zgodnie z kolejnymi monitami, aby się zalogować, Zezwól na dostęp i Zezwalaj na powrót do Visual Studio Code.

   > [!NOTE]
   > Jeśli zbyt dużo czasu upłynie przed ukończeniem wyświetlania, przekroczenie limitu czasu procesu uwierzytelniania i zakończy się niepowodzeniem. W takim przypadku Wróć do projektanta i ponów próbę zalogowania się, aby utworzyć połączenie.

1. Gdy rozszerzenie Azure Logic Apps (wersja zapoznawcza) wyświetli komunikat z prośbą o zgodę na dostęp do Twojego konta e-mail, wybierz pozycję **Otwórz**. Postępuj zgodnie z kolejnym monitem, aby zezwolić na dostęp.

   ![Zrzut ekranu pokazujący monit o rozszerzenie podglądu, aby zezwolić na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Aby zapobiec wyświetlaniu w przyszłości, wybierz opcję **nie Monituj ponownie dla tego rozszerzenia**.

   Po Visual Studio Code utworzeniu połączenia w niektórych łącznikach zostanie wyświetlony komunikat `The connection will be valid for {n} days only` . Ten limit czasu dotyczy tylko czasu trwania podczas tworzenia aplikacji logiki w Visual Studio Code. Po wdrożeniu ten limit nie obowiązuje, ponieważ aplikacja logiki może uwierzytelnić się w czasie wykonywania przy użyciu automatycznie włączonej [tożsamości zarządzanej przypisanej przez system](../logic-apps/create-managed-service-identity.md). Ta tożsamość zarządzana różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę tożsamość zarządzaną przypisaną przez system, połączenia nie będą działały w czasie wykonywania.

1. W projektancie, jeśli Akcja **Wyślij wiadomość e-mail** nie jest zaznaczona, wybierz tę akcję.

1. W okienku Szczegóły akcji na karcie **Parametry** podaj wymagane informacje dotyczące akcji, na przykład:

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy ze szczegółowymi informacjami dotyczącymi pakietu Office 365 Outlook "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*adres e-mail użytkownika*> | Odbiorca wiadomości e-mail, który może być Twoim adresem e-mail do celów testowych. Ten przykład używa fikcyjnej poczty e-mail `sophiaowen@fabrikam.com` . |
   | **Temat** | Tak | `An email from your example workflow` | Temat wiadomości e-mail |
   | **Treść** | Tak | `Hello from your example workflow!` | Zawartość wiadomości e-mail |
   ||||

   > [!NOTE]
   > Jeśli chcesz wprowadzić zmiany w okienku szczegółów na karcie **Ustawienia**, **wynik statyczny** lub **Uruchom po** zakładce, upewnij się, że wybrano pozycję **gotowe** , aby zatwierdzić te zmiany przed przełączeniem kart lub zmianę fokusu projektanta. W przeciwnym razie Visual Studio Code nie będą zachować zmian. Aby uzyskać więcej informacji, zapoznaj się ze [stroną usługi Logic Apps Public Preview znane problemy w witrynie GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. W Projektancie wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Aby lokalnie uruchomić przepływ pracy, który używa wyzwalacza lub akcji opartych na elemencie webhook, takich jak [wbudowany wyzwalacz lub akcja elementu webhook protokołu HTTP](../connectors/connectors-native-webhook.md), należy włączyć tę funkcję przez [skonfigurowanie przesyłania dalej dla adresu URL wywołania zwrotnego elementu webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Włącz lokalnie uruchomione elementy webhook

W przypadku użycia wyzwalacza lub akcji opartej na elemencie webhook, takiej jak **element webhook protokołu HTTP**, z aplikacją logiki działającej na platformie Azure, środowisko uruchomieniowe Logic Apps subskrybuje punkt końcowy usługi przez wygenerowanie i zarejestrowanie adresu URL wywołania zwrotnego z tym punktem końcowym. Wyzwalacz lub akcja czeka, aż punkt końcowy usługi wywoła adres URL. Jednak podczas pracy w Visual Studio Code wygenerowany adres URL wywołania zwrotnego zaczyna się od `http://localhost:7071/...` . Ten adres URL jest przeznaczony dla serwera localhost, który jest prywatny, więc punkt końcowy usługi nie może wywołać tego adresu URL.

Aby lokalnie uruchomić wyzwalacze i akcje oparte na elementach webhook w Visual Studio Code, należy skonfigurować publiczny adres URL, który ujawnia serwer localhost i bezpiecznie przekazuje wywołania z punktu końcowego usługi do adresu URL wywołania zwrotnego elementu webhook. Możesz użyć usługi i narzędzia przekazującego, takiego jak [**ngrok**](https://ngrok.com/), co spowoduje otwarcie tunelu http na porcie hosta lokalnego lub użycie własnego narzędzia.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Skonfiguruj przekazywanie połączeń przy użyciu **ngrok**

1. [Utwórz konto usługi **ngrok**](https://dashboard.ngrok.com/signup) , jeśli go nie masz. W przeciwnym razie [Zaloguj się do swojego konta](https://dashboard.ngrok.com/login).

1. Pobierz osobisty token uwierzytelniania, którego klient **ngrok** musi połączyć się i uwierzytelnić dostęp do konta.

   1. Aby znaleźć [stronę tokenu uwierzytelniania](https://dashboard.ngrok.com/auth/your-authtoken), w menu pulpitu nawigacyjnego konta rozwiń węzeł **uwierzytelnianie** i wybierz pozycję **authToken**.

   1. W polu **authToken** Skopiuj token do bezpiecznej lokalizacji.

1. Na [stronie pobierania **ngrok**](https://ngrok.com/download) lub [na pulpicie nawigacyjnym konta](https://dashboard.ngrok.com/get-started/setup)Pobierz żądaną wersję **ngrok** i wyodrębnij plik. zip. Aby uzyskać więcej informacji, zobacz krok 1. rozpakowywanie [do zainstalowania](https://ngrok.com/download).

1. Na komputerze otwórz narzędzie wiersza polecenia. Przejdź do lokalizacji, w której znajduje się plik **ngrok.exe** .

1. Aby połączyć klienta **ngrok** z kontem **ngrok** , należy uruchomić następujące polecenie. Aby uzyskać więcej informacji, zobacz [krok 2. Łączenie konta](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Otwórz tunel HTTP do portu localhost 7071, uruchamiając następujące polecenie. Aby uzyskać więcej informacji, zobacz [krok 3. Wyzwalanie](https://ngrok.com/download).

   `ngrok http 7071`

1. W danych wyjściowych znajdź następujący wiersz:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Skopiuj i Zapisz adres URL, który ma następujący format: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Konfigurowanie adresu URL przekazywania w ustawieniach aplikacji

1. W Visual Studio Code, w projektancie, Dodaj wyzwalacz **http + element webhook** lub akcję.

1. Gdy pojawi się monit dla lokalizacji punktu końcowego hosta, wprowadź wcześniej utworzony adres URL przekazywania (przekierowania).

   > [!NOTE]
   > Ignorowanie monitu powoduje wyświetlenie ostrzeżenia o tym, że należy podać adres URL przekazywania, więc wybierz pozycję **Konfiguruj**, a następnie wprowadź adres URL. Po zakończeniu tego kroku monit nie będzie wyświetlany w kolejnych wyzwalaczach lub akcjach elementu webhook, które mogą zostać dodane.
   >
   > Aby monit pojawił się ponownie, na poziomie głównym projektu, otwórz menu skrótów pliku **local.settings.js** , a następnie wybierz pozycję **Konfiguruj punkt końcowy przekierowania elementu webhook**. Zostanie wyświetlony monit o podanie adresu URL przekazywania.

   Visual Studio Code dodaje adres URL przekazywania do **local.settings.js** w pliku w folderze głównym projektu. W `Values` obiekcie jest wyświetlana właściwość o nazwie `Workflows.WebhookRedirectHostUri` teraz, która jest ustawiana na adres URL przekazywania, na przykład:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Po raz pierwszy po rozpoczęciu sesji debugowania lokalnego lub uruchomieniu przepływu pracy bez debugowania, środowisko uruchomieniowe Logic Apps rejestruje przepływ pracy w punkcie końcowym usługi i subskrybuje ten punkt końcowy w celu powiadomienia o operacjach elementu webhook. Przy następnym uruchomieniu przepływu pracy środowisko uruchomieniowe nie zarejestruje ani nie ponownie subskrybuje, ponieważ rejestracja subskrypcji już istnieje w magazynie lokalnym.

Po zatrzymaniu sesji debugowania dla przebiegu przepływu pracy, który używa lokalnie uruchamianych wyzwalaczy lub akcji opartych na elemencie webhook, istniejące rejestracje subskrypcji nie są usuwane. Aby wyrejestrować, musisz ręcznie usunąć lub usunąć rejestracje subskrypcji.

> [!NOTE]
> Po uruchomieniu przepływu pracy w oknie terminalu mogą być wyświetlane błędy podobne do tego przykładu:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> W takim przypadku Otwórz **local.settings.jsw** pliku w folderze głównym projektu i upewnij się, że właściwość jest ustawiona na `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Zarządzanie punktami przerwania na potrzeby debugowania

Przed uruchomieniem i testowaniem przepływu pracy aplikacji logiki przez uruchomienie sesji debugowania można ustawić [punkty przerwania](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) wewnątrz **workflow.jsw** pliku dla każdego przepływu pracy. Nie jest wymagana żadna inna konfiguracja. 

W tej chwili punkty przerwania są obsługiwane tylko dla akcji, a nie wyzwalaczy. Każda definicja akcji ma następujące lokalizacje punktów przerwania:

* Ustaw początkowy punkt przerwania w wierszu, który pokazuje nazwę akcji. Gdy ten punkt przerwania trafi podczas sesji debugowania, możesz przejrzeć dane wejściowe akcji przed ich oceną.

* Ustaw końcowy punkt przerwania w wierszu, który pokazuje zamykające nawiasy klamrowe akcji (**}**). Gdy ten punkt przerwania trafi podczas sesji debugowania, możesz przejrzeć wyniki akcji przed zakończeniem działania.

Aby dodać punkt przerwania, wykonaj następujące kroki:

1. Otwórz **workflow.jsw** pliku dla przepływu pracy, który chcesz debugować.

1. W wierszu, w którym ma zostać ustawiony punkt przerwania, w lewej kolumnie Wybierz wewnątrz tej kolumny. Aby usunąć punkt przerwania, wybierz ten punkt przerwania.

   Po uruchomieniu sesji debugowania widok przebiegu pojawia się po lewej stronie okna kod, podczas gdy pasek narzędzi debugowania pojawia się u góry.

   > [!NOTE]
   > Jeśli widok uruchamiania nie pojawia się automatycznie, naciśnij klawisze CTRL + SHIFT + D.

1. Aby przejrzeć dostępne informacje, gdy trafień punktu przerwania, w widoku uruchamiania Sprawdź **zmienne** okienka.

1. Aby kontynuować wykonywanie przepływu pracy, na pasku narzędzi debugowania wybierz pozycję **Kontynuuj** (przycisk odtwarzania). 

Punkty przerwania można dodawać i usuwać w dowolnym momencie podczas przebiegu przepływu pracy. Jednakże w przypadku aktualizacji **workflow.jsw** pliku po rozpoczęciu uruchamiania punkty przerwania nie są automatycznie aktualizowane. Aby zaktualizować punkty przerwania, uruchom ponownie aplikację logiki.

Aby uzyskać ogólne informacje, zobacz [punkty przerwania — Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Uruchom, Testuj i Debuguj lokalnie

Aby przetestować aplikację logiki, wykonaj następujące kroki, aby rozpocząć sesję debugowania, a następnie znajdź adres URL dla punktu końcowego, który został utworzony przez wyzwalacz żądania. Ten adres URL będzie potrzebny do późniejszego wysłania żądania do tego punktu końcowego.

1. Aby ułatwić debugowanie bezstanowego przepływu pracy, można [włączyć historię uruchamiania dla tego przepływu pracy](#enable-run-history-stateless).

1. Na pasku działania Visual Studio Code Otwórz menu **Uruchom** , a następnie wybierz polecenie **Rozpocznij debugowanie** (F5).

   Zostanie otwarte okno **terminalu** umożliwiające przejrzenie sesji debugowania.

   > [!NOTE]
   > Jeśli wystąpi błąd, **"błąd istnieje po uruchomieniu preLaunchTask" generateDebugSymbols ""**, zobacz sekcję Rozwiązywanie problemów. [nie można uruchomić sesji debugowania](#debugging-fails-to-start).

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

   1. W obszarze **Wybierz kolekcję lub folder, w którym chcesz zapisać**, wybierz pozycję **Utwórz kolekcję**.

   1. W obszarze **wszystkie kolekcje** Podaj nazwę kolekcji, która ma zostać utworzona do organizowania żądań, naciśnij klawisz ENTER, a następnie wybierz pozycję **zapisz, aby <*nazwę* > kolekcji**. Ten przykład używa `Logic Apps requests` jako nazwy kolekcji.

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
   | **Uruchomienie** | Uruchomienie zostało wyzwolone i jest w toku, ale ten stan może również pojawić się dla przebiegu, który jest ograniczany ze względu na [limity akcji](logic-apps-limits-and-config.md) lub [bieżący plan cenowy](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Porada**: w przypadku skonfigurowania [rejestrowania diagnostycznego](monitor-logic-apps-log-analytics.md)można uzyskać informacje o wszelkich zdarzeniach związanych z ograniczaniem. |
   | **Powodzenie** | Przebieg zakończył się pomyślnie. Jeśli jakakolwiek akcja zakończyła się niepowodzeniem, kolejna Akcja w przepływie pracy obsłuży ten błąd. |
   | **Przekroczono limit czasu** | Przekroczono limit czasu uruchamiania, ponieważ bieżący czas trwania przekracza limit czasu trwania przebiegu, który jest kontrolowany przez [ustawienie **przechowywania historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits). Czas trwania przebiegu jest obliczany przy użyciu limitu czasu rozpoczęcia i czas trwania uruchomienia w tym czasie. <p><p>**Uwaga**: Jeśli czas trwania przebiegu przekracza *Limit przechowywania bieżącej historii przebiegów*, który jest również kontrolowany przez ustawienie trwa [ **przechowywanie historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits), uruchomienie jest usuwane z historii uruchamiania przez codzienne zadanie oczyszczania. Bez względu na to, czy czas trwania lub zakończeniu przebiegu, okres przechowywania jest zawsze obliczany przy użyciu czasu rozpoczęcia i *bieżącego* limitu przechowywania. W związku z tym, jeśli skracasz limit czasu trwania dla uruchomienia w locie, przekroczenie limitu czasu przebiegu. Jednak przebieg jest wyświetlany lub zostaje usunięty z historii uruchamiania w zależności od tego, czy czas trwania przebiegu przekroczył limit przechowywania. |
   | **Oczekiwanie** | Uruchomienie nie zostało uruchomione lub zostało wstrzymane, na przykład z powodu wcześniejszego wystąpienia przepływu pracy, które nadal działa. |
   |||

1. Aby sprawdzić stan każdego kroku w określonym przebiegu oraz dane wejściowe i wyjściowe kroku, wybierz przycisk wielokropka (**...**) dla tego przebiegu, a następnie wybierz polecenie **Pokaż uruchomienie**.

   ![Zrzut ekranu pokazujący wiersz historii przebiegu przepływu pracy z wielokropkiem i wybraną opcją "Pokaż przebieg"](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code otwiera widok Monitorowanie i pokazuje stan każdego kroku w przebiegu.

   ![Zrzut ekranu pokazujący każdy krok w przebiegu przepływu pracy i ich stan](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Jeśli uruchomienie nie powiodło się, a krok w widoku monitorowania pokazuje `400 Bad Request` błąd, ten problem może wynikać z dłuższej nazwy wyzwalacza lub nazwy akcji, która powoduje, że bazowy Uniform Resource Identifier (URI) przekroczy domyślny limit znaków. Aby uzyskać więcej informacji, zobacz ["400 Nieprawidłowe żądanie"](#400-bad-request).

   Oto możliwe stany, które każdy krok w przepływie pracy może:

   | Stan akcji | Ikona | Opis |
   |---------------|------|-------------|
   | **Zostało przerwane** | ![Ikona stanu akcji "przerwane"][aborted-icon] | Akcja została zatrzymana lub nie została zakończona z powodu problemów zewnętrznych, na przykład awarii systemu lub subskrypcji platformy Azure. |
   | **Zerwan** | ![Ikona stanu akcji "anulowana"][cancelled-icon] | Akcja była uruchomiona, ale odebrała żądanie anulowania. |
   | **Niepowodzenie** | ![Ikona stanu akcji "Niepowodzenie"][failed-icon] | Akcja nie powiodła się. |
   | **Uruchomienie** | ![Ikona stanu akcji "uruchomiona"][running-icon] | Akcja jest obecnie uruchomiona. |
   | **Pominięto** | ![Ikona stanu akcji "pominięto"][skipped-icon] | Akcja została pominięta, ponieważ Poprzednia akcja nie powiodła się. Akcja ma `runAfter` warunek, który wymaga, aby poprzednia akcja została zakończona pomyślnie, zanim będzie można uruchomić bieżącą akcję. |
   | **Powodzenie** | ![Ikona stanu akcji "powodzenie"][succeeded-icon] | Akcja zakończyła się pomyślnie. |
   | **Powodzenie z ponownymi próbami** | ![Ikona stanu akcji "powodzenie z ponownymi próbami"][succeeded-with-retries-icon] | Akcja zakończyła się powodzeniem, ale tylko po wykonaniu jednej lub kilku ponownych prób. Aby przejrzeć historię ponownych prób, w widoku szczegółów historii uruchamiania wybierz tę akcję, aby wyświetlić dane wejściowe i wyjściowe. |
   | **Przekroczono limit czasu** | ![Ikona stanu akcji "Przekroczono limit czasu"][timed-out-icon] | Akcja została zatrzymana z powodu przekroczenia limitu czasu określonego przez ustawienia tej akcji. |
   | **Oczekiwanie** | ![Ikona stanu akcji "oczekiwanie"][waiting-icon] | Dotyczy akcji elementu webhook, która oczekuje na żądanie przychodzące od wywołującego. |
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

## <a name="return-a-response"></a>Zwracanie odpowiedzi

Aby zwrócić odpowiedź do obiektu wywołującego, który wysłał żądanie do aplikacji logiki, można użyć wbudowanej [akcji odpowiedzi](../connectors/connectors-native-reqres.md) dla przepływu pracy, który rozpoczyna się od wyzwalacza żądania.

1. W Projektancie przepływu pracy w obszarze **Wyślij wiadomość e-mail** wybierz pozycję **nowy krok**.

   Zostanie wyświetlony monit **Wybierz operację** w projektancie, a **okienko Dodawanie akcji** zostanie otwarte ponownie, aby można było wybrać następną akcję.

1. W okienku **Dodaj akcję** , w polu wyszukiwania **Wybierz akcję** upewnij się, że jest zaznaczona opcja **wbudowane** . W polu wyszukiwania wpisz `response` , a następnie wybierz akcję **odpowiedź** .

   ![Zrzut ekranu pokazujący projektanta przepływu pracy z wybraną akcją odpowiedź.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Gdy akcja **odpowiedzi** pojawi się w projektancie, zostanie automatycznie otwarta okienko Szczegóły akcji.

   ![Zrzut ekranu, który pokazuje projektanta przepływu pracy z otwartym okienkiem akcji "odpowiedź", i właściwość "treść" ma ustawioną wartość właściwości "treść" wysyłania wiadomości e-mail.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

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

Po wprowadzeniu aktualizacji aplikacji logiki można uruchomić inny test, ponownie uruchamiając debuger w programie Visual Studio i wysyłając kolejne żądanie, aby wyzwolić zaktualizowaną aplikację logiki, podobnie jak w przypadku [uruchamiania, testowania i debugowania lokalnie](#run-test-debug-locally).

1. Na pasku działania Visual Studio Code Otwórz menu **Uruchom** , a następnie wybierz polecenie **Rozpocznij debugowanie** (F5).

1. Wyślij kolejne żądanie, aby wyzwolić przepływ pracy w programie Poster lub w narzędziu do tworzenia i wysyłania żądań.

1. Jeśli utworzono stanowy przepływ pracy, na stronie Przegląd przepływu pracy Sprawdź stan ostatniego uruchomienia. Aby wyświetlić stan, dane wejściowe i wyjściowe dla każdego kroku w tym przebiegu, wybierz przycisk wielokropka (**...**) dla tego przebiegu, a następnie wybierz polecenie **Pokaż uruchomienie**.

   Na przykład poniżej przedstawiono stan krok po kroku dla uruchomienia po zaktualizowaniu przykładowego przepływu pracy przy użyciu akcji odpowiedzi.

   ![Zrzut ekranu pokazujący stan każdego kroku w zaktualizowanym przepływie pracy oraz dane wejściowe i wyjściowe w rozwiniętej akcji "Response".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Aby zatrzymać sesję debugowania, w menu **Uruchom** wybierz polecenie **Zatrzymaj debugowanie** (Shift + F5).

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

W Visual Studio Code możesz bezpośrednio opublikować projekt na platformie Azure, który wdraża aplikację logiki przy użyciu nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** . Podobnie jak w przypadku zasobu aplikacji funkcji w Azure Functions, wdrożenie dla tego nowego typu zasobu wymaga wybrania [planu hostingu i warstwy cenowej](../app-service/overview-hosting-plans.md), którą można skonfigurować podczas wdrażania. Aby uzyskać więcej informacji o planach hostingu i cenach, zapoznaj się z następującymi tematami:

* [Skalowanie w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)

Aplikację logiki można opublikować jako nowy zasób, co spowoduje automatyczne utworzenie wszelkich niezbędnych zasobów, takich jak [konto usługi Azure Storage, podobne do wymagań aplikacji funkcji](../azure-functions/storage-considerations.md). Możesz również opublikować aplikację logiki w wcześniej wdrożonym zasobie **aplikacji logiki (wersja zapoznawcza)** , która zastępuje tę aplikację logiki.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publikowanie w nowym zasobie aplikacji logiki (wersja zapoznawcza)

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure.

1. Na pasku narzędzi okienka **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Wdróż do aplikacji logiki**.

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i pasek narzędzi okienka z wybraną opcją "wdróż w aplikacji logiki".](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Jeśli zostanie wyświetlony monit, wybierz subskrypcję platformy Azure, która ma być używana na potrzeby wdrożenia aplikacji logiki.

1. Z wyświetlonej Visual Studio Code listy wybierz jedną z następujących opcji:

   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure** (szybka)
   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure Advanced**
   * Wcześniej wdrożony zasób **aplikacji logiki (wersja zapoznawcza)** , jeśli istnieje

   Ten przykład kontynuuje **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure Advanced**.

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" z listą z wybraną opcją "Utwórz nową aplikację logiki (wersja zapoznawcza) na platformie Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Aby utworzyć nowy zasób **aplikacji logiki (wersja zapoznawcza)** , wykonaj następujące kroki:

   1. Podaj globalnie unikatową nazwę nowej aplikacji logiki, która jest nazwą używaną dla zasobu **aplikacji logiki (wersja zapoznawcza)** . W tym przykładzie użyto wartości `Fabrikam-Workflows-App`.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" oraz monit o podanie nazwy nowej aplikacji logiki do utworzenia.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Wybierz [Plan hostingu](../app-service/overview-hosting-plans.md) dla nowej aplikacji logiki, [ **App Service plan** (dedykowany)](../azure-functions/dedicated-plan.md) lub [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > Plany zużycia nie są obsługiwane ani niedostępne dla tego typu zasobu. Wybrany plan ma wpływ na możliwości i warstwy cenowe, które są później dostępne dla Ciebie. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami: 
      >
      > * [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)
      > * [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Na przykład plan Premium zapewnia dostęp do funkcji sieciowych, takich jak łączenie i integrowanie prywatnie z sieciami wirtualnymi platformy Azure, podobnie jak Azure Functions podczas tworzenia i wdrażania aplikacji logiki. 
      > Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:
      > 
      > * [Opcje sieciowe usługi Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps uruchamianie dowolnych możliwości sieciowych za pomocą wersji zapoznawczej Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      Ten przykład używa **planu App Service**.

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

   1. Jeśli ustawienia tworzenia i wdrażania aplikacji logiki obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), można opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale można utworzyć ten zasób [z wyprzedzeniem](../azure-monitor/app/create-workspace-resource.md), podczas wdrażania aplikacji logiki lub po wdrożeniu.

      Aby włączyć rejestrowanie i śledzenie teraz, wykonaj następujące kroki:

      1. Wybierz istniejący zasób Application Insights lub **Utwórz nowy zasób Application Insights**.

      1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu Application Insights.

      1. W menu zasób wybierz pozycję **Przegląd**. Znajdź i skopiuj wartość **klucza Instrumentacji** .

      1. W Visual Studio Code w folderze głównym projektu Otwórz **local.settings.jsna** pliku.

      1. W `Values` obiekcie Dodaj `APPINSIGHTS_INSTRUMENTATIONKEY` Właściwość i ustaw wartość na klucz instrumentacji, na przykład:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Możesz sprawdzić, czy nazwy wyzwalacza i akcji są poprawnie wyświetlane w wystąpieniu Application Insights.
         >
         > 1. W Azure Portal przejdź do zasobu Application Insights.
         >
         > 2. W menu zasób zasobów w obszarze **Zbadaj** wybierz pozycję **Mapa aplikacji**.
         >
         > 3. Przejrzyj nazwy operacji, które są wyświetlane na mapie.
         >
         > Niektóre żądania przychodzące z wbudowanych wyzwalaczy mogą pojawić się jako duplikaty w mapie aplikacji. 
         > Zamiast korzystać z tego `WorkflowName.ActionName` formatu te duplikaty używają nazwy przepływu pracy jako nazwy operacji i pochodzą od hosta Azure Functions.

      1. Następnie można opcjonalnie dostosować poziom ważności danych śledzenia, które aplikacja logiki zbiera i wysyła do wystąpienia Application Insights.

         Za każdym razem, gdy wystąpi zdarzenie związane z przepływem pracy, na przykład gdy zostanie wyzwolony przepływ pracy lub gdy akcja zostanie uruchomiona, środowisko uruchomieniowe emituje różne ślady. Te ślady obejmują okres istnienia przepływu pracy i obejmują, ale nie są ograniczone do, następujących typów zdarzeń:

         * Działanie usługi, takie jak uruchamianie, zatrzymywanie i błędy.
         * Działanie zadania i dyspozytora.
         * Działanie przepływu pracy, takie jak wyzwalacz, Akcja i uruchomienie.
         * Działanie żądania magazynu, takie jak powodzenie lub niepowodzenie.
         * Działanie żądania HTTP, takie jak przychodzące, wychodzące, sukces i niepowodzenie.
         * Wszystkie ślady rozwojowe, takie jak komunikaty debugowania.

         Każdy typ zdarzenia jest przypisany do poziomu ważności. Na przykład `Trace` poziom przechwytuje najbardziej szczegółowe komunikaty, a `Information` poziom przechwytuje ogólne działanie w przepływie pracy, np. gdy aplikacja logiki, przepływ pracy, wyzwalacz i akcje zostały uruchomione i zatrzymane. W tej tabeli opisano poziomy ważności i ich typy śledzenia:

         | Poziom ważności | Typ śledzenia |
         |----------------|------------|
         | Krytyczne | Dzienniki opisujące nieodwracalny błąd w aplikacji logiki. |
         | Debugowanie | Dzienniki, których można użyć do badania podczas opracowywania, na przykład przychodzące i wychodzące wywołania HTTP. |
         | Błąd | Dzienniki wskazujące niepowodzenie wykonywania przepływu pracy, ale nie ogólny błąd w aplikacji logiki. |
         | Informacje | Dzienniki, które śledzą działanie ogólne w aplikacji logiki lub przepływie pracy, na przykład: <p><p>— Gdy wyzwalacz, Akcja lub przebieg zostanie uruchomiony i zostanie zakończona. <br>— Gdy aplikacja logiki zostanie uruchomiona lub zostanie zakończona. |
         | Ślad | Dzienniki zawierające najbardziej szczegółowe komunikaty, na przykład żądania magazynu lub działania dyspozytora oraz wszystkie komunikaty powiązane z działaniem przepływu pracy. |
         | Ostrzeżenie | Dzienniki z wyróżnionym nietypowym stanem w aplikacji logiki, ale nie uniemożliwiają jego działania. |
         |||

         Aby ustawić poziom ważności na poziomie głównym projektu, Otwórz **host.jsw** pliku i Znajdź `logging` obiekt. Ten obiekt kontroluje filtrowanie dzienników dla wszystkich przepływów pracy w aplikacji logiki i postępuje zgodnie z [układem ASP.NET Core na potrzeby filtrowania typów dzienników](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Jeśli `logging` obiekt nie zawiera `logLevel` obiektu, który zawiera `Host.Triggers.Workflow` Właściwość, Dodaj te elementy. Ustaw właściwość na poziom ważności dla żądanego typu śledzenia, na przykład:

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

   Po wykonaniu kroków wdrażania Visual Studio Code rozpoczyna tworzenie i wdrażanie zasobów niezbędnych do opublikowania aplikacji logiki.

1. Aby przejrzeć i monitorować proces wdrażania, w menu **Widok** wybierz pozycję **dane wyjściowe**. Z listy pasków narzędzi okna dane wyjściowe wybierz pozycję **Azure Logic Apps**.

   ![Zrzut ekranu pokazujący okno danych wyjściowych z opcją "Azure Logic Apps" wybraną na liście pasków narzędzi wraz z postępem i Stanami wdrożenia.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Po zakończeniu Visual Studio Code wdrożenia aplikacji logiki na platformie Azure zostanie wyświetlony następujący komunikat:

   ![Zrzut ekranu pokazujący komunikat, że pomyślnie ukończono wdrażanie wdrożenia na platformie Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulacje, Twoja aplikacja logiki jest teraz aktywna na platformie Azure i domyślnie włączona.

Następnie można dowiedzieć się, jak wykonywać następujące zadania:

* [Dodaj pusty przepływ pracy do projektu](#add-workflow-existing-project).

* [Zarządzaj wdrożonymi aplikacjami logiki w Visual Studio Code](#manage-deployed-apps-vs-code) lub przy użyciu [Azure Portal](#manage-deployed-apps-portal).

* [Włącz historię uruchamiania na bezstanowych przepływach pracy](#enable-run-history-stateless).

* [Włącz widok monitorowania w Azure Portal dla wdrożonej aplikacji logiki](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Dodawanie pustego przepływu pracy do projektu

W projekcie aplikacji logiki można mieć wiele przepływów pracy. Aby dodać pusty przepływ pracy do projektu, wykonaj następujące kroki:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure.

1. W okienku platformy Azure obok pozycji **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Utwórz przepływ pracy** (ikona dla Azure Logic Apps).

1. Wybierz typ przepływu pracy, który chcesz dodać: **stanowe** lub **bezstanowe**

1. Podaj nazwę dla przepływu pracy.

Gdy wszystko będzie gotowe, w projekcie zostanie wyświetlony nowy folder przepływu pracy zawierający **workflow.jsw** pliku dla definicji przepływu pracy.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Zarządzanie wdrożonymi aplikacjami logiki w programie Visual Studio Code

W Visual Studio Code można wyświetlić wszystkie wdrożone Aplikacje logiki w ramach subskrypcji platformy Azure, niezależnie od tego, czy są to oryginalne **Logic Apps** , typ zasobu **aplikacji logiki (wersja zapoznawcza)** , a także wybrać zadania, które ułatwiają zarządzanie tymi aplikacjami logiki. Aby jednak uzyskać dostęp do obu typów zasobów, potrzebne są zarówno **Azure Logic Apps** , jak i **Azure Logic Apps (wersja zapoznawcza)** rozszerzeń Visual Studio Code.

1. Na pasku narzędzi po lewej stronie wybierz ikonę platformy Azure. W okienku **Azure: Logic Apps (wersja zapoznawcza)** Rozwiń swoją subskrypcję, która zawiera wszystkie wdrożone Aplikacje logiki dla tej subskrypcji.

1. Otwórz aplikację logiki, którą chcesz zarządzać. Z menu skrótów aplikacji logiki wybierz zadanie, które chcesz wykonać.

   Można na przykład wybrać zadania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie lub usuwanie wdrożonej aplikacji logiki.

   ![Zrzut ekranu, który pokazuje Visual Studio Code z otwartym okienkiem rozszerzenia "Azure Logic Apps (wersja zapoznawcza)" i wdrożonym przepływem pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Aby wyświetlić wszystkie przepływy pracy w aplikacji logiki, rozwiń aplikację logiki, a następnie rozwiń węzeł **przepływy pracy** .

1. Aby wyświetlić konkretny przepływ pracy, otwórz menu skrótów przepływu pracy i wybierz polecenie **Otwórz w projektancie**, co spowoduje otwarcie przepływu pracy w trybie tylko do odczytu.

   Aby edytować przepływ pracy, dostępne są następujące opcje:

   * W Visual Studio Code Otwórz **workflow.jsprojektu na** pliku w Projektancie przepływów pracy, wprowadź zmiany i Wdróż ponownie aplikację logiki na platformie Azure.

   * W Azure Portal [Znajdź i Otwórz aplikację logiki](#manage-deployed-apps-portal). Znajdź, Edytuj i Zapisz przepływ pracy.

1. Aby otworzyć wdrożoną aplikację logiki w Azure Portal, otwórz menu skrótów aplikacji logiki i wybierz polecenie **Otwórz w portalu**.

   Azure Portal zostanie otwarta w przeglądarce, program automatycznie zaloguje się do portalu, jeśli zalogujesz się do Visual Studio Code i zobaczysz aplikację logiki.

   ![Zrzut ekranu przedstawiający stronę Azure Portal aplikacji logiki w programie Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Możesz również zalogować się niezależnie do Azure Portal, użyj pola wyszukiwania portalu, aby znaleźć aplikację logiki, a następnie wybierz aplikację logiki z listy wyników.

   ![Zrzut ekranu pokazujący Azure Portal i pasek wyszukiwania z wynikami wyszukiwania wdrożonej aplikacji logiki, która zostanie wyświetlona.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Zarządzanie wdrożonymi aplikacjami logiki w portalu

W Azure Portal można wyświetlić wszystkie wdrożone Aplikacje logiki, które znajdują się w subskrypcji platformy Azure, niezależnie od tego, czy są one oryginalnym typem zasobu **Logic Apps** czy typu zasobu **aplikacja logiki (wersja zapoznawcza)** . Obecnie każdy typ zasobu jest zorganizowany i zarządzany jako osobne kategorie na platformie Azure. Aby znaleźć aplikacje logiki, które mają typ zasobu **aplikacja logiki (wersja zapoznawcza)** , wykonaj następujące kroki:

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

   Zostanie otwarty projektant przepływu pracy i zostanie wyświetlony przepływ pracy utworzony w Visual Studio Code. Teraz można wprowadzać zmiany w tym przepływie pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i przepływ pracy wdrożony z Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Dodawanie kolejnego przepływu pracy w portalu

Za pomocą Azure Portal można dodać puste przepływy pracy do zasobu **aplikacji logiki (wersja zapoznawcza)** , który został wdrożony z Visual Studio Code i utworzyć te przepływy pracy w Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób wdrożonej **aplikacji logiki (wersja zapoznawcza)** .

1. W menu aplikacji logiki wybierz pozycję **przepływy pracy**. W okienku **przepływy pracy** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu pokazujący okienko "przepływy pracy" i pasek narzędzi wybranych aplikacji logiki z wybranym poleceniem "Dodaj".](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. W okienku **Nowy przepływ pracy** Podaj nazwę dla przepływu pracy. Wybierz opcję **stanowe** lub **bezstanowe** **>** .

   Po wdrożeniu nowego przepływu pracy przez platformę Azure, który jest wyświetlany w okienku **przepływy** pracy, wybierz ten przepływ pracy, aby zarządzać i wykonywać inne zadania, na przykład otwierając projektanta lub widok kodu.

   ![Zrzut ekranu pokazujący wybrany przepływ pracy z opcjami zarządzania i przeglądu.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Na przykład otwarcie projektanta dla nowego przepływu pracy pokazuje pustą kanwę. Teraz można utworzyć ten przepływ pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i pusty przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Włącz historię uruchamiania dla bezstanowych przepływów pracy

Aby ułatwić debugowanie bezstanowego przepływu pracy, można włączyć historię uruchamiania dla tego przepływu pracy, a następnie wyłączyć historię uruchamiania po zakończeniu. Wykonaj następujące kroki, aby uzyskać Visual Studio Code, lub jeśli pracujesz w Azure Portal, zobacz [Tworzenie stanowych przepływów pracy i bezstanowych w Azure Portal](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. W projekcie Visual Studio Code rozwiń folder **Workflow-DesignTime** i Otwórz **local.settings.jsna** pliku.

1. Dodaj `Workflows.{yourWorkflowName}.operationOptions` Właściwość i ustaw wartość na, na `WithStatelessRunHistory` przykład:

   **Windows**

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

   **macOS lub Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Aby wyłączyć historię uruchamiania po zakończeniu, należy ustawić `Workflows.{yourWorkflowName}.OperationOptions` Właściwość na `None` lub usunąć właściwość i jej wartość.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Włącz widok monitorowania w Azure Portal

Po wdrożeniu zasobu **aplikacji logiki (wersja zapoznawcza)** z Visual Studio Code na platformie Azure można przejrzeć wszystkie dostępne historię uruchamiania i szczegóły dotyczące przepływu pracy w tym zasobie przy użyciu Azure Portal i środowiska **monitorowania** dla tego przepływu pracy. Należy jednak najpierw włączyć funkcję widok **monitora** dla tego zasobu aplikacji logiki.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz zasób wdrożonej **aplikacji logiki (wersja zapoznawcza)** .

1. W menu tego zasobu w obszarze **interfejs API** wybierz pozycję **CORS**.

1. W okienku **CORS** w obszarze **dozwolone źródła** Dodaj symbol wieloznaczny (*).

1. Gdy skończysz, na pasku narzędzi **CORS** wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający Azure Portal ze wdrożonym zasobem logiki (wersja zapoznawcza). W menu zasób jest zaznaczone polecenie "CORS" z nowym wpisem "dozwolone źródła" ustawione na symbol wieloznaczny "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Włącz lub Otwórz Application Insights po wdrożeniu

Podczas wykonywania przepływu pracy aplikacja logiki emituje dane telemetryczne wraz z innymi zdarzeniami. Za pomocą tych danych telemetrycznych można uzyskać lepszy wgląd w działanie przepływu pracy oraz sposób działania Logic Apps środowiska uruchomieniowego na różne sposoby. Przepływ pracy można monitorować przy użyciu [Application Insights](../azure-monitor/app/app-insights-overview.md), który zapewnia niemal w czasie rzeczywistym dane telemetryczne (metryki na żywo). Ta funkcja ułatwia badanie błędów i problemów z wydajnością w łatwiejszy sposób w przypadku używania tych danych do diagnozowania problemów, konfigurowania alertów i tworzenia wykresów.

Jeśli ustawienia tworzenia i wdrażania aplikacji logiki obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), można opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale można utworzyć ten zasób [z wyprzedzeniem](../azure-monitor/app/create-workspace-resource.md), podczas wdrażania aplikacji logiki lub po wdrożeniu.

Aby włączyć Application Insights w wdrożonej aplikacji logiki lub przejrzeć dane Application Insights, gdy są już włączone, wykonaj następujące kroki:

1. W Azure Portal Znajdź wdrożoną aplikację logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Application Insights**.

1. Jeśli Application Insights nie jest włączona, w okienku **Application Insights** wybierz pozycję **Włącz Application Insights**. Po zaktualizowaniu okienka w dolnej części wybierz pozycję **Zastosuj**.

   Jeśli Application Insights jest włączona, w okienku **Application Insights** wybierz pozycję **Wyświetl Application Insights dane**.

Po otwarciu Application Insights można przejrzeć różne metryki dla aplikacji logiki. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

* [Azure Logic Apps uruchamianie w dowolnym miejscu i monitorze z Application Insights — część 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps uruchamianie w dowolnym miejscu i monitorze z Application Insights — część 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Wdróż w platformie Docker

Aplikację logiki można wdrożyć do [kontenera Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) jako środowiska hostingu przy użyciu [interfejsu wiersza polecenia platformy .NET](/dotnet/core/tools/). Za pomocą tych poleceń można skompilować i opublikować projekt aplikacji logiki. Następnie można skompilować i uruchomić kontener Docker jako miejsce docelowe wdrożenia aplikacji logiki.

Jeśli nie znasz już platformy Docker, zapoznaj się z następującymi tematami:

* [Co to jest Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Wprowadzenie do kontenerów i platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Wprowadzenie do platform .NET i Docker](/dotnet/core/docker/introduction)
* [Kontenery, obrazy i rejestry platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Samouczek: wprowadzenie do platformy Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Wymagania

* Konto usługi Azure Storage używane przez aplikację logiki do wdrożenia

* Plik platformy Docker dla przepływu pracy, który jest używany podczas tworzenia kontenera platformy Docker

  Przykładowo ten plik platformy Docker wdraża aplikację logiki. Określa parametry połączenia, które zawierają klucz dostępu dla konta usługi Azure Storage, który został użyty do opublikowania aplikacji logiki w Azure Portal. Aby znaleźć ten ciąg, zobacz [pobieranie parametrów połączenia z kontem magazynu](#find-storage-account-connection-string).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania plików platformy Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Pobierz parametry połączenia konta magazynu

Przed rozpoczęciem kompilowania i uruchamiania obrazu kontenera Docker należy uzyskać parametry połączenia, które zawierają klucz dostępu do konta magazynu. Wcześniej konto magazynu zostało utworzone w taki sposób, aby korzystało z rozszerzenia w systemie macOS lub Linux, lub podczas wdrażania aplikacji logiki na Azure Portal.

Aby znaleźć i skopiować te parametry połączenia, wykonaj następujące kroki:

1. W Azure Portal w menu konto magazynu w obszarze **Ustawienia** wybierz pozycję **klucze dostępu**. 

1. W okienku **klucze dostępu** Znajdź i skopiuj parametry połączenia konta magazynu, które wyglądają podobnie do tego przykładu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Zrzut ekranu przedstawiający Azure Portal z kluczami dostępu do konta magazynu i skopiowanymi parametrami połączenia.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Aby uzyskać więcej informacji, zapoznaj się z tematem [Zarządzanie kluczami konta magazynu](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Zapisz parametry połączenia w bezpiecznym miejscu, aby można było dodać ten ciąg do pliku platformy Docker używanego do wdrożenia. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Znajdź klucz główny dla konta magazynu

Gdy przepływ pracy zawiera wyzwalacz żądania, należy [uzyskać adres URL wywołania zwrotnego wyzwalacza](#get-callback-url-request-trigger) po skompilowaniu i uruchomieniu obrazu kontenera Docker. W przypadku tego zadania należy również określić wartość klucza głównego dla konta magazynu używanego do wdrożenia.

1. Aby znaleźć ten klucz główny, w projekcie Otwórz plik **Azure-WebJobs-Secrets/{Deployment-Name}/host.jsw** pliku.

1. Znajdź `AzureWebJobsStorage` Właściwość i skopiuj wartość klucza z tej sekcji:

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

1. Zapisz tę wartość klucza w bezpiecznym miejscu, aby użyć jej później.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Kompilowanie i uruchamianie obrazu kontenera platformy Docker

1. Skompiluj obraz kontenera Docker przy użyciu pliku Docker i uruchamiając to polecenie:

   `docker build --tag local/workflowcontainer .`

   Aby uzyskać więcej informacji, zobacz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Uruchom kontener lokalnie za pomocą tego polecenia:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Aby uzyskać więcej informacji, zobacz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Pobierz adres URL wywołania zwrotnego dla wyzwalacza żądania

Dla przepływu pracy korzystającego z wyzwalacza żądania Pobierz adres URL wywołania zwrotnego wyzwalacza, wysyłając to żądanie:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}`Wartość jest nazwą wyzwalacza żądania, który pojawia się w definicji JSON przepływu pracy. `{master-key}`Wartość jest definiowana na koncie usługi Azure Storage ustawionym dla `AzureWebJobsStorage` właściwości w pliku, **na platformie Azure — Zadania WebJob — wpisy tajne/{Deployment-name}/host.json**. Aby uzyskać więcej informacji, zobacz [Znajdowanie klucza głównego konta magazynu](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Usuwanie elementów z projektanta

Aby usunąć element z projektanta, wykonaj jedną z następujących czynności:

* Zaznacz element, otwórz menu skrótów elementu (Shift + F10) i wybierz polecenie **Usuń**. Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element, a następnie naciśnij klawisz Delete. Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element, aby okienko szczegółów było otwierane dla tego elementu. W prawym górnym rogu okienka Otwórz menu wielokropka (**...**), a następnie wybierz pozycję **Usuń**. Aby potwierdzić, wybierz pozycję **OK**.

  ![Zrzut ekranu pokazujący wybrany element w Projektancie z otwartym okienkiem szczegółów oraz poleceniem "Usuń" z wybranymi elipsami.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Jeśli menu wielokropka nie jest widoczne, rozwiń okno Visual Studio Code wystarczająco szerokie, aby okienko szczegółów pokazywało przycisk wielokropka (**...**) w prawym górnym rogu.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Rozwiązywanie problemów z błędami i problemami

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Nie można otworzyć projektanta

Podczas próby otwarcia projektanta pojawia się ten błąd, "nie można **uruchomić czasu projektowania przepływu pracy"**. Jeśli wcześniej podjęto próbę otwarcia projektanta, a następnie wycofanie lub usunięcie projektu, pakiet rozszerzeń może nie być pobierany prawidłowo. Aby sprawdzić, czy przyczyną tego problemu jest problem, wykonaj następujące kroki:

  1. W Visual Studio Code Otwórz okno dane wyjściowe. Z menu **Widok** wybierz pozycję **dane wyjściowe**.

  1. Z listy na pasku tytułu okna danych wyjściowych wybierz pozycję **Azure Logic Apps (wersja zapoznawcza)** , aby można było przejrzeć dane wyjściowe z rozszerzenia, na przykład:

     ![Zrzut ekranu pokazujący okno danych wyjściowych z wybranym elementem "Azure Logic Apps".](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Przejrzyj dane wyjściowe i sprawdź, czy ten komunikat o błędzie jest wyświetlany:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Aby rozwiązać ten problem, Usuń folder **ExtensionBundles** w tej lokalizacji **. ..\Users nazwę \{ użytkownika} \AppData\Local\Temp\Functions\ExtensionBundles**, a następnie ponów próbę otwarcia **workflow.jsw** pliku w projektancie.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>W selektorze projektanta brakuje nowych wyzwalaczy i akcji dla wcześniej utworzonych przepływów pracy

Wersja zapoznawcza Azure Logic Apps obsługuje wbudowane akcje dla operacji funkcji platformy Azure, operacji związanych z operacjami ciekłymi i operacjami XML, takich jak **Sprawdzanie poprawności kodu XML** i **przekształcanie XML**. Jednak w przypadku wcześniej utworzonych aplikacji logiki te akcje mogą nie być wyświetlane w selektorze projektanta, aby wybrać, czy Visual Studio Code używa nieaktualnej wersji pakietu rozszerzenia `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Ponadto łącznik operacji i akcje **funkcji platformy Azure** nie są wyświetlane w selektorze projektanta, chyba że podczas tworzenia aplikacji logiki nie zostanie włączone ani wybrane **Używanie łączników z platformy Azure** . Jeśli nie włączono łączników wdrożonych przez platformę Azure podczas tworzenia aplikacji, możesz je włączyć z poziomu projektu w Visual Studio Code. Otwórz **workflow.jsw** menu skrótów, a następnie wybierz pozycję **Użyj łączników z platformy Azure**.

Aby naprawić nieaktualny pakiet, wykonaj następujące kroki, aby usunąć nieaktualny pakiet, dzięki czemu Visual Studio Code automatycznie aktualizować pakiet rozszerzeń do najnowszej wersji.

> [!NOTE]
> To rozwiązanie dotyczy tylko aplikacji logiki tworzonych i wdrażanych przy użyciu Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza), a nie aplikacjami logiki, które zostały utworzone przy użyciu Azure Portal. Zobacz [obsługiwane wyzwalacze i akcje są niedostępne w projektancie w Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Zapisz wszelkie zadania, które nie mają zostać utracone, i Zamknij program Visual Studio.

1. Na komputerze przejdź do następującego folderu, który zawiera foldery z wersjami dla istniejącego pakietu:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Usuń folder wersji dla wcześniejszego zbioru, na przykład jeśli masz folder w wersji 1.1.3, Usuń ten folder.

1. Teraz przejdź do następującego folderu, który zawiera foldery z wersjami dla wymaganego pakietu NuGet:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Usuń folder wersji starszego pakietu, na przykład jeśli masz folder dla wersji 1.0.0.8-Preview, Usuń ten folder.

1. Ponownie otwórz Visual Studio Code, projekt i **workflow.jsna** pliku w projektancie.

Brakujące wyzwalacze i akcje są teraz wyświetlane w projektancie.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Nieprawidłowe żądanie" pojawia się w wyzwalaczu lub akcji

W przypadku niepowodzenia przebiegu i sprawdzenia przebiegu w widoku monitorowanie ten błąd może pojawić się w wyzwalaczu lub akcji o dłuższej nazwie, co powoduje, że bazowy Uniform Resource Identifier (URI) przekroczy domyślny limit znaków.

Aby rozwiązać ten problem i dostosować go do dłuższego identyfikatora URI, `UrlSegmentMaxCount` Edytuj `UrlSegmentMaxLength` klucze rejestru i na komputerze, wykonując poniższe kroki. Te wartości domyślne klucza zostały opisane w tym temacie, [Http.sys ustawienia rejestru dla systemu Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Przed rozpoczęciem upewnij się, że Zapisano swoją pracę. To rozwiązanie wymaga ponownego uruchomienia komputera po zakończeniu, aby zmiany zaczęły obowiązywać.

1. Na komputerze otwórz okno **uruchamiania** , a następnie uruchom `regedit` polecenie, które otwiera Edytor rejestru.

1. W polu **Kontrola konta użytkownika** wybierz pozycję **tak** , aby zezwolić na wprowadzanie zmian na komputerze.

1. W lewym okienku w obszarze **komputer** rozwiń węzły wzdłuż ścieżki, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** a następnie wybierz **Parametry**.

1. W okienku po prawej stronie Znajdź `UrlSegmentMaxCount` `UrlSegmentMaxLength` klucze rejestru i.

1. Zwiększ te najważniejsze wartości tak, aby identyfikatory URI mogły pomieścić nazwy, których chcesz użyć. Jeśli te klucze nie istnieją, Dodaj je do folderu **Parameters** , wykonując następujące czynności:

   1. Z menu skrótów **Parametry** wybierz pozycję **Nowa**  >  **wartość DWORD (32-bitowa)**.

   1. W wyświetlonym polu edycji wprowadź `UrlSegmentMaxCount` nową nazwę klucza.

   1. Otwórz menu skrótów nowego klucza i wybierz polecenie **Modyfikuj**.

   1. W wyświetlonym polu **ciągu edycji** wprowadź wartość klucza **danych wartości** , która ma być w formacie szesnastkowym lub dziesiętnym. Na przykład `400` w formacie szesnastkowym jest równoważne wartości `1024` w postaci dziesiętnej.

   1. Aby dodać `UrlSegmentMaxLength` wartość klucza, Powtórz te kroki.

   Po zwiększeniu lub dodaniu tych wartości klucza Edytor rejestru wygląda podobnie do tego przykładu:

   ![Zrzut ekranu pokazujący Edytor rejestru.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Gdy wszystko będzie gotowe, uruchom ponownie komputer, aby zmiany zaczęły obowiązywać.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Nie można uruchomić sesji debugowania

Podczas próby uruchomienia sesji debugowania zostanie wyświetlony komunikat o błędzie **"istnieje po uruchomieniu preLaunchTask" generateDebugSymbols ""**. Aby rozwiązać ten problem, Edytuj **tasks.jsw** pliku w projekcie, aby pominąć generowanie symboli.

1. W projekcie rozwiń folder **. programu vscode** , a następnie otwórz **tasks.jsw** pliku.

1. W poniższym zadaniu usuń wiersz, `"dependsOn: "generateDebugSymbols"` a także przecinek kończący poprzedni wiersz, na przykład:

   Przed:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Po:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Następne kroki

Chcemy poznać Twoją opinię na temat środowiska z rozszerzeniem Azure Logic Apps (wersja zapoznawcza).

* W przypadku usterek lub problemów [Utwórz swoje problemy w serwisie GitHub](https://github.com/Azure/logicapps/issues).
* Aby uzyskać odpowiedzi na pytania, żądania, komentarze i inne opinie, [Skorzystaj z tej formy opinii](https://aka.ms/lafeedback).
