---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 629fc5e3bc41377fe852a1648680d77b22395d02
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400932"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `helloworld.cpp`projekt i otwórz .

## <a name="add-a-references"></a>Dodawanie odwołań

Aby przyspieszyć tworzenie kodu, będziemy używać kilku zewnętrznych składników:
* [CPP Spoczynek SDK](https://github.com/microsoft/cpprestsdk) Biblioteka klienta do wykonywania wywołań REST do usługi REST.
* [nlohmann/json](https://github.com/nlohmann/json) Poręczne JSON Parsing / Serializacja / Biblioteka Deserializacji.

Oba mogą być instalowane przy użyciu [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Opakowania JSON

Jako rest interfejsu API przyjmować żądania w formacie JSON, a także zwraca wyniki w JSON możemy wchodzić z nimi przy użyciu tylko ciągów, ale to nie jest zalecane.
Aby ułatwić zarządzanie żądaniami i odpowiedziami, ogłosimy kilka klas do użycia do serializacji / deserializacji JSON i niektórych metod pomocy nlohmann/json.

Śmiało i umieścić swoje `recognizeSpeech` deklaracje przed .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Tworzenie i konfigurowanie klienta http
Pierwszą rzeczą, której potrzebujemy, jest klient http, który ma poprawny podstawowy adres URL i zestaw uwierzytelniania.
Wstaw ten kod do`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generowanie żądania transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten kod do`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz publikujemy żądanie w usłudze mowy i sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi po prostu wskaże, czy usługa otrzymała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, która jest lokalizacją, w której będzie przechowywać stan transkrypcji.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza transkrypcji asynchronicznie, musimy sondować jego status tak często. Sprawdzamy co 5 sekund.

Możemy sprawdzić stan, pobierając zawartość pod adresem URL, który otrzymaliśmy po opublikowaniu żądania. Kiedy przywracamy zawartość, możemy deserialize go do jednej z naszych klasy pomocnika, aby ułatwić interakcję z.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zrobimy to dalej.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Po pomyślnym zakończeniu transkrypcji usługi wyniki będą przechowywane w innym adresie URL, który możemy uzyskać z odpowiedzi na stan.

Pobierzemy zawartość tego adresu URL, zdamy wersję JSON i zapętlamy wyniki drukowania wyświetlanego tekstu w miarę przechodzenia.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (Dodaliśmy kilka komentarzy do tej wersji)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
