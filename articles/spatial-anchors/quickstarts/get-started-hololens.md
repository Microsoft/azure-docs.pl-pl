---
title: 'Szybki Start: Tworzenie aplikacji HoloLens przy użyciu technologii DirectX'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając usługi Spatial Anchors.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 66b8008312c1cf81d4fca7fb8ee5f26fc4c98f60
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096167"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Szybki Start: Tworzenie aplikacji HoloLens z zakotwiczeniami przestrzennymi platformy Azure w językach C++/WinRT i DirectX

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla urządzenia HoloLens przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku C++/WinRT z użyciem programu DirectX. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:
- Maszyna z systemem Windows z programem <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> z pakietem roboczym **programowanie platforma uniwersalna systemu Windows** i **zestawem SDK systemu Windows 10 (10.0.18362.0 lub nowszym)** . Należy również zainstalować narzędzie <a href="https://git-scm.com/download/win" target="_blank">git dla systemu Windows</a> i <a href="https://git-lfs.github.com/">narzędzia Git LFS</a>.
- Powinno być zainstalowane rozszerzenie [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) dla programu Visual Studio z usługi [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Urządzenie HoloLens z włączonym [trybem dewelopera](/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows 10 z maja 2020](/windows/mixed-reality/whats-new/release-notes-may-2020). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.
- Aplikacja musi ustawiać możliwość **spatialPerception** w manifeście AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `HoloLens\DirectX\SampleHoloLens.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

Otwórz plik `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

Znajdź `SpatialAnchorsAccountDomain` pole i Zastąp `Set me` je domeną konta.

## <a name="deploy-the-app-to-your-hololens"></a>Wdrażanie aplikacji na urządzeniu HoloLens

Zmień **konfigurację rozwiązania** na **Wydanie**, zmień **platformę rozwiązania** na **x86** i wybierz **urządzenie** spośród opcji miejsc docelowych wdrażania.

W przypadku korzystania z urządzenia HoloLens 2 Użyj **arm64** jako **platformy rozwiązania**zamiast **architektury x86**.

![Konfiguracja programu Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz kolejno opcje **Debuguj**  >  **Rozpocznij debugowanie** , aby wdrożyć aplikację i rozpocząć debugowanie.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

W programie Visual Studio zatrzymaj aplikację, wybierając pozycję **Zatrzymaj debugowanie** lub naciskając klawisze **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)