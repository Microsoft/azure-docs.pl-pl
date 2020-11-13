---
title: Korzystanie z programu Personalizacja w programie Chat bot — Personalizacja
description: Dostosuj bot rozmowy .NET do języka C# przy użyciu pętli personalizacji, aby zapewnić poprawną zawartość użytkownikowi na podstawie akcji (z funkcjami) i funkcji kontekstu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4920eaa7a5619be37d38afd763e7be416d3124
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565725"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Samouczek: Używanie funkcji personalizacji w programie .NET Chat bot

Aby zapewnić użytkownikowi poprawną zawartość, należy użyć języka C# .NET Chat bot z pętlą Personalizujer. Ten bot rozmowy sugeruje konkretną kawę lub herbata do użytkownika. Użytkownik może zaakceptować lub odrzucić tę sugestię. Zapewnia to informacje dotyczące personalizowania, aby lepiej dostosować następną sugestię.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Konfigurowanie zasobów platformy Azure
> * Konfigurowanie i uruchamianie bot
> * Korzystanie z bot przy użyciu emulatora platformy bot Framework
> * Dowiedz się, gdzie i jak bot używa personalizacji


## <a name="how-does-the-chat-bot-work"></a>Jak działa rozmowa bot?

Bot rozmowy jest zwykle konwersacją z tyłu i z użytkownikiem. Ta konkretna rozmowa bot używa personalizacji, aby wybrać najlepszą akcję (kawę lub herbata) do zaoferowania użytkownikowi. Personalizacja korzysta z uczenia wzmacniania, aby wybrać tę opcję.

