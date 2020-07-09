---
title: 'Szybki Start: wykluczanie mowy, obiektyw-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w celu macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: 2c859965c951ad271b1b9e272ce60de64aa3d3d5
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391370"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Szybki Start: wykluczanie mowy w celu języka C w systemie macOS przy użyciu zestawu Speech SDK

W tym artykule dowiesz się, jak utworzyć aplikację macOS w zamierzeniu-C przy użyciu zestawu Speech SDK Cognitive Services, aby przeprowadzić funkcję syntezy mowy z tekstu i odtworzyć ją z domyślnym wyjściem audio.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi mowy
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz macOS 10,13 lub nowszym

## <a name="get-the-speech-sdk-for-macos"></a>Pobieranie zestawu Speech SDK dla macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.7.0.

Zestaw SDK mowy Cognitive Services dla komputerów Mac jest dystrybuowany jako pakiet platformy.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany z https://aka.ms/csspeech/macosbinary i połączony ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, klikając pozycję **plik**  >  **Nowy**  >  **projekt**.
W oknie dialogowym Wybieranie szablonu wybierz szablon "aplikacja kakaowa".

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że jako język projektu wybrano język Objective-C.
    1. Wyłącz pola wyboru, aby użyć scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Wybieranie katalog projektu
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. Powoduje to utworzenie katalogu `helloworld` w katalogu macierzystym, który zawiera wszystkie pliki projektu programu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci. Kliknij nazwę aplikacji w pierwszym wierszu przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji, a następnie wybierz kartę "możliwości".
    1. Włącz ustawienie "piaskownica aplikacji" dla aplikacji.
    1. Włącz pola wyboru dla dostępu wychodzącego "połączenia".
    ![Ustawienia piaskownicy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej ( `helloworld` ). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install` . Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz `helloworld.xcworkspace` obszar roboczy w Xcode.
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.m` następującą zawartością:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Wyświetlaj dane wyjściowe debugowania (**Wyświetl**  >  **Debug Area**  >  **konsolę aktywacji**obszaru debugowania).
1. Kompiluj i uruchamiaj przykładowy kod **, wybierając pozycję**  ->  **Uruchom** z menu lub klikając przycisk **Odtwórz** .
1. Po wprowadzeniu tekstu i kliknięciu tego przycisku w aplikacji należy usłyszeć dźwięk, który jest odtwarzany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)

