---
title: Korzystanie z aplikacji sieci Web — Personalizacja
description: Dostosuj aplikację sieci Web w języku C# przy użyciu pętli personalizacji, aby zapewnić użytkownikowi poprawną zawartość na podstawie akcji (z funkcjami) i funkcji kontekstu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e06d191573219df44631f6ffaee86f895166de57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777262"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Samouczek: Dodawanie personalizacji do aplikacji sieci Web platformy .NET

Dostosuj aplikację sieci Web w języku C# przy użyciu pętli personalizacji, aby zapewnić użytkownikowi poprawną zawartość na podstawie akcji (z funkcjami) i funkcji kontekstu.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skonfiguruj klucz i punkt końcowy personalizacji
> * Zbierz funkcje
> * Wywoływanie interfejsów API rangi i nagrody
> * Wyświetl górną akcję wyoznaczoną jako _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Wybierz najlepszą zawartość dla aplikacji sieci Web

Aplikacja sieci Web powinna używać personalizacji, gdy istnieje lista _akcji_ (część zawartości) na stronie sieci Web, która musi być spersonalizowana do pojedynczego elementu Top (rewardActionId) do wyświetlenia. Przykładowe listy akcji obejmują artykuły z wiadomościami, lokalizacje umieszczania przycisków i wybory wyrazów dla nazw produktów.

Do pętli personalizacji jest wysyłana lista akcji wraz z funkcjami kontekstu. Personalizacja wybiera jedną najlepszą akcję, a następnie aplikacja sieci Web Wyświetla tę akcję.

W tym samouczku akcje są typami żywności:

* pochodzące
* Lody
* Narrow
* owoc
* popcorn
* ekspres
* zupy

Aby ułatwić personalizację informacji o działaniach, Wyślij obie _akcje z funkcjami_ i _funkcjami kontekstu_ przy użyciu każdego żądania interfejsu API rangi.

**Funkcja** modelu to informacje o akcjach lub kontekście, które mogą być agregowane (pogrupowane) między członkami bazy użytkownika aplikacji sieci Web. Funkcja _nie jest_ pojedynczo specyficzna (na przykład identyfikator użytkownika) lub wysoce konkretnie (na przykład w danym dniu).

### <a name="actions-with-features"></a>Akcje z funkcjami

Każda akcja (element zawartości) zawiera funkcje, które ułatwiają odróżnienie elementu żywności.

Funkcje nie są konfigurowane jako część konfiguracji pętli w Azure Portal. Zamiast tego są one wysyłane jako obiekt JSON z każdym wywołaniem interfejsu API rangi. Dzięki temu można elastycznie rozwijać, zmieniać i zmniejszać działania w miarę upływu czasu, co umożliwia Personalizowanie następujących trendów.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Funkcje kontekstu

Funkcje kontekstu ułatwiają personalizację kontekstu akcji. Kontekst tej przykładowej aplikacji obejmuje:

* pora dnia rano, południe, wieczór, noc
* Preferencja dla ziarna, słodkie, gorzkie, kwaśne lub Savory
* kontekst użytkownika w przeglądarce, lokalizacja geograficzna, odwołujący

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Jak aplikacja sieci Web używa personalizacji?

Aplikacja sieci Web używa personalizacji, aby wybrać najlepszą akcję z listy dostępnych opcji żywności. Robi to, wysyłając następujące informacje z każdym wywołaniem interfejsu API rangi:
* **Akcje** z ich funkcjami, takimi jak `taste` i `spiceLevel`
* funkcje **kontekstu** , takie jak `time` dzień, `taste` preferencja użytkownika i informacje o agencie użytkownika przeglądarki oraz funkcje kontekstu
* **akcje do wykluczenia** , takie jak sok
* **EventID**, który różni się dla każdego wywołania interfejsu API rangi.

## <a name="personalizer-model-features-in-a-web-app"></a>Funkcje modelu narzędzia Personalizacja w aplikacji sieci Web

