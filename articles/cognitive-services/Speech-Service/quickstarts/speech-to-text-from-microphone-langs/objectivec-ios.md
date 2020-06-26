---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, usługa "zamierz-C-Speech"'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w celu języka C w systemie iOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: c355071c4bbaff5ddb22b2db5fc4a3a0fdb561d4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391557"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w celu języka C w systemie iOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w celu założenia języka C przy użyciu zestawu Azure Cognitive Services Speech SDK, aby transkrypcja mowę na tekst z mikrofonu lub z pliku z zarejestrowanym dźwiękiem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi mowy.
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszą.
* Element docelowy jest ustawiony na system iOS w wersji 9,3 lub nowszej.

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Zestaw Cognitive Services Speech SDK dla systemu iOS jest obecnie dystrybuowany jako platforma kakao.
Można go pobrać z [tej witryny sieci Web](https://aka.ms/csspeech/iosbinary). Pobierz plik do katalogu macierzystego.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, wybierając pozycję **plik**  >  **Nowy**  >  **projekt**.
W oknie dialogowym Wybieranie szablonu wybierz szablon aplikacji z **pojedynczym widokiem systemu iOS** .

W poniższych oknach dialogowych wybierz następujące opcje.

1. W oknie dialogowym **Opcje projektu** :
    1. Wprowadź nazwę aplikacji szybkiego startu, na przykład *HelloWorld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych Użyj nazwy, takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dla deweloperów firmy Apple](https://developer.apple.com/).
    1. **Upewnij się, że** zaznaczono jako język dla projektu.
    1. Wyczyść wszystkie pola wyboru dla testów i danych podstawowych.

    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Wybierz katalog projektu:
   1. Wybierz katalog macierzysty, aby umieścić w nim projekt. W tym kroku zostanie utworzony katalog HelloWorld w katalogu macierzystym, który zawiera wszystkie pliki projektu Xcode.
   1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
   1. Dostosuj ścieżki do zestawu SDK na ekranie Ustawienia projektu.
      1. Na karcie **Ogólne** w nagłówku **osadzone pliki binarne** Dodaj bibliotekę zestawu SDK jako platformę, wybierając pozycję **Dodaj osadzone pliki binarne**  >  **Dodaj inne**. Przejdź do katalogu macierzystego i wybierz plik `MicrosoftCognitiveServicesSpeech.framework` . Ta akcja powoduje automatyczne dodanie biblioteki SDK do **połączonej struktury i bibliotek** nagłówka.
         ![Dodano strukturę](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Przejdź do karty **Ustawienia kompilacji** i wybierz ustawienie **wszystkie** .
      1. Dodaj katalog $ (SRCROOT)/... na **ścieżki wyszukiwania struktury** pod nagłówkiem **ścieżki wyszukiwania** .

      ![Ustawienie ścieżek wyszukiwania struktury](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurowanie Interfejsu użytkownika

Przykładowa aplikacja ma bardzo prosty interfejs użytkownika. Ma dwa przyciski do uruchamiania rozpoznawania mowy z pliku lub danych wejściowych mikrofonu oraz etykieta tekstowa, aby wyświetlić wynik. Interfejs użytkownika jest skonfigurowany w części `Main.storyboard` projektu. Otwórz widok XML scenorysu, klikając prawym przyciskiem myszy `Main.storyboard` wpis drzewa projektu i wybierając pozycję **Otwórz jako**  >  **kod źródłowy**.

Zastąp wygenerowany automatycznie kod XML następującym kodem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Pobierz [przykładowy plik WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) , klikając prawym przyciskiem myszy łącze i wybierając pozycję **Zapisz element docelowy jako**.
   Dodaj plik wav do projektu jako zasób, przeciągając go z okna programu Finder do poziomu głównego widoku projektu.
   Wybierz pozycję **Zakończ** w następującym oknie dialogowym bez zmiany ustawień.
1. Zastąp zawartość automatycznie generowanego `ViewController.m` pliku następującym kodem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zamień ciąg na `YourServiceRegion` [region](~/articles/cognitive-services/Speech-Service/regions.md) skojarzony z subskrypcją. Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej.
1. Dodaj żądanie, aby uzyskać dostęp do mikrofonu. Kliknij prawym przyciskiem myszy `Info.plist` wpis drzewa projektu i wybierz polecenie **Otwórz jako**  >  **kod źródłowy**. Dodaj następujące wiersze do `<dict>` sekcji, a następnie Zapisz plik.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Aby wyświetlić dane wyjściowe debugowania, wybierz opcję **Wyświetl**  >  **obszar debugowania**  >  **Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w **Product**  >  menu**miejsce docelowe** produktu.
1. Kompiluj i uruchamiaj przykładowy kod w symulatorze systemu iOS, **wybierając pozycję**  >  **Uruchom** jako z menu. Możesz również wybrać przycisk **Odtwórz** .
1. Po wybraniu przycisku **Rozpoznaj (plik)** w aplikacji powinna zostać wyświetlona zawartość pliku audio "co to jest pogoda"? w dolnej części ekranu.

   ![Symulowana aplikacja systemu iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Po wybraniu przycisku **rozpoznawania (mikrofon)** w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
