---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 804cc2213379364002a093509f2393e375c3971b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400960"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Otwórz swój projekt w aplikacji Eclipse

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w aplikacji Eclipse.

1. Uruchamianie programu Eclipse
2. Załaduj `Main.java`projekt i otwórz .

## <a name="add-a-reference-to-gson"></a>Dodawanie odwołania do Gson
Będziemy używać zewnętrznego serializatora JSON / deserializer w tym przewodniku Szybki start. Dla Javy wybraliśmy [Gson](https://github.com/google/gson).

Otwórz plik pom.xml i dodaj następujące odwołanie.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Opakowania JSON

Jako rest interfejsu API przyjmować żądania w formacie JSON, a także zwraca wyniki w JSON możemy wchodzić z nimi przy użyciu tylko ciągów, ale to nie jest zalecane.
Aby ułatwić zarządzanie żądaniami i odpowiedziami, zadeklarujemy kilka klas do użycia do serializacji / deserializacji JSON.

Śmiało i umieścić swoje `Main`deklaracje przed .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Tworzenie i konfigurowanie klienta http
Pierwszą rzeczą, której potrzebujemy, jest klient http, który ma poprawny podstawowy adres URL i zestaw uwierzytelniania.
Wstaw ten `Main` kod do[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generowanie żądania transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten `Main` kod do[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz publikujemy żądanie w usłudze mowy i sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi po prostu wskaże, czy usługa otrzymała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, która jest lokalizacją, w której będzie przechowywać stan transkrypcji.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza transkrypcji asynchronicznie, musimy sondować jego status tak często. Sprawdzamy co 5 sekund.

Możemy sprawdzić stan, pobierając zawartość pod adresem URL, który otrzymaliśmy po opublikowaniu żądania. Kiedy przywracamy zawartość, możemy deserialize go do jednej z naszych klasy pomocnika, aby ułatwić interakcję z.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zrobimy to dalej.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Po pomyślnym zakończeniu transkrypcji usługi wyniki będą przechowywane w innym adresie URL, który możemy uzyskać z odpowiedzi na stan.

Pobierzemy zawartość tego adresu URL, zdamy wersję JSON i zapętlamy wyniki drukowania wyświetlanego tekstu w miarę przechodzenia.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (Dodaliśmy kilka komentarzy do tej wersji) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
