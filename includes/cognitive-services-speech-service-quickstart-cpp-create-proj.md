---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 2960a4f55997c0db60c22e7841b4ba82708dd4e1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215260"
---
Aby utworzyć projekt programu Visual Studio na potrzeby programowania aplikacji klasycznych w języku C++, należy skonfigurować opcje deweloperskie programu Visual Studio, utworzyć projekt, wybrać architekturę docelową i zainstalować zestaw Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji programistycznych programu Visual Studio

Aby rozpocząć, upewnij się, że program Visual Studio jest poprawnie skonfigurowany do tworzenia aplikacji klasycznych w języku C++:

1. Otwórz program Visual Studio 2019, aby wyświetlić okno **uruchamiania** .

   ![Zrzut ekranu przedstawiający okno "Start" programu Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Wybierz pozycję **Kontynuuj bez kodu** , aby przejść do środowiska IDE programu Visual Studio.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie** .

   ![Zrzut ekranu pokazujący kartę "obciążenia" okna dialogowego "Modyfikowanie" dla "Instalator programu Visual Studio".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na karcie **obciążenia** w obszarze **systemu Windows** Znajdź **Programowanie aplikacji klasycznych w języku C++** . Jeśli pole wyboru obok tego obciążenia nie jest już zaznaczone, zaznacz je.

1. Na karcie **poszczególne składniki** Znajdź pole wyboru **Menedżer pakietów NuGet** . Jeśli pole wyboru nie jest jeszcze zaznaczone, zaznacz je.

1. Wybierz przycisk w rogu z etykietą **Close** lub **Modify**. (Nazwa przycisku różni się w zależności od tego, czy wybrano wszystkie funkcje do instalacji). W przypadku wybrania opcji **Modyfikuj** rozpocznie się instalacja, która może chwilę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Utwórz projekt i wybierz architekturę docelową

Następnie utwórz projekt:

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **plik**  >  **Nowy**  >  **projekt** , aby wyświetlić okno **Utwórz nowy projekt** .

   ![Tworzenie nowego projektu, C++ — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Znajdź i wybierz pozycję **Aplikacja konsolowa**. Upewnij się, że wybrano wersję C++ tego typu projektu (w przeciwieństwie do języka C# lub Visual Basic).

1. Wybierz pozycję **dalej** , aby wyświetlić ekran **Konfiguruj nowy projekt** .

   ![Konfigurowanie nowego projektu, C++ — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. W polu **Nazwa projektu** wprowadź `helloworld` .

1. W obszarze **Lokalizacja** przejdź do, a następnie wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.

Teraz wybierz architekturę platformy docelowej. Na pasku narzędzi programu Visual Studio Znajdź pole listy rozwijanej **platformy rozwiązania** . (Jeśli go nie widzisz, wybierz pozycję **Wyświetl**  >  **Paski narzędzi**  >  **Standard** , aby wyświetlić pasek narzędzi zawierający **platformy rozwiązań**.) Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu listy rozwijanej. 64 — bit systemu Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86** , jeśli wolisz.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Na koniec zainstaluj [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget)i odwołuje się do zestawu Speech SDK w projekcie:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania** , aby przejść do okna **rozwiązania NuGet** .

1. Wybierz pozycję **Przeglądaj**.

   ![NuGet — Karta rozwiązanie, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. W obszarze **Źródło pakietu** wybierz pozycję **NuGet.org**.

1. W polu **wyszukiwania** wpisz `Microsoft.CognitiveServices.Speech` , a następnie wybierz ten pakiet po pojawieniu się w wynikach wyszukiwania.

   ![Instalacja pakietu Microsoft. CognitiveServices. Speech C++ — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. W okienku stan pakietu obok wyników wyszukiwania wybierz swój projekt **HelloWorld** .

1. Wybierz pozycję **Zainstaluj**.

1. W oknie dialogowym **Podgląd zmian** wybierz **przycisk OK**.

1. W oknie dialogowym **Akceptacja licencji** Wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Rozpocznie się instalacja pakietu i po zakończeniu instalacji w okienku **dane wyjściowe** zostanie wyświetlony komunikat podobny do następującego: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