Personalizacja wymaga funkcji dla akcji (zawartości) i bieżącego kontekstu (użytkownika i środowiska). Funkcje służą do wyrównywania akcji do bieżącego kontekstu w modelu. Model jest reprezentacją poprzedniej wiedzy personalizowania dotyczącej akcji, kontekstu i ich funkcji, które umożliwiają działowi IT podejmowanie decyzji.

Model, w tym funkcje, jest aktualizowany zgodnie z harmonogramem na podstawie ustawienia **częstotliwości aktualizacji modelu** w Azure Portal.

> [!CAUTION]
> Funkcje w tej aplikacji mają na celu zilustrowanie funkcji i wartości funkcji, ale niekoniecznie najlepszym funkcjom do użycia w aplikacji sieci Web.

### <a name="plan-for-features-and-their-values"></a>Planowanie funkcji i ich wartości

Należy wybrać funkcje z tym samym planowaniem i projektem, które mają być stosowane do dowolnego schematu lub modelu w architekturze technicznej. Wartości funkcji można ustawić za pomocą logiki biznesowej lub systemów innych firm. Wartości funkcji nie powinny być tak wysoce dokładne, że nie mają zastosowania w ramach grupy lub klasy funkcji.

### <a name="generalize-feature-values"></a>Uogólnij wartości funkcji

#### <a name="generalize-into-categories"></a>Uogólnij do kategorii