Bot rozmowy musi zarządzać włączaniem konwersacji. Usługa Chat bot używa [platformy bot](https://github.com/microsoft/botframework-sdk) w celu zarządzania architekturą bot i konwersacją oraz używa usługi poznawczej [Language Understanding](../LUIS/index.yml) (Luis), aby zrozumieć intencję języka naturalnego od użytkownika.

Czat bot to witryna sieci Web z określoną trasą dostępną do odpowiedzi na żądania `http://localhost:3978/api/messages` . Można użyć emulatora bot, aby wizualnie współdziałać z uruchomionym czatem bot, podczas tworzenia bot lokalnie.

### <a name="user-interactions-with-the-bot"></a>Interakcje użytkownika z bot

To jest prosta rozmowa bot, która umożliwia wprowadzanie zapytań tekstowych.

|Użytkownik wprowadza tekst|Bot reaguje z tekstem|Opis akcji bot ma na celu określenie tekstu odpowiedzi|
|--|--|--|
|Nie wprowadzono tekstu — bot rozpoczyna konwersację.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Bot rozpoczyna konwersację z tekstem instruktażowym i informuje o tym, czym jest kontekst: `Tuesday` , `Snowy` .|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Określ zamiar zapytania przy użyciu LUIS, a następnie Wyświetl opcje menu wyboru dla pozycji kawy i herbaty. Funkcje akcji są |
|`What do you suggest`|`How about Latte?`|Określ zamiar zapytania przy użyciu LUIS, a następnie Wywołaj **interfejs API rangi** i Wyświetl z góry wybór jako pytanie `How about {response.RewardActionId}?` . Wyświetla również wywołania JSON i odpowiedzi dla celów ilustracji.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Określ zamiar zapytania przy użyciu LUIS, a następnie Wywołaj **interfejs API nagradzania** z wynagrodzeniem `1` , wyświetla wywołania JSON i odpowiedzi dla celów ilustracji.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Określ zamiar zapytania przy użyciu LUIS, a następnie Wywołaj **interfejs API nagradzania** z wynagrodzeniem `0` , wyświetla wywołania JSON i odpowiedzi dla celów ilustracji.|
|`Reset`|Zwraca tekst instrukcji.|Określ zamiar zapytania przy użyciu LUIS, a następnie wyświetla tekst instrukcji i resetuje kontekst.|


### <a name="personalizer-in-this-bot"></a>Personalizacja w tym bot

Ten bot rozmowy używa narzędzia do personalizacji, aby wybrać górną akcję (konkretną kawę lub herbata), na podstawie listy _akcji_ (pewnego typu zawartości) i funkcji kontekstowych.

Bot wysyła listę akcji wraz z funkcjami kontekstu do pętli narzędzia Personalizacja. Personalizacja zwraca jedną najlepszą akcję do bot, która jest wyświetlana w bot.

W tym samouczku **Akcje** są typami kawy i herbaty:

|Ekspres|Herbata|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Środowiska Mocha|GreenTea<br>Rooibos|

**Interfejs API rangi:** Aby ułatwić personalizację informacji o swoich działaniach, bot wysyła następujące elementy z każdym żądaniem interfejsu API rangi:

* Akcje _z funkcjami_
* Funkcje kontekstu

**Funkcja** modelu to informacje o akcjach lub kontekście, które mogą być agregowane (pogrupowane) między elementami członkowskimi bazy danych programu Chat bot. Funkcja _nie jest_ pojedynczo specyficzna (na przykład identyfikator użytkownika) lub wysoce konkretnie (na przykład w danym dniu).

Funkcje służą do wyrównywania akcji do bieżącego kontekstu w modelu. Model jest reprezentacją poprzedniej wiedzy personalizowania dotyczącej akcji, kontekstu i ich funkcji, które umożliwiają działowi IT podejmowanie decyzji.

Model, w tym funkcje, jest aktualizowany zgodnie z harmonogramem na podstawie ustawienia **częstotliwości aktualizacji modelu** w Azure Portal.

Należy wybrać funkcje z tym samym planowaniem i projektem, które mają być stosowane do dowolnego schematu lub modelu w architekturze technicznej. Wartości funkcji można ustawić za pomocą logiki biznesowej lub systemów innych firm.

> [!CAUTION]
> Funkcje w tej aplikacji mają na celu pokazanie i nie muszą być najlepszą funkcją do użycia w aplikacji sieci Web dla Twojego przypadku użycia.

#### <a name="action-features"></a>Funkcje akcji

Każda akcja (element zawartości) zawiera funkcje, które ułatwiają odróżnianie towaru kawy lub herbaty.

Funkcje nie są konfigurowane jako część konfiguracji pętli w Azure Portal. Zamiast tego są one wysyłane jako obiekt JSON z każdym wywołaniem interfejsu API rangi. Dzięki temu można elastycznie rozwijać, zmieniać i zmniejszać działania w miarę upływu czasu, co umożliwia Personalizowanie następujących trendów.

Do funkcji kawy i herbaty należą:

* Lokalizacja pochodzenia ziarna kawy, taka jak Kenia i Brazylia
* Czy jest to rolnictwo lub herbata organiczna?
* Jasne lub ciemne pieczenie kawy

Podczas gdy Kawa zawiera trzy funkcje z powyższej listy, herbata ma tylko jeden. Przekazuj funkcje tylko do narzędzia personalizacji, które mają sens dla akcji. Nie należy przekazywać pustej wartości dla funkcji, jeśli nie ma ona zastosowania do akcji.

#### <a name="context-features"></a>Funkcje kontekstu

Funkcje kontekstu ułatwiają Personalizowanie kontekstu środowiska, takiego jak urządzenie wyświetlające, użytkownik, lokalizacja i inne funkcje, które są istotne dla Twojego przypadku użycia.

Kontekst dla tego bot rozmowy obejmuje następujące:

* Typ pogody (śnieg, deszcz, Sunny)
* Dzień tygodnia

Wybór funkcji jest losowy w tym bot rozmowy. W rzeczywistym bot należy używać rzeczywistych danych dla funkcji kontekstu.

### <a name="design-considerations-for-this-bot"></a>Zagadnienia dotyczące projektowania dla tego bot

Należy pamiętać o kilku uwagach dotyczących tej konwersacji:
* **Interakcja bot** : konwersacja jest bardzo prosta, ponieważ wykazuje rangę i wynagrodzenie w prostym przypadku użycia. Nie wykazuje pełnej funkcjonalności zestawu SDK bot Framework ani emulatora.
* **Personalizacja** : funkcje są wybierane losowo, aby symulować użycie. Nie Generuj losowo funkcji w scenariuszu narzędzia do tworzenia w środowisku produkcyjnym.
* **Language Understanding (Luis)** : kilka przykładów wyrażenia długości modelu Luis jest przeznaczonych tylko dla tego przykładu. Nie należy używać tak kilku przykładowych wyrażenia długości w aplikacji produkcyjnej LUIS.


## <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Repozytorium Samples do pobrania zawiera instrukcje, jeśli wolisz używać interfejs wiersza polecenia platformy .NET Core.
- [Microsoft bot Framework emulator](https://aka.ms/botframeworkemulator) to aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów na hoście lokalnym lub zdalnie za pomocą tunelu.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Pobieranie przykładowego kodu z rozmowy bot

Bot rozmowy jest dostępna w repozytorium przykładów personalizacji. Sklonuj lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) repozytorium, a następnie otwórz przykład w `/samples/ChatbotExample` katalogu za pomocą programu Visual Studio 2019.

Aby sklonować repozytorium, użyj następującego polecenia Git w bash Shell (Terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Tworzenie i Konfigurowanie personalizowania i LUIS zasobów

### <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Aby użyć tej bot rozmowy, należy utworzyć zasoby platformy Azure dla programu Personalizacja i Language Understanding (LUIS).

* [Tworzenie zasobów Luis](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal). Wybierz **oba** elementy w kroku tworzenia, ponieważ potrzebne są zasoby tworzenia i przewidywania.
* [Utwórz zasób personalizacji](how-to-create-resource.md) , a następnie skopiuj klucz i punkt końcowy z Azure Portal. Należy ustawić te wartości w `appsettings.json` pliku projektu .NET.

### <a name="create-luis-app"></a>Tworzenie aplikacji LUIS

Jeśli jesteś nowym LUIS, musisz [się zalogować](https://www.luis.ai) i natychmiast przeprowadzić migrację konta. Nie musisz tworzyć nowych zasobów, zamiast tego Wybierz zasoby utworzone w poprzedniej sekcji tego samouczka.

1. Aby utworzyć nową aplikację LUIS, w [portalu Luis](https://www.luis.ai)wybierz swoją subskrypcję i zasób tworzenia.
1. Następnie na tej samej stronie wybierz pozycję **+ Nowa aplikacja do konwersacji** , a następnie **zaimportuj jako plik JSON**.
1. W podręcznym oknie dialogowym wybierz pozycję **Wybierz plik** , a następnie wybierz `/samples/ChatbotExample/CognitiveModels/coffeebot.json` plik. Wprowadź nazwę `Personalizer Coffee bot` .
1. Wybierz przycisk **uczenie** w prawym górnym rogu portalu Luis.
1. Wybierz przycisk **Publikuj** , aby opublikować aplikację w **gnieździe produkcyjnym** dla środowiska uruchomieniowego przewidywania.
1. Wybierz pozycje **Zarządzaj** , a następnie **Ustawienia**. Skopiuj wartość **identyfikatora aplikacji**. Należy ustawić tę wartość w `appsettings.json` pliku projektu .NET.
1. Nadal w sekcji **Zarządzanie** wybierz pozycję **zasoby platformy Azure**. Spowoduje to wyświetlenie skojarzonych zasobów w aplikacji.
1. Wybierz pozycję **Dodaj zasób predykcyjny**. W podręcznym oknie dialogowym wybierz subskrypcję i zasób przewidywania utworzony w poprzedniej sekcji tego samouczka, a następnie wybierz pozycję **gotowe**.
1. Skopiuj wartości **klucza podstawowego** i **adresu URL punktu końcowego**. Należy ustawić te wartości w `appsettings.json` pliku projektu .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Konfiguruj bot z appsettings.jsw pliku

1. Otwórz plik rozwiązania Chat bot `ChatbotSamples.sln` z programem Visual Studio 2019.
1. Otwórz `appsettings.json` w katalogu głównym projektu.
1. Ustaw wszystkie pięć ustawień skopiowane w poprzedniej sekcji tego samouczka.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Kompiluj i uruchamiaj bot

Po skonfigurowaniu programu `appsettings.json` możesz przystąpić do kompilowania i uruchamiania programu Chat bot. Gdy to zrobisz, zostanie otwarta przeglądarka z uruchomioną witryną sieci Web `http://localhost:3978` .

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Zrzut ekranu przedstawiający przeglądarkę wyświetlającą witrynę sieci Web czatu bot.":::

Utrzymuj działającą witrynę sieci Web, ponieważ samouczek wyjaśnia, co robi bot, dzięki czemu możesz korzystać z bot.


## <a name="set-up-the-bot-emulator"></a>Konfigurowanie emulatora bot

1. Otwórz emulator bot Framework i wybierz pozycję **Otwórz bot**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Zrzut ekranu przedstawiający ekran uruchamiania emulatora bot.":::


1. Skonfiguruj bot z następującym **adresem URL bot** , a następnie wybierz pozycję **Połącz** :

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia bot emulatora bot.":::

    Emulator nawiązuje połączenie z usługą Chat bot i wyświetla tekst instruktażowy wraz z informacjami dotyczącymi rejestrowania i debugowania przydatną do lokalnego tworzenia.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Zrzut ekranu emulatora bot w pierwszej kolejności konwersacji.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Korzystanie z bot w emulatorze bot

1. Poproszenie o wyświetlenie menu przez wprowadzenie `I would like to see the menu` . Bot rozmowy wyświetla elementy.
1. Pozwól bot zasugerować element, wprowadzając `Please suggest a drink for me.` emulator wyświetli żądanie rangi i odpowiedź w oknie rozmowy, aby zobaczyć pełny kod JSON. A Bot wykonuje sugestię, podobną do `How about Latte?`
1. Odpowiedź na to, co oznacza, że zaakceptujesz górną rangę opcji personalizacji, `I like it.` emulator wyświetli żądanie nagrody z wynikiem nagrody 1 i odpowiedzi w oknie rozmowy, aby zobaczyć pełny kod JSON. A bot reaguje z `That’s great! I’ll keep learning your preferences over time.` i `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Jeśli użytkownik odpowie na `no` wybór, wynik nagrody 0 jest wysyłany do personalizacji.


## <a name="understand-the-net-code-using-personalizer"></a>Zrozumienie kodu platformy .NET przy użyciu narzędzia Personalizacja

Rozwiązanie .NET to proste bot Framework bot. Kod związany z personalizacją znajduje się w następujących folderach:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` plik interakcji między bot i personalizacją
* `/samples/ChatbotExample/ReinforcementLearning` — zarządza akcjami i funkcjami dla modelu personalizowania
* `/samples/ChatbotExample/Model` -pliki dla akcji i funkcji narzędzia Personalizacja oraz dla intencji LUIS

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs — praca z personalizacją

`PersonalizerChatbot`Klasa pochodzi od `Microsoft.Bot.Builder.ActivityHandler` . Ma trzy właściwości i metody zarządzania przepływem konwersacji.

> [!CAUTION]
> Nie Kopiuj kodu z tego samouczka. Użyj przykładowego kodu w [repozytorium przykładów personalizacji](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Metody poprzedzone przez `Send` Zarządzanie konwersacją z bot i Luis. Metody `ChooseRankAsync` i `RewardAsync` współpracują z personalizacją.

#### <a name="calling-rank-api-and-display-results"></a>Wywoływanie interfejsu API rangi i wyświetlanie wyników

Metoda `ChooseRankAsync` kompiluje dane JSON w celu wysłania do interfejsu API rangi narzędzia personalizacji, zbierając akcje z funkcjami i funkcjami kontekstu.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Wywoływanie interfejsu API nagradzania i wyświetlanie wyników

Metoda `RewardAsync` kompiluje dane JSON w celu wysłania do interfejsu API nagradzania personalizacji przez określenie wyniku. Wynik jest określany na podstawie zamiaru LUIS zidentyfikowane w tekście użytkownika i wysyłanej z `OnTurnAsync` metody.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Zagadnienia dotyczące projektowania bot

Ten przykład jest przeznaczony do zademonstrowania prostego, kompleksowego rozwiązania personalizacji w bot. Przypadek użycia może być bardziej skomplikowany.

Jeśli zamierzasz użyć personalizacji w bot produkcyjnym, Zaplanuj:
* Dostęp w czasie rzeczywistym do personalizowania za _każdym razem, gdy_ potrzebna jest ranga wyboru. Nie można przeprowadzić wsadowego interfejsu API rangi ani w pamięci podręcznej.  Połączenie płatne może zostać opóźnione lub odłożone na oddzielny proces. Jeśli nie zwrócisz wynagrodzenia w danym okresie, zostanie ustawiona domyślna wartość nagrody dla zdarzenia.
* Obliczanie na podstawie wielkości liter dla nagrody: w tym przykładzie pokazano dwa nagrody równe zero i jeden bez zakresu od i brak wartości ujemnej dla wyniku. System musiał być bardziej szczegółowym ocenianiem.
* Kanały bot: Ten przykład korzysta z pojedynczego kanału, ale jeśli zamierzasz używać więcej niż jednego kanału lub odmian botów w jednym kanale, które mogą być traktowane jako część funkcji kontekstu modelu personalizowania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Oczyść następujące zasoby:

* Usuń przykładowy katalog projektu.
* Usuń spersonalizowany i LUIS zasób w Azure Portal.

## <a name="next-steps"></a>Następne kroki
* [Jak działa usługa Personalizacja](how-personalizer-works.md)
* [Funkcje](concepts-features.md): Poznaj koncepcje dotyczące funkcji za pomocą akcji i kontekstu
* [Nagrody](concept-rewards.md): informacje o obliczaniu nagrody
