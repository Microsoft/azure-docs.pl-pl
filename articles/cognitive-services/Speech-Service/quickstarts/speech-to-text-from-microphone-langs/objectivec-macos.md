---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, usługa "zamierz-C-Speech"'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w celu macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75380596"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w celu macOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

W tym artykule dowiesz się, jak utworzyć aplikację macOS w zamierzeniu-C przy użyciu zestawu Azure Cognitive Services Speech SDK, aby transkrypcja mowę zarejestrowaną z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi mowy.
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz macOS 10,13 lub nowszym.

## <a name="get-the-speech-sdk-for-macos"></a>Pobieranie zestawu Speech SDK dla macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Zestaw SDK mowy Cognitive Services dla komputerów Mac jest dystrybuowany jako pakiet platformy. Mogą być używane w projektach Xcode jako [CocoaPod](https://cocoapods.org/) lub pobierane z https://aka.ms/csspeech/macosbinary i połączone ręcznie. W tym artykule jest stosowany CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, wybierając pozycję **plik** > **Nowy** > **projekt**. W oknie dialogowym Wybieranie szablonu wybierz szablon **aplikacji kakao** .

W poniższych oknach dialogowych wybierz następujące opcje.

1. W oknie dialogowym **Opcje projektu** :
    1. Wprowadź nazwę aplikacji szybkiego startu, na przykład *HelloWorld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych Użyj nazwy, takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dla deweloperów firmy Apple](https://developer.apple.com/).
    1. **Upewnij się, że** zaznaczono jako język dla projektu.
    1. Wyczyść pola wyboru, aby użyć scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji jest tworzony programowo.
    1. Wyczyść wszystkie pola wyboru dla testów i danych podstawowych.

    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Wybierz katalog projektu:
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. W tym kroku zostanie utworzony katalog HelloWorld w katalogu macierzystym, który zawiera wszystkie pliki projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci i mikrofonu. Wybierz nazwę aplikacji w pierwszym wierszu przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji. Następnie wybierz kartę **możliwości** .
    1. Włącz ustawienie **piaskownicy aplikacji** dla aplikacji.
    1. Zaznacz pola wyboru dla **połączeń wychodzących** i dostępu do **mikrofonu** .

    ![Ustawienia piaskownicy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Wybierz plik w przeglądzie, a następnie Dodaj klucz **Opis użycia funkcji prywatność-mikrofon** z wartością taką jak *mikrofon jest wymagany do rozpoznawania mowy*.

    ![Ustawienia w info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Zamknij projekt Xcode. Możesz użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej, czyli HelloWorld. Umieść plik tekstowy o nazwie *plik podfile* i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Przejdź do katalogu HelloWorld w terminalu i uruchom polecenie `pod install`. To polecenie generuje obszar `helloworld.xcworkspace` roboczy Xcode, który zawiera zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy jest używany w poniższych krokach.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz obszar roboczy `helloworld.xcworkspace` w Xcode.
1. Zastąp zawartość automatycznie generowanego `AppDelegate.m` pliku następującym kodem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zamień ciąg `YourServiceRegion` na [region](~/articles/cognitive-services/Speech-Service/regions.md) skojarzony z subskrypcją. Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Aby wyświetlić dane wyjściowe debugowania, wybierz opcję **Wyświetl** > **obszar** > debugowania**Aktywuj konsolę**.
1. Kompiluj i uruchamiaj przykładowy kod, wybierając **pozycję** > **Uruchom** jako z menu. Możesz również wybrać opcję **Odtwórz**.
1. Po wybraniu przycisku i rozpoczęciu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu. Po uruchomieniu aplikacji po raz pierwszy powinien zostać wyświetlony monit o przyznanie aplikacji dostępu do mikrofonu na komputerze.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
