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
ms.openlocfilehash: ddf16334830b64c57e9d09a75dfc80fc319a9ccd
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133881"
---
[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [Node.js](https://nodejs.org) i npm.

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start


Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Konfiguracja** Zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby utworzyć `package.json` plik.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Zainstaluj bibliotekę Node.js dla programu Personalizacja

Zainstaluj bibliotekę klienta Personalizuj dla Node.js przy użyciu następującego polecenia:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Zainstaluj pozostałe pakiety NPM dla tego przewodnika Szybki Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby zażądać pojedynczego najlepszego elementu zawartości, Utwórz element [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest), a następnie Przekaż go do [klienta. Ranga](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) metody. Metoda rangi zwraca RankResponse.

Aby wysłać wynagrodzenie do personalizacji, Utwórz element [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest), a następnie Przekaż go do metody [nagradzania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) klasy Events (zdarzenia).

Ustalenie nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](../concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Powinna to być jedna z podstawowych decyzji projektowych w architekturze personalizacji.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej personalizacji dla Node.js:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Interfejs API nagradzania](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Utwórz nową aplikację Node.js w preferowanym edytorze lub środowisku IDE o nazwie `sample.js` .

## <a name="add-the-dependencies"></a>Dodawanie zależności

Otwórz plik **sample.js** w preferowanym edytorze lub w środowisku IDE. Dodaj następujące elementy `requires` , aby dodać pakiety npm:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource ściągnięte ze zmiennych środowiskowych o nazwie `PERSONALIZER_KEY` i `PERSONALIZER_ENDPOINT` . Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody ma wartość, `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey jest `PERSONALIZER_RESOURCE_KEY` .

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Pobierz opcje zawartości reprezentowane jako akcje

Akcje reprezentują opcje zawartości, z których chcesz spersonalizować, aby wybrać najlepszy element zawartości. Dodaj następujące metody do klasy program, aby reprezentować zestaw akcji i ich funkcji.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań [rangi](#request-the-best-action) i [nagrody](#send-a-reward) . W tym przewodniku szybki start każde wywołanie rangi, aby spersonalizować zawartość, nastąpi wywołanie płatne, aby poinformować program Personalizuj, jak dobrze przeprowadzono usługę.

Poniższy kod wykonuje pętlę przez cykl monitowania użytkownika o preferencje w wierszu polecenia, wysyłając te informacje do narzędzia personalizacji w celu wybrania najlepszej akcji, która przedstawia wybór dla klienta, aby wybrać z listy, a następnie wysyłać wynagrodzenie do narzędzia personalizacji sygnalizujące, jak dobrze została wybrana usługa.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Zwróć uwagę na wywołania rangi i nagrody w poniższych sekcjach.

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-content-choices-represented-as-actions), przed uruchomieniem pliku kodu:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Żądaj najlepszej akcji

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia opcji zawartości. Proces może tworzyć zawartość, która ma zostać wykluczona z akcji, pokazana jako `excludeActions` . Żądanie rangi wymaga [działań](../concepts-features.md#actions-represent-a-list-of-options) i ich funkcji, funkcji CurrentContext, excludeActions i unikatowego identyfikatora zdarzenia rangi, aby otrzymać żądaną odpowiedź.

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być nieuproszczona.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie


Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje do zaznaczenia wartość liczbową, a następnie wysyła unikatowy identyfikator zdarzenia oraz wynik nagrody jako wartość liczbową do interfejsu API nagradzania.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](../concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację z Node.js z katalogu aplikacji.

```console
node sample.js
```

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
