---
title: Tworzenie przepływów Logic Apps w wersji zapoznawczej w programie Visual Studio Code
description: Twórz i uruchamiaj przepływy pracy dla scenariuszy automatyzacji i integracji w Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772509"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Tworzenie stanowych i bez stanowych przepływów pracy w Visual Studio Code za pomocą rozszerzenia Azure Logic Apps (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Program [Azure Logic Apps Preview](logic-apps-overview-preview.md)umożliwia tworzenie rozwiązań automatyzacji i integracji w aplikacjach, danych, usługach w chmurze i systemach przez tworzenie i uruchamianie aplikacji logiki, które obejmują stanowe i bez stanowe przepływy pracy w usłudze Visual Studio Code przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza). [   ](logic-apps-overview-preview.md#stateful-stateless) Korzystając z tego nowego typu aplikacji logiki, można utworzyć wiele przepływów pracy obsługiwanych przez przeprojektowane środowisko uruchomieniowe programu Azure Logic Apps Preview, które zapewnia przenośność, lepszą wydajność i elastyczność wdrażania i uruchamiania w różnych środowiskach hostingu, nie tylko na platformie Azure, ale również w kontenerach platformy Docker. Aby dowiedzieć się więcej o nowym typie aplikacji logiki, zobacz Overview for Azure Logic Apps Preview (Omówienie aplikacji [Azure Logic Apps wersji zapoznawczej).](logic-apps-overview-preview.md)

![Zrzut ekranu przedstawiający Visual Studio Code, projekt aplikacji logiki i przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

W Visual Studio Code możesz rozpocząć od utworzenia projektu,  w którym możesz lokalnie kompilować i uruchamiać przepływy pracy aplikacji logiki w środowisku dewelopera przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza). Chociaż możesz również rozpocząć od utworzenia nowego zasobu aplikacji logiki [ **(wersja zapoznawcza)**](create-stateful-stateless-workflows-azure-portal.md)w środowisku Azure Portal, obie metody zapewniają możliwość wdrażania i uruchamiania aplikacji logiki w tego samego rodzaju środowiskach hostingu.

W międzyczasie nadal można utworzyć oryginalny typ aplikacji logiki. Mimo że środowisko programowe w Visual Studio Code typów aplikacji logiki różni się między oryginalnymi i nowymi typami aplikacji logiki, subskrypcja platformy Azure może obejmować oba typy. Możesz wyświetlać wszystkie wdrożone aplikacje logiki w ramach subskrypcji platformy Azure i uzyskać do nich dostęp, ale aplikacje są zorganizowane w ich własne kategorie i sekcje.

W tym artykule pokazano, jak utworzyć aplikację logiki i przepływ pracy w programie Visual Studio Code przy użyciu rozszerzenia Azure Logic Apps (wersja zapoznawcza) i wykonywać następujące zadania wysokiego poziomu:

* Utwórz projekt dla aplikacji logiki i przepływu pracy.

* Dodaj wyzwalacz i akcję.

* Uruchamianie, testowanie, debugowanie i lokalne przeglądanie historii uruchamiania.

* Znajdź szczegóły nazwy domeny na temat dostępu do zapory.

* Wdrażanie na platformie Azure, co obejmuje opcjonalne włączanie Application Insights.

* Zarządzanie wdrożoną aplikacją logiki w Visual Studio Code i Azure Portal.

* Włącz historię uruchamiania dla bez stanowych przepływów pracy.

* Włącz lub otwórz Application Insights po wdrożeniu.

* Wdrażanie w kontenerze platformy Docker, który można uruchomić w dowolnym miejscu.

> [!NOTE]
> Aby uzyskać informacje o bieżących znanych problemach, zapoznaj się ze Logic Apps znane problemy w publicznej wersji [zapoznawczej w usłudze GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="access-and-connectivity"></a>Dostęp i łączność

* Dostęp do Internetu, aby można było pobrać wymagania, nawiązać połączenie z usługi Visual Studio Code do konta platformy Azure i opublikować z usługi Visual Studio Code na platformie Azure, w kontenerze platformy Docker lub w innym środowisku.

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Do skompilowania tej samej przykładowej aplikacji logiki w tym artykule potrzebne jest konto e-mail usługi Office 365 Outlook, które używa konta służbowego Microsoft do logowania się.

  Jeśli zdecydujesz się używać innego łącznika poczty e-mail obsługiwanego przez usługę [Azure Logic Apps](/connectors/), takiego jak Outlook.com lub [Gmail,](../connectors/connectors-google-data-security-privacy-policy.md)nadal możesz wykonać przykład, a ogólne ogólne kroki są takie same, ale interfejs użytkownika i opcje mogą się różnić pod pewnymi względami. Jeśli na przykład używasz łącznika Outlook.com, użyj swojego osobistego konto Microsoft, aby się zalogować.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Wymagania dotyczące magazynu

#### <a name="windows"></a>Windows

Aby lokalnie skompilować i uruchomić projekt aplikacji logiki w programie Visual Studio Code w systemie Windows, wykonaj następujące kroki, aby skonfigurować emulator usługi Azure Storage:

1. Pobierz i zainstaluj [emulator usługi Azure Storage w wersji 5.10.](https://go.microsoft.com/fwlink/p/?linkid=717179)

1. Jeśli jeszcze jej nie masz, musisz mieć lokalną instalację usługi SQL DB, taką jak bezpłatna wersja [SQL Server 2019 Express Edition,](https://go.microsoft.com/fwlink/p/?linkid=866658)aby można było uruchomić emulator.

   Aby uzyskać więcej informacji, zobacz Use the Azure Storage emulator for development and testing (Używanie [emulatora usługi Azure Storage do tworzenia i testowania).](../storage/common/storage-use-emulator.md)

1. Przed uruchomieniem projektu upewnij się, że uruchamiasz emulator.

   ![Zrzut ekranu przedstawiający uruchomiony emulator usługi Azure Storage.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS i Linux

Aby lokalnie skompilować i uruchomić projekt aplikacji logiki w usłudze Visual Studio Code przypadku korzystania z systemu macOS lub Linux, wykonaj następujące kroki, aby utworzyć i skonfigurować konto usługi Azure Storage.

> [!NOTE]
> Obecnie projektant w programie Visual Studio Code nie działa w systemie operacyjnym Linux, ale nadal można uruchamiać kompilowanie, uruchamianie i wdrażanie aplikacji logiki, które korzystają ze środowiska uruchomieniowego wersji zapoznawczej programu Logic Apps na maszynach wirtualnych z systemem Linux. Na razie możesz tworzyć aplikacje logiki w środowisku Visual Studio Code systemie Windows lub macOS, a następnie wdrażać je na maszynie wirtualnej z systemem Linux.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com)i [utwórz konto usługi Azure Storage,](../storage/common/storage-account-create.md?tabs=azure-portal)które jest [wymaganiem wstępnym](../azure-functions/storage-considerations.md)Azure Functions .

1. W menu konta magazynu w obszarze **Ustawienia** wybierz pozycję **Klucze dostępu.**

1. W **okienku Klucze** dostępu znajdź i skopiuj ciąg połączenia konta magazynu, który wygląda podobnie do tego przykładu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Zrzut ekranu przedstawiający Azure Portal skopiowane klucze dostępu do konta magazynu i parametrów połączenia.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Aby uzyskać więcej informacji, zapoznaj się [z tematem Zarządzanie kluczami konta magazynu.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Zapisz te ciągi połączenia w bezpiecznym miejscu. Po utworzeniu projektu aplikacji logiki w Visual Studio Code należy dodać ciąg do pliku **local.settings.js** w folderze na poziomie głównym projektu.

   > [!IMPORTANT]
   > Jeśli planujesz wdrożenie w kontenerze platformy Docker, musisz również użyć tych parametrów połączenia z plikiem platformy Docker, którego używasz do wdrożenia. W przypadku scenariuszy produkcyjnych upewnij się, że chronisz i zabezpieczasz takie wpisy tajne i poufne informacje, na przykład przy użyciu magazynu kluczy.
  
### <a name="tools"></a>Narzędzia

* [Visual Studio Code 1.30.1 (styczeń 2019 r.)](https://code.visualstudio.com/)lub wyższej , która jest bezpłatna. Ponadto pobierz i zainstaluj te narzędzia dla Visual Studio Code, jeśli jeszcze ich nie masz:

  * [Rozszerzenie konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), które zapewnia jedno wspólne środowisko logowania i filtrowania subskrypcji platformy Azure dla wszystkich innych rozszerzeń platformy Azure w Visual Studio Code.

  * [C# dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), które umożliwia uruchamianie aplikacji logiki za pomocą funkcji F5.

  * [Azure Functions Core Tools 3.0.3245](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) lub nowszej przy użyciu Instalatora Microsoft (MSI) w wersji . `func-cli-3.0.3245-x*.msi`

    Narzędzia te obejmują wersję tego samego środowiska uruchomieniowego, które Azure Functions uruchomieniowe, którego rozszerzenie w wersji zapoznawczej używa w Visual Studio Code.

    > [!IMPORTANT]
    > Jeśli masz instalację wcześniejszą niż te wersje, najpierw odinstaluj tę wersję lub upewnij się, że zmienna środowiskowa PATH wskazuje wersję, którą pobierasz i instalujesz.

  * [Azure Logic Apps (wersja zapoznawcza) dla Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). To rozszerzenie umożliwia tworzenie aplikacji logiki, w których można tworzyć stanowe i bez stanowe przepływy pracy uruchamiane lokalnie w usłudze Visual Studio Code, a następnie wdrażać te aplikacje logiki bezpośrednio na platformie Azure lub w kontenerach platformy Docker.

    Obecnie w programie można zainstalować zarówno oryginalne rozszerzenie Azure Logic Apps, jak i rozszerzenie publicznej wersji zapoznawczej Visual Studio Code. Mimo że środowisko programowe różni się pod pewnymi względami między rozszerzeniami, subskrypcja platformy Azure może obejmować oba typy aplikacji logiki, które tworzysz za pomocą rozszerzeń. Visual Studio Code wszystkie wdrożone aplikacje **logiki** w ramach subskrypcji platformy Azure, ale organizuje je w różnych sekcjach według nazw rozszerzeń, nazw Logic Apps i **Azure Logic Apps (wersja zapoznawcza).**

    > [!IMPORTANT]
    > Jeśli utworzono projekty aplikacji logiki z wcześniejszym rozszerzeniem prywatnej wersji zapoznawczej, te projekty nie będą działać z rozszerzeniem publiczna wersja zapoznawcza. Można jednak migrować te projekty po odinstalowaniu rozszerzenia prywatnej wersji zapoznawczej, usunięciu skojarzonych plików i zainstalowaniu rozszerzenia publicznej wersji zapoznawczej. Następnie utworzysz nowy projekt w Visual Studio Code i skopiuj plik **workflow.definition** utworzonej wcześniej aplikacji logiki do nowego projektu. Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia prywatnej wersji zapoznawczej](#migrate-private-preview).
    > 
    > Jeśli utworzono projekty aplikacji logiki z wcześniejszym rozszerzeniem publicznej wersji zapoznawczej, możesz nadal używać tych projektów bez żadnych kroków migracji.

    **Aby zainstalować rozszerzenie **Azure Logic Apps (wersja zapoznawcza),** wykonaj następujące kroki:**

    1. Na Visual Studio Code pasku narzędzi po lewej stronie wybierz **pozycję Rozszerzenia**.

    1. W polu wyszukiwania rozszerzeń wprowadź `azure logic apps preview` . Z listy wyników wybierz pozycję Zainstaluj **Azure Logic Apps (wersja** **>** **zapoznawcza).**

       Po zakończeniu instalacji rozszerzenie w wersji zapoznawczej zostanie wyświetlone na **liście Rozszerzenia: zainstalowane.**

       ![Zrzut ekranu Visual Studio Code listy zainstalowanych rozszerzeń z podkreślonym rozszerzeniem "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Jeśli rozszerzenia nie ma na liście zainstalowanych, spróbuj ponownie uruchomić Visual Studio Code.

* Aby użyć [](../logic-apps/logic-apps-add-run-inline-code.md) akcji Operacje kodu wbudowanego, która uruchamia język JavaScript, zainstaluj programNode.js w wersji [10.x.x, 11.x.x lub 12.x.x.](https://nodejs.org/en/download/releases/)

  > [!TIP] 
  > W przypadku systemu Windows pobierz wersję msi. Jeśli zamiast tego używasz wersji ZIP, musisz ręcznie udostępnić Node.js przy użyciu zmiennej środowiskowej PATH dla systemu operacyjnego.

* Aby lokalnie uruchamiać wyzwalacze i akcje oparte na linkach [webhook,](../connectors/connectors-native-webhook.md)takie jak wbudowany wyzwalacz http webhook , na platformie Visual Studio Code, należy skonfigurować przekazywanie dla adresu URL wywołania [zwrotnego](#webhook-setup).

* Aby przetestować przykładową aplikację logiki, którą utworzysz w tym artykule, potrzebujesz narzędzia, które może wysyłać wywołania do wyzwalacza Żądania, który jest pierwszym krokiem przykładowej aplikacji logiki. Jeśli nie masz takiego narzędzia, możesz pobrać, zainstalować i użyć narzędzia [Postman.](https://www.postman.com/downloads/)

* Jeśli utworzysz aplikację logiki i wdrożysz ją przy użyciu ustawień, które obsługują Application Insights , możesz [opcjonalnie](../azure-monitor/app/app-insights-overview.md)włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale możesz utworzyć ten zasób z wyprzedzeniem [,](../azure-monitor/app/create-workspace-resource.md)podczas wdrażania aplikacji logiki lub po wdrożeniu.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrowanie z rozszerzenia prywatnej wersji zapoznawczej

Żadne projekty aplikacji logiki utworzone za pomocą **rozszerzenia Azure Logic Apps (prywatna wersja zapoznawcza)** nie będą działać z rozszerzeniem publicznej wersji zapoznawczej. Można jednak migrować te projekty do nowych projektów, korzystając z następujących kroków:

1. Odinstaluj rozszerzenie prywatnej wersji zapoznawczej.

1. Usuń wszystkie skojarzone pakiety rozszerzeń i foldery pakietów NuGet w tych lokalizacjach:

   * Folder **Microsoft.Azure.Functions.ExtensionBundle.Workflows,** który zawiera poprzednie pakiety rozszerzeń i znajduje się wzdłuż jednej z tych ścieżek:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Folder **microsoft.azure.workflows.webjobs.extension,** który jest pamięcią podręczną [NuGet](/nuget/what-is-nuget) dla rozszerzenia prywatnej wersji zapoznawczej i znajduje się w tej ścieżce:

     `C:\Users\{userName}\.nuget\packages`

1. Zainstaluj rozszerzenie **Azure Logic Apps (wersja zapoznawcza).**

1. Utwórz nowy projekt w Visual Studio Code.

1. Skopiuj wcześniej utworzony plik **workflow.definition** aplikacji logiki do nowego projektu.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

1. Aby upewnić się, że wszystkie rozszerzenia są poprawnie zainstalowane, załaduj ponownie lub uruchom ponownie Visual Studio Code.

1. Upewnij się, Visual Studio Code automatycznie znajdzie i zainstaluje aktualizacje rozszerzeń, aby rozszerzenie w wersji zapoznawczej pobierało najnowsze aktualizacje. W przeciwnym razie musisz ręcznie odinstalować nieaktualną wersję i zainstalować najnowszą wersję.

   1. W menu **Plik** przejdź do pozycji **Ustawienia** **>** **preferencji.**

   1. Na karcie **Użytkownik** przejdź do **rozszerzenia** **>** **funkcji.**

   1. Upewnij **się, że wybrano aktualizacje automatycznego** sprawdzania **i** aktualizacji automatycznych.

Ponadto domyślnie są włączone i ustawione następujące ustawienia dla Logic Apps wersji zapoznawczej:

* **Azure Logic Apps V2: środowisko uruchomieniowe projektu,** które jest ustawione na wersję **~3**

  > [!NOTE]
  > Ta wersja jest wymagana do korzystania z akcji [Wbudowane operacje kodu.](../logic-apps/logic-apps-add-run-inline-code.md)

* **Azure Logic Apps wersji 2: Eksperymentalny menedżer widoków,** który umożliwia korzystanie z najnowszego projektanta w Visual Studio Code. Jeśli w projektancie występują problemy, takie jak przeciąganie i upuszczanie elementów, wyłącz to ustawienie.

Aby znaleźć i potwierdzić te ustawienia, wykonaj następujące kroki:

1. W menu **Plik** przejdź do pozycji **Ustawienia** **>** **preferencji.**

1. Na karcie **Użytkownik** przejdź do strony **>** **Rozszerzenia Azure Logic Apps** **>** **(wersja zapoznawcza).**

   Możesz na przykład znaleźć tutaj ustawienie **Azure Logic Apps V2:** Środowisko uruchomieniowe projektu lub użyć pola wyszukiwania, aby znaleźć inne ustawienia:

   ![Zrzut ekranu przedstawiający Visual Studio Code rozszerzenia "Azure Logic Apps (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

1. Na pasku Visual Studio Code wybierz ikonę Azure.

   ![Zrzut ekranu przedstawiający pasek Visual Studio Code i wybraną ikonę platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. W okienku Azure w obszarze **Azure: Logic Apps (wersja zapoznawcza)** wybierz **pozycję Zaloguj się do platformy Azure.** Po Visual Studio Code uwierzytelniania zaloguj się przy użyciu konta platformy Azure.

   ![Zrzut ekranu przedstawiający okienko platformy Azure i wybrany link logowania do platformy Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Po zalogowaniu się w okienku Azure będą pojawiać się subskrypcje na koncie platformy Azure. Jeśli masz również publicznie wydane rozszerzenie, możesz znaleźć wszystkie aplikacje logiki utworzone za pomocą tego rozszerzenia w sekcji **Logic Apps, a** nie w sekcji Logic Apps **(wersja** zapoznawcza).
   
   Jeśli oczekiwane subskrypcje nie są wyświetlane lub chcesz, aby w okienku były wyświetlane tylko określone subskrypcje, wykonaj następujące kroki:

   1. Na liście subskrypcji przesuń wskaźnik obok pierwszej subskrypcji,  aż pojawi się przycisk Wybierz subskrypcje (ikona filtru). Wybierz ikonę filtru.

      ![Zrzut ekranu przedstawiający okienko platformy Azure i wybraną ikonę filtru.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Lub na pasku Visual Studio Code wybierz swoje konto platformy Azure. 

   1. Gdy zostanie wyświetlona inna lista subskrypcji, wybierz odpowiednie subskrypcje, a następnie upewnij się, że wybierasz przycisk **OK.**

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Tworzenie projektu lokalnego

Przed utworzeniem aplikacji logiki utwórz projekt lokalny, aby można było zarządzać aplikacją logiki, uruchamiać ją i wdrażać z jej Visual Studio Code. Podstawowy projekt jest podobny do Azure Functions, znanego również jako projekt aplikacji funkcji. Jednak te typy projektów są oddzielone od siebie, więc aplikacje logiki i aplikacje funkcji nie mogą istnieć w tym samym projekcie.

1. Na komputerze utwórz *pusty* folder lokalny do użycia dla projektu, który zostanie później Visual Studio Code.

1. W Visual Studio Code zamknij wszystkie otwarte foldery.

1. W okienku Azure obok opcji **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję **Create New Project** (Utwórz nowy projekt) (ikona z folderem i piorunem).

   ![Zrzut ekranu przedstawiający pasek narzędzi okienka Platformy Azure z wybraną pozycję "Utwórz nowy projekt".](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Jeśli Zapora Windows Defender monit o udzielenie dostępu sieciowego dla programu , który jest Visual Studio Code, i dla opcji , która jest Azure Functions Core Tools, wybierz pozycję Sieci prywatne, takie jak moja sieć domowa lub służbowa Zezwalaj na `Code.exe` `func.exe`  **>** **dostęp.**

1. Przejdź do lokalizacji, w której utworzono folder projektu, wybierz ten folder i kontynuuj.

   ![Zrzut ekranu przedstawiający okno dialogowe "Wybieranie folderu" z nowo utworzonym folderem projektu i wybranym przyciskiem "Wybierz".](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Z wyświetlonej listy szablonów wybierz pozycję **Stateful Workflow (Stanowy przepływ pracy)** lub **Stateless Workflow (Bez stanowy przepływ pracy).** W tym przykładzie jest **wybierany stanowy przepływ pracy.**

   ![Zrzut ekranu przedstawiający listę szablonów przepływu pracy z wybraną elekcją "Stanowy przepływ pracy".](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Podaj nazwę przepływu pracy i naciśnij klawisz Enter. W tym przykładzie `Fabrikam-Stateful-Workflow` użyto nazwy .

   ![Zrzut ekranu przedstawiający pola "Utwórz nowy stanowy przepływ pracy (3/4)" i "Fabrikam-Stateful-Workflow" jako nazwę przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code zakończy tworzenie projektu i otworzy plik **workflow.jsprzepływu** pracy w edytorze kodu.

   > [!NOTE]
   > Jeśli zostanie wyświetlony monit o wybranie sposobu otwierania projektu, wybierz pozycję Otwórz w bieżącym oknie, jeśli chcesz otworzyć projekt w bieżącym Visual Studio Code oknie.  Aby otworzyć nowe wystąpienie usługi Visual Studio Code, wybierz **pozycję Otwórz w nowym oknie.**

1. Na pasku Visual Studio narzędzi otwórz okienko Eksplorator, jeśli nie zostało jeszcze otwarte.

   W okienku Explorer (Eksplorator) jest dostępny projekt, który zawiera teraz automatycznie generowane pliki projektu. Na przykład projekt ma folder, który pokazuje nazwę przepływu pracy. W tym folderzeworkflow.js **w pliku** zawiera podstawową definicję JSON przepływu pracy.

   ![Zrzut ekranu przedstawiający okienko Eksplorator z folderem projektu, folderem przepływu pracy i workflow.js"wł.".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Jeśli używasz systemu macOS lub Linux, skonfiguruj dostęp do konta magazynu, wykonać następujące kroki, które są wymagane do lokalnego uruchamiania projektu:

   1. W folderze głównym projektu otwórz plik **local.settings.jsw pliku** .

      ![Zrzut ekranu przedstawiający okienko Eksplorator i local.settings.js"wł." w projekcie.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Zastąp wartość właściwości zapisanymi wcześniej ciągami połączenia konta `AzureWebJobsStorage` magazynu, na przykład:

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

      > [!IMPORTANT]
      > W przypadku scenariuszy produkcyjnych upewnij się, że chronisz i zabezpieczasz takie wpisy tajne i poufne informacje, na przykład przy użyciu magazynu kluczy.

   1. Gdy wszystko będzie gotowe, upewnij się, że zapisujesz zmiany.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Włączanie tworzenia łączników wbudowanych

Możesz utworzyć własne wbudowane łączniki dla dowolnej usługi, która jest potrzebna, przy użyciu struktury [rozszerzalności wersji zapoznawczej](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Podobnie jak w przypadku łączników wbudowanych, takich jak Azure Service Bus i SQL Server, te łączniki zapewniają wyższą przepływność, małe opóźnienia, łączność lokalną i działają natywnie w tym samym procesie co środowisko uruchomieniowe w wersji zapoznawczej.

Możliwość tworzenia jest obecnie dostępna tylko w Visual Studio Code, ale nie jest domyślnie włączona. Aby utworzyć te łączniki, musisz najpierw przekonwertować projekt z opartego na pakiecie rozszerzeń (Node.js) na oparty na pakietach NuGet (.NET).

> [!IMPORTANT]
> Ta akcja jest operacją jednokierunkową, których nie można cofnąć.

1. W okienku Eksplorator w katalogu głównym projektu przesuń wskaźnik myszy na dowolny pusty obszar poniżej wszystkich innych plików i folderów, otwórz menu skrótów i wybierz pozycję Konwertuj na projekt aplikacji logiki opartej na **nugetach.**

   ![Zrzut ekranu przedstawiający okienko Eksplorator z menu skrótów projektu otwieranym z pustego obszaru w oknie projektu.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Po wyświetleniu monitu potwierdź konwersję projektu.

1. Aby kontynuować, przejrzyj i wykonaj kroki opisane w artykule [Azure Logic Apps Running Anywhere — Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)(Uruchamianie w dowolnym miejscu — rozszerzalność wbudowanego łącznika).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Otwieranie pliku definicji przepływu pracy w projektancie

1. Sprawdź wersje zainstalowane na komputerze, uruchamiając to polecenie:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Jeśli masz już zestaw .NET Core SDK 5.x, ta wersja może uniemożliwić otwarcie podstawowej definicji przepływu pracy aplikacji logiki w projektancie. Zamiast odinstalowywać tę wersję, w folderze głównym projektu utwórz plik **global.js,** który odwołuje się do środowiska uruchomieniowego .NET Core w wersji 3.x, która jest późniejsza niż 3.1.201, na przykład:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby jawnie dodać **global.jspliku** w folderze głównym projektu z folderu Visual Studio Code. W przeciwnym razie projektant nie zostanie otwarty.

1. Rozwiń folder projektu dla przepływu pracy. Otwórzworkflow.js **menu** skrótów pliku, a następnie wybierz **pozycję Otwórz w projektancie**.

   ![Zrzut ekranu przedstawiający okienko Eksploratora i okno skrótów dla workflow.jsw pliku z wybraną oknie "Otwórz w projektancie".](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Z listy **Włącz łączniki** na platformie Azure wybierz pozycję Użyj łączników z platformy **Azure,** która ma zastosowanie do wszystkich łączników zarządzanych, które są dostępne i wdrożone na platformie Azure, a nie tylko łączników dla usług platformy Azure.

   ![Zrzut ekranu przedstawiający okienko Eksploratora z otwartą listą "Włącz łączniki na platformie Azure" i wybranymi opcjami "Użyj łączników z platformy Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Bez stanowe przepływy pracy obsługują obecnie tylko *akcje dla* [zarządzanych](../connectors/managed.md)łączników , które są wdrażane na platformie Azure, a nie wyzwalaczy. Mimo że masz możliwość włączenia łączników na platformie Azure dla bez stanowego przepływu pracy, projektant nie pokazuje żadnych wyzwalaczy łącznika zarządzanego do wyboru.

1. Z listy **Wybierz subskrypcję** wybierz subskrypcję platformy Azure do użycia dla projektu aplikacji logiki.

   ![Zrzut ekranu przedstawiający okienko Eksplorator z polem "Wybierz subskrypcję" i wybraną subskrypcją.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Z listy grup zasobów wybierz pozycję **Utwórz nową grupę zasobów.**

   ![Zrzut ekranu przedstawiający okienko Eksplorator z listą grup zasobów i wybranymi opcjami "Utwórz nową grupę zasobów".](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Podaj nazwę grupy zasobów i naciśnij klawisz Enter. W tym przykładzie użyto wartości `Fabrikam-Workflows-RG`.

   ![Zrzut ekranu przedstawiający okienko Eksplorator i pole nazwy grupy zasobów.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Na liście lokalizacji znajdź i wybierz region platformy Azure do użycia podczas tworzenia grupy zasobów i zasobów. W tym przykładzie **użyto zachodnio-środkowych stanów USA**.

   ![Zrzut ekranu przedstawiający okienko Eksplorator z listą lokalizacji i wybranymi opcjami "Zachodnio-środkowe stany USA".](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Po zakończeniu tego kroku Visual Studio Code projektanta przepływu pracy.

   > [!NOTE]
   > Po Visual Studio Code interfejsu API czasu projektowania przepływu pracy może zostać wyświetlony komunikat informujący o tym, że uruchomienie może potrwać kilka sekund. Możesz zignorować ten komunikat lub wybrać przycisk **OK.**
   >
   > Jeśli projektant nie otworzy się, przejrzyj sekcję rozwiązywania problemów. Projektant [nie może otworzyć .](#designer-fails-to-open)

   Po wyświetleniu projektanta w projektancie zostanie wyświetlony monit Wybierz operację i zostanie on domyślnie wybrany, co pokazuje **okienko Dodaj akcję.** 

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Następnie dodaj [wyzwalacz i akcje do przepływu](#add-trigger-actions) pracy.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Dodawanie wyzwalacza i akcji

Po otwarciu projektanta w projektancie zostanie wyświetlony monit Wybierz **operację** i zostanie on domyślnie wybrany. Teraz możesz rozpocząć tworzenie przepływu pracy, dodając wyzwalacz i akcje.

Przepływ pracy w tym przykładzie używa tego wyzwalacza i tych akcji:

* Wbudowany wyzwalacz żądania [,](../connectors/connectors-native-reqres.md)po otrzymaniu żądania **HTTP,** który odbiera wywołania przychodzące lub żądania i tworzy punkt końcowy, który mogą wywoływać inne usługi lub aplikacje logiki.

* Akcja [programu Outlook w usłudze Office 365](../connectors/connectors-create-api-office365-outlook.md), Wyślij wiadomość **e-mail.**

* Wbudowana akcja [Odpowiedź](../connectors/connectors-native-reqres.md), która umożliwia wysłanie odpowiedzi i zwrócenie danych z powrotem do wywołującego.

### <a name="add-the-request-trigger"></a>Dodawanie wyzwalacza Żądania

1. Obok projektanta w okienku Dodawanie wyzwalacza  w polu Wyszukiwania wybierz  operację upewnij się, że jest zaznaczona opcja Wbudowane, aby można było wybrać wyzwalacz uruchamiany natywnie. 

1. W **polu wyszukiwania Wybierz operację** wprowadź , a następnie wybierz wbudowany wyzwalacz Żądanie o nazwie `when a http request` Po **otrzymaniu żądania HTTP.**

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i okienko **Dodaj wyzwalacz** z wybranym wyzwalaczem "Po otrzymaniu żądania HTTP".](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Gdy wyzwalacz pojawi się w projektancie, zostanie otwarte okienko szczegółów wyzwalacza, w którym zostaną wyświetlone właściwości, ustawienia i inne akcje wyzwalacza.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy z wybranym wyzwalaczem "Po otrzymaniu żądania HTTP" i otwartym okienkiem szczegółów wyzwalacza.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Jeśli okienko szczegółów nie zostanie wyświetlone, upewnij się, że wyzwalacz został wybrany w projektancie.

1. Jeśli musisz usunąć element z projektanta, wykonaj następujące kroki, aby [usunąć elementy z projektanta](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Dodawanie akcji usługi Office 365 Outlook

1. W projektancie w obszarze dodanego wyzwalacza wybierz pozycję **Nowy krok**.

   W **projektancie zostanie** wyświetlony monit Wybierz operację, **a** okienko Dodaj akcję zostanie ponownie otwarte, aby można było wybrać następną akcję.

1. W **okienku Dodawanie akcji**  w polu wyszukiwania Wybierz operację wybierz pozycję **Azure,** aby znaleźć i wybrać akcję łącznika zarządzanego wdrożonego na platformie Azure.

   W tym przykładzie wybiera i używa akcji Office 365 Outlook **Wyślij wiadomość e-mail (wersja 2).**

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i okienko **Dodaj akcję** z wybraną akcją "Wyślij wiadomość e-mail" usługi Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. W okienku szczegółów akcji wybierz pozycję **Zaloguj,** aby utworzyć połączenie z kontem e-mail.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i okienko **Wyślij wiadomość e-mail (V2)** z wybraną pozycję "Zaloguj".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Gdy Visual Studio Code monit o zgodę na dostęp do konta e-mail, wybierz pozycję **Otwórz**.

   ![Zrzut ekranu przedstawiający Visual Studio Code monit o zezwolenie na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Aby uniknąć przyszłych monitów, wybierz **pozycję Konfiguruj zaufane domeny,** aby można było dodać stronę uwierzytelniania jako zaufaną domenę.

1. Postępuj zgodnie z kolejnymi monitami, aby się zalogować, zezwolić na dostęp i zezwolić na powrót do Visual Studio Code.

   > [!NOTE]
   > Jeśli przed ukończeniem monitów minie zbyt dużo czasu, proces uwierzytelniania zostanie umyty i zakończy się niepowodzeniem. W takim przypadku wróć do projektanta i spróbuj ponownie się zalogować, aby utworzyć połączenie.

1. Gdy rozszerzenie Azure Logic Apps (wersja zapoznawcza) wyświetli monit o zgodę na dostęp do konta e-mail, wybierz pozycję **Otwórz.** Postępuj zgodnie z kolejnymi monitami, aby zezwolić na dostęp.

   ![Zrzut ekranu przedstawiający monit rozszerzenia w wersji zapoznawczej o zezwolenie na dostęp.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Aby uniknąć przyszłych monitów, wybierz pozycję Nie **pytaj ponownie o to rozszerzenie.**

   Po Visual Studio Code połączenia niektóre łączniki pokazują komunikat informujący o tym, że `The connection will be valid for {n} days only` . Ten limit czasu dotyczy tylko czasu trwania podczas tworzenia aplikacji logiki w Visual Studio Code. Po wdrożeniu ten limit nie ma już stosowania, ponieważ aplikacja logiki może uwierzytelniać się w czasie wykonywania przy użyciu automatycznie włączonej [tożsamości zarządzanej przypisanej przez system.](../logic-apps/create-managed-service-identity.md) Ta tożsamość zarządzana różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę przypisaną przez system tożsamość zarządzaną, połączenia nie będą działać w czasie wykonywania.

1. Jeśli w projektancie nie jest **wyświetlana** akcja Wyślij wiadomość e-mail, wybierz tę akcję.

1. W okienku szczegółów akcji na karcie **Parametry** podaj wymagane informacje dotyczące akcji, na przykład:

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy ze szczegółami akcji "Wyślij wiadomość e-mail" usługi Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*Twój adres e-mail*> | Adresat wiadomości e-mail, który może być Twoim adresem e-mail do celów testowych. W tym przykładzie użyto fikcyjnej wiadomości e-mail, `sophiaowen@fabrikam.com` . |
   | **Temat** | Tak | `An email from your example workflow` | Temat wiadomości e-mail |
   | **Treść** | Tak | `Hello from your example workflow!` | Zawartość treści wiadomości e-mail |
   ||||

   > [!NOTE]
   > Jeśli chcesz wprowadzić jakiekolwiek zmiany w okienku szczegółów na  karcie **Ustawienia,** Statyczny wynik  lub Uruchom po, pamiętaj, aby wybrać pozycję Gotowe, aby zatwierdzić te zmiany przed przełączeniem kart lub zmianą fokusu w projektancie. W przeciwnym Visual Studio Code zmiany nie zostaną wprowadzone. Aby uzyskać więcej informacji, zapoznaj się ze Logic Apps znane problemy w publicznej wersji [zapoznawczej w usłudze GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

1. W projektancie wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Aby lokalnie uruchomić przepływ pracy, który używa wyzwalacza lub akcji opartych na webhook, takich jak wbudowany wyzwalacz lub akcja http [webhook,](../connectors/connectors-native-webhook.md)należy włączyć tę funkcję, ustawiając przekazywanie dalej dla adresu [URL](#webhook-setup)wywołania zwrotnego.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Włączanie lokalnie uruchomionych webhook

Jeśli używasz wyzwalacza lub akcji opartej na webhook, takiej jak http **webhook,** z aplikacją logiki uruchomianą na platformie Azure, środowisko uruchomieniowe usługi Logic Apps subskrybuje punkt końcowy usługi, generując i rejestrując adres URL wywołania zwrotnego w tym punkcie końcowym. Wyzwalacz lub akcja czeka, aż punkt końcowy usługi wywoła adres URL. Jednak podczas pracy z programem Visual Studio Code adres URL wygenerowanego wywołania zwrotnego rozpoczyna się od `http://localhost:7071/...` . Ten adres URL dotyczy serwera localhost, który jest prywatny, więc punkt końcowy usługi nie może wywołać tego adresu URL.

Aby lokalnie uruchamiać wyzwalacze i akcje oparte na webhook w usłudze Visual Studio Code, należy skonfigurować publiczny adres URL, który uwidacznia serwer localhost i bezpiecznie przekazuje wywołania z punktu końcowego usługi do adresu URL wywołania zwrotnego. Możesz użyć usługi przekazywania dalej i narzędzia, takiego jak [**ngrok**](https://ngrok.com/), które otwiera tunel HTTP do portu hosta lokalnego, lub własnego narzędzia.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Konfigurowanie przekazywania wywołań przy użyciu **narzędzia ngrok**

1. [Zarejestruj się, aby **uzyskać konto ngrok,**](https://dashboard.ngrok.com/signup) jeśli go nie masz. W przeciwnym [razie zaloguj się do swojego konta](https://dashboard.ngrok.com/login).

1. Pobierz osobisty token uwierzytelniania, którego klient **ngrok** potrzebuje do nawiązania połączenia i uwierzytelnienia dostępu do konta.

   1. Aby znaleźć stronę [tokenu uwierzytelniania](https://dashboard.ngrok.com/auth/your-authtoken), w menu pulpitu nawigacyjnego konta rozwiń pozycję **Uwierzytelnianie** i wybierz **pozycję Twój uwierzytelniony .**

   1. Z pola **Your Authtoken (Twoje uwierzytelnione)** skopiuj token do bezpiecznej lokalizacji.

1. Ze strony [ **pobierania ngrok**](https://ngrok.com/download) lub pulpitu nawigacyjnego konta pobierz wersję **ngrok** i wyodrębnij plik zip. [](https://dashboard.ngrok.com/get-started/setup) Aby uzyskać więcej informacji, zobacz [Krok 1. Rozpakuj w celu zainstalowania programu](https://ngrok.com/download).

1. Na komputerze otwórz narzędzie wiersza polecenia. Przejdź do lokalizacji, w której znajduje **sięngrok.exe** plik.

1. Połącz klienta **ngrok** z kontem **ngrok,** uruchamiając następujące polecenie. Aby uzyskać więcej informacji, [zobacz Krok 2. Łączenie konta.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Otwórz tunel HTTP do portu localhost 7071, uruchamiając następujące polecenie. Aby uzyskać więcej informacji, zobacz [Krok 3.](https://ngrok.com/download)

   `ngrok http 7071`

1. W danych wyjściowych znajdź następujący wiersz:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Skopiuj i zapisz adres URL w tym formacie: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Konfigurowanie adresu URL przekazywania w ustawieniach aplikacji

1. W Visual Studio Code w projektancie dodaj wyzwalacz lub akcję **HTTP + webhook.**

1. Po wyświetleniu monitu o lokalizację punktu końcowego hosta wprowadź utworzony wcześniej adres URL przekazywania (przekierowania).

   > [!NOTE]
   > Zignorowanie monitu powoduje, że pojawi się ostrzeżenie z prośbą o podanie adresu URL przekazywania, więc wybierz pozycję **Konfiguruj** i wprowadź adres URL. Po zakończeniu tego kroku monit nie będzie ponownie wyświetlany dla kolejnych wyzwalaczy lub akcji, które można dodać.
   >
   > Aby ponownie utworzyć monit, na poziomie głównym projektu otwórz plik **local.settings.js** menu skrótów pliku, a następnie wybierz pozycję Konfiguruj punkt końcowy przekierowania elementu **webhook.** Zostanie wyświetlony monit, aby można było podać adres URL przekazywania.

   Visual Studio Code dodaje adres URL przekazywania do **local.settings.jsw** pliku w folderze głównym projektu. W `Values` obiekcie zostanie wyświetlona właściwość o nazwie i ustawiona na adres `Workflows.WebhookRedirectHostUri` URL przekazywania, na przykład:
   
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

Przy pierwszym uruchomieniu lokalnej sesji debugowania lub uruchomieniu przepływu pracy bez debugowania środowisko uruchomieniowe usługi Logic Apps rejestruje przepływ pracy z punktem końcowym usługi i subskrybuje ten punkt końcowy w celu powiadamiania o operacjach na nich. Przy następnym uruchomieniu przepływu pracy środowisko uruchomieniowe nie zarejestruje się ani nie ponownie zasubskrybuje, ponieważ rejestracja subskrypcji już istnieje w magazynie lokalnym.

Po zatrzymaniu sesji debugowania dla uruchomienia przepływu pracy, który używa lokalnie uruchamianych wyzwalaczy lub akcji opartych na elementy webhook, istniejące rejestracje subskrypcji nie są usuwane. Aby wyrejestrować, musisz ręcznie usunąć rejestracje subskrypcji.

> [!NOTE]
> Po uruchomieniu przepływu pracy w oknie terminalu mogą być wyświetlane błędy podobne do tych w tym przykładzie:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> W takim przypadku otwórzlocal.settings.js **pliku** w folderze głównym projektu i upewnij się, że właściwość jest ustawiona na `true` wartość :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Zarządzanie punktami przerwania na potrzeby debugowania

Przed uruchomieniem i przetestowanie przepływu pracy aplikacji logiki przez uruchomienie sesji debugowania można ustawić punkty przerwania wewnątrz pliku **workflow.jsdla** każdego przepływu pracy. [](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) Nie jest wymagana żadna inna konfiguracja. 

Obecnie punkty przerwania są obsługiwane tylko dla akcji, a nie wyzwalaczy. Każda definicja akcji ma następujące lokalizacje punktów przerwania:

* Ustaw początkowy punkt przerwania w wierszu, który pokazuje nazwę akcji. Gdy ten punkt przerwania zostanie trafiony podczas sesji debugowania, możesz przejrzeć dane wejściowe akcji przed ich oceną.

* Ustaw końcowy punkt przerwania w wierszu, który pokazuje zamykający nawias klamrowy akcji (**}**). Gdy ten punkt przerwania zostanie trafienia podczas sesji debugowania, możesz przejrzeć wyniki akcji przed zakończeniem działania akcji.

Aby dodać punkt przerwania, wykonaj następujące kroki:

1. Otwórz plik **workflow.jsdla** przepływu pracy, który chcesz debugować.

1. W wierszu, w którym chcesz ustawić punkt przerwania, w lewej kolumnie wybierz wewnątrz tej kolumny. Aby usunąć punkt przerwania, wybierz ten punkt przerwania.

   Po rozpoczęciu sesji debugowania po lewej stronie okna kodu zostanie wyświetlony widok Uruchom, a w górnej części pojawi się pasek narzędzi Debugowanie.

   > [!NOTE]
   > Jeśli widok Uruchom nie zostanie automatycznie wyświetlony, naciśnij klawisze Ctrl+Shift+D.

1. Aby przejrzeć dostępne informacje w przypadku trafienia punktu przerwania, w widoku Uruchom sprawdź **okienko** Zmienne.

1. Aby kontynuować wykonywanie przepływu pracy, na pasku narzędzi Debugowanie wybierz pozycję **Kontynuuj** (przycisk odtwarzania).

Punkty przerwania można dodawać i usuwać w dowolnym momencie podczas uruchamiania przepływu pracy. Jeśli jednak zaktualizujemy plik **workflow.jspo** uruchomieniu, punkty przerwania nie będą automatycznie aktualizowane. Aby zaktualizować punkty przerwania, uruchom ponownie aplikację logiki.

Aby uzyskać ogólne informacje, zobacz [Punkty przerwania — Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Lokalne uruchamianie, testowanie i debugowanie

Aby przetestować aplikację logiki, wykonaj następujące kroki, aby rozpocząć sesję debugowania i znaleźć adres URL punktu końcowego utworzonego przez wyzwalacz żądania. Ten adres URL jest potrzebny, aby można było później wysłać żądanie do tego punktu końcowego.

1. Aby łatwiej debugować bez stanowy przepływ pracy, możesz włączyć historię uruchamiania [dla tego przepływu pracy.](#enable-run-history-stateless)

1. Na pasku Visual Studio Code, otwórz menu **Uruchom,** a następnie wybierz pozycję **Rozpocznij debugowanie** (F5).

   Zostanie **otwarte okno Terminal,** aby można było przejrzeć sesję debugowania.

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd "Błąd występuje po uruchomieniu **preLaunchTask 'generateDebugSymbols'"**, zobacz sekcję rozwiązywania problemów, nie można uruchomić sesji [debugowania](#debugging-fails-to-start).

1. Teraz znajdź adres URL wywołania zwrotnego dla punktu końcowego w wyzwalaczu żądania.

   1. Otwórz ponownie okienko Eksplorator, aby wyświetlić projekt.

   1. Zworkflow.js **menu** skrótów pliku wybierz pozycję **Przegląd.**

      ![Zrzut ekranu przedstawiający okienko Eksploratora i okno skrótów dla workflow.jspliku z wybraną oknie "Przegląd".](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Znajdź wartość **Callback URL (Adres URL** wywołania zwrotnego), która wygląda podobnie do tego adresu URL dla przykładowego wyzwalacza Żądania:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Zrzut ekranu przedstawiający stronę przeglądu przepływu pracy z adresem URL wywołania zwrotnego](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Aby przetestować adres URL wywołania zwrotnego przez wyzwolenie przepływu pracy aplikacji logiki, otwórz narzędzie [Postman](https://www.postman.com/downloads/) lub preferowane narzędzie do tworzenia i wysyłania żądań.

   Ten przykład jest kontynuowany przy użyciu narzędzia Postman. Aby uzyskać więcej informacji, zobacz [Postman Wprowadzenie](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na pasku narzędzi narzędzia Postman wybierz pozycję **Nowy.**

      ![Zrzut ekranu przedstawiający program Postman z wybranym przyciskiem Nowy](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. W **okienku Utwórz nowy** w obszarze **Bloki konstrukcyjne** wybierz pozycję **Zażądaj**.

   1. W **oknie Zapisywanie** żądania w **obszarze Nazwa żądania** podaj nazwę żądania, na przykład `Test workflow trigger` .

   1. W **obszarze Wybierz kolekcję lub folder do zapisania w programie** wybierz **pozycję Utwórz kolekcję**.

   1. W **obszarze Wszystkie** kolekcje podaj nazwę kolekcji do utworzenia w celu organizowania żądań, naciśnij klawisz Enter i wybierz pozycję Zapisz <***kolekcji.* >** W tym przykładzie `Logic Apps requests` użyto nazwy kolekcji .

      Zostanie otwarte okienko żądania postman, dzięki czemu można wysłać żądanie do adresu URL wywołania zwrotnego dla wyzwalacza żądania.

      ![Zrzut ekranu przedstawiający program Postman z okienkiem otwartego żądania](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Wróć do Visual Studio Code. Na stronie przeglądu przepływu pracy skopiuj wartość właściwości **Adres URL** wywołania zwrotnego.

   1. Wróć do postmanu. W okienku żądania obok listy metod, na której obecnie jest wyświetlona domyślna metoda żądania **GET,** wklej skopiowany wcześniej adres URL wywołania zwrotnego w polu adresu, a następnie wybierz pozycję **Wyślij**.

      ![Zrzut ekranu przedstawiający program Postman i adres URL wywołania zwrotnego w polu adresu z wybranym przyciskiem Wyślij](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Przykładowy przepływ pracy aplikacji logiki wysyła wiadomość e-mail podobną do tej w tym przykładzie:

      ![Zrzut ekranu przedstawiający pocztę e-mail programu Outlook zgodnie z opisem w przykładzie](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. W Visual Studio Code wróć do strony przeglądu przepływu pracy.

   Jeśli utworzono stanowy przepływ pracy, po wysłaniu wysłanego żądania wyzwalany jest przepływ pracy, na stronie przeglądu jest przedstawiany stan i historia uruchomienia przepływu pracy.

   > [!TIP]
   > Jeśli stan uruchomienia nie jest wyświetlany, spróbuj odświeżyć stronę przeglądu, wybierając pozycję **Odśwież.** W przypadku wyzwalacza, który został pominięty z powodu niezaspokojonych kryteriów lub nieznajdowania żadnych danych, uruchomienie nie jest uruchamiane.

   ![Zrzut ekranu przedstawiający stronę przeglądu przepływu pracy ze stanem uruchomienia i historią](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Stan uruchomienia | Opis |
   |------------|-------------|
   | **Zostało przerwane** | Przebieg został zatrzymany lub nie zakończył się z powodu problemów zewnętrznych, takich jak przerwa w pracy systemu lub przerwana subskrypcja platformy Azure. |
   | **Anulowane** | Przebieg został wyzwolony i uruchomiony, ale otrzymał żądanie anulowania. |
   | **Niepowodzenie** | Co najmniej jedna akcja w uruchomieniu nie powiodła się. Żadne kolejne akcje w przepływie pracy nie zostały ustawione do obsługi błędu. |
   | **Uruchomienie** | Przebieg został wyzwolony i jest w toku, ale ten stan [](logic-apps-limits-and-config.md) może być również wyświetlany dla uruchomienia, które zostało ograniczone z powodu limitów akcji lub [bieżącego planu cenowego.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Porada:** W przypadku skonfigurowania [rejestrowania diagnostycznego](monitor-logic-apps-log-analytics.md)możesz uzyskać informacje o wszystkich zdarzeniach ograniczania przepustowości, które się zdarzają. |
   | **Powodzenie** | Przebieg zakończył się pomyślnie. Jeśli jakakolwiek akcja nie powiodła się, kolejna akcja w przepływie pracy obsłużyła ten błąd. |
   | **Uchylino czas** | Przekroczono limit czasu uruchomienia, ponieważ bieżący czas trwania przekroczył limit czasu trwania uruchomienia, który jest kontrolowany przez ustawienie Przechowywanie historii przebiegów [ **w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits). Czas trwania uruchomienia jest obliczany przy użyciu limitu czasu rozpoczęcia i czasu trwania uruchomienia w tym czasie rozpoczęcia. <p><p>**Uwaga:** jeśli czas trwania uruchomienia przekracza również bieżący limit przechowywania historii przebiegów *,* który jest również kontrolowany przez ustawienie Przechowywanie historii przebiegów w dniach, [](logic-apps-limits-and-config.md#run-duration-retention-limits)przebieg zostanie wyczyszony z historii przebiegów za pomocą codziennego zadania oczyszczania. Niezależnie od tego, czy limit czasu uruchomienia zostanie ukończony, okres przechowywania jest zawsze obliczany przy użyciu czasu rozpoczęcia uruchomienia i *bieżącego limitu* przechowywania. Dlatego jeśli zmniejszysz limit czasu trwania dla przebiegów w locie, limit czasu uruchomienia zostanie ukreślony. Jednak przebieg pozostaje lub jest wyczyszowany z historii przebiegów w zależności od tego, czy czas trwania uruchomienia przekroczył limit przechowywania. |
   | **Oczekiwanie** | Przebieg nie został uruchomiony lub został wstrzymany, na przykład z powodu wcześniejszego wystąpienia przepływu pracy, które nadal działa. |
   |||

1. Aby przejrzeć stany dla każdego kroku w określonym uruchomieniu oraz dane wejściowe i wyjściowe kroku, wybierz przycisk wielokropka (**...**) dla tego uruchomienia, a następnie wybierz pozycję **Pokaż** uruchomienie .

   ![Zrzut ekranu przedstawiający wiersz historii uruchamiania przepływu pracy z przyciskiem wielokropka i wybranym przyciskiem "Pokaż przebieg"](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code widok monitorowania i pokazuje stan każdego kroku w uruchomieniu.

   ![Zrzut ekranu przedstawiający każdy krok w uruchomieniu przepływu pracy i jego stan](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Jeśli uruchomienie nie powiodło się, a krok w widoku monitorowania pokazuje błąd, ten problem może wynikać z dłuższej nazwy wyzwalacza lub nazwy akcji, która powoduje przekroczenie domyślnego limitu znaków przez podstawowy Uniform Resource Identifier `400 Bad Request` (URI). Aby uzyskać więcej informacji, zobacz ["400 Bad Request" (400 Złe żądanie).](#400-bad-request)

   Poniżej podano możliwe stany, które mogą mieć poszczególne kroki przepływu pracy:

   | Stan akcji | Ikona | Opis |
   |---------------|------|-------------|
   | **Zostało przerwane** | ![Ikona stanu akcji "Przerwano"][aborted-icon] | Akcja została zatrzymana lub nie została przerwana z powodu problemów zewnętrznych, na przykład outage systemu lub przerwanej subskrypcji platformy Azure. |
   | **Anulowane** | ![Ikona stanu akcji "Anulowano"][cancelled-icon] | Akcja została uruchomiona, ale otrzymano żądanie anulowania. |
   | **Niepowodzenie** | ![Ikona stanu akcji "Niepowodzenie"][failed-icon] | Akcja nie powiodła się. |
   | **Uruchomienie** | ![Ikona stanu akcji "Uruchomiona"][running-icon] | Akcja jest obecnie uruchomiona. |
   | **Pominięto** | ![Ikona stanu akcji "Pominięto"][skipped-icon] | Akcja została pominięta, ponieważ bezpośrednio poprzedzana akcja nie powiodła się. Akcja ma warunek, który wymaga pomyślnego zakończenia poprzedniej akcji przed `runAfter` uruchomieniem bieżącej akcji. |
   | **Powodzenie** | ![Ikona stanu akcji "Powodzenie"][succeeded-icon] | Akcja zakończyła się pomyślnie. |
   | **Powodzenie po ponownych próbach** | ![Ikona stanu akcji "Powodzenie z ponowną próbą"][succeeded-with-retries-icon] | Akcja zakończyła się pomyślnie, ale tylko po co najmniej jednej próbie. Aby przejrzeć historię ponawiania prób, w widoku szczegółów historii uruchamiania wybierz tę akcję, aby wyświetlić dane wejściowe i wyjściowe. |
   | **Uchylino czas** | ![Ikona stanu akcji "Uchyliliśmy czas"][timed-out-icon] | Akcja została zatrzymana z powodu limitu czasu określonego przez ustawienia tej akcji. |
   | **Oczekiwanie** | ![Ikona stanu akcji "Oczekiwanie"][waiting-icon] | Dotyczy akcji element webhook, która oczekuje na żądanie przychodzące od wywołującego. |
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

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, wybierz krok, który chcesz sprawdzić.

   ![Zrzut ekranu przedstawiający stan każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe w rozwiniętej akcji "Wyślij wiadomość e-mail"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Aby jeszcze bardziej przejrzeć nieprzetworzone dane wejściowe i wyjściowe dla tego kroku, wybierz pozycję Pokaż nieprzetworzone dane **wejściowe** lub **Pokaż nieprzetworzone dane wyjściowe**.

1. Aby zatrzymać sesję debugowania, w menu **Uruchom** wybierz pozycję **Zatrzymaj** debugowanie (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Zwracanie odpowiedzi

Aby zwrócić odpowiedź do wywołującego, który wysłał żądanie do aplikacji logiki, możesz użyć wbudowanej akcji Odpowiedź dla przepływu pracy, który rozpoczyna się od wyzwalacza Żądania. [](../connectors/connectors-native-reqres.md)

1. W projektancie przepływu pracy w obszarze akcji **Wyślij wiadomość e-mail** wybierz **pozycję Nowy krok.**

   W **projektancie zostanie** wyświetlony monit Wybierz operację, **a** okienko Dodaj akcję zostanie ponownie otwarte, aby można było wybrać następną akcję.

1. W **okienku Dodawanie akcji** w **polu** wyszukiwania Wybierz akcję upewnij się, że **wybrano pozycję** Wbudowane. W polu wyszukiwania wprowadź `response` i wybierz **akcję** Odpowiedź.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy z wybraną akcją Odpowiedź.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Gdy akcja **Odpowiedź** pojawi się w projektancie, zostanie automatycznie otwarte okienko szczegółów akcji.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy z otwartym okienkiem szczegółów akcji "Odpowiedź" i właściwością "Treść" ustawioną na wartość właściwości "Treść" akcji "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Na **karcie Parametry** podaj wymagane informacje dla funkcji, którą chcesz wywołać.

   W tym przykładzie **zwracana jest** wartość właściwości Treść, która jest wynikiem akcji **Wyślij wiadomość e-mail.**

   1. Kliknij wewnątrz pola **właściwości Treść,** aby wyświetlić listę zawartości dynamicznej i wyświetlić dostępne wartości wyjściowe z poprzedniego wyzwalacza i akcji w przepływie pracy.

      ![Zrzut ekranu przedstawiający okienko szczegółów akcji "Odpowiedź" ze wskaźnikiem myszy wewnątrz właściwości "Treść", aby wyświetlić listę zawartości dynamicznej.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Na liście zawartości dynamicznej w obszarze **Wyślij wiadomość e-mail** wybierz pozycję **Treść.**

      ![Zrzut ekranu przedstawiający otwartą listę zawartości dynamicznej. Na liście w nagłówku "Wyślij wiadomość e-mail" jest zaznaczona wartość wyjściowa "Treść".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Gdy wszystko będzie gotowe, właściwość  Treść akcji Odpowiedź będzie  teraz ustawiona na wartość wyjściową Treść akcji Wyślij wiadomość **e-mail.**

      ![Zrzut ekranu przedstawiający stan każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe rozwiniętej akcji "Odpowiedź".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. W projektancie wybierz pozycję **Zapisz.**

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Ponowne test aplikacji logiki

Po wrzuceniu aktualizacji do aplikacji logiki możesz uruchomić kolejny test, uruchamiając ponownie debuger w programie Visual Studio i wysyłając kolejne żądanie w celu wyzwolenia zaktualizowanej aplikacji logiki, podobnie jak w przypadku kroków w teście [Run, test,](#run-test-debug-locally)and debug locally (Uruchamianie, testowanie i debugowanie lokalnie).

1. Na pasku Visual Studio Code otwórz menu **Uruchom,** a następnie wybierz pozycję **Rozpocznij debugowanie** (F5).

1. W narzędziu Postman lub narzędziu do tworzenia i wysyłania żądań wyślij kolejne żądanie w celu wyzwolenia przepływu pracy.

1. Jeśli utworzono stanowy przepływ pracy, na stronie przeglądu przepływu pracy sprawdź stan ostatniego uruchomienia. Aby wyświetlić stan, dane wejściowe i dane wyjściowe dla każdego kroku w tym uruchomieniu, wybierz przycisk wielokropka (**...**) dla tego uruchomienia, a następnie wybierz pozycję **Pokaż** uruchamianie .

   Oto na przykład stan krok po kroku uruchomienia po zaktualizowaniu przykładowego przepływu pracy za pomocą akcji Odpowiedź.

   ![Zrzut ekranu przedstawiający stan każdego kroku w zaktualizowanym przepływie pracy oraz dane wejściowe i wyjściowe rozwiniętej akcji "Odpowiedź".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Aby zatrzymać sesję debugowania, w menu **Uruchom** wybierz pozycję **Zatrzymaj debugowanie** (Shift + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Znajdowanie nazw domen dla dostępu za zaporą

Przed wdrożeniem i uruchomieniem przepływu pracy aplikacji logiki w środowisku Azure Portal, jeśli środowisko ma ścisłe wymagania dotyczące sieci lub zapory ograniczające ruch, musisz skonfigurować uprawnienia dla wszystkich połączeń wyzwalaczy lub akcji, które istnieją w przepływie pracy.

Aby znaleźć w pełni kwalifikowane nazwy domen (FQNS) dla tych połączeń, wykonaj następujące kroki:

1. W projekcie aplikacji logiki otwórz plikconnections.jsutworzony po dodaniu pierwszego wyzwalacza lub akcji opartej na połączeniu do przepływu pracy i znajdź obiekt  `managedApiConnections` .

1. Dla każdego utworzonego połączenia znajdź, skopiuj i zapisz wartość właściwości w bezpiecznym miejscu, aby można było skonfigurować zaporę `connectionRuntimeUrl` przy użyciu tych informacji.

   Ten przykład **connections.jspliku** zawiera dwa połączenia: połączenie AS2 i połączenie usługi Office 365 z tymi `connectionRuntimeUrl` wartościami:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Usługa Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

Z Visual Studio Code można bezpośrednio opublikować projekt na platformie Azure, która wdraża aplikację logiki przy użyciu nowego typu zasobu Aplikacja logiki **(wersja** zapoznawcza). Podobnie jak w przypadku zasobu aplikacji funkcji w Azure Functions, wdrożenie dla tego nowego typu zasobu wymaga wybrania [planu hostingu](../app-service/overview-hosting-plans.md)i warstwy cenowej , którą można skonfigurować podczas wdrażania. Aby uzyskać więcej informacji na temat planów hostingu i cen, zapoznaj się z tymi tematami:

* [Skalowanie w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)

Aplikację logiki można opublikować jako nowy zasób, który automatycznie tworzy wszelkie niezbędne zasoby, takie jak konto usługi [Azure Storage, podobnie jak w przypadku wymagań aplikacji funkcji.](../azure-functions/storage-considerations.md) Możesz też opublikować aplikację logiki we wcześniej wdrożonym zasobie aplikacji logiki **(wersja** zapoznawcza), co spowoduje zastąpienie tej aplikacji logiki.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publikowanie w nowym zasobie aplikacji logiki (wersja zapoznawcza)

1. Na pasku Visual Studio Code wybierz ikonę Platformy Azure.

1. Na pasku **narzędzi okienka Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję Deploy to Logic App (Wd **wdrażaj w aplikacji logiki).**

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i pasek narzędzi okienka z wybranym oknie "Wdrażanie w aplikacji logiki".](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Jeśli zostanie wyświetlony monit, wybierz subskrypcję platformy Azure do użycia na użytek wdrożenia aplikacji logiki.

1. Z otwartej listy Visual Studio Code wybierz jedną z tych opcji:

   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) na platformie Azure** (szybkie)
   * **Tworzenie nowej aplikacji logiki (wersja zapoznawcza) w zaawansowanej wersji platformy Azure**
   * Wcześniej wdrożony **zasób aplikacji logiki (wersja zapoznawcza),** jeśli istnieje

   Ten przykład jest kontynuacją **tematu Tworzenie nowej aplikacji logiki (wersja zapoznawcza) w zaawansowanej wersji platformy Azure.**

   ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" z wybraną listą "Utwórz nową aplikację logiki (wersja zapoznawcza) na platformie Azure".](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Aby utworzyć nowy **zasób aplikacji logiki (wersja zapoznawcza),** wykonaj następujące kroki:

   1. Podaj globalnie unikatową nazwę nowej aplikacji logiki, która będzie nazwą używaną dla zasobu aplikacji **logiki (wersja zapoznawcza).** W tym przykładzie użyto wartości `Fabrikam-Workflows-App`.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i monit o podanie nazwy nowej aplikacji logiki do utworzenia.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Wybierz plan [hostingu](../app-service/overview-hosting-plans.md) dla nowej aplikacji logiki: [ **App Service** (dedykowana)](../azure-functions/dedicated-plan.md) lub [**Premium.**](../azure-functions/functions-premium-plan.md)

      > [!IMPORTANT]
      > Plany zużycia nie są obsługiwane ani dostępne dla tego typu zasobu. Wybrany plan ma wpływ na możliwości i warstwy cenowe, które są później dostępne dla Ciebie. Aby uzyskać więcej informacji, zapoznaj się z tymi tematami: 
      >
      > * [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)
      > * [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Na przykład plan Premium zapewnia dostęp do funkcji sieciowych, takich jak prywatne łączenie i integrowanie z sieciami wirtualnymi platformy Azure, podobnie jak Azure Functions podczas tworzenia i wdrażania aplikacji logiki. 
      > Aby uzyskać więcej informacji, zapoznaj się z tymi tematami:
      > 
      > * [Opcje sieciowe usługi Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps Running Anywhere — możliwości sieci z Azure Logic Apps zapoznawcza](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      W tym przykładzie użyto **App Service planie**.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i monit o wybranie opcji "App Service Plan" lub "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Utwórz nowy plan App Service lub wybierz istniejący plan. W tym przykładzie jest **wybierana App Service Utwórz nowy plan.**

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i monit o "Tworzenie nowego planu App Service" lub wybranie istniejącego App Service planu.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Podaj nazwę planu App Service, a następnie wybierz warstwę [cenową](../app-service/overview-hosting-plans.md) dla planu. W tym przykładzie wybierany jest bezpłatny plan **F1.**

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i monit o wybranie warstwy cenowej.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Aby uzyskać optymalną wydajność, znajdź i wybierz tę samą grupę zasobów co projekt dla wdrożenia.

      > [!NOTE]
      > Mimo że można utworzyć lub użyć innej grupy zasobów, może to mieć wpływ na wydajność. Jeśli utworzysz lub wybierzesz inną grupę zasobów, ale anulujesz ją po wyświetleniu monitu o potwierdzenie, wdrożenie również zostanie anulowane.

   1. W przypadku stanowych przepływów pracy wybierz **pozycję Utwórz nowe konto magazynu** lub istniejące konto magazynu.

      ![Zrzut ekranu przedstawiający okienko "Azure: Logic Apps (wersja zapoznawcza)" i monit o utworzenie lub wybranie konta magazynu.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Jeśli ustawienia tworzenia i wdrażania aplikacji logiki obsługują Application Insights [,](../azure-monitor/app/app-insights-overview.md)możesz opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale możesz utworzyć ten zasób z wyprzedzeniem [,](../azure-monitor/app/create-workspace-resource.md)podczas wdrażania aplikacji logiki lub po wdrożeniu.

      Aby teraz włączyć rejestrowanie i śledzenie, wykonaj następujące kroki:

      1. Wybierz istniejący zasób Application Insights lub **utwórz nowy Application Insights zasobów.**

      1. W [Azure Portal](https://portal.azure.com)przejdź do swojego Application Insights zasobu.

      1. W menu zasobu wybierz pozycję **Przegląd.** Znajdź i skopiuj **wartość Klucz instrumentacji.**

      1. W Visual Studio Code w folderze głównym projektu otwórzlocal.settings.js **pliku.**

      1. W obiekcie dodaj właściwość i ustaw wartość na `Values` `APPINSIGHTS_INSTRUMENTATIONKEY` klucz instrumentacji, na przykład:

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
         > Możesz sprawdzić, czy nazwy wyzwalaczy i akcji są poprawnie wyświetlane Application Insights wystąpieniu.
         >
         > 1. W Azure Portal przejdź do zasobu Application Insights zasobów.
         >
         > 2. W menu zasobu w obszarze **Zbadaj** wybierz pozycję **Mapa aplikacji.**
         >
         > 3. Przejrzyj nazwy operacji, które są wyświetlane na mapie.
         >
         > Niektóre żądania przychodzące z wbudowanych wyzwalaczy mogą być wyświetlane jako duplikaty na mapie aplikacji. 
         > Zamiast używać formatu, te duplikaty używają nazwy przepływu pracy jako nazwy operacji i pochodzą z `WorkflowName.ActionName` Azure Functions hosta.

      1. Następnie możesz opcjonalnie dostosować poziom ważności danych śledzenia zbieranych i przesyłanych przez aplikację logiki do Application Insights logiki.

         Za każdym razem, gdy ma miejsce zdarzenie związane z przepływem pracy, na przykład wyzwolenie przepływu pracy lub uruchomienie akcji, środowisko uruchomieniowe emituje różne ślady. Te ślady obejmują okres istnienia przepływu pracy i obejmują między innymi następujące typy zdarzeń:

         * Działania usługi, takie jak uruchamianie, zatrzymywanie i błędy.
         * Zadania i działania dyspozytora.
         * Działanie przepływu pracy, takie jak wyzwalacz, akcja i uruchamianie.
         * Działanie żądania magazynu, takie jak powodzenie lub niepowodzenie.
         * Działanie żądania HTTP, takie jak przychodzące, wychodzące, powodzenie i niepowodzenie.
         * Wszelkie ślady rozwoju, takie jak komunikaty debugowania.

         Każdy typ zdarzenia jest przypisany do poziomu ważności. Na przykład poziom przechwytuje najbardziej szczegółowe komunikaty, a poziom przechwytuje ogólne działania w przepływie pracy, takie jak uruchomienie i zatrzymanie aplikacji logiki, przepływu pracy, wyzwalacza i `Trace` `Information` akcji. W tej tabeli opisano poziomy ważności i ich typy śledzenia:

         | Poziom ważności | Typ śledzenia |
         |----------------|------------|
         | Krytyczne | Dzienniki, które opisują nieodwracalny błąd w aplikacji logiki. |
         | Debugowanie | Dzienniki, których można użyć do badania podczas opracowywania, na przykład przychodzące i wychodzące wywołania HTTP. |
         | Błąd | Dzienniki, które wskazują błąd podczas wykonywania przepływu pracy, ale nie ogólny błąd w aplikacji logiki. |
         | Informacje | Dzienniki śledzące ogólne działania w aplikacji logiki lub przepływie pracy, na przykład: <p><p>— Po uruchomieniu i zakończeniu wyzwalacza, akcji lub uruchomienia. <br>— Gdy aplikacja logiki jest uruchamiana lub kończy się. |
         | Ślad | Dzienniki zawierające najbardziej szczegółowe komunikaty, na przykład żądania magazynu lub działania dyspozytora, a także wszystkie komunikaty związane z działaniem wykonywania przepływu pracy. |
         | Ostrzeżenie | Dzienniki, które wyróżnią nietypowy stan w aplikacji logiki, ale nie uniemożliwiają jej uruchomienia. |
         |||

         Aby ustawić poziom ważności, na poziomie głównym projektu otwórzhost.js **pliku** i znajdź `logging` obiekt . Ten obiekt kontroluje filtrowanie dzienników dla wszystkich przepływów pracy w aplikacji logiki i jest zgodny z układem ASP.NET Core do filtrowania [typów dzienników.](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)

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

         Jeśli obiekt `logging` nie zawiera obiektu `logLevel` zawierającego właściwość , dodaj te `Host.Triggers.Workflow` elementy. Ustaw właściwość na poziom ważności dla typu śledzenia, którego potrzebujesz, na przykład:

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

   Po zakończeniu kroków wdrażania program Visual Studio Code tworzenie i wdrażanie zasobów niezbędnych do opublikowania aplikacji logiki.

1. Aby przejrzeć i monitorować proces wdrażania, w menu **Widok** wybierz pozycję **Dane wyjściowe**. Na liście narzędzi okna Dane wyjściowe wybierz pozycję **Azure Logic Apps**.

   ![Zrzut ekranu przedstawiający okno Dane wyjściowe z wybraną Azure Logic Apps na liście pasków narzędzi wraz z postępem i stanami wdrożenia.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Po Visual Studio Code wdrażania aplikacji logiki na platformie Azure zostanie wyświetlony następujący komunikat:

   ![Zrzut ekranu przedstawiający komunikat informujący o pomyślnym ukończeniu wdrożenia na platformie Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulacje, Twoja aplikacja logiki jest teraz aktywna na platformie Azure i domyślnie włączona.

Następnie dowiesz się, jak wykonywać następujące zadania:

* [Dodaj pusty przepływ pracy do projektu](#add-workflow-existing-project).

* [Zarządzanie wdrożonych aplikacji logiki w Visual Studio Code](#manage-deployed-apps-vs-code) lub przy użyciu [Azure Portal](#manage-deployed-apps-portal).

* [Włącz historię uruchamiania dla bez stanowych przepływów pracy.](#enable-run-history-stateless)

* [Włącz widok monitorowania w Azure Portal dla wdrożonej aplikacji logiki.](#enable-monitoring)

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Dodawanie pustego przepływu pracy do projektu

Projekt aplikacji logiki może mieć wiele przepływów pracy. Aby dodać pusty przepływ pracy do projektu, wykonaj następujące kroki:

1. Na pasku Visual Studio Code wybierz ikonę Azure.

1. W okienku Azure obok opcji **Azure: Logic Apps (wersja zapoznawcza)** wybierz pozycję Utwórz przepływ **pracy** (ikona Azure Logic Apps).

1. Wybierz typ przepływu pracy, który chcesz dodać: **Stanowy lub** **Bez stanowy**

1. Podaj nazwę przepływu pracy.

Gdy wszystko będzie gotowe, w projekcie pojawi się nowy folder przepływu pracy wraz zworkflow.js **pliku** dla definicji przepływu pracy.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Zarządzanie wdrożonych aplikacji logiki w Visual Studio Code

W usłudze Visual Studio Code można wyświetlić wszystkie wdrożone aplikacje logiki w ramach subskrypcji platformy Azure, niezależnie od **tego,** czy są to zasoby oryginalnego typu Logic Apps, czy aplikacja logiki **(wersja zapoznawcza),** a następnie wybrać zadania, które ułatwiają zarządzanie tymi aplikacjami logiki. Jednak aby uzyskać dostęp do obu typów zasobów, potrzebne są rozszerzenia **Azure Logic Apps** i **Azure Logic Apps (wersja zapoznawcza)** dla Visual Studio Code.

1. Na pasku narzędzi po lewej stronie wybierz ikonę platformy Azure. W **okienku Azure: Logic Apps (wersja zapoznawcza)** rozwiń subskrypcję, która zawiera wszystkie wdrożone aplikacje logiki dla tej subskrypcji.

1. Otwórz aplikację logiki, którą chcesz zarządzać. Z menu skrótów aplikacji logiki wybierz zadanie, które chcesz wykonać.

   Możesz na przykład wybrać zadania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie lub usuwanie wdrożonej aplikacji logiki.

   ![Zrzut ekranu przedstawiający Visual Studio Code otwarte okienko rozszerzenia "Azure Logic Apps (wersja zapoznawcza)" i wdrożony przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Aby wyświetlić wszystkie przepływy pracy w aplikacji logiki, rozwiń aplikację logiki, a następnie rozwiń węzeł **Przepływy** pracy.

1. Aby wyświetlić określony przepływ pracy, otwórz menu skrótów przepływu pracy, a następnie wybierz pozycję Otwórz w projektancie **,** co spowoduje otwarcie przepływu pracy w trybie tylko do odczytu.

   Dostępne są następujące opcje edytowania przepływu pracy:

   * W Visual Studio Code projektu otwórz plikworkflow.js w projektancie przepływu pracy, dokonaj zmian i ponownie wdaj aplikację logiki na platformie Azure.

   * W Azure Portal [znajdź i otwórz aplikację logiki](#manage-deployed-apps-portal). Znajdowanie, edytowanie i zapisywanie przepływu pracy.

1. Aby otworzyć wdrożoną aplikację logiki w Azure Portal, otwórz menu skrótów aplikacji logiki i wybierz **pozycję Otwórz w portalu.**

   Aplikacja Azure Portal w przeglądarce, automatycznie zalogował się do portalu, jeśli zalogowano się do aplikacji Visual Studio Code i wyświetla aplikację logiki.

   ![Zrzut ekranu przedstawiający Azure Portal aplikacji logiki w Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Możesz również zalogować się oddzielnie do Azure Portal, użyć pola wyszukiwania portalu, aby znaleźć aplikację logiki, a następnie wybrać aplikację logiki z listy wyników.

   ![Zrzut ekranu przedstawiający Azure Portal i pasek wyszukiwania z wyświetlonymi wynikami wyszukiwania dla wdrożonej aplikacji logiki.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Zarządzanie wdrożonych aplikacji logiki w portalu

W witrynie Azure Portal można wyświetlić wszystkie wdrożone aplikacje logiki, które znajdują się w subskrypcji platformy Azure, niezależnie od tego, czy są to oryginalny typ zasobu **Logic Apps, czy** typ zasobu Aplikacja logiki **(wersja** zapoznawcza). Obecnie każdy typ zasobu jest zorganizowany i zarządzany jako osobne kategorie na platformie Azure. Aby znaleźć aplikacje logiki, które mają typ zasobu **Aplikacja logiki (wersja** zapoznawcza), wykonaj następujące kroki:

1. W polu Azure Portal wyszukiwania wpisz `logic app preview` . Gdy zostanie wyświetlona lista wyników, w **obszarze Usługi** wybierz pozycję **Aplikacja logiki (wersja zapoznawcza).**

   ![Zrzut ekranu przedstawiający pole Azure Portal wyszukiwania z tekstem wyszukiwania "Wersja zapoznawcza aplikacji logiki".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. W **okienku Aplikacja logiki (wersja zapoznawcza)** znajdź i wybierz wdrożoną aplikację logiki z Visual Studio Code.

   ![Zrzut ekranu przedstawiający zasoby Azure Portal i aplikacji logiki (wersja zapoznawcza) wdrożone na platformie Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   W Azure Portal zostanie otwarta strona poszczególnych zasobów dla wybranej aplikacji logiki.

   ![Zrzut ekranu przedstawiający stronę zasobów przepływu pracy aplikacji logiki w Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Aby wyświetlić przepływy pracy w tej aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przepływy pracy.**

   Okienko **Przepływy** pracy zawiera wszystkie przepływy pracy w bieżącej aplikacji logiki. W tym przykładzie przedstawiono przepływ pracy utworzony w Visual Studio Code.

   ![Zrzut ekranu przedstawiający stronę zasobu "Aplikacja logiki (wersja zapoznawcza)" z otwartym okienkiem "Przepływy pracy" i wdrożonym przepływem pracy](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Aby wyświetlić przepływ pracy, w **okienku Przepływy** pracy wybierz ten przepływ pracy.

   Zostanie otwarte okienko przepływu pracy z większej liczby informacji i zadań, które można wykonać w tym przepływie pracy.

   Aby na przykład wyświetlić kroki przepływu pracy, wybierz pozycję **Projektant**.

   ![Zrzut ekranu przedstawiający okienko "Przegląd" wybranego przepływu pracy, a menu przepływu pracy zawiera wybrane polecenie "Projektant".](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Zostanie otwarty projektant przepływu pracy z przepływem pracy, który został Visual Studio Code. Teraz możesz wprowadzać zmiany w tym przepływie pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i przepływ pracy wdrożony z Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Dodawanie kolejnego przepływu pracy w portalu

Za pomocą Azure Portal można dodać puste przepływy pracy do zasobu aplikacji **logiki (wersja zapoznawcza),** który został wdrożony z usługi Visual Studio Code, i skompilować te przepływy pracy w Azure Portal.

1. W [oknie Azure Portal](https://portal.azure.com)znajdź i wybierz wdrożony zasób aplikacji **logiki (wersja zapoznawcza).**

1. W menu aplikacji logiki wybierz pozycję **Przepływy pracy.** W **okienku Przepływy** pracy wybierz pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający okienko "Przepływy pracy" wybranej aplikacji logiki i pasek narzędzi z wybranym poleceniem "Dodaj".](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. W **okienku Nowy** przepływ pracy podaj nazwę przepływu pracy. Wybierz pozycję **Stanowe lub** **Bez stanowe** **>** **Utwórz.**

   Po wdrożeniu przez platformę Azure nowego  przepływu pracy, który jest wyświetlany w okienku Przepływy pracy, wybierz ten przepływ pracy, aby można było zarządzać innymi zadaniami, takimi jak otwieranie projektanta lub widoku kodu, oraz wykonywać inne zadania.

   ![Zrzut ekranu przedstawiający wybrany przepływ pracy z opcjami zarządzania i przeglądu.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Na przykład otwarcie projektanta dla nowego przepływu pracy pokazuje pustą kanwę. Teraz możesz skompilować ten przepływ pracy w Azure Portal.

   ![Zrzut ekranu przedstawiający projektanta przepływu pracy i pusty przepływ pracy.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Włączanie historii uruchamiania dla bez stanowych przepływów pracy

Aby łatwiej debugować bez stanowy przepływ pracy, możesz włączyć historię uruchamiania dla tego przepływu pracy, a następnie wyłączyć historię uruchamiania, gdy wszystko będzie gotowe. Wykonaj następujące kroki w Visual Studio Code, a jeśli pracujesz w aplikacji Azure Portal, zobacz Tworzenie stanowych i bez stanowych przepływów pracy w [Azure Portal.](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)

1. W projekcie Visual Studio Code rozwiń folder **workflow-designtime** i otwórz **local.settings.jspliku.**

1. Dodaj właściwość `Workflows.{yourWorkflowName}.operationOptions` i ustaw wartość na , na `WithStatelessRunHistory` przykład:

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

1. Aby wyłączyć historię uruchamiania, gdy wszystko będzie gotowe, ustaw właściwość na wartość lub usuń `Workflows.{yourWorkflowName}.OperationOptions` `None` właściwość i jej wartość.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Włączanie widoku monitorowania w Azure Portal

Po wdrożeniu zasobu aplikacji **logiki (wersja zapoznawcza)** z usługi Visual Studio Code na platformie Azure możesz przejrzeć dowolną dostępną  historię przebiegów i szczegóły przepływu pracy w tym zasobie przy użyciu narzędzia Azure Portal i funkcji Monitorowania dla tego przepływu pracy. Należy jednak najpierw włączyć możliwość **wyświetlania** monitora dla tego zasobu aplikacji logiki.

1. W [oknie Azure Portal](https://portal.azure.com)znajdź i wybierz wdrożony zasób aplikacji **logiki (wersja zapoznawcza).**

1. W menu tego zasobu w obszarze **interfejsu API** wybierz pozycję **CORS.**

1. W **okienku CORS** w obszarze **Dozwolone źródła** dodaj symbol wieloznaczny (*).

1. Gdy wszystko będzie gotowe, na pasku narzędzi **CORS** wybierz pozycję **Zapisz.**

   ![Zrzut ekranu przedstawiający Azure Portal z wdrożonym zasobem aplikacji logiki (wersja zapoznawcza). W menu zasobów jest wybierana pozycja "CORS" z nowym wpisem "Dozwolone źródła" ustawionym na symbol wieloznaczny "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Włączanie lub otwieranie Application Insights po wdrożeniu

Podczas wykonywania przepływu pracy aplikacja logiki emituje dane telemetryczne wraz z innymi zdarzeniami. Możesz użyć tej telemetrii, aby uzyskać lepszy wgląd w to, jak dobrze działa przepływ pracy i jak Logic Apps środowisko uruchomieniowe działa na różne sposoby. Przepływ pracy można monitorować przy użyciu [Application Insights](../azure-monitor/app/app-insights-overview.md), która zapewnia telemetrię niemal w czasie rzeczywistym (metryki na żywo). Ta funkcja może ułatwić badanie błędów i problemów z wydajnością, gdy te dane są wykorzystywane do diagnozowania problemów, kompilowania alertów i tworzenia wykresów.

Jeśli ustawienia tworzenia i wdrażania aplikacji logiki obsługują Application Insights , możesz [opcjonalnie](../azure-monitor/app/app-insights-overview.md)włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas wdrażania aplikacji logiki z Visual Studio Code lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale możesz utworzyć ten zasób z wyprzedzeniem [,](../azure-monitor/app/create-workspace-resource.md)podczas wdrażania aplikacji logiki lub po wdrożeniu.

Aby włączyć Application Insights wdrożonej aplikacji logiki lub przejrzeć dane Application Insights, gdy są już włączone, wykonaj następujące kroki:

1. W Azure Portal znajdź wdrożoną aplikację logiki.

1. W menu aplikacji logiki w **obszarze Ustawienia** wybierz **pozycję Application Insights**.

1. Jeśli Application Insights nie jest włączona, w **okienku Application Insights** wybierz pozycję **Włącz Application Insights**. Po aktualizacji okienka w dolnej części wybierz pozycję **Zastosuj**.

   Jeśli Application Insights jest włączona, w **okienku Application Insights** wybierz pozycję **Wyświetl Application Insights danych.**

Po Application Insights można przejrzeć różne metryki dla aplikacji logiki. Aby uzyskać więcej informacji, zapoznaj się z tymi tematami:

* [Azure Logic Apps Running Anywhere — monitorowanie za pomocą Application Insights — część 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Running Anywhere — monitorowanie za pomocą Application Insights — część 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Wdrażanie na platformy Docker

Aplikację logiki można wdrożyć w kontenerze [platformy Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) jako środowisko hostingu przy użyciu interfejsu wiersza [polecenia platformy .NET.](/dotnet/core/tools/) Za pomocą tych poleceń możesz skompilować i opublikować projekt aplikacji logiki. Następnie możesz skompilować i uruchomić kontener platformy Docker jako miejsce docelowe wdrożenia aplikacji logiki.

Jeśli nie znasz platformy Docker, zapoznaj się z tymi tematami:

* [Co to jest Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Wprowadzenie do kontenerów i platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Wprowadzenie do platform .NET i Docker](/dotnet/core/docker/introduction)
* [Kontenery, obrazy i rejestry platformy Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Samouczek: wprowadzenie do platformy Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Wymagania

* Konto usługi Azure Storage używane przez aplikację logiki do wdrażania

* Plik platformy Docker dla przepływu pracy, który jest używać podczas budowania kontenera platformy Docker

  Na przykład ten przykładowy plik platformy Docker wdraża aplikację logiki i określa ciąg połączenia zawierający klucz dostępu dla konta usługi Azure Storage, który został użyty do opublikowania aplikacji logiki w Azure Portal. Aby znaleźć ten ciąg, zobacz [Get storage account connection string (Uzyskiwanie parametrów połączenia konta magazynu).](#find-storage-account-connection-string) Aby uzyskać więcej informacji, zapoznaj [się z artykułem Best practices for writing Docker files (Najlepsze rozwiązania dotyczące pisania plików platformy Docker).](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  
  > [!IMPORTANT]
  > W przypadku scenariuszy produkcyjnych upewnij się, że chronisz i zabezpieczasz takie wpisy tajne i poufne informacje, na przykład przy użyciu magazynu kluczy. W szczególności w przypadku plików platformy Docker zapoznaj się z tematem Build images with BuildKit (Tworzenie obrazów [za pomocą zestawu BuildKit)](https://docs.docker.com/develop/develop-images/build_enhancements/) i [Manage sensitive data with Docker Secrets (Zarządzanie poufnymi danymi za pomocą wpisów tajnych platformy Docker).](https://docs.docker.com/engine/swarm/secrets/)

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Uzyskiwanie parametrów połączenia konta magazynu

Aby można było skompilować i uruchomić obraz kontenera platformy Docker, należy uzyskać parametrów połączenia, które zawierają klucz dostępu do konta magazynu. Wcześniej utworzono to konto magazynu jako rozszerzenie w systemie macOS lub Linux albo podczas wdrażania aplikacji logiki w Azure Portal.

Aby znaleźć i skopiować te ciągi połączenia, wykonaj następujące kroki:

1. W menu Azure Portal magazynu w obszarze Ustawienia **wybierz** pozycję **Klucze dostępu.** 

1. W **okienku Klucze** dostępu znajdź i skopiuj ciąg połączenia konta magazynu, który wygląda podobnie do tego przykładu:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Zrzut ekranu przedstawiający Azure Portal skopiowane klucze dostępu do konta magazynu i parametrów połączenia.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Aby uzyskać więcej informacji, zapoznaj się [z tematem Zarządzanie kluczami konta magazynu.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Zapisz te ciągi połączenia w bezpiecznym miejscu, aby można było dodać te ciągi do pliku platformy Docker, którego używasz do wdrożenia. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Znajdowanie klucza głównego dla konta magazynu

Jeśli przepływ pracy zawiera wyzwalacz Żądania, po skompilowaniu i uruchomieniu obrazu kontenera platformy Docker musisz pobrać adres [URL](#get-callback-url-request-trigger) wywołania zwrotnego wyzwalacza. W tym zadaniu należy również określić wartość klucza głównego dla konta magazynu, które będzie potrzebne do wdrożenia.

1. Aby znaleźć ten klucz główny, w projekcie otwórz plik **azure-webjobs-secrets/{nazwa-wdrożenia}/host.jspliku.**

1. Znajdź właściwość `AzureWebJobsStorage` i skopiuj wartość klucza z tej sekcji:

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

1. Zapisz tę wartość klucza w bezpiecznym miejscu do późniejszego użycia.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Kompilowanie i uruchamianie obrazu kontenera platformy Docker

1. Skompilowanie obrazu kontenera platformy Docker przy użyciu pliku platformy Docker i uruchomienie tego polecenia:

   `docker build --tag local/workflowcontainer .`

   Aby uzyskać więcej informacji, zobacz [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Uruchom kontener lokalnie przy użyciu tego polecenia:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Aby uzyskać więcej informacji, zobacz [docker run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Uzyskiwanie adresu URL wywołania zwrotnego dla wyzwalacza żądania

W przypadku przepływu pracy, który używa wyzwalacza Request, pobierz adres URL wywołania zwrotnego wyzwalacza, wysyłając to żądanie:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Wartość to nazwa wyzwalacza Żądania, która pojawia się w definicji `{trigger-name}` JSON przepływu pracy. Wartość jest definiowana na koncie usługi Azure Storage ustawionym dla właściwości w pliku `{master-key}` `AzureWebJobsStorage` **azure-webjobs-secrets/{nazwa-wdrożenia}/host.jsw systemie**. Aby uzyskać więcej informacji, zobacz [Znajdowanie klucza głównego konta magazynu.](#find-storage-account-master-key)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Usuwanie elementów z projektanta

Aby usunąć element w przepływie pracy z projektanta, wykonaj dowolną z poniższych czynności:

* Wybierz element, otwórz menu skrótów elementu (Shift+F10), a następnie wybierz pozycję **Usuń.** Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element i naciśnij klawisz Delete. Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element, aby dla tego elementu było otwarte okienko szczegółów. W prawym górnym rogu okienka otwórz menu wielokropka (**...**), a następnie wybierz pozycję **Usuń**. Aby potwierdzić, wybierz pozycję **OK**.

  ![Zrzut ekranu przedstawiający wybrany element w projektancie z otwartym okienkiem szczegółów oraz wybranym przyciskiem wielokropka i poleceniem "Usuń".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Jeśli menu wielokropka nie jest widoczne, rozwiń okno Visual Studio Code wystarczająco szerokie, aby w okienku szczegółów był widoczny przycisk wielokropka (**...**) w prawym górnym rogu.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Rozwiązywanie problemów i błędów

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Nie można otworzyć projektanta

Podczas próby otwarcia projektanta jest wyświetlany komunikat o błędzie "Nie można uruchomić czasu projektowania **przepływu pracy".** Jeśli wcześniej próbowano otworzyć projektanta, a następnie wycofano lub usunięto projekt, pakiet rozszerzeń może nie być poprawnie pobierany. Aby sprawdzić, czy przyczyną jest problem, wykonaj następujące kroki:

  1. W Visual Studio Code otwórz okno Dane wyjściowe. W menu **Widok** wybierz pozycję **Dane wyjściowe.**

  1. Na liście na pasku tytułu okna Dane wyjściowe wybierz pozycję **Azure Logic Apps (wersja zapoznawcza),** aby przejrzeć dane wyjściowe z rozszerzenia, na przykład:

     ![Zrzut ekranu przedstawiający okno Dane wyjściowe z wybraną Azure Logic Apps ".](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Przejrzyj dane wyjściowe i sprawdź, czy jest wyświetlany następujący komunikat o błędzie:

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

   Aby usunąć ten błąd, usuń folder **ExtensionBundles** w tej lokalizacji **...\Users \{ twoja-nazwa_użytkownika}\AppData\Local\Temp\Functions\ExtensionBundles** i spróbuj ponownie otworzyć plik **workflow.js** w projektancie.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Brak nowych wyzwalaczy i akcji w s wyboru projektanta dla wcześniej utworzonych przepływów pracy

Azure Logic Apps Preview obsługuje wbudowane akcje dla operacji funkcji platformy Azure, operacji Liquid i operacji XML, takich jak **walidacja XML** i **przekształcanie kodu XML.** Jednak w przypadku wcześniej utworzonych aplikacji logiki te akcje mogą nie być wyświetlane w selektorze projektanta, aby wybrać, Visual Studio Code korzysta z nieaktualnej wersji pakietu rozszerzeń, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Ponadto łącznik Operacje funkcji **platformy Azure** i akcje nie są wyświetlane w selektorze projektanta, chyba że podczas tworzenia aplikacji logiki włączono lub wybrano opcję Użyj łączników z platformy **Azure.** Jeśli łączniki wdrożone na platformie Azure nie zostały włączyć podczas tworzenia aplikacji, możesz je włączyć z projektu w Visual Studio Code. Otwórz okno **workflow.jsmenu skrótów,** a następnie wybierz **pozycję Użyj łączników z platformy Azure.**

Aby naprawić nieaktualny pakiet, wykonaj następujące kroki, aby usunąć nieaktualny pakiet, Visual Studio Code automatycznie zaktualizować pakiet rozszerzeń do najnowszej wersji.

> [!NOTE]
> To rozwiązanie dotyczy tylko aplikacji logiki utworzonych i wdrożonych przy użyciu usługi Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza), a nie aplikacji logiki utworzonych przy użyciu Azure Portal. Zobacz [Obsługiwane wyzwalacze i akcje, których brakuje w projektancie w Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Zapisz pracę, której nie chcesz utracić, i zamknij Visual Studio.

1. Na komputerze przejdź do następującego folderu, który zawiera foldery wersji dla istniejącego pakietu:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Usuń folder version dla wcześniejszego pakietu, na przykład jeśli masz folder w wersji 1.1.3, usuń ten folder.

1. Teraz przejdź do następującego folderu, który zawiera foldery z wersją dla wymaganego pakietu NuGet:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Usuń folder version dla wcześniejszego pakietu, na przykład jeśli masz folder w wersji 1.0.0.8-preview, usuń ten folder.

1. Otwórz Visual Studio Code, projekt iworkflow.js **w pliku** w projektancie.

Brakujące wyzwalacze i akcje są teraz wyświetlane w projektancie.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>W wyzwalaczu lub akcji pojawia się komunikat "400 Bad Request" (400 Złe żądanie)

Jeśli uruchomienie nie powiedzie się i sprawdzisz przebieg w widoku monitorowania, ten błąd może pojawić się w wyzwalaczu lub akcji o dłuższej nazwie, co spowoduje przekroczenie domyślnego limitu znaków przez podstawowy Uniform Resource Identifier (URI).

Aby rozwiązać ten problem i dostosować się do dłuższego czasu URI, edytuj klucze rejestru i na `UrlSegmentMaxCount` `UrlSegmentMaxLength` komputerze, korzystając z poniższych kroków. Wartości domyślne klucza zostały opisane w tym temacie,Http.sys [ustawienia rejestru dla systemu Windows.](/troubleshoot/iis/httpsys-registry-windows)

> [!IMPORTANT]
> Przed rozpoczęciem upewnij się, że zapisujesz swoją pracę. To rozwiązanie wymaga ponownego uruchomienia komputera po zakończeniu, aby zmiany weszły w życie.

1. Na komputerze otwórz **okno** Uruchamianie i uruchom `regedit` polecenie , co spowoduje otwarcie edytora rejestru.

1. W polu **Kontrola konta użytkownika** wybierz pozycję **Tak,** aby zezwolić na zmiany na komputerze.

1. W okienku po lewej stronie w obszarze **Komputer** rozwiń węzły wzdłuż ścieżki,HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters, **a** następnie wybierz pozycję **Parametry**.

1. W okienku po prawej stronie znajdź `UrlSegmentMaxCount` klucze rejestru `UrlSegmentMaxLength` i .

1. Zwiększ te wartości klucza na tyle, aby URI mieściły się w nazwach, których chcesz użyć. Jeśli te klucze nie istnieją, dodaj je do folderu **Parameters,** wykonać następujące kroki:

   1. W menu **skrótów** Parametry wybierz pozycję **Nowa**  >  **wartość DWORD (32-bitowa).**

   1. W wyświetlonym oknie edycji wprowadź `UrlSegmentMaxCount` jako nową nazwę klucza.

   1. Otwórz menu skrótów nowego klucza, a następnie wybierz pozycję **Modyfikuj**.

   1. W **wyświetlonym polu Edytuj** ciąg  wprowadź wartość Klucza danych wartości w formacie szesnastkowym lub dziesiętnym. Na przykład w `400` przypadku wartości szesnastkowych jest to odpowiednik wartości `1024` dziesiętnej.

   1. Aby dodać `UrlSegmentMaxLength` wartość klucza, powtórz te kroki.

   Po zwiększeniu lub dodaniu tych wartości klucza edytor rejestru będzie wyglądać podobnie do poniższego przykładu:

   ![Zrzut ekranu przedstawiający edytor rejestru.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Gdy wszystko będzie gotowe, uruchom ponownie komputer, aby zmiany weszły w życie.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Nie można uruchomić sesji debugowania

Podczas próby uruchomienia sesji debugowania jest wyświetlany błąd "Błąd występuje po uruchomieniu **preLaunchTask 'generateDebugSymbols'".** Aby rozwiązać ten problem, **tasks.jspliku** w projekcie, aby pominąć generowanie symboli.

1. W projekcie rozwiń folder **.vscode,** a następnie otwórz **tasks.jspliku.**

1. W poniższym zadaniu usuń wiersz wraz z przecinkiem, który kończy poprzedni `"dependsOn: "generateDebugSymbols"` wiersz, na przykład:

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

Chcemy się do Ciebie odsłuchać na temat Twoich doświadczeń z rozszerzeniem Azure Logic Apps (wersja zapoznawcza).

* W przypadku usterek lub problemów [utwórz problemy w usłudze GitHub.](https://github.com/Azure/logicapps/issues)
* W przypadku pytań, żądań, komentarzy i innych opinii skorzystaj [z tego formularza opinii.](https://aka.ms/lafeedback)
