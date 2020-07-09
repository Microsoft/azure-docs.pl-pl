---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a24c0a3129a851eb94c43158424f786b1df5a532
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414366"
---
Aby utworzyć projekt programu Visual Studio na potrzeby tworzenia platforma uniwersalna systemu Windows (platformy UWP), musisz skonfigurować opcje programistyczne programu Visual Studio, utworzyć projekt, wybrać architekturę docelową, skonfigurować funkcję przechwytywania audio i zainstalować zestaw Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji programistycznych programu Visual Studio

Aby rozpocząć, upewnij się, że program Visual Studio jest poprawnie skonfigurowany do programowania platformy UWP:

1. Otwórz program Visual Studio 2019, aby wyświetlić okno **uruchamiania** .

   ![Okno uruchamiania — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Wybierz pozycję **Kontynuuj bez kodu** , aby przejść do środowiska IDE programu Visual Studio.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie** .

   ![Karta obciążenia, modyfikowanie okna dialogowego, Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na karcie **obciążenia** w obszarze **systemu Windows**Znajdź **platforma uniwersalna systemu Windows obciążenie programowaniem** . Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, Zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 6.

1. Zaznacz pole wyboru **programowanie platforma uniwersalna systemu Windows** , wybierz pozycję **Modyfikuj**, a następnie w oknie dialogowym **przed** rozpoczęciem wybierz pozycję **Kontynuuj** , aby zainstalować obciążenie programowanie platformy UWP. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Utwórz projekt i wybierz architekturę docelową

Następnie utwórz projekt:

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **plik**  >  **Nowy**  >  **projekt** , aby wyświetlić okno **Utwórz nowy projekt** .

   ![Tworzenie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Znajdź i wybierz pozycję **pusta aplikacja (uniwersalna platforma Windows)**. Upewnij się, że wybrano wersję C# tego typu projektu (w przeciwieństwie do Visual Basic).

1. Wybierz pozycję **dalej** , aby wyświetlić ekran **Konfiguruj nowy projekt** .

   ![Konfigurowanie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. W polu **Nazwa projektu**wprowadź `helloworld` .

1. W obszarze **Lokalizacja**przejdź do, a następnie wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.

1. Wybierz pozycję **Utwórz** , aby przejść do okna **Nowy projekt platforma uniwersalna systemu Windows** .

   ![Okno dialogowe Nowy projekt platforma uniwersalna systemu Windows — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. W polu **minimalna wersja** (drugie pole listy rozwijanej) wybierz pozycję **Aktualizacja systemu Windows 10 dla twórców (10,0; Kompilacja 16299)**, która jest minimalnym wymaganiem dla zestawu Speech SDK.

1. W **wersji docelowej** (pierwsze pole listy rozwijanej) wybierz wartość identyczną lub późniejszą niż wartość w **wersji minimalnej**.

1. Wybierz przycisk **OK**. Nastąpi powrót do środowiska IDE programu Visual Studio z nowym projektem utworzonym i widocznym w okienku **Eksplorator rozwiązań** .

   ![projekt HelloWorld — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Teraz wybierz architekturę platformy docelowej. Na pasku narzędzi programu Visual Studio Znajdź pole listy rozwijanej **platformy rozwiązania** . (Jeśli go nie widzisz, wybierz pozycję **Wyświetl**  >  **Paski narzędzi**  >  **Standard** , aby wyświetlić pasek narzędzi zawierający **platformy rozwiązań**.) Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu listy rozwijanej. 64 — bit systemu Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86** , jeśli wolisz.

> [!NOTE]
> Zestaw Speech SDK obsługuje wyłącznie procesory zgodne z technologią Intel. Procesory ARM nie są obecnie obsługiwane.

### <a name="set-up-audio-capture"></a>Konfigurowanie przechwytywania audio

Zezwól projektowi na przechwytywanie danych wejściowych audio:

1. W **Eksplorator rozwiązań**kliknij dwukrotnie plik **Package. appxmanifest** , aby otworzyć manifest aplikacji pakietu.

1. Wybierz kartę **Capabilities** (Możliwości).

   ![Karta możliwości, manifest aplikacji pakietu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Wybierz pole z możliwością **mikrofonu** .

1. Na pasku menu wybierz kolejno opcje **plik**  >  **Zapisz pakiet. appxmanifest** , aby zapisać zmiany.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Na koniec zainstaluj [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget)i odwołuje się do zestawu Speech SDK w projekcie:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania** , aby przejść do okna **rozwiązania NuGet** .

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W obszarze **Źródło pakietu**wybierz pozycję **NuGet.org**.

1. W polu **wyszukiwania** wpisz `Microsoft.CognitiveServices.Speech` , a następnie wybierz ten pakiet po pojawieniu się w wynikach wyszukiwania.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. W okienku stan pakietu obok wyników wyszukiwania wybierz swój projekt **HelloWorld** .

1. Wybierz pozycję **Zainstaluj**.

1. W oknie dialogowym **Podgląd zmian** wybierz **przycisk OK**.

1. W oknie dialogowym **Akceptacja licencji** Wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Rozpocznie się instalacja pakietu i po zakończeniu instalacji w okienku **dane wyjściowe** zostanie wyświetlony komunikat podobny do następującego: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.12.1' to helloworld` .
