---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001109"
---
Aby utworzyć projekt programu Visual Studio dla rozwoju systemu Windows, musisz utworzyć projekt, skonfigurować program Visual Studio do tworzenia aplikacji klasycznych platformy .NET, zainstalować zestaw Speech SDK i wybrać architekturę docelową.

### <a name="create-the-project-and-add-the-workload"></a>Utwórz projekt i Dodaj obciążenie

Aby rozpocząć, Utwórz projekt w programie Visual Studio i upewnij się, że program Visual Studio został skonfigurowany do tworzenia aplikacji klasycznych platformy .NET:

1. Otwórz program Visual Studio 2019.

1. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**. 

1. W oknie **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź wartość *HelloWorld* w polu **Nazwa projektu**, wybierz lub Utwórz ścieżkę katalogu w **lokalizacji**, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**, które otwiera Instalator programu Visual Studio i wyświetla okno dialogowe **Modyfikowanie** .

1. Sprawdź, czy jest dostępne obciążenie **Programowanie aplikacji klasycznych dla platformy .NET** . Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru obok niego, a następnie wybierz pozycję **Modyfikuj** , aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru obok pozycji **Programowanie aplikacji klasycznych platformy .NET** jest już zaznaczone, wybierz pozycję **Zamknij** , aby zamknąć okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zamknij Instalator programu Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Następnym krokiem jest zainstalowanie [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget), aby można było odwołać się do niego w kodzie.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **HelloWorld** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet** , aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu Znajdź pole listy rozwijanej **Źródło pakietu** i upewnij się, że wybrano opcję **NuGet.org** .

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz *Microsoft. CognitiveServices. Speech* i wybierz **Enter**.

1. Z wyników wyszukiwania wybierz pakiet **Microsoft. CognitiveServices. Speech** , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet NuGet Microsoft. CognitiveServices. Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **konsola Menedżera pakietów** zostanie wyświetlone potwierdzenie.

### <a name="choose-the-target-architecture"></a>Wybieranie architektury docelowej

Teraz, aby skompilować i uruchomić aplikację konsolową, Utwórz konfigurację platformy zgodną z architekturą komputera.

1. Na pasku menu wybierz kolejno opcje **Kompiluj**  >  **Configuration Manager**. Zostanie wyświetlone okno dialogowe **Configuration Manager** .

   ![Okno dialogowe Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. W polu listy rozwijanej **platforma aktywnego rozwiązania** wybierz pozycję **Nowy**. Zostanie wyświetlone okno dialogowe **Nowa platforma rozwiązania** .

1. W polu **Typ lub wybierz nową platformę z** listy rozwijanej:
   - Jeśli korzystasz z 64-bitowego systemu Windows, wybierz pozycję **x64**.
   - Jeśli korzystasz z 32-bitowego systemu Windows, wybierz pozycję **x86**.

1. Wybierz przycisk **OK** , a następnie **Zamknij**.
