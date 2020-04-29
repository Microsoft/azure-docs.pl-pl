---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, usługa SWIFT-mowę'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w usłudze SWIFT w systemie macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: cbasoglu
ms.openlocfilehash: 7c5611a142087cff06eefb0277b12ff786074e1a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446829"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w usłudze SWIFT w systemie macOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md).

W tym artykule dowiesz się, jak utworzyć aplikację macOS w usłudze SWIFT przy użyciu zestawu SDK mowy Cognitive Services, aby transkrypcja mowę zarejestrowano z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi mowy.
* Maszyna macOS z zainstalowanym [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-macos"></a>Pobieranie zestawu Speech SDK dla macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.6.0.

Zestaw Cognitive Services Speech SDK for macOS jest dystrybuowany jako pakiet platformy.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany z https://aka.ms/csspeech/macosbinary i połączony ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, klikając pozycję **plik** > **Nowy** > **projekt**.
W oknie dialogowym Wybieranie szablonu wybierz szablon "aplikacja kakaowa".

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że wybrano kod SWIFT jako język dla projektu.
    1. Wyłącz pola wyboru, aby użyć scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
1. Wybieranie katalog projektu
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. Spowoduje to utworzenie `helloworld` katalogu w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci i mikrofonu. Kliknij nazwę aplikacji w pierwszym wierszu przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji, a następnie wybierz kartę "możliwości".
    1. Włącz ustawienie "piaskownica aplikacji" dla aplikacji.
    1. Włącz pola wyboru dla dostępu "połączenia wychodzące" i "mikrofon".
    ![Ustawienia piaskownicy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Kliknij plik w przeglądzie i Dodaj klucz "Opis użycia mikrofonu prywatności" z wartością, taką jak "mikrofon jest wymagany w przypadku rozpoznawania mowy".
    ![Ustawienia w info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówkowy o nazwie `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` w `helloworld` katalogu w projekcie HelloWorld i wklej do niego następujący kod:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Dodaj ścieżkę `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` względną do nagłówka mostkowania do ustawień projektu SWIFT dla elementu docelowego HelloWorld w obszarze właściwości ![nagłówka *mostkowania "cel-C"* .](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.swift` następującą zawartością:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. W `AppDelegate.swift`programie Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji.
1. Zastąp ciąg `YourServiceRegion`regionem skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz `helloworld.xcworkspace` obszar roboczy w Xcode.
1. Wyświetlaj dane wyjściowe debugowania (**Wyświetl** > **Debug Area** > **konsolę aktywacji**obszaru debugowania).
1. Kompiluj i uruchamiaj przykładowy kod **, wybierając** > pozycję**Uruchom** z menu lub klikając przycisk **Odtwórz** .
1. Po kliknięciu przycisku "Rozpoznaj" w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst wymawiany w dolnej części okna aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)

