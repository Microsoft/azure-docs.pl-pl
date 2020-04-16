---
title: 'Samouczek: Głosy włączą bota za pomocą zestawu Speech SDK - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz Echo Bot przy użyciu microsoft bot-framework, wdrożyć go na platformie Azure i zarejestrować go za pomocą kanału mowy direct line bot framework. Następnie skonfigurujesz przykładową aplikację kliencką dla systemu Windows, która pozwala rozmawiać z botem i słyszeć, jak reaguje z powrotem na Ciebie.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.openlocfilehash: b2c119f6552773bce7bb93a503c22324278ac0bc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399472"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Samouczek: Voice-włącz bota za pomocą zestawu SDK mowy

Teraz możesz użyć mocy usługi Mowy, aby łatwo włączyć obsługę głosową bota czatu.

W tym samouczku utworzysz Echo Bot przy użyciu microsoft bot-framework, wdrożyć go na platformie Azure i zarejestrować go za pomocą kanału mowy direct line bot framework. Następnie skonfigurujesz przykładową aplikację kliencką dla systemu Windows, która pozwala rozmawiać z botem i słyszeć, jak reaguje z powrotem na Ciebie.

Ten samouczek jest przeznaczony dla deweloperów, którzy dopiero rozpoczynają swoją podróż za pomocą platformy Azure, botów botów botów, direct line speech lub zestawu SDK mowy i chcą szybko utworzyć system roboczy z ograniczonym kodowaniem. Nie jest potrzebne doświadczenie ani znajomość tych usług.

Po zakończeniu tego ćwiczenia zostanie skonfigurowany system, który będzie działał w następujący sposób:

1. Przykładowa aplikacja kliencka jest skonfigurowana do łączenia się z kanałem Direct Line Speech i Echo Bot
1. Dźwięk jest rejestrowany z domyślnego mikrofonu po naciśnięciu przycisku (lub w sposób ciągły rejestrowany, jeśli niestandardowe słowo kluczowe jest włączone)
1. Opcjonalnie odbywa się niestandardowe wykrywanie słów kluczowych, przesyłanie strumieniowe dźwięku do chmury
1. Za pomocą speech SDK aplikacja łączy się z kanałem Direct Line Speech i strumieniuje dźwięk
1. Opcjonalnie w usłudze odbywa się weryfikacja słów kluczowych o większej dokładności
1. Dźwięk jest przekazywany do usługi rozpoznawania mowy i transkrybowany na tekst
1. Rozpoznany tekst jest przekazywany do Echo-Bot jako bot framework działania 
1. Tekst odpowiedzi jest przekształcany w dźwięk przez usługę TTS (Text-to-Speech) i przesyłany strumieniowo z powrotem do aplikacji klienckiej w celu odtwarzania

![znacznik diagramu](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Przepływ kanału mowy")

> [!NOTE]
> Kroki opisane w tym samouczku nie wymagają płatnej usługi. Jako nowy użytkownik platformy Azure będziesz mógł używać kredytów z bezpłatnej subskrypcji próbnej platformy Azure i bezpłatnej warstwy usługi mowy, aby ukończyć ten samouczek.

Oto, co obejmuje ten poradnik:
> [!div class="checklist"]
> * Tworzenie nowych zasobów platformy Azure
> * Tworzenie, testowanie i wdrażanie przykładu echobota w usłudze Azure App Service
> * Zarejestruj swojego bota za pomocą kanału Direct Line Speech
> * Tworzenie i uruchamianie klienta Asystenta głosowego systemu Windows w celu interakcji z urządzeniem Echo Bot
> * Dodawanie niestandardowej aktywacji słów kluczowych
> * Naucz się zmieniać język rozpoznanej i mówionej mowy

## <a name="prerequisites"></a>Wymagania wstępne

Oto, czego potrzebujesz, aby ukończyć ten samouczek:

- Komputer z systemem Windows 10 z działającym mikrofonem i głośnikami (lub słuchawkami)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) lub nowsze
- [Środowisko uruchomieniowe programu .NET Framework 4.6.1](https://dotnet.microsoft.com/download) lub nowszego
- Konto platformy Azure. [Zarejestruj się za darmo](https://azure.microsoft.com/free/ai/).
- Konto [GitHub](https://github.com/)
- [Git dla systemu Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aplikacja kliencka, która zostanie utworzona w tym samouczku, korzysta z kilku usług platformy Azure. Aby skrócić czas podróży w obie strony dla odpowiedzi z bota, należy się upewnić, że te usługi znajdują się w tym samym regionie platformy Azure. W tej sekcji utworzysz grupę zasobów w regionie **Zachodnie stany USA.** Ta grupa zasobów będzie używana podczas tworzenia indywidualnych zasobów dla bot-framework, direct line speech channel i usługi mowy.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Tworzenie grupy zasobów<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Ustaw **subskrypcję** na **bezpłatną wersję próbną** (możesz również użyć istniejącej subskrypcji).
   * Wprowadź nazwę **grupy zasobów**. Zalecamy **SpeechEchoBotTutorial-ResourceGroup**.
   * Z listy rozwijanej **Region** wybierz pozycję **Zachodnie stany USA**.
1. Kliknij pozycję **Przejrzyj i utwórz**. Powinien zostać wyświetlony baner z napisem **Sprawdzanie poprawności przekazany**.
1. Kliknij przycisk **Utwórz**. Utworzenie grupy zasobów może potrwać kilka minut.
1. Podobnie jak w przypadku zasobów, które utworzysz w dalszej części tego samouczka, warto przypiąć tę grupę zasobów do pulpitu nawigacyjnego, aby uzyskać łatwy dostęp. Jeśli chcesz przypiąć tę grupę zasobów, kliknij ikonę pinezki w prawym górnym rogu pulpitu nawigacyjnego.

### <a name="choosing-an-azure-region"></a>Wybieranie regionu platformy Azure

Jeśli chcesz użyć innego regionu dla tego samouczka, te czynniki mogą ograniczyć twoje wybory:

* Upewnij się, że używasz [obsługiwanego regionu platformy Azure](regions.md#voice-assistants).
* Kanał Direct Line Speech używa usługi zamiany tekstu na mowę, która ma standardowe i nerwowe głosy. Głosy neuronowe są [ograniczone do określonych regionów platformy Azure.](regions.md#standard-and-neural-voices)
* Bezpłatne klucze próbne mogą być ograniczone do określonego regionu.

Aby uzyskać więcej informacji o regionach, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Tworzenie zasobów

Teraz, gdy masz grupę zasobów w obsługiwanym regionie, następnym krokiem jest utworzenie poszczególnych zasobów dla każdej usługi, która będzie używana w tym samouczku.

### <a name="create-a-speech-service-resource"></a>Tworzenie zasobu usługi mowy

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć zasób mowy:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi mowy<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Nadaj zasobowi **nazwę**. Polecamy **SpeechEchoBotTutorial-Speech**
   * W przypadku **subskrypcji**upewnij się, że wybrano **bezpłatną wersję próbną.**
   * W obszarze **Lokalizacja**wybierz pozycję **Zachodnie stany USA**.
   * W obszarze **Warstwa cenowa**wybierz pozycję **F0**. Jest to warstwa bezpłatna.
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-ResourceGroup**.
5. Po wprowadzeniu wszystkich wymaganych informacji kliknij przycisk **Utwórz**. Utworzenie zasobu może potrwać kilka minut.
6. W dalszej części tego samouczka będziesz potrzebować kluczy subskrypcji dla tej usługi. Dostęp do tych kluczy można uzyskać w dowolnym momencie z **przeglądu** zasobu (Zarządzanie kluczami) lub **kluczami**.

W tym momencie sprawdź, czy grupa zasobów (**SpeechEchoBotTutorial-ResourceGroup**) ma zasób mowy:

| Nazwa | Typ  | Lokalizacja |
|------|-------|----------|
| SpeechEchoBotTutorial-Mowa | Cognitive Services | Zachodnie stany USA |

### <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

Następnym krokiem jest utworzenie planu usługi aplikacji. Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Tworzenie planu usługi Azure App Service<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Ustaw **subskrypcję** na **bezpłatną wersję próbną** (możesz również użyć istniejącej subskrypcji).
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-ResourceGroup**.
   * Nadaj zasobowi **nazwę**. Polecamy **SpeechEchoBotTutorial-AppServicePlan**
   * W przypadku **opcji System operacyjny**wybierz pozycję **Windows**.
   * W obszarze **Region**wybierz pozycję **Zachodnie stany USA**.
   * W przypadku **warstwy cenowej**upewnij się, że wybrano **standard S1.** Powinna to być wartość domyślna. Jeśli tak nie jest, upewnij się, że **system operacyjny** został ustawiony na **system Windows** w sposób opisany powyżej.
5. Kliknij pozycję **Przejrzyj i utwórz**. Powinien zostać wyświetlony baner z napisem **Sprawdzanie poprawności przekazany**.
6. Kliknij przycisk **Utwórz**. Utworzenie grupy zasobów może potrwać kilka minut.

W tym momencie sprawdź, czy grupa zasobów (**SpeechEchoBotTutorial-ResourceGroup**) ma dwa zasoby:

| Nazwa | Typ  | Lokalizacja |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-Mowa | Cognitive Services | Zachodnie stany USA |

## <a name="build-an-echo-bot"></a>Zbuduj Bot Echo

Teraz, gdy utworzono pewne zasoby, zbudujmy bota. Zaczniemy od próbki Echo Bot, która jak sama nazwa wskazuje, odzwierciedla tekst, który został wprowadzony jako jego odpowiedź. Nie martw się, przykładowy kod jest gotowy do użycia bez żadnych zmian. Jest skonfigurowany do pracy z kanałem Direct Line Speech, który połączymy się po wdrożeniu bota na platformie Azure.

> [!NOTE]
> Instrukcje, które należy wykonać, jak również dodatkowe informacje na temat Echo Bot są dostępne w [próbce README na GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Uruchamianie próbki bota na komputerze

1. Sklonuj repozytorium próbek:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Uruchom program Visual Studio.
3. Na pasku narzędzi wybierz **opcję Otwórz plik** > **Open** > **projektu/rozwiązania**i otwórz rozwiązanie projektu Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Po załadowaniu projektu naciśnij klawisz <kbd>F5,</kbd> aby zbudować i uruchomić projekt.
5. Przeglądarka powinna zostać uruchomiona, a zobaczysz ekran wyglądający podobnie do tego.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot działa na localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testowanie próbki bota za pomocą emulatora struktury botów

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) to aplikacja klasyczna, która umożliwia deweloperom botów do testowania i debugowania ich botów lokalnie lub zdalnie przez tunel. Emulator obsługuje wpisany tekst jako wejście (nie głos). Bot będzie odpowiadać tekstem. Wykonaj następujące kroki, aby użyć emulatora platformy Bot Framework, aby przetestować echo bot działa lokalnie, z wprowadzania tekstu i danych wyjściowych tekstu. Po wdrożeniu bota na platformie Azure przetestujemy go za pomocą danych wejściowych głosowych i danych głosowych.

1. Zainstaluj [emulatora platformy Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) w wersji 4.3.0 lub większej
2. Uruchom Emulator Struktury Bot i otwórz bota:
   * **Plik** -> **Otwórz Bot**.
3. Wprowadź adres URL swojego bota. Przykład:

   ```
   http://localhost:3978/api/messages
   ```
   i naciśnij przycisk "Połącz".
4. Bot powinien natychmiast powitać Cię "Cześć i witamy!" . Wpisz dowolną wiadomość tekstową i potwierdź, że otrzymasz odpowiedź od bota.
5. Tak może wyglądać wymiana komunikacji z wystąpieniem Echo Bot: [ ![bot-framework-emulator](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulator platformy Bot Framework")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Wdrażanie bota w usłudze Azure App Service

Następnym krokiem jest wdrożenie Echo Bot na platformie Azure. Istnieje kilka sposobów wdrażania bota, ale w tym samouczku skupimy się na publikowaniu bezpośrednio z programu Visual Studio.

> [!NOTE]
> Alternatywnie można wdrożyć bota przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) i [szablonów wdrażania.](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)

1. W programie Visual Studio otwórz echobota skonfigurowany do użytku z kanałem Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **EchoBot** i wybierz polecenie **Publikuj...**
1. Zostanie otwarte nowe okno o nazwie **Wybierz cel publikowania.**
1. Wybierz **pozycję Usługa aplikacji** z nawigacji usług platformy **Azure,** wybierz pozycję **Utwórz nowy**, a następnie kliknij pozycję **Utwórz profil**.
1. Po **wyświetleniu okna Utwórz usługę App Service:**
   * Kliknij **pozycję Dodaj konto**i zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli jesteś już zalogowany, wybierz konto, które chcesz z listy rozwijanej.
   * W przypadku **nazwy aplikacji**musisz wprowadzić unikatową globalnie nazwę bota. Ta nazwa jest używana do tworzenia unikatowego adresu URL bota. Wartość domyślna zostanie wypełniona wraz z datą i godziną (na przykład: "EchoBot20190805125647"). Można użyć domyślnej nazwy dla tego samouczka.
   * W przypadku **subskrypcji**ustaw go na **bezpłatną wersję próbną**
   * W przypadku **grupy zasobów**wybierz **pozycję SpeechEchoBotTutorial-ResourceGroup**
   * W przypadku **planu hostingowego**wybierz **SpeechEchoBotTutorial-AppServicePlan**
   * W przypadku **usługi Application Insights**, pozostaw jako **Brak**
1. Kliknij **przycisk Utwórz**
1. Powinien zostać wyświetlony komunikat o powodach w programie Visual Studio, który wygląda następująco:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Domyślna przeglądarka powinna otworzyć i wyświetlić stronę z napisem: "Twój bot jest gotowy!".
1. W tym momencie sprawdź grupę zasobów **SpeechEchoBotTutorial-ResourceGroup** w witrynie Azure portal i upewnij się, że istnieją trzy zasoby:

| Nazwa | Typ  | Lokalizacja |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-Mowa | Cognitive Services | Zachodnie stany USA |

## <a name="enable-web-sockets"></a>Włączanie gniazd internetowych

Należy wprowadzić małą zmianę konfiguracji, aby bot mógł komunikować się z kanałem Direct Line Speech za pomocą gniazd internetowych. Wykonaj następujące kroki, aby włączyć gniazda sieci Web:

1. Przejdź do [witryny Azure portal](https://portal.azure.com)i znajdź usługę App Service. Zasób powinien mieć nazwę podobną do **EchoBot20190805125647** (unikatowa nazwa aplikacji).
2. W obszarze Nawigacja **usług platformy Azure** w obszarze **Ustawienia**kliknij pozycję **Konfiguracja**.
3. Wybierz kartę **Ustawienia ogólne.**
4. Znajdź przełącznik dla **gniazd sieci Web** i ustaw go na **Włączone**.
5. Kliknij pozycję **Zapisz**.

> [!TIP]
> Można użyć formantów w górnej części strony usługi Azure App Service, aby zatrzymać lub ponownie uruchomić usługę. Może się to przydać podczas rozwiązywania problemów.

## <a name="create-a-channel-registration"></a>Tworzenie rejestracji kanału

Teraz, gdy utworzono usługę Azure App Service do obsługi bota, następnym krokiem jest utworzenie **rejestracji kanałów botów.** Utworzenie rejestracji kanału jest warunkiem wstępnym rejestracji bota za pomocą kanałów Bot-Framework, w tym kanału Direct Line Speech.

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o tym, jak boty wykorzystują kanały, zobacz [Łączenie bota z kanałami](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Tworzenie rejestracji kanałów botów platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Dla **dojścia bota**wpisz **SpeechEchoBotTutorial-BotRegistration**.
   * W przypadku **subskrypcji**wybierz **opcję Bezpłatna wersja próbna**.
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-ResourceGroup**.
   * W obszarze **Lokalizacja**wybierz pozycję **Zachodnie stany USA**.
     * W obszarze **Warstwa cenowa**wybierz pozycję **F0**.
     * W przypadku **punktu końcowego wiadomości**wprowadź adres `/api/messages` URL aplikacji sieci web ze ścieżką dołączaną na końcu. Na przykład: jeśli unikatowa globalnie nazwa aplikacji to **EchoBot20190805125647,** punktem końcowym wiadomości będzie: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * W przypadku **szczegółowych informacji o aplikacji**można ustawić na **Wyłączone**. Aby uzyskać więcej informacji, zobacz [Analiza botów](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignoruj **automatyczne tworzenie identyfikatora aplikacji i hasła**.
5. U dołu bloku **Rejestracja kanałów botów** kliknij przycisk **Utwórz**.

W tym momencie sprawdź grupę zasobów **SpeechEchoBotTutorial-ResourceGroup** w witrynie Azure portal. Powinien teraz pokazać cztery zasoby:

| Nazwa | Typ  | Lokalizacja |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| Logopedy-BotRegistration | Rejestracja kanałów botów | global |
| SpeechEchoBotTutorial-Mowa | Cognitive Services | Zachodnie stany USA |

> [!IMPORTANT]
> Zasób Rejestracja kanałów botów wyświetli region globalny, nawet jeśli wybrano zachodnie stany USA. Jest to oczekiwane.

## <a name="register-the-direct-line-speech-channel"></a>Rejestrowanie kanału Direct Line Speech

Teraz nadszedł czas, aby zarejestrować bota za pomocą kanału Direct Line Speech. Ten kanał jest to, co jest używane do tworzenia połączenia między echo bot i aplikacji klienckiej skompilowany za pomocą SDK mowy.

1. Znajdź i otwórz zasób **SpeechEchoBotTutorial-BotRegistration** w [witrynie Azure portal](https://portal.azure.com).
1. W obszarze nawigacji **usług platformy Azure** wybierz pozycję **Kanały**.
   * **Poszukaj więcej kanałów**, znajdź i kliknij **direct line speech**.
   * Przejrzyj tekst na stronie zatytułowanej **Konfigurowanie mowy linii bezpośredniej,** a następnie rozwiń menu rozwijane z etykietą "Konto usługi kognitywnej".
   * Wybierz zasób mowy utworzony wcześniej (np. **SpeechEchoBotTutorial-Speech)** z menu, aby skojarzyć bota z kluczem subskrypcji mowy.
   * Kliknij pozycję **Zapisz**.

1. W nawigacji **zarządzania botami** kliknij pozycję **Ustawienia**.
   * Zaznacz pole wyboru **Włącz punkt końcowy przesyłania strumieniowego**. Jest to konieczne, aby włączyć protokół komunikacyjny zbudowany na gniazdach internetowych między botem a kanałem Direct Line Speech.
   * Kliknij pozycję **Zapisz**.

> [!TIP]
> Jeśli chcesz dowiedzieć się więcej, zobacz [Łączenie bota z bezpośrednią mową liniową](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Ta strona zawiera dodatkowe informacje i znane problemy.

## <a name="run-the-windows-voice-assistant-client"></a>Uruchamianie klienta Asystenta głosowego systemu Windows

W tym kroku uruchomisz klienta Asystenta głosowego systemu Windows. Klient jest windows presentation foundation (WPF) aplikacja w języku C#, który używa [speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) do zarządzania komunikacją z botem przy użyciu kanału Direct Line Speech. Użyj go do interakcji z botem i przetestowania go przed napisaniem niestandardowej aplikacji klienckiej. Jest open source, więc możesz pobrać plik wykonywalny i uruchomić go lub zbudować samodzielnie.

Klient Asystenta głosowego systemu Windows ma prosty interfejs użytkownika, który umożliwia skonfigurowanie połączenia z botem, wyświetlanie konwersacji tekstowej, wyświetlanie działań bot-framework w formacie JSON i wyświetlanie kart adaptacyjnych. Obsługuje również użycie niestandardowych słów kluczowych. Użyjesz tego klienta, aby porozmawiać z botem i otrzymać odpowiedź głosową.

Zanim przejdziemy dalej, upewnij się, że mikrofon i głośniki są włączone i działają.

1. Przejdź do repozytorium GitHub dla [klienta Asystenta głosowego systemu Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
1. Postępuj zgodnie z instrukcjami podanymi w tym miejscu, aby
   * pobrać pakiet ZIP zawierający plik wykonywalny do uruchomienia, lub
   * samodzielnie zbudować plik wykonywalny, klonując repozytorium i budując projekt.

1. Uruchom aplikację kliencką i skonfiguruj ją.
1. Kliknij **przycisk Połącz ponownie** i upewnij się, że jest wyświetlany komunikat Naciśnij przycisk **mikrofonu lub wpisz, aby rozpocząć rozmowę z botem**.
1. Przetestujmy to. Kliknij przycisk mikrofonu i mów kilka słów w języku angielskim. Rozpoznany tekst pojawi się podczas mówienia. Po zakończeniu mówienia bot odpowie własnym głosem, mówiąc "echo", a następnie rozpoznane słowa.
1. Można również użyć tekstu do komunikowania się z botem. Wystarczy wpisać tekst na dolnym pasku. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Rozwiązywanie problemów z błędami w kliencie Asystenta głosowego systemu Windows

Jeśli w głównym oknie aplikacji zostanie wyświetlony komunikat o błędzie, użyj tej tabeli, aby zidentyfikować i rozwiązać problem z błędem:

| Błąd | Co musisz zrobić? |
|-------|----------------------|
|Błąd AuthenticationFailure: Uaktualnienie websocket nie powiodło się z błędem uwierzytelniania (401). Sprawdź poprawny klucz subskrypcji (lub token autoryzacji) i nazwę regionu| Na stronie Ustawienia aplikacji upewnij się, że klucz subskrypcji mowy i jego region zostały wprowadzone poprawnie.<br>Upewnij się, że klucz mowy i region klucza zostały wprowadzone poprawnie. |
|Błąd ConnectionFailure: Połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1011. Szczegóły błędu: Nie mogliśmy połączyć się z botem przed wysłaniem wiadomości | Upewnij się, że zaznaczono pole ["Włącz punkt końcowy przesyłania strumieniowego"](#register-the-direct-line-speech-channel) i/lub [ **przełączone gniazda sieci Web** ](#enable-web-sockets) na Włączone.<br>Upewnij się, że usługa Azure App Service jest uruchomiona. Jeśli tak, spróbuj ponownie uruchomić usługę App Service.|
|Błąd ConnectionFailure: Połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1011. Szczegóły błędu: Kod stanu odpowiedzi nie wskazuje powodzenia: 500 (InternalServerError)| Bot określił głos neuronowy w swoim wyjściowym polu Activity [Speak,](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) ale region platformy Azure skojarzony z kluczem subskrypcji mowy nie obsługuje głosów neuronowych. Zobacz [Głosy standardowe i nerwowe](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Błąd ConnectionFailure: Połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1000. Szczegóły błędu: Przekroczono maksymalny czas trwania bezczynności połączenia gniazda sieci web (> 300000 ms)| Jest to oczekiwany błąd, gdy połączenie z kanałem pozostaje otwarte i nieaktywne przez ponad pięć minut. |

Jeśli problem nie został rozwiązany w tabeli, zobacz [Asystenci głosowi: Często zadawane pytania](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Wyświetlanie działań botów

Każdy bot wysyła i odbiera **komunikaty aktywności.** W oknie **Dziennik aktywności** klienta Asystenta głosowego systemu Windows zobaczysz dzienniki sygnatury czasowej z każdym działaniem, które klient otrzymał od bota. Można również zobaczyć działania, które klient wysłał do [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) bota przy użyciu metody. Po wybraniu elementu dziennika, wyświetli szczegóły skojarzonego działania jako JSON.

Oto przykładowy json działania, które klient otrzymał:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Aby dowiedzieć się więcej o tym, co jest zwracane w danych wyjściowych JSON, zobacz [pola w działaniu](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Na potrzeby tego samouczka można skupić się na polach [Tekst](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) i [Mów.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Wyświetlanie kodu źródłowego klienta dla wywołań SDK mowy

Klient Asystenta głosowego systemu Windows używa pakietu NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), który zawiera zestawu SDK mowy. Dobrym miejscem do rozpoczęcia przeglądania przykładowego kodu jest metoda InitSpeechConnector() w pliku, [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)która tworzy te dwa obiekty SDK mowy:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- Dla ustawień konfiguracji (np. klucz subskrypcji mowy, region klucza)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Aby zarządzać połączeniem kanału i zdarzenia subskrypcji klienta do obsługi rozpoznanych odpowiedzi mowy i botów.

## <a name="add-custom-keyword-activation"></a>Dodawanie niestandardowej aktywacji słów kluczowych

Zestaw SDK mowy obsługuje niestandardową aktywację słów kluczowych. Podobnie jak "Hey Cortana" dla Asystenta Firmy Microsoft, możesz napisać aplikację, która będzie stale słuchać wybranego słowa kluczowego. Pamiętaj, że słowo kluczowe może być pojedynczym słowem lub frazą wielowyrazową.

> [!NOTE]
> Termin *słowo kluczowe* jest często używany zamiennie z terminem *wznawianie słowa*, i może być zarówno używane w dokumentacji firmy Microsoft.

Wykrywanie słów kluczowych odbywa się w aplikacji klienckiej. Jeśli używasz słowa kluczowego, dźwięk jest przesyłany strumieniowo do kanału Direct Line Speech tylko wtedy, gdy zostanie wykryte słowo kluczowe. Kanał Direct Line Speech zawiera składnik o nazwie *weryfikacja słów kluczowych (KWV),* który wykonuje bardziej złożone przetwarzanie w chmurze, aby sprawdzić, czy wybrane słowo kluczowe znajduje się na początku strumienia audio. Jeśli weryfikacja słów kluczowych zakończy się pomyślnie, kanał będzie komunikować się z botem.

Wykonaj następujące kroki, aby utworzyć model słowa kluczowego, skonfiguruj klienta Asystenta głosowego systemu Windows do korzystania z tego modelu, a na koniec przetestuj go za pomocą bota.

1. Postępuj zgodnie z tymi instrukcjami, aby [utworzyć niestandardowe słowo kluczowe za pomocą usługi Mowa](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Rozpauj plik modelu pobrany w poprzednim kroku. Powinien być nazwany dla słowa kluczowego. Szukasz pliku o nazwie `kws.table`.
3. W kliencie Asystenta głosowego systemu Windows znajdź menu **Ustawienia** (poszukaj ikony koła zębatego w prawym górnym rogu). Zlokalizuj **ścieżkę pliku** modelu `kws.table` i wprowadź pełną nazwę ścieżki dla pliku z kroku 2.
4. Upewnij się, że zaznaczono pole oznaczone jako **Włączone**. Ten komunikat powinien być widoczny obok pola wyboru: "Nasłuchij słowa kluczowego przy następnym połączeniu". Jeśli podano niewłaściwy plik lub nieprawidłową ścieżkę, powinien zostać wyświetlony komunikat o błędzie.
5. Wprowadź **klucz subskrypcji**mowy , **region klucza subskrypcji**, a następnie kliknij przycisk **OK,** aby zamknąć menu **Ustawienia.**
6. Kliknij przycisk **Połącz ponownie**. Powinien zostać wyświetlony komunikat o treści: "Nowa konwersacja rozpoczęta - wpisz, naciśnij przycisk mikrofonu lub powiedz słowo kluczowe". Aplikacja jest teraz stale nasłuchiwania.
7. Mów dowolną frazę, która zaczyna się od słowa kluczowego. Na przykład:**"{twoje słowo kluczowe}**, o której godzinie?". Nie musisz się wstrzymywać po wypowiedzeniu słowa kluczowego. Po zakończeniu dzieją się dwie rzeczy:
   * Zobaczysz transkrypcję tego, co mówiłeś
   * Wkrótce usłyszysz odpowiedź bota
8. Kontynuuj eksperymentowanie z trzema typami danych wejściowych, które obsługuje bot:
   * Wpisany tekst na dolnym pasku
   * Naciśnięcie ikony mikrofonu i mówienie
   * Wypowiadanie frazy rozpoczynającej się od słowa kluczowego

### <a name="view-the-source-code-that-enables-keyword"></a>Wyświetlanie kodu źródłowego, który umożliwia słowo kluczowe

W kodzie źródłowym klienta Asystenta głosowego systemu Windows, spójrz na te pliki, aby przejrzeć kod, który jest używany do włączenia wykrywania słów kluczowych:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)zawiera wywołanie metody [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)SDK mowy, która służy do tworzenia wystąpienia modelu z pliku lokalnego na dysku.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)zawiera metodę [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)wywołania SDK mowy, która aktywuje ciągłe wykrywanie słów kluczowych.

## <a name="optional-change-the-language-and-bot-voice"></a>(Opcjonalnie) Zmienianie języka i głosu bota

Bot, który został utworzony będzie słuchać i odpowiadać w języku angielskim, z domyślnym amerykańskim angielskim głosem zamiany tekstu na mowę. Nie ograniczasz się jednak do używania języka angielskiego ani domyślnego głosu. W tej sekcji dowiesz się, jak zmienić język, który bot będzie słuchać i odpowiadać. Dowiesz się również, jak wybrać inny głos dla tego języka.

### <a name="change-the-language"></a>Zmienianie języka

Można wybrać jeden z języków wymienionych w tabeli [zamiany mowy na tekst.](language-support.md#speech-to-text) W poniższym przykładzie zmienimy język na niemiecki.

1. Otwórz aplikację Klienta Asystenta głosowego systemu Windows, kliknij przycisk ustawień `de-de` (ikona koła zębatego w prawym górnym rogu) i wprowadź w polu Język (jest to wartość ustawień regionalnych wymieniona w tabeli [zamiana mowy na tekst).](language-support.md#speech-to-text) Spowoduje to ustawienie języka mówionego do `en-us`rozpoznania, zastępując domyślny . To również nakazuje direct line speech kanał używać domyślnego niemieckiego głosu dla odpowiedzi Bot.
2. Zamknij stronę ustawień i kliknij przycisk Połącz ponownie, aby nawiązać nowe połączenie z echobotem.
3. Kliknij przycisk mikrofonu i wypo powiedziała frazę w języku niemieckim. Zobaczysz rozpoznany tekst i echo bot odpowiedzi z domyślnym niemieckim głosem.

### <a name="change-the-default-bot-voice"></a>Zmienianie domyślnego głosu bota

Wybranie głosu zamiany tekstu na mowę i kontrolowanie wymowy można wykonać, jeśli bot określi odpowiedź w postaci [języka znaczników syntezy mowy](speech-synthesis-markup.md) (SSML) zamiast prostego tekstu. Echo bot nie używa SSML, ale możemy łatwo zmodyfikować kod, aby to zrobić. W poniższym przykładzie dodajemy SSML do odpowiedzi echo bota, tak że niemiecki głos Stefan Apollo (męski głos) będzie używany zamiast domyślnego głosu żeńskiego. Zobacz listę [standardowych głosów](language-support.md#standard-voices) i [głosów neuronowych](language-support.md#neural-voices) obsługiwanych dla Twojego języka.

1. Zacznijmy od otwarcia `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Zlokalizuj te dwie linie:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Wymień je na:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Skompiluj rozwiązanie w programie Visual Studio i napraw wszelkie błędy kompilacji.

Drugi argument w metodzie "MessageFactory.Text" ustawia [pole Aktywność mówić](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) w odpowiedzi bota. Wraz z powyższą zmianą został on zastąpiony z prostego tekstu na SSML w celu określenia nieobywkowego głosu niemieckiego.

### <a name="redeploy-your-bot"></a>Ponowne wdrożenie bota

Teraz, gdy dokonano niezbędnych zmian w bot, następnym krokiem jest ponowne opublikowanie go w usłudze Azure App Service i wypróbowanie go:

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **EchoBot** i wybierz polecenie **Publikuj**.
2. Poprzednia konfiguracja wdrożenia została już załadowana jako domyślna. Po prostu kliknij **przycisk Publikuj** obok **EchoBot20190805125647 - Web Deploy**.
3. Komunikat **Opublikuj pomyślnie** pojawi się w oknie danych wyjściowych programu Visual Studio, a strona sieci web zostanie uruchomiona z komunikatem "Twój bot jest gotowy!".
4. Otwórz aplikację Klienta Asystenta głosowego systemu Windows, kliknij przycisk ustawień (ikona `de-de` koła zębatego w prawym górnym rogu) i upewnij się, że nadal masz w polu Język.
5. Postępuj zgodnie z instrukcjami w [Uruchom klienta Asystenta głosowego systemu Windows,](#run-the-windows-voice-assistant-client) aby ponownie połączyć się z nowo wdrożonym botem, mówić w nowym języku i słyszeć odpowiedź bota w tym języku za pomocą nowego głosu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal używać echo-bota wdrożonego w tym samouczku, możesz usunąć go i wszystkie skojarzone z nim zasoby platformy Azure, po prostu usuwając grupę zasobów platformy Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. W [witrynie Azure portal](https://portal.azure.com)kliknij na **grupy zasobów** z nawigacji usług **platformy Azure.**
2. Znajdź grupę zasobów o **nazwie: SpeechEchoBotTutorial-ResourceGroup**. Kliknij trzy kropki (...).
3. Wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie własnej aplikacji klienckiej za pomocą sdk mowy](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Zobacz też

* Wdrażanie w [regionie platformy Azure w pobliżu,](https://azure.microsoft.com/global-infrastructure/locations/) aby zobaczyć poprawę czasu reakcji bota
* Wdrażanie w [regionie platformy Azure, który obsługuje wysokiej jakości neuronowe głosy TTS](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Ceny skojarzone z kanałem Direct Line Speech:
  * [Ceny usługi Bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Usługa mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Tworzenie i wdrażanie własnego bota z obsługą głosu:
  * Zbuduj [bot-framework bot](https://dev.botframework.com/). Zarejestruj go za pomocą [kanału Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) i dostosuj [bota do pracy głosowej](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Poznaj istniejące [rozwiązania Bot-Framework:](https://microsoft.github.io/botframework-solutions/index)Zbuduj [wirtualnego asystenta](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) i [rozszerzyj go na Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
