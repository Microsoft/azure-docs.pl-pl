---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, usługa SWIFT-mowę'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w usłudze SWIFT w systemie iOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: 95c69aad24ff10b49bdc09411553e6ed43bfdccd
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391438"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w usłudze SWIFT w systemie iOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w usłudze SWIFT przy użyciu zestawu Azure Cognitive Services Speech SDK, aby transkrypcja mowę nagraną z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi mowy.
* Maszyna macOS z zainstalowanym [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.6.0.

Zestaw SDK mowy Cognitive Services dla systemu iOS jest dystrybuowany jako pakiet platformy. Mogą być używane w projektach Xcode jako [CocoaPod](https://cocoapods.org/) lub pobierane z https://aka.ms/csspeech/iosbinary i połączone ręcznie. W tym artykule jest stosowany CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, wybierając pozycję **plik**  >  **Nowy**  >  **projekt**.
W oknie dialogowym Wybieranie szablonu wybierz szablon aplikacji z **pojedynczym widokiem systemu iOS** .

W poniższych oknach dialogowych wybierz następujące opcje.

1. W oknie dialogowym **Opcje projektu** :
    1. Wprowadź nazwę aplikacji szybkiego startu, na przykład *HelloWorld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych Użyj nazwy, takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że wybrano **kod SWIFT** jako język dla projektu.
    1. Wyłącz pola wyboru, aby używać scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji jest tworzony programowo.
    1. Wyczyść wszystkie pola wyboru dla testów i danych podstawowych.
1. Wybierz katalog projektu:
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. W tym kroku zostanie utworzony katalog HelloWorld w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Wybierz plik w przeglądzie, a następnie Dodaj klucz **Opis użycia funkcji prywatność-mikrofon** z wartością taką jak *mikrofon jest wymagany do rozpoznawania mowy*.

    ![Ustawienia w info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Zamknij projekt Xcode. Możesz użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówka z nazwą `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` w katalogu HelloWorld w projekcie HelloWorld. Wklej do niego następujący kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Dodaj ścieżkę względną `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do nagłówka mostkowania do ustawień projektu SWIFT dla elementu docelowego HelloWorld w polu **nagłówka mostkowania "cel-C"** .

   ![Właściwości nagłówka](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Zastąp zawartość automatycznie generowanego `AppDelegate.swift` pliku następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość automatycznie generowanego `ViewController.swift` pliku następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. W programie `ViewController.swift` Zamień ciąg na `YourSubscriptionKey` klucz subskrypcji.
1. Zamień ciąg na `YourServiceRegion` [region](~/articles/cognitive-services/Speech-Service/regions.md) skojarzony z subskrypcją. Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej, czyli HelloWorld. Umieść plik tekstowy o nazwie *plik podfile* i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Przejdź do katalogu HelloWorld w terminalu i uruchom polecenie `pod install` . To polecenie generuje `helloworld.xcworkspace` obszar roboczy Xcode, który zawiera zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy jest używany w poniższych krokach.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz obszar roboczy `helloworld.xcworkspace` w Xcode.
1. Aby wyświetlić dane wyjściowe debugowania, wybierz opcję **Wyświetl**  >  **obszar debugowania**  >  **Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w **Product**  >  menu**miejsce docelowe** produktu.
1. Kompiluj i uruchamiaj przykładowy kod w symulatorze systemu iOS, **wybierając pozycję**  >  **Uruchom** jako z menu. Możesz również wybrać przycisk **Odtwórz** .
1. Po wybraniu przycisku **Rozpoznaj** w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst wymawiany w dolnej części ekranu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
