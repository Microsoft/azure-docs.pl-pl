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
ms.openlocfilehash: 94ffc22cc7e8e44e99d5eb42518030ff41267996
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133883"
---
[Dokumentacja](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Korzystanie z tego przewodnika Szybki Start

Aby skorzystać z tego przewodnika Szybki Start, należy wykonać kilka czynności:

* W Azure Portal Utwórz zasób personalizacji
* W Azure Portal dla zasobu Personalizacja na stronie **Konfiguracja** Zmień częstotliwość aktualizacji modelu na bardzo krótki interwał
* W edytorze kodu Utwórz plik kodu i edytuj plik kodu
* W wierszu polecenia lub terminalu Zainstaluj zestaw SDK z wiersza polecenia
* W wierszu polecenia lub terminalu uruchom plik kodu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE.

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia dotnet, `new` Aby utworzyć nową aplikację konsolową o nazwie `personalizer-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: `Program.cs` .

```console
dotnet new console -n personalizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta programu Personalizacja dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Klient narzędzia personalizacji jest obiektem [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Aby zażądać pojedynczego najlepszego elementu zawartości, Utwórz element [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), a następnie Przekaż go do [klienta. Ranga](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metody. Metoda rangi zwraca RankResponse.

Aby wysłać wynik nagrody do personalizacji, Utwórz [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), a następnie Przekaż go do [klienta. Metoda nagradzania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) .

Określenie wyniku nagrody w tym przewodniku Szybki Start jest proste. W systemie produkcyjnym określenie, co ma wpływ na [wynik nagrody](../concept-rewards.md) i według ile może być złożonym procesem, można zmienić z upływem czasu. Ta decyzja projektowa powinna być jedną z podstawowych decyzji w architekturze personalizacji.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta programu Personalizacja dla platformy .NET:

* [Tworzenie klienta programu Personalizacja](#create-a-personalizer-client)
* [Interfejs API rangi](#request-the-best-action)
* [Interfejs API nagradzania](#send-a-reward)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik **program.cs** w preferowanym edytorze lub w środowisku IDE. Zastąp istniejący `using` kod następującymi `using` dyrektywami:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Dodawanie informacji o zasobach personalizacji

W klasie **program** Utwórz zmienne dla klucza i punktu końcowego usługi Azure Resource pobrane ze zmiennych środowiskowych o nazwie `PERSONALIZER_RESOURCE_KEY` i `PERSONALIZER_RESOURCE_ENDPOINT` . Jeśli po uruchomieniu aplikacji zostały utworzone zmienne środowiskowe, Edytor, środowisko IDE lub powłoka, na których działa, będzie musiał zostać zamknięte i ponownie załadowane w celu uzyskania dostępu do zmiennej. Metody zostaną utworzone w dalszej części tego przewodnika Szybki Start.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Tworzenie klienta programu Personalizacja

Następnie Utwórz metodę zwracającą klienta programu Personalizacja. Parametr do metody ma wartość, `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey jest `PERSONALIZER_RESOURCE_KEY` .

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Pobierz elementy żywnościowe jako akcje do rangi

Akcje reprezentują opcje zawartości, z których chcesz spersonalizować, aby wybrać najlepszy element zawartości. Dodaj następujące metody do klasy program, aby reprezentować zestaw akcji i ich funkcji. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Pobierz preferencje użytkownika dla kontekstu

Dodaj następujące metody do klasy program, aby uzyskać dane wejściowe użytkownika z wiersza polecenia dla pory dnia i bieżącego preferencji żywności. Zostaną one użyte jako funkcje kontekstu.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Obie metody wykorzystują `GetKey` metodę, aby odczytać wybór użytkownika z wiersza polecenia.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Tworzenie pętli uczenia

Pętla szkoleniowa personalizacji jest cyklem wywołań [rangi](#request-the-best-action) i [nagrody](#send-a-reward) . W tym przewodniku szybki start każde wywołanie rangi, aby spersonalizować zawartość, nastąpi wywołanie płatne, aby poinformować program Personalizuj, jak dobrze przeprowadzono usługę.

Poniższy kod wykonuje pętlę przez cykl monitowania użytkownika o preferencje w wierszu polecenia, wysyłając te informacje do narzędzia personalizacji w celu wybrania najlepszej akcji, która przedstawia wybór dla klienta, aby wybrać z listy, a następnie wysłać wynik nagrody do narzędzia personalizacji sygnalizującego, jak dobrze została wybrana usługa.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Dodaj następujące metody, które [pobierają Opcje zawartości](#get-food-items-as-rankable-actions), przed uruchomieniem pliku kodu:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Żądaj najlepszej akcji

Aby ukończyć żądanie rangi, program prosi o preferencje użytkownika w celu utworzenia `currentContent` opcji zawartości. Proces może tworzyć zawartość, która ma zostać wykluczona z akcji, pokazana jako `excludeActions` . Żądanie rangi wymaga działań i ich funkcji, funkcji currentContext, excludeActions i unikatowego identyfikatora zdarzenia, aby otrzymać odpowiedź.

Ten przewodnik Szybki Start zawiera proste funkcje kontekstu o porze dnia i preferencjach żywności dla użytkowników. W systemach produkcyjnych określenie i [Ocena](../concept-feature-evaluation.md) [działań i funkcji](../concepts-features.md) może być nieuproszczona.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Wyślij wynagrodzenie

Aby uzyskać wynik nagrody do wysłania w żądaniu nagrody, program pobiera wybór użytkownika z wiersza polecenia, przypisuje do zaznaczenia wartość liczbową, a następnie wysyła unikatowy identyfikator zdarzenia oraz wynik nagrody jako wartość liczbową do interfejsu API nagradzania.

Ten przewodnik Szybki Start przypisuje prostą liczbę jako wynik nagrody, zero lub 1. W systemach produkcyjnych określenie, kiedy i co mają być wysyłane do [płatnego wywołania,](../concept-rewards.md) może być nieuproszczone, w zależności od konkretnych potrzeb.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom aplikację za pomocą polecenia dotnet `run` z katalogu aplikacji.

```console
dotnet run
```

![Program szybkiego startu prosi o kilka pytań, aby zebrać preferencje użytkownika, znane jako funkcje, a następnie zapewnia najwyższą akcję.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Kod źródłowy dla tego przewodnika Szybki Start](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) jest dostępny w repozytorium.
