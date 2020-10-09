---
title: 'Samouczek: Notes platformy Azure — Personalizacja'
titleSuffix: Azure Cognitive Services
description: Ten samouczek symuluje pętlę personalizacji _system w notesie platformy Azure, która sugeruje, jaki typ kawy powinien zamówić. Użytkownicy i ich preferencje są przechowywane w zestawie danych użytkownika. Informacje o kawy są również dostępne i przechowywane w zestawie danych kawy.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0cf2aa504fd7a36a55740b3a59eeb3759e67f469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509913"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Samouczek: używanie programu Personalizujer w notesie platformy Azure

W tym samouczku uruchomiono pętlę personalizacji w notesie platformy Azure, ukazującą kompleksowy cykl życia pętli personalizacji.

Pętla sugeruje, który typ kawy powinien być zamówiony przez klienta. Użytkownicy i ich preferencje są przechowywane w zestawie danych użytkownika. Informacje o kawy są przechowywane w zestawie danych kawy.

## <a name="users-and-coffee"></a>Użytkownicy i kawa

Notes, symulowanie interakcji użytkownika z witryną sieci Web, wybieranie losowego użytkownika, pory dnia i typu pogody z zestawu danych. Podsumowanie informacji o użytkowniku:

|Klienci — funkcje kontekstu|Razy dziennie|Typy pogodowe|
|--|--|--|
|Robert<br>Bob<br>Cathy<br>Dave|Dobry<br>Południe<br>Wieczorem|Sunny<br>Rainy<br>Sosna|

Aby ułatwić personalizację, w miarę upływu czasu _system_ wie również o wyborze kawy dla każdej osoby.

|Funkcje działania kawy|Typy temperatur|Miejsca pochodzenia|Typy palone|organiczne|
|--|--|--|--|--|
|Cappacino|Gorąca|Kenia|Ciemny|organiczne|
|Zimna rozwiązania brew|Chłodne|Brazylia|Jasny|organiczne|
|Iced środowiska Mocha|Chłodne|Etiopia|Jasny|Nie organiczny|
|Latte|Gorąca|Brazylia|Ciemny|Nie organiczny|

Pętla personalizacji ma **na celu znalezienie** najlepszego dopasowania między użytkownikami i kawą tak długo, jak to możliwe.

