---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 7386cfaaf410614c6eeee19669f8f769ad691c1f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215171"
---
Aby utworzyć projekt programu Visual Studio na potrzeby tworzenia platforma uniwersalna systemu Windows (platformy UWP), musisz skonfigurować opcje programistyczne programu Visual Studio, utworzyć projekt, wybrać architekturę docelową, skonfigurować funkcję przechwytywania audio i zainstalować zestaw Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji programistycznych programu Visual Studio

Aby rozpocząć, upewnij się, że program Visual Studio jest poprawnie skonfigurowany do programowania platformy UWP:

1. Otwórz program Visual Studio 2019, aby wyświetlić okno **uruchamiania** .

   ![Zrzut ekranu pokazujący okno "Start" z wyróżnioną akcją "Kontynuuj bez kodu".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Wybierz pozycję **Kontynuuj bez kodu** , aby przejść do środowiska IDE programu Visual Studio.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie** .

   ![Zrzut ekranu pokazujący kartę "obciążenia" okna dialogowego "Modyfikowanie" z wyróżnioną pozycją "platforma uniwersalna systemu Windows Development".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na karcie **obciążenia** w obszarze **systemu Windows** Znajdź **platforma uniwersalna systemu Windows obciążenie programowaniem** . Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, Zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 6.

1. Zaznacz pole wyboru **programowanie platforma uniwersalna systemu Windows** , wybierz pozycję **Modyfikuj**, a następnie w oknie dialogowym **przed** rozpoczęciem wybierz pozycję **Kontynuuj** , aby zainstalować obciążenie programowanie platformy UWP. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Utwórz projekt i wybierz architekturę docelową

Następnie utwórz projekt:

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **plik**  >  **Nowy**  >  **projekt** , aby wyświetlić okno **Utwórz nowy projekt** .

   ![Zrzut ekranu przedstawiający okno "Tworzenie nowego projektu" z wybraną opcją "pusta aplikacja (uniwersalna system Windows)" i wyróżniony przycisk "dalej".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Znajdź i wybierz pozycję **pusta aplikacja (uniwersalna platforma Windows)**. Upewnij się, że wybrano wersję C# tego typu projektu (w przeciwieństwie do Visual Basic).

1. Wybierz pozycję **dalej** , aby wyświetlić ekran **Konfiguruj nowy projekt** .

   ![Zrzut ekranu pokazujący ekran "Konfigurowanie nowego projektu" z wyróżnionymi polami "Nazwa projektu" i "lokalizacja" oraz przycisk "Utwórz".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. W polu **Nazwa projektu** wprowadź `helloworld` .

1. W obszarze **Lokalizacja** przejdź do, a następnie wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.

1. Wybierz pozycję **Utwórz** , aby przejść do okna **Nowy projekt platforma uniwersalna systemu Windows** .

   ![Zrzut ekranu przedstawiający okno dialogowe "nowy projekt platforma uniwersalna systemu Windows".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. W polu **minimalna wersja** (drugie pole listy rozwijanej) wybierz pozycję **Aktualizacja systemu Windows 10 dla twórców (10,0; Kompilacja 16299)**, która jest minimalnym wymaganiem dla zestawu Speech SDK.

1. W **wersji docelowej** (pierwsze pole listy rozwijanej) wybierz wartość identyczną lub późniejszą niż wartość w **wersji minimalnej**.

1. Wybierz przycisk **OK**. Nastąpi powrót do środowiska IDE programu Visual Studio z nowym projektem utworzonym i widocznym w okienku **Eksplorator rozwiązań** .

   ![projekt HelloWorld — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Teraz wybierz architekturę platformy docelowej. Na pasku narzędzi programu Visual Studio Znajdź pole listy rozwijanej **platformy rozwiązania** . (Jeśli go nie widzisz, wybierz pozycję **Wyświetl**  >  **Paski narzędzi**  >  **Standard** , aby wyświetlić pasek narzędzi zawierający **platformy rozwiązań**.) Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu listy rozwijanej. 64 — bit systemu Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86** , jeśli wolisz.

> [!NOTE]
> Zestaw Speech SDK obsługuje wszystkie procesory zgodne z technologią Intel, ale **tylko x64** wersji procesorów ARM.

### <a name="set-up-audio-capture"></a>Konfigurowanie przechwytywania audio

Zezwól projektowi na przechwytywanie danych wejściowych audio:

1. W **Eksplorator rozwiązań** kliknij dwukrotnie plik **Package. appxmanifest** , aby otworzyć manifest aplikacji pakietu.

1. Wybierz kartę **Capabilities** (Możliwości).

   ![Karta możliwości, manifest aplikacji pakietu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Wybierz pole z możliwością **mikrofonu** .

1. Na pasku menu wybierz kolejno opcje **plik**  >  **Zapisz pakiet. appxmanifest** , aby zapisać zmiany.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Na koniec zainstaluj [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget)i odwołuje się do zestawu Speech SDK w projekcie:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania** , aby przejść do okna **rozwiązania NuGet** .

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu przedstawiający okno dialogowe "Zarządzanie pakietami dla rozwiązania" z wyróżnioną kartą "Przeglądaj", "wyszukiwanie" i "Źródło pakietów".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W obszarze **Źródło pakietu** wybierz pozycję **NuGet.org**.

1. W polu **wyszukiwania** wpisz `Microsoft.CognitiveServices.Speech` , a następnie wybierz ten pakiet po pojawieniu się w wynikach wyszukiwania.

   ![Zrzut ekranu pokazujący, że wybrano opcję "Microsoft. CognitiveServices. Speech" z wyróżnionym przyciskiem projekt i "Zainstaluj".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. W okienku stan pakietu obok wyników wyszukiwania wybierz swój projekt **HelloWorld** .

1. Wybierz pozycję **Zainstaluj**.

1. W oknie dialogowym **Podgląd zmian** wybierz **przycisk OK**.

1. W oknie dialogowym **Akceptacja licencji** Wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Rozpocznie się instalacja pakietu i po zakończeniu instalacji w okienku **dane wyjściowe** zostanie wyświetlony komunikat podobny do następującego: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
