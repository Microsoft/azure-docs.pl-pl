---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 1b0f0fdbed20b8ab9a48b3156faf176e8ecfc44b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425424"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)


## <a name="open-your-project-in-eclipse"></a>Otwórz projekt w programie zaćmienie

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w przezaćmieniu.

1. Uruchamianie programu Eclipse
2. Załaduj projekt i otwórz go `Main.java` .

## <a name="add-a-reference-to-gson"></a>Dodaj odwołanie do Gson
W tym przewodniku szybki start będziemy używać zewnętrznego serializatora JSON/deserializacji. W przypadku języka Java została wybrana [Gson](https://github.com/google/gson).

Otwórz pom.xml i Dodaj następujące odwołanie.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Otoki JSON

Jako że żądania API REST w formacie JSON i zwracają wyniki w kodzie JSON, możemy z nich korzystać tylko przy użyciu ciągów, ale nie jest to zalecane.
Aby ułatwić zarządzanie żądaniami i odpowiedziami, deklarujemy kilka klas do użycia na potrzeby serializowania/deserializacji kodu JSON.

Przejdź dalej i umieść ich deklaracje przed `Main` .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Tworzenie i Konfigurowanie klienta http
Najpierw musimy być klientem http z prawidłowym podstawowym adresem URL i zestawem uwierzytelniania.
Wstaw ten kod w `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generuj żądanie transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten kod do `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz wyślemy żądanie do usługi mowy i Sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi będzie po prostu wskazywać, czy usługa odebrała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, które są lokalizacją przechowywania stanu transkrypcji.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza proces transkrypcji asynchronicznie, musimy wykonać sondowanie pod kątem jego stanu co do tego często. Sprawdzimy co 5 sekund.

Stan można sprawdzić, pobierając zawartość pod adresem URL, który otrzymał po wysłaniu żądania. Po otrzymaniu zawartości, deserializacjimy ją na jedną z naszych klas pomocnika, aby ułatwić korzystanie z programu.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zajmiemy się tym dalej.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Gdy usługa pomyślnie ukończy transkrypcję, wyniki będą przechowywane w innym adresie URL, który można uzyskać z odpowiedzi na stan.

Pobierzemy zawartość tego adresu URL, deserializować kod JSON i pętlę przez wyniki drukowania wyświetlanego tekstu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji) [! code-Java [] (~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]