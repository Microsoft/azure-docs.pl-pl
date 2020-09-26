---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C# (Xamarin) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie wieloplatformowej aplikacji Xamarin dla programu platforma uniwersalna systemu Windows (platformy UWP), Android i iOS przy użyciu zestawu SDK Cognitive Services Speech. Transkrypcja mowę na tekst w czasie rzeczywistym z mikrofonu urządzenia lub symulatora. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: f89bdbfd144fb327c1daae020a1e371c00045859
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376281"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

Jeśli to już zrobione, świetnie. Kontynuujmy.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Dodaj przykładowy kod dla wspólnego projektu HelloWorld

Typowy projekt HelloWorld zawiera implementacje niezależne od platformy dla aplikacji dla wielu platform. Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj kod w języku C# związany z implementacją.

1. W **Eksplorator rozwiązań**, pod wspólnym projektem HelloWorld, Otwórz `MainPage.xaml` .

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do tagu **Grid** między `<StackLayout>` i `</StackLayout>` :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs` . Jest on pogrupowany pod `MainPage.xaml` .

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. W programie obsługi pliku źródłowego `OnRecognitionButtonClicked` Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji.


1. W programie `OnRecognitionButtonClicked` obsługi Znajdź ciąg `YourServiceregion` i zastąp go **identyfikatorem regionu** z [regionu](https://aka.ms/speech/sdkregion) skojarzonego z subskrypcją. 

1. Następnie należy utworzyć [usługę platformy Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), która jest używana do wykonywania zapytań dotyczących uprawnień mikrofonu z różnych projektów platformy, takich jak platformy UWP, Android i iOS. W tym celu należy dodać nowy folder o nazwie *Services* w projekcie HelloWorld i utworzyć w nim nowy plik źródłowy języka C#. Możesz kliknąć prawym przyciskiem myszy folder *usługi* , a następnie wybrać pozycję **Dodaj**  >  **Nowy**  >  **plik kodu**elementu. Zmień nazwę pliku `IMicrophoneService.cs` i umieść cały kod z następującego fragmentu kodu w tym pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Dodawanie przykładowego kodu dla `helloworld.Android` projektu

Teraz Dodaj kod C#, który definiuje część aplikacji dla systemu Android.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. Projekt systemu Android, Otwórz `MainActivity.cs` .

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu Android, `MicrophoneService` tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt systemu Android. Następnie utwórz nowy plik źródłowy C# w tym obszarze. Zmień nazwę pliku `MicrophoneService.cs` . Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Następnie otwórz `AndroidManifest.xml` w folderze *Właściwości* . Dodaj następujące zastosowania — ustawienie uprawnienia dla mikrofonu między `<manifest>` i `</manifest>` :

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Dodawanie przykładowego kodu dla `helloworld.iOS` projektu

Teraz Dodaj kod w języku C#, który definiuje część aplikacji określoną dla systemu iOS. Utwórz także konfiguracje specyficzne dla urządzenia firmy Apple w projekcie HelloWorld. iOS.

1. W **Eksplorator rozwiązań**, w ramach projektu HelloWorld. iOS, Otwórz `AppDelegate.cs` .

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu iOS, `MicrophoneService` tworząc nowe *usługi* folderów w projekcie HelloWorld.IO. Następnie utwórz nowy plik źródłowy C# w tym obszarze. Zmień nazwę pliku `MicrophoneService.cs` . Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otwórz w `Info.plist` projekcie HelloWorld. iOS w edytorze tekstu. Dodaj następującą parę klucz-wartość w sekcji Dict:

   <key>NSMicrophoneUsageDescription</key> 
    <string>Ta przykładowa aplikacja wymaga dostępu do mikrofonu</string>

   > [!NOTE]
   > Jeśli tworzysz urządzenie iPhone, upewnij się, że jest `Bundle Identifier` zgodny z identyfikatorem aplikacji profilu aprowizacji urządzenia. W przeciwnym razie kompilacja zakończy się niepowodzeniem. Za pomocą iPhoneSimulator można pozostawić to.

1. Jeśli tworzysz na komputerze z systemem Windows, nawiąż połączenie z urządzeniem Mac, aby skompilować za pośrednictwem pary **narzędzi**  >  **systemu iOS**  >  **z systemem Mac**. Aby włączyć połączenie z urządzeniem Mac, postępuj zgodnie z instrukcjami wyświetlanymi przez program Visual Studio.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodawanie przykładowego kodu dla `helloworld.UWP` projektu

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy kod dla HelloWorld. Projekt platformy UWP

Teraz Dodaj kod C#, który definiuje część aplikacji platformy UWP.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. PLATFORMY UWP projekt, Otwórz `MainPage.xaml.cs` .

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Następnie Dodaj implementację specyficzną dla platformy UWP, `MicrophoneService` tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt platformy UWP. Następnie utwórz nowy plik źródłowy C# w tym obszarze. Zmień nazwę pliku `MicrophoneService.cs` . Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Następnie kliknij dwukrotnie `Package.appxmanifest` plik w obszarze HelloWorld. PLATFORMY UWP projekt w programie Visual Studio. W obszarze **możliwości**upewnij się, że wybrano **mikrofon** i Zapisz plik.

1. Kliknij dwukrotnie `Package.appxmanifest` plik w obszarze projektu w programie `helloworld.UWP` Visual Studio i w obszarze **możliwości**  >  **mikrofon** jest zaznaczone i Zapisz plik.
   > Uwaga: w przypadku wyświetlenia ostrzeżenia: plik certyfikatu nie istnieje: HelloWorld. UWP_TemporaryKey. pfx, zapoznaj [się z próbką zamiany mowy na tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) , aby uzyskać więcej informacji.

1. Na pasku menu wybierz pozycję **plik**  >  **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-uwp-application"></a>Kompilowanie i uruchamianie aplikacji platformy UWP

1. Ustaw HelloWorld. PLATFORMY UWP jako projekt startowy. Kliknij prawym przyciskiem myszy plik HelloWorld. PLATFORMY UWP projekt i wybierz opcję **Kompiluj** , aby skompilować aplikację.

1. Wybierz kolejno opcje **Debuguj**  >  **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja rozpoznawania mowy platformy UWP w języku C# — Szybki Start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Wybierz pozycję **Włącz mikrofon**. Gdy zostanie wyświetlone żądanie uprawnienia dostępu, wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Wybierz kolejno pozycje **Uruchom rozpoznawanie mowy**i mów do mikrofonu w języku angielskim lub zdania. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

   ![Interfejs użytkownika rozpoznawania mowy](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Kompiluj i Uruchamiaj aplikacje dla systemów Android i iOS

Kompilowanie i uruchamianie aplikacji dla systemów Android i iOS na urządzeniu lub symulatorze odbywa się w podobny sposób do platformy UWP. Upewnij się, że wszystkie zestawy SDK są poprawnie zainstalowane, zgodnie z wymaganiami w sekcji "wymagania wstępne" tego artykułu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
