---
title: 'Szybki Start: Tworzenie aplikacji Unity dla systemu iOS'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS, używając aparatu Unity i usługi Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b341237d0e703239233f7ac0e9664f5fb90bbb4b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105815"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Szybki Start: Tworzenie aplikacji Unity dla systemu iOS z zakotwiczeniami przestrzennymi platformy Azure

W tym przewodniku szybki start opisano, jak utworzyć aplikację Unity dla systemu iOS przy użyciu [kotwic przestrzennych platformy Azure](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARKit dla systemu iOS utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu Xcode
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna macOS z zainstalowaną najnowszą wersją <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> i <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> . Użyj **aparatu unity 2020 LTS** z zestawem SDK w wersji 2,9 lub nowszej (korzystającej z [struktury wtyczki XR Unity](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) lub **aparatu Unity 2019 LTS** za pomocą zestawu ASA SDK w wersji 2,8 lub starszej.
- Narzędzie git zainstalowane za pośrednictwem usługi oprogramowania homebrew. Wprowadź następujące polecenie w jednym wierszu terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Następnie uruchom `brew install git` i `brew install git-lfs` .
- Deweloperskie urządzenie z systemem iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zgodne z zestawem ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu środowiska Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](../how-tos/setup-unity-project.md#download-asa-packages) , aby pobrać i ZAIMPORTOWAĆ pakiety SDK ASA wymagane dla platformy iOS.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>Eksportowanie projektu Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

W aplikacji wybierz pozycję **BasicDemo** za pomocą strzałek, a następnie naciśnij pozycję **Przejdź!** przycisk służący do uruchamiania demonstracji. Postępuj zgodnie z instrukcjami, aby umieścić i odwołać kotwicę.

![Zrzut ekranu 1 zrzut ekranu ](./media/get-started-unity-ios/screenshot-1.jpg)
 ![ 2 ](./media/get-started-unity-ios/screenshot-2.jpg)
 ![ zrzut ekranu 3](./media/get-started-unity-ios/screenshot-3.jpg)

Po zakończeniu Zatrzymaj aplikację, naciskając przycisk **Zatrzymaj** w Xcode.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="rendering-issues"></a>Problemy z renderowaniem

Jeśli podczas uruchamiania aplikacji nie widzisz aparatu jako tła (np. widzisz puste miejsce, niebieski kolor lub inne tekstury), prawdopodobnie musisz ponownie zaimportować zasoby w aparacie Unity. Zatrzymaj aplikację. W górnym menu w aparacie Unity wybierz pozycję **zasoby — > ponownie zaimportuj wszystkie**. Następnie ponownie uruchom aplikację.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Instrukcje: Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity](../how-tos/setup-unity-project.md)