Kod dla tego samouczka jest dostępny w [repozytorium programu Personalizacja przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Jak działa symulacja

Na początku działającego systemu sugestie z personalizacji działają tylko pomyślnie od 20% do 30%. Ten sukces jest wskazywany przez wynagrodzenie wysłane z powrotem do interfejsu API nagradzania personalizacji z wynikiem 1. Po wystąpieniu pewnego wywołania rangi i nagrody system ulepsza.

Po początkowych żądaniach Uruchom ocenę w trybie offline. Umożliwia to programowi Personalizujemu przeglądanie danych i zaproponowanie lepszych zasad uczenia się. Zastosuj nowe zasady uczenia i ponownie uruchom Notes z 20% poprzedniej liczby żądań. Pętla będzie działać lepiej przy użyciu nowych zasad nauki.

## <a name="rank-and-reward-calls"></a>Wywołania rangi i nagrody

Dla każdego z kilku tysięcy wywołań usługi personalizacji, Notes platformy Azure wysyła żądanie **rangi** do interfejsu API REST:

* Unikatowy identyfikator dla zdarzenia rangi/żądania
* Funkcje kontekstu — wybór losowy użytkownika, Pogoda i godzinę — symulowanie użytkownika w witrynie sieci Web lub na urządzeniu przenośnym
* Akcje z funkcjami — _wszystkie_ dane kawowe — z których Personalizuj wykonuje sugestię

System odbiera żądanie, a następnie porównuje to prognozę ze znanym wyborem użytkownika przez ten sam dzień i pogoda. Jeśli znany wybór jest taki sam jak wybór przewidywany, **wynagrodzenie** 1 jest wysyłane z powrotem do personalizacji. W przeciwnym razie otrzymaną opłatą jest 0.

> [!Note]
> Jest to symulacja, dzięki czemu algorytm dla nagrody jest prosty. W rzeczywistym scenariuszu algorytm powinien używać logiki biznesowej, prawdopodobnie z wagami różnych aspektów środowiska klienta, aby określić wynik nagrody.


## <a name="prerequisites"></a>Wymagania wstępne

* Konto [notesu platformy Azure](https://notebooks.azure.com/) .
* [Zasób personalizacji platformy Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Jeśli zasób personalizacji został już użyty, upewnij się, że dane w Azure Portal dla zasobu zostały [wyczyszczone](how-to-settings.md#clear-data-for-your-learning-loop) .
* Przekaż wszystkie pliki [tego przykładu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) do projektu notesu platformy Azure.

Opisy plików:

* [Personalizacja. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) to Notes Jupyter dla tego samouczka.
* [Zestaw danych użytkownika](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) jest przechowywany w obiekcie JSON.
* [Zestaw danych kawy](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) jest przechowywany w obiekcie JSON.
* [Przykładowy kod JSON żądania](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) jest oczekiwanym FORMATEM żądania post do interfejsu API rangi.

## <a name="configure-personalizer-resource"></a>Konfiguruj zasób personalizacji

W Azure Portal Skonfiguruj [zasób personalizacji](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) z **częstotliwością aktualizacji** z ustawioną na 15 sekund i **poczekaj** na 15 sekund. Te wartości można znaleźć na stronie **[Konfiguracja](how-to-settings.md#configure-service-settings-in-the-azure-portal)** .

|Ustawienie|Wartość|
|--|--|
|Aktualizuj częstotliwość modelu|15 sekund|
|nagradzany czas oczekiwania|15 sekund|

Te wartości mają bardzo krótki czas trwania, aby można było wyświetlić zmiany w tym samouczku. Te wartości nie powinny być używane w scenariuszu produkcyjnym, bez weryfikowania, że osiągają cel w pętli personalizacji.

## <a name="set-up-the-azure-notebook"></a>Konfigurowanie notesu platformy Azure

1. Zmień jądro na `Python 3.6` .
1. Otwórz plik `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Uruchamianie komórek notesu

Uruchom każdą komórkę wykonywalną i poczekaj na jej zwrócenie. Wiadomo, że jest to wykonywane, gdy nawiasy obok komórki wyświetlają liczbę zamiast `*` . W poniższych sekcjach wyjaśniono, co każda komórka wykonuje programowo i czego można oczekiwać na dane wyjściowe.

### <a name="include-the-python-modules"></a>Uwzględnij moduły języka Python

Uwzględnij wymagane moduły języka Python. Komórka nie ma danych wyjściowych.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Określ nazwę i klucz zasobu personalizacji

W Azure Portal Znajdź klucz i punkt końcowy na stronie **szybkiego startu** zasobu Personalizacja. Zmień wartość `<your-resource-name>` na nazwę zasobu personalizacji. Zmień wartość `<your-resource-key>` na klucz personalizacji.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Drukuj bieżącą datę i godzinę
Ta funkcja służy do zanotowania czasu rozpoczęcia i zakończenia funkcji iteracyjnej, iteracji.

Te komórki nie mają danych wyjściowych. Funkcja wyprowadza bieżącą datę i godzinę, gdy zostanie wywołana.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Pobierz ostatni czas aktualizacji modelu

Gdy funkcja, `get_last_updated` ,, jest wywoływana, funkcja drukuje datę i godzinę ostatniej modyfikacji modelu.

Te komórki nie mają danych wyjściowych. Funkcja wykonuje ostatnią datę uczenia modelu po wywołaniu.

Funkcja używa interfejsu API REST do [pobierania właściwości modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Pobieranie zasad i konfiguracji usługi

Sprawdź stan usługi za pomocą tych dwóch wywołań REST.

Te komórki nie mają danych wyjściowych. Funkcja wyprowadza wartości usługi po wywołaniu.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Konstruowanie adresów URL i odczytywanie plików danych JSON

Ta komórka

* kompiluje adresy URL używane w wywołaniach REST
* ustawia nagłówek zabezpieczeń przy użyciu klucza zasobu narzędzia Personalizacja
* Ustawia losowy inicjator dla identyfikatora zdarzenia rangi
* odczytuje w plikach danych JSON
* Metoda Calls `get_last_updated` — zasady uczenia zostały usunięte z przykładowych danych wyjściowych
* Calls — `get_service_settings` Metoda

Komórka zawiera dane wyjściowe wywołania `get_last_updated` `get_service_settings` funkcji i.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Sprawdź, czy dane wyjściowe `rewardWaitTime` i `modelExportFrequency` są ustawione na 15 sekund.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Rozwiązywanie problemów z pierwszym wywołaniem REST

Ta Poprzednia komórka jest pierwszą komórką, która wywołuje do personalizacji. Upewnij się, że kod stanu REST w danych wyjściowych to `<Response [200]>` . Jeśli wystąpi błąd, na przykład 404, ale upewnij się, że klucz zasobu i nazwa są poprawne, Załaduj ponownie Notes.

Upewnij się, że liczba kawy i użytkowników to 4. Jeśli wystąpi błąd, sprawdź, czy zostały przekazane wszystkie 3 pliki JSON.

### <a name="set-up-metric-chart-in-azure-portal"></a>Skonfiguruj wykres metryk w Azure Portal

W dalszej części tego samouczka długotrwały proces działania 10 000 żądań jest widoczny z przeglądarki z polem tekstowym aktualizowanie. Po zakończeniu długotrwałego procesu może być łatwiej widoczny na wykresie lub jako łączna suma. Aby wyświetlić te informacje, użyj metryk dostarczanych z zasobem. Możesz utworzyć wykres teraz, gdy zakończysz żądanie do usługi, a następnie Odśwież wykres okresowo, gdy trwa proces długotrwały.

1. W Azure Portal wybierz zasób personalizacji.
1. W obszarze nawigacji zasobów wybierz pozycję **metryki** poniżej monitorowanie.
1. Na wykresie wybierz pozycję **Dodaj metrykę**.
1. Przestrzeń nazw zasobu i metryki została już ustawiona. Wystarczy wybrać metrykę **udanych wywołań** i agregację **sum**.
1. Zmień filtr czasu na ostatnie 4 godziny.

    ![Skonfiguruj wykres metryk w Azure Portal, dodając metrykę dla udanych wywołań dla ostatnich 4 godzin.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Na wykresie powinny być widoczne trzy pomyślne wywołania.

### <a name="generate-a-unique-event-id"></a>Generuj unikatowy identyfikator zdarzenia

Ta funkcja generuje unikatowy identyfikator dla każdego wywołania rangi. Identyfikator jest używany do identyfikowania informacji o zadaniu rangi i nagrody. Ta wartość może pochodzić z procesu biznesowego, takiego jak identyfikator widoku sieci Web lub identyfikator transakcji.

Komórka nie ma danych wyjściowych. Funkcja wyprowadza unikatowy identyfikator, gdy zostanie wywołana.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Pobierz losowego użytkownika, Pogoda i godzinę

Ta funkcja wybiera unikatowy użytkownik, Pogoda i godzinę dnia, a następnie dodaje te elementy do obiektu JSON w celu wysłania do żądania rangi.

Komórka nie ma danych wyjściowych. Gdy funkcja jest wywoływana, zwraca nazwę losowego użytkownika, losową Pogoda i losowy czas.

Lista 4 użytkowników i ich preferencje — dla zwięzłości są wyświetlane tylko niektóre preferencje:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Dodaj wszystkie dane kawy

Ta funkcja dodaje całą listę kawy do obiektu JSON w celu wysłania do żądania rangi.

Komórka nie ma danych wyjściowych. Funkcja zmienia wartość `rankjsonobj` po wywołaniu.


Przykładem funkcji pojedynczej kawy jest:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Porównanie prognoz z znanym preferencją użytkownika

Ta funkcja jest wywoływana po wywołaniu interfejsu API rangi dla każdej iteracji.

Ta funkcja porównuje preferencje użytkownika dotyczące kawy, na podstawie pogody i pory dnia, z sugestią dla użytkownika dla tych filtrów. W przypadku dopasowania sugestii zwraca się wynik 1. w przeciwnym razie wynik jest równy 0. Komórka nie ma danych wyjściowych. Funkcja wyprowadza wynik po wywołaniu.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Pętla przez wywołania rangi i nagrody

Następna komórka to _główna_ część notesu, pobierająca losowego użytkownika, pobierającego listę kawy, wysyłającą oba elementy do interfejsu API rangi. Porównanie prognoz z znanymi preferencjami użytkownika, a następnie wysłanie premii z powrotem do usługi personalizacji.

Pętla jest uruchamiana przez `num_requests` czas. Personalizacja potrzebuje kilku tysięcy wywołań do rangi i nagrody, aby utworzyć model.

Przykład kodu JSON wysyłanego do interfejsu API rangi znajduje się poniżej. Lista kawy nie jest kompletna dla zwięzłości. Cały kod JSON dla kawy można zobaczyć w `coffee.json` .

Dane JSON wysłane do interfejsu API rangi:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Odpowiedź JSON z interfejsu API rangi:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Na koniec Każda pętla pokazuje losowy wybór użytkownika, Pogoda, pory dnia i określony wynagrodzenie. Wartość 1 oznacza, że zasób personalizacji zaznaczył właściwy typ kawy dla danego użytkownika, pogody i godziny dnia.

```console
1 Alice Rainy Morning Latte 1
```

Funkcja używa:

* Ranga: interfejs API REST w celu [uzyskania rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Wynagrodzenie: interfejs API REST do [raportowania nagrody](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Uruchom dla iteracji 10 000
Uruchom pętlę personalizacji dla iteracji 10 000. To jest długotrwałe zdarzenie. Nie zamykaj przeglądarki z uruchomionym notesem. Odśwież wykres metryk w Azure Portal okresowo, aby zobaczyć łączną liczbę wywołań do usługi. Gdy masz około 20 000 wywołań, ranga i wynagrodzenie dla każdej iteracji pętli, wykonywane są iteracje.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Wyniki wykresu, aby zobaczyć ulepszenie

Utwórz wykres z `count` i `rewards` .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Uruchom wykres dla żądań rangi 10 000

Uruchom `createChart` funkcję.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Odczytywanie wykresu

Ten wykres pokazuje powodzenie modelu dla bieżących domyślnych zasad nauki.

![Ten wykres pokazuje pomyślne bieżące zasady uczenia w czasie trwania testu.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Idealnym miejscem docelowym, który kończy się na końcu testu, pętla jest przeciętnie Częstotliwość powodzeń zbliżoną do 100 procent minus eksploracji. Domyślna wartość eksploracji to 20%.

`100-20=80`

Ta wartość eksploracji znajduje się w Azure Portal dla zasobu Personalizacja na stronie **Konfiguracja** .

Aby znaleźć lepsze zasady uczenia na podstawie danych do interfejsu API rangi, uruchom [ocenę w trybie offline](how-to-offline-evaluation.md) w portalu dla pętli personalizacji.

## <a name="run-an-offline-evaluation"></a>Uruchamianie oceny w trybie offline

1. W Azure Portal Otwórz stronę **oceny** zasobów dla programu Personalizacja.
1. Wybierz pozycję **Utwórz ocenę**.
1. Wprowadź wymagane dane nazwy oceny i zakres dat dla oceny pętli. Zakres dat powinien zawierać tylko dni, w których użytkownik koncentruje się na potrzeby oceny.
    ![W Azure Portal Otwórz stronę oceny zasobów dla programu Personalizacja. Wybierz pozycję Utwórz ocenę. Wprowadź nazwę ewaluacyjną i zakres dat.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Celem tej oceny w trybie offline jest ustalenie, czy istnieją lepsze zasady uczenia dotyczące funkcji i akcji używanych w tej pętli. Aby znaleźć lepsze zasady uczenia, upewnij się, że **Funkcja odnajdywania optymalizacji** jest włączona.

1. Wybierz **przycisk OK** , aby rozpocząć obliczanie.
1. Na tej stronie **oceny** są wyświetlane nowe oceny i ich bieżący stan. W zależności od ilości danych, ta Ocena może zająć trochę czasu. Możesz wrócić do tej strony po kilku minutach, aby zobaczyć wyniki.
1. Po zakończeniu oceny Wybierz ocenę, a następnie wybierz opcję **porównanie różnych zasad nauki**. Przedstawiono w nim dostępne zasady uczenia i sposób ich zachowania z danymi.
1. Wybierz najważniejsze zasady uczenia w tabeli i wybierz pozycję **Zastosuj**. Spowoduje to zastosowanie _najlepszych_ zasad uczenia w modelu i ponowne przeszkolenie.

## <a name="change-update-model-frequency-to-5-minutes"></a>Zmień częstotliwość modelu aktualizacji na 5 minut

1. W Azure Portal nadal w obszarze zasób Personalizacja wybierz stronę **Konfiguracja** .
1. Zmień **częstotliwość aktualizacji modelu** i zmniejsz **czas oczekiwania** na 5 minut, a następnie wybierz pozycję **Zapisz**.

Dowiedz się więcej na temat [nagrody czasu oczekiwania](concept-rewards.md#reward-wait-time) i [częstotliwości aktualizacji modelu](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Sprawdź, czy dane wyjściowe `rewardWaitTime` i `modelExportFrequency` są ustawione na 5 minut.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Weryfikuj nowe zasady uczenia

Wróć do pliku Azure Notebooks i Kontynuuj, uruchamiając tę samą pętlę, ale tylko dla iteracji 2 000. Odśwież wykres metryk w Azure Portal okresowo, aby zobaczyć łączną liczbę wywołań do usługi. Gdy masz około 4 000 wywołań, ranga i wynagrodzenie dla każdej iteracji pętli, wykonywane są iteracje.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Uruchom wykres dla żądań rangi 2 000

Uruchom `createChart` funkcję.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Przejrzyj drugi wykres

Drugi wykres powinien pokazać widoczny wzrost prognoz klasyfikacji z preferencjami użytkownika.

![Drugi wykres powinien pokazać widoczny wzrost prognoz klasyfikacji z preferencjami użytkownika.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz kontynuować pracy z serią samouczków, Oczyść następujące zasoby:

* Usuń projekt notesu platformy Azure.
* Usuń zasób personalizacji.

## <a name="next-steps"></a>Następne kroki

[Notes Jupyter i pliki danych](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) używane w tym przykładzie są dostępne w repozytorium GitHub dla personalizacji.

