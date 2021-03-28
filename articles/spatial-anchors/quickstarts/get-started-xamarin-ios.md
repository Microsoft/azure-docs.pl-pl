---
title: 'Szybki Start: Tworzenie aplikacji platformy Xamarin dla systemu iOS'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS za pomocą platformy Xamarin przy użyciu kotwic przestrzennych.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 05b58bc88aeb4157e4aab6d33eb5093f796c7e58
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642622"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Szybki Start: Tworzenie aplikacji platformy Xamarin dla systemu iOS przy użyciu kotwic Azure przestrzennego

W tym przewodniku szybki start opisano sposób tworzenia aplikacji dla systemu iOS przy użyciu funkcji [Azure przestrzenny kotwic](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Po zakończeniu będziesz mieć aplikację systemu iOS, która będzie mogła zapisywać i odwoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:
- Komputery Mac z systemem macOS High Sierra (10,13) lub nowszym z:
  - Najnowsza wersja oprogramowania Xcode i zestawu iOS SDK zainstalowana ze [sklepu App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Aktualna wersja <a href="/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true" target="_blank">Visual Studio dla komputerów Mac 8.1 +</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git for macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `Xamarin/SampleXamarin.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

Otwórz plik `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

Znajdź `SpatialAnchorsAccountDomain` pole i Zastąp `Set me` je domeną konta.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Włącz urządzenie z systemem iOS, zaloguj się i połącz je z komputerem przy użyciu kabla USB.

Ustaw projekt startowy na **SampleXamarin. iOS**, Zmień **konfigurację rozwiązania** na **Zwolnij**, a następnie wybierz urządzenie, które chcesz wdrożyć, na liście rozwijanej selektora urządzeń.

![Konfiguracja programu Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby wdrożyć i uruchomić aplikację.

W aplikacji wybierz pozycję **podstawowa** , aby uruchomić demonstrację, i postępuj zgodnie z instrukcjami, aby umieścić i odwołać kotwicę.

> ![Zrzut ekranu 1 zrzut ekranu ](./media/get-started-xamarin-ios/screenshot-1.jpg)
>  ![ 2 ](./media/get-started-xamarin-ios/screenshot-2.jpg)
>  ![ zrzut ekranu 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)