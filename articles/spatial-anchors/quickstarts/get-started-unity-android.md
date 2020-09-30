---
title: 'Szybki Start: Tworzenie aplikacji dla systemu Android w środowisku Unity'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c84ebfe9752b8594e97c7c72d706ee25c4852f6e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538149"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Szybki Start: Tworzenie aplikacji dla systemu Android w środowisku Unity przy użyciu kotwic przestrzennych platformy Azure

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji dla systemu Android w środowisku Unity przy użyciu [kotwic przestrzennych platformy Azure](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu programu Android Studio
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna z systemem Windows lub macOS z programem <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,4 (LTS)</a>, włącznie z **obsługą kompilacji systemu Android** z **Android SDK & narzędzia NDK** i moduły **OpenJDK** .
  - W przypadku korzystania z systemu Windows potrzebna jest również <a href="https://git-scm.com/download/win" target="_blank">git dla systemu Windows</a> i <a href="https://git-lfs.github.com/">narzędzia Git LFS</a>.
  - W przypadku uruchamiania w systemie macOS Pobierz narzędzie git zainstalowane za pośrednictwem usługi oprogramowania homebrew. Wprowadź następujące polecenie w jednym wierszu terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Następnie uruchom `brew install git` i `brew install git-lfs` .
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
  - Aby komputer mógł komunikować się z urządzeniem z systemem Android, mogą być wymagane dodatkowe sterowniki urządzeń. Aby uzyskać dodatkowe informacje i instrukcje, zobacz [tutaj](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu środowiska Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Eksportowanie projektu programu Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz urządzenie w obszarze **Uruchom urządzenie** , a następnie wybierz opcję **Kompiluj i uruchom**. Zostanie wyświetlony monit o zapisanie `.apk` pliku, dla którego można wybrać dowolną nazwę.

W aplikacji wybierz pozycję **BasicDemo** za pomocą strzałek, a następnie naciśnij pozycję **Przejdź!** przycisk służący do uruchamiania demonstracji. Postępuj zgodnie z instrukcjami, aby umieścić i odwołać kotwicę.

![Zrzut ekranu 1 zrzut ekranu ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ 2 ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ zrzut ekranu 3](./media/get-started-unity-android/screenshot-3.jpg)

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="rendering-issues"></a>Problemy z renderowaniem

Gdy aplikacja jest uruchamiana, jeśli nie widzisz kamery jako tła (na przykład widzisz niepustą, niebieską lub inną teksturę), najkorzystniej będzie trzeba ponownie zaimportować zasoby w środowisku Unity. Zatrzymaj aplikację. Z górnego menu aparatu Unity wybierz pozycje **Assets -> Reimport all (Zasoby -> Zaimportuj ponownie wszystko)**. Następnie ponownie uruchom aplikację.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Instrukcje: Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity](../how-tos/setup-unity-project.md)