Ta aplikacja używa `time` jako funkcji, ale w grupach czasu do kategorii, takich jak `morning` ,, `afternoon` `evening` i `night` . Jest to przykład użycia informacji o czasie, ale nie w wysoce specyficzny sposób, takich jak `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Uogólnij do części

Ta aplikacja używa funkcji żądania HTTP z przeglądarki. Rozpoczyna się to od bardzo konkretnego ciągu ze wszystkimi danymi, na przykład:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

Biblioteka klas **HttpRequestFeatures** służy do uogólniania tego ciągu do obiektu **userAgentInfo** z poszczególnymi wartościami. Wszystkie wartości, które są zbyt specyficzne, są ustawiane na pusty ciąg. Po wysłaniu funkcji kontekstu dla żądania ma on następujący format JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Korzystanie z przykładowej aplikacji internetowej

W przykładowej aplikacji internetowej opartej na przeglądarce (wszystkie kody są dostarczane) wymagane jest zainstalowanie następujących aplikacji w celu uruchomienia aplikacji.

Zainstaluj następujące oprogramowanie:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) — przykładowy serwer zaplecza używa platformy .NET Core
* [Node.js](https://nodejs.org/) — klient/fronton są zależne od tej aplikacji
* [Program Visual studio 2019](https://visualstudio.microsoft.com/vs/)lub [interfejs wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/) — używanie środowiska deweloperskiego programu visual Studio 2019 lub interfejs wiersza polecenia platformy .NET Core do kompilowania i uruchamiania aplikacji

### <a name="set-up-the-sample"></a>Konfigurowanie przykładu
1. Sklonuj repozytorium przykładów narzędzia Personalizacja platformy Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Przejdź do _przykładów/HttpRequestFeatures_ , aby otworzyć rozwiązanie `HttpRequestFeaturesExample.sln` .

    Jeśli jest to wymagane, Zezwól programowi Visual Studio na zaktualizowanie pakietu .NET dla programu Personalizacja.

### <a name="set-up-azure-personalizer-service"></a>Konfigurowanie usługi personalizacji platformy Azure

1. [Utwórz zasób personalizacji](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) w Azure Portal.

1. W Azure Portal Znajdź `Endpoint` i albo `Key1` `Key2` (będzie działał) na karcie **klucze i punkty końcowe** . Są to Twoje `PersonalizerServiceEndpoint` i Twoje Twoje `PersonalizerApiKey` .
1. Wypełnij pola `PersonalizerServiceEndpoint` w **appsettings.js**.
1. Skonfiguruj `PersonalizerApiKey` jako wpisy [tajne aplikacji](https://docs.microsoft.com/aspnet/core/security/app-secrets) w jeden z następujących sposobów:

    * Jeśli używasz interfejs wiersza polecenia platformy .NET Core, możesz użyć `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` polecenia.
    * Jeśli używasz programu Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz opcję Zarządzaj wpisami **tajnymi użytkownika** , aby skonfigurować klucze personalizacji. Dzięki temu program Visual Studio otworzy `secrets.json` plik, w którym można dodać klucze w następujący sposób:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Kompiluj i uruchamiaj HttpRequestFeaturesExample przy użyciu jednej z następujących metod:

* Visual Studio 2019: naciśnij klawisz **F5**
* Interfejs wiersza polecenia platformy .NET Core: `dotnet build` następnie `dotnet run`

Za pośrednictwem przeglądarki sieci Web można wysłać żądanie rangi i żądanie nagrody oraz zobaczyć ich odpowiedzi, a także funkcje żądania HTTP wyodrębnione ze środowiska.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawia przykład funkcji żądania HTTP w przeglądarce internetowej.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Zademonstrowanie pętli personalizacji

1. Wybierz przycisk **Generuj nowe żądanie rangi** , aby utworzyć nowy obiekt JSON dla wywołania interfejsu API rangi. Spowoduje to utworzenie akcji (z funkcjami) i funkcji kontekstu, a następnie wyświetlenie wartości, aby zobaczyć, jak wygląda kod JSON.

    W przypadku własnej przyszłej aplikacji generowanie akcji i funkcji może wystąpić na kliencie, na serwerze, w połączeniu z dwoma lub z wywołaniami do innych usług.

1. Wybierz pozycję **Wyślij żądanie rangi** , aby wysłać obiekt JSON do serwera. Serwer wywołuje interfejs API rangi personalizacji. Serwer odbiera odpowiedź i zwraca górną rangą akcję do wyświetlenia przez klienta.

1. Ustaw wartość nagrody, a następnie wybierz przycisk **Wyślij żądanie nagrody** . Jeśli wartość nagrody nie zostanie zmieniona, aplikacja kliencka zawsze wyśle wartość `1` do personalizacji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia sekcję żądanie nagrody.](./media/tutorial-web-app/reward-score-api-call.png)

    W przypadku własnej przyszłej aplikacji generowanie wyniku odnoszącego się do nagrody może wystąpić po zebraniu informacji z zachowania użytkownika na kliencie wraz z logiką biznesową na serwerze.

## <a name="understand-the-sample-web-app"></a>Opis przykładowej aplikacji internetowej

Przykładowa aplikacja internetowa zawiera serwer **.NET w języku C#** , który zarządza kolekcją funkcji oraz wysyłaniem i odbieraniem wywołań http do punktu końcowego narzędzia Personalizacja.

Przykładowa aplikacja internetowa używa **odcinania aplikacji klienckiej frontonu** do przechwytywania funkcji i przetwarzania akcji interfejsu użytkownika, takich jak klikanie przycisków i wysyłanie danych do serwera .NET.

W poniższych sekcjach opisano części serwera i klienta, które Deweloper musi zrozumieć, aby użyć personalizacji.

## <a name="rank-api-client-application-sends-context-to-server"></a>Interfejs API rangi: aplikacja kliencka wysyła kontekst do serwera

Aplikacja kliencka zbiera _agenta użytkownika_przeglądarki.

> [!div class="mx-imgBorder"]
> ![Kompiluj i uruchamiaj projekt HTTPRequestFeaturesExample. Otworzy się okno przeglądarki, aby wyświetlić aplikację jednostronicową.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Ranga API: wywołania aplikacji serwera Personalizujer

Jest to typowa aplikacja sieci Web platformy .NET z aplikacją kliencką. jest dla Ciebie dostępna większość kodu kliszy kotłowej. Dowolny kod niespecyficzny dla personalizacji jest usuwany z poniższych fragmentów kodu, dzięki czemu możesz skupić się na kodzie specyficznym dla personalizacji.

### <a name="create-personalizer-client"></a>Utwórz klienta personalizacji

W **Startup.cs**serwera, punkt końcowy i klucz personalizacji są używane do tworzenia klienta personalizacji. Aplikacja kliencka nie musi komunikować się z personalizacją w tej aplikacji, zamiast polegać na serwerze, aby wykonać te wywołania zestawu SDK.

Kod uruchomienia platformy .NET serwera sieci Web:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Wybierz najlepszą akcję

W **PersonalizerController.cs**serwera interfejs API serwera **GenerateRank** podsumowuje przygotowanie do wywołania interfejsu API rangi

* Utwórz nową `eventId` dla wywołania rangi
* Pobierz listę akcji
* Pobierz listę funkcji z użytkownika i Utwórz funkcje kontekstu
* Opcjonalnie Ustaw wszystkie wykluczone akcje
* Wywołaj interfejs API rangi, zwróć wyniki do klienta

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

KOD JSON wysłany do narzędzia personalizacji, zawierający obie akcje (z funkcjami) i bieżące funkcje kontekstu, wygląda następująco:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Zwróć rewardActionId personalizacji do klienta

Interfejs API rangi zwraca wybraną najlepszą akcję **rewardActionId** do serwera.

Wyświetl akcję zwróconą w **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Klient wyświetla akcję rewardActionId

W tym samouczku `rewardActionId` zostanie wyświetlona wartość.

We własnej aplikacji, która może mieć dokładny tekst, przycisk lub część wyróżnionej strony sieci Web. Lista jest zwracana dla wszystkich wyników analizy, a nie kolejności zawartości. `rewardActionId`Powinna zostać wyświetlona tylko zawartość.

## <a name="reward-api-collect-information-for-reward"></a>Interfejs API nagradzania: zbieranie informacji na temat nagrody

[Wynik nagrody](concept-rewards.md) należy uważnie zaplanować, tak jak funkcje są planowane. Wynik nagrody zazwyczaj powinien być wartością z przeważnią od 0 do 1. Wartość _można_ obliczyć częściowo w aplikacji klienckiej na podstawie zachowań użytkownika i częściowo na serwerze, na podstawie logiki biznesowej i celów.

Jeśli serwer nie wywoła interfejsu API nagradzania w **czasie oczekiwania** , który skonfigurowano w Azure Portal dla zasobu personalizacji, wówczas dla tego zdarzenia zostanie użyta **wartość domyślna Nagroda** (również skonfigurowana w Azure Portal).

W tej przykładowej aplikacji możesz wybrać wartość, aby zobaczyć, jak wpływ na wybór.

## <a name="additional-ways-to-learn-from-this-sample"></a>Dodatkowe sposoby uczenia się z tego przykładu

W przykładzie używane są kilka zdarzeń opartych na czasie skonfigurowanych w Azure Portal dla zasobu personalizacji. Odtwórz te wartości, a następnie wróć do tej przykładowej aplikacji sieci Web, aby zobaczyć, jak zmiany wpływają na rangę i wynagrodzenie:

* Nagradzany czas oczekiwania
* Częstotliwość aktualizacji modelu

Dodatkowe ustawienia do odtworzenia przy użyciu programu obejmują:
* Wynagrodzenie domyślne
* Procent eksploracji


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Oczyść następujące zasoby:

* Usuń przykładowy katalog projektu.
* Usuń zasób personalizowania — Pomyśl o zasobie narzędzia personalizacji jako dedykowanym dla akcji i kontekstu — ponownie Użyj zasobu, jeśli nadal korzystasz z żywności jako domeny podmiotu akcji.


## <a name="next-steps"></a>Następne kroki
* [Jak działa usługa Personalizacja](how-personalizer-works.md)
* [Funkcje](concepts-features.md): Poznaj koncepcje dotyczące funkcji za pomocą akcji i kontekstu
* [Nagrody](concept-rewards.md): informacje o obliczaniu nagrody
