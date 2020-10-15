---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097239"
---
Aby utworzyć projekt programu Visual Studio dla wieloplatformowego programowania aplikacji mobilnych platformy .NET przy użyciu platformy Xamarin, należy:
- Skonfiguruj opcje deweloperskie programu Visual Studio.
- Utwórz projekt i wybierz architekturę docelową. 
- Zainstaluj zestaw Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji programistycznych programu Visual Studio

Aby rozpocząć, upewnij się, że program Visual Studio jest poprawnie skonfigurowany do tworzenia aplikacji mobilnych na wiele platform za pomocą platformy .NET:

1. Otwórz program Visual Studio 2019.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie** .

   ![Zrzut ekranu pokazujący kartę obciążenia, modyfikowanie okna dialogowego i Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na karcie **obciążenia** w obszarze **Windows**Znajdź pozycję **Programowanie aplikacji mobilnych za pomocą obciążenia .NET** . Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, Zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 5.

1. Zaznacz pole wyboru **Programowanie aplikacji mobilnych za pomocą platformy .NET** i wybierz polecenie **Modyfikuj**. W oknie dialogowym **przed rozpoczęciem** wybierz pozycję **Kontynuuj** , aby zainstalować Programowanie aplikacji mobilnych za pomocą obciążenia .NET. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project"></a>Tworzenie projektu

1. Na pasku menu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt** , aby wyświetlić okno **Utwórz nowy projekt** .

   ![Zrzut ekranu pokazujący sposób tworzenia nowego projektu w programie Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Znajdź i wybierz pozycję **aplikacja mobilna (Xamarin. Forms)**.

1. Wybierz pozycję **dalej** , aby wyświetlić ekran **Konfiguruj nowy projekt** .

   ![Zrzut ekranu pokazujący sposób konfigurowania nowego projektu w programie Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. W polu **Nazwa projektu**wprowadź *HelloWorld*.

1. W obszarze **Lokalizacja**przejdź do i wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.

1. Wybierz pozycję **Utwórz** , aby przejść do **nowego okna projektu aplikacji mobilnej platformy Xamarin** .

   ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt programu Xamarin aplikacji mobilnych w programie Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Wybierz **pusty** szablon.

1. Na **platformie**zaznacz pola wyboru dla systemów **Android**, **iOS**i **Windows (platformy UWP)**.

1. Wybierz przycisk **OK**. Nastąpi powrót do środowiska IDE programu Visual Studio z nowym projektem utworzonym i widocznym w okienku **Eksplorator rozwiązań** .

   ![Projekt HelloWorld — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Teraz wybierz docelową architekturę platformy i projekt startowy. Na pasku narzędzi programu Visual Studio Znajdź pole listy rozwijanej **platformy rozwiązania** . (Jeśli go nie widzisz, wybierz pozycję **Widok**  >  **Paski narzędzi**  >  **Standard** , aby wyświetlić pasek narzędzi zawierający **platformy rozwiązań**. Jeśli korzystasz z 64-bitowego systemu Windows, wybierz pozycję **x64** w polu listy rozwijanej. Możesz wybrać opcję **x86** , jeśli chcesz, aby system Windows 64-bitowy mógł również uruchamiać aplikacje 32-bitowe. W polu listy rozwijanej **projekty startowe** ustaw opcję **HelloWorld. PLATFORMY UWP (uniwersalny system Windows)**.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zainstaluj [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget)i odwołuje się do zestawu Speech SDK w projekcie.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie. Wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania** , aby przejść do okna **rozwiązania NuGet** .

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie pakietami dla rozwiązania podczas instalowania zestawu Speech SDK.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W obszarze **Źródło pakietu**wybierz pozycję NuGet.org.

1. W polu **wyszukiwania** wprowadź *Microsoft. CognitiveServices. Speech*. Następnie wybierz ten pakiet po pojawieniu się w wynikach wyszukiwania.

   ![Zrzut ekranu, który podświetla pakiet Microsoft. CognitiveServices. Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Biblioteka systemu iOS wewnątrz narzędzia `Microsoft.CognitiveServices.Speech` NuGet nie ma włączonej kodu bitowego. Jeśli potrzebujesz biblioteki kodu bitowego dla aplikacji, użyj narzędzia `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet dla projektu dla systemu iOS.

1. W okienku stan pakietu obok wyników wyszukiwania wybierz pozycję Wszystkie projekty: **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS**i **HelloWorld. PLATFORMY UWP**.

1. Wybierz pozycję **Zainstaluj**.

1. W oknie dialogowym **Podgląd zmian** wybierz **przycisk OK**.

1. W oknie dialogowym **Akceptacja licencji** Wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Zainstaluj odwołanie pakietu SDK mowy do wszystkich projektów. Po pomyślnym zakończeniu instalacji może zostać wyświetlone następujące ostrzeżenie dla HelloWorld. iOS. Jest to znany problem i nie powinien wpływać na funkcjonalność aplikacji.

   > Nie można rozpoznać odwołania "C:\Users\Default \. nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.Core.a". Jeśli to odwołanie jest wymagane przez kod, mogą wystąpić błędy kompilacji.
