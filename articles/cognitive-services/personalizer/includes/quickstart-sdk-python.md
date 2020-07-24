---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.openlocfilehash: b602ab1a6aa6f9c4c153924bce63da12d872a62e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133884"
---
[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-cognitiveservices-personalizer/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start


Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Konfiguracja** Zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Zainstaluj bibliotekę języka Python dla programu Personalizacja

Zainstaluj bibliotekę klienta programu Personalizacja dla języka Python za pomocą następującego polecenia:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby zażądać pojedynczego najlepszego elementu zawartości, Utwórz element [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python), a następnie Przekaż go do klienta. Ranga metody. Metoda rangi zwraca RankResponse.

Aby wysłać wynik nagrody do personalizacji, należy ustawić identyfikator zdarzenia i wynik nagrody (wartość) do wysłania do metody [nagrody](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) w klasie EventOperations.

Ustalenie nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](../concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Powinna to być jedna z podstawowych decyzji projektowych w architekturze personalizacji.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej personalizacji dla języka Python:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Interfejs API nagradzania](#send-a-reward)

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python przy użyciu preferowanego edytora lub środowiska IDE o nazwie `sample.py` .

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik **Sample.py** w preferowanym edytorze lub w środowisku IDE. Dodaj następujące elementy:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource ściągnięte ze zmiennych środowiskowych o nazwie `PERSONALIZER_RESOURCE_KEY` i `PERSONALIZER_RESOURCE_ENDPOINT` . Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

Nazwa zasobu jest częścią adresu URL punktu końcowego: `https://<your-resource-name>.api.cognitive.microsoft.com/` .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody ma wartość, `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey jest `PERSONALIZER_RESOURCE_KEY` .

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Pobierz opcje zawartości reprezentowane jako akcje

Akcje reprezentują opcje zawartości, z których chcesz spersonalizować, aby wybrać najlepszy element zawartości. Dodaj następujące metody do klasy program, aby reprezentować zestaw akcji i ich funkcji.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań [rangi](#request-the-best-action) i [nagrody](#send-a-reward) . W tym przewodniku szybki start każde wywołanie rangi, aby spersonalizować zawartość, nastąpi wywołanie płatne, aby poinformować program Personalizuj, jak dobrze przeprowadzono usługę.

Poniższy kod wykonuje pętlę przez cykl monitowania użytkownika o preferencje w wierszu polecenia, wysyłając te informacje do narzędzia personalizacji w celu wybrania najlepszej akcji, która przedstawia wybór dla klienta, aby wybrać z listy, a następnie wysyłać wynagrodzenie do narzędzia personalizacji sygnalizujące, jak dobrze została wybrana usługa.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-content-choices-represented-as-actions), przed uruchomieniem pliku kodu:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Żądaj najlepszej akcji


Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia `currentContent` opcji zawartości. Proces może tworzyć zawartość, która ma zostać wykluczona z akcji, pokazana jako `excludeActions` . Żądanie rangi wymaga działań i ich funkcji, funkcji currentContext, excludeActions i unikatowego identyfikatora zdarzenia, aby otrzymać odpowiedź.

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być nieuproszczona.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie


Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje do zaznaczenia wartość liczbową, a następnie wysyła unikatowy identyfikator zdarzenia oraz wynik nagrody jako wartość liczbową do interfejsu API nagradzania.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](../concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację przy użyciu języka Python z katalogu aplikacji.

```console
python sample.py
```

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)