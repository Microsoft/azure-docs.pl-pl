---
title: 'Szybki Start: zestaw Speech SDK dla .NET Framework (Windows) Konfiguracja platformy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę dla języka C# w obszarze .NET Framework dla systemu Windows z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: f63c97fd23d56ff508de8d5953eefac6b15ae1aa
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097069"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla .NET Framework (Windows). Jeśli chcesz, aby nazwa pakietu została już uruchomiona, uruchom polecenie `Install-Package Microsoft.CognitiveServices.Speech` w konsoli programu NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Tworzenie projektu programu Visual Studio i Instalowanie zestawu Speech SDK

Musisz zainstalować [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) , aby można było odwołać się do niego w kodzie. Aby to zrobić, konieczne może być utworzenie projektu **HelloWorld** . Jeśli masz już projekt z udostępnionym obciążeniem **programu .NET Desktop** , możesz użyć tego projektu i przejść do korzystania z [Menedżera pakietów NuGet w celu zainstalowania zestawu Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Utwórz projekt HelloWorld

1. Otwórz program Visual Studio 2019.

1. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**. 

1. W oknie **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź wartość *HelloWorld* w polu **Nazwa projektu**, wybierz lub Utwórz ścieżkę katalogu w **lokalizacji**, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**, które otwiera Instalator programu Visual Studio i wyświetla okno dialogowe **Modyfikowanie** .

1. Sprawdź, czy jest dostępne obciążenie **Programowanie aplikacji klasycznych dla platformy .NET** . Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru obok niego, a następnie wybierz pozycję **Modyfikuj** , aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru obok pozycji **Programowanie aplikacji klasycznych platformy .NET** jest już zaznaczone, wybierz pozycję **Zamknij** , aby zamknąć okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zamknij Instalator programu Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Instalowanie zestawu Speech SDK przy użyciu Menedżera pakietów NuGet

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **HelloWorld** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet** , aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu Znajdź pole listy rozwijanej **Źródło pakietu** i upewnij się, że **`nuget.org`** jest zaznaczone.

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz *Microsoft. CognitiveServices. Speech* i wybierz **Enter**.

1. Z wyników wyszukiwania wybierz pakiet **Microsoft. CognitiveServices. Speech** , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet NuGet Microsoft. CognitiveServices. Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **konsola Menedżera pakietów** zostanie wyświetlone potwierdzenie.

### <a name="choose-target-architecture"></a>Wybieranie architektury docelowej

Aby skompilować i uruchomić aplikację konsolową, Utwórz konfigurację platformy zgodną z architekturą komputera.

1. Na pasku menu wybierz kolejno opcje **Kompiluj**  >  **Configuration Manager**. Zostanie wyświetlone okno dialogowe **Configuration Manager** .

   ![Okno dialogowe Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. W polu listy rozwijanej **platforma aktywnego rozwiązania** wybierz pozycję **Nowy**. Zostanie wyświetlone okno dialogowe **Nowa platforma rozwiązania** .

1. W polu **Typ lub wybierz nową platformę z** listy rozwijanej:
   - Jeśli korzystasz z 64-bitowego systemu Windows, wybierz pozycję **x64**.
   - Jeśli korzystasz z 32-bitowego systemu Windows, wybierz pozycję **x86**.

1. Wybierz przycisk **OK** , a następnie **Zamknij**.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
