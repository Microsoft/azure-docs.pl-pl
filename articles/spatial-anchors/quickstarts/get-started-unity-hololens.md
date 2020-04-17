---
title: 'Szybki start: tworzenie aplikacji HoloLens za pomocą unity'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482006"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji Unity HoloLens, która korzysta z zakotwiczeń przestrzennych platformy Azure

W tym przewodniku Szybki start utworzysz aplikację Unity HoloLens, która używa [zakotwiczeń przestrzennych platformy Azure.](../overview.md) Przestrzenne kotwice to wieloplatformowa usługa deweloperska, która umożliwia tworzenie środowisk rzeczywistości mieszanej z obiektami, które utrzymują swoją lokalizację na różnych urządzeniach w czasie. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

- Utwórz konto Kotwice przestrzenne.
- Przygotuj ustawienia kompilacji Unity.
- Skonfiguruj identyfikator konta zakotwiczenia przestrzennego i klucz konta.
- Eksportuj projekt Programu HoloLens Visual Studio.
- Wdrażanie aplikacji i uruchamianie jej na urządzeniu HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- Potrzebny jest komputer z systemem Windows z <a href="https://unity3d.com/get-unity/download" target="_blank">unity 2019.1 lub 2019.2</a> i <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> lub nowsze są zainstalowane. Instalacja programu Visual Studio musi zawierać obciążenie **deweloperskie platformy systemu** Windows i składnik **SDK systemu Windows 10 (10.0.18362.0 lub nowszy).** Należy również zainstalować <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a> i <a href="https://git-lfs.github.com/">Git LFS</a>.
- Potrzebujesz urządzenia HoloLens, na którym włączony [jest tryb dewelopera.](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) [Windows 10 October 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (znany również jako RS5) musi być zainstalowany na urządzeniu. Aby zaktualizować do najnowszej wersji na HoloLens, otwórz aplikację **Ustawienia,** przejdź do **pozycji Aktualizuj & zabezpieczenia,** a następnie wybierz pozycję **Sprawdź dostępność aktualizacji**.
- W aplikacji należy włączyć **spatialperception** możliwości. To ustawienie znajduje się w obszarze Ustawienia ustawień **kompilacji** > Ustawienia**odtwarzacza** > **Ustawienia** > publikowania **.**
- W aplikacji musisz włączyć **wirtualną rzeczywistość obsługiwaną** za pomocą **sdk Windows Mixed Reality**. To ustawienie znajduje się w **ustawieniach** >  > **XR****ustawienia odtwarzacza**kompilacji .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Zmień **urządzenie docelowe** na **HoloLens**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Unity może monitować o zainstalowanie składników pomocy technicznej platformy uniwersalnej systemu Windows, jeśli ich brakuje.

![Okno Ustawienia kompilacji jedności](./media/get-started-unity-hololens/unity-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

## <a name="configure-the-account-identifier-and-key"></a>Konfigurowanie identyfikatora konta i klucza

W okienku **Projekt** przejdź `Assets/AzureSpatialAnchors.Examples/Scenes` do `AzureSpatialAnchorsBasicDemo.unity` pliku sceny i otwórz go.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając **pozycję Zapisz plik** > **Save**.

## <a name="export-the-hololens-visual-studio-project"></a>Eksportowanie projektu programu Visual Studio dla urządzenia HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz **pozycję Zbuduj**. W oknie dialogowym wybierz folder, w którym ma być eksportowany projekt programu HoloLens Visual Studio.

Po zakończeniu eksportowania pojawi się folder zawierający eksportowany projekt HoloLens.

## <a name="deploy-the-hololens-application"></a>Wdrażanie aplikacji dla urządzenia HoloLens

W folderze kliknij dwukrotnie **HelloAR U3D.sln,** aby otworzyć projekt w programie Visual Studio.

Zmień **konfigurację rozwiązania** na **wydanie,** zmień **platformę rozwiązania** na **x86**i wybierz **urządzenie** z opcji docelowych wdrożenia.

Jeśli używasz HoloLens 2, użyj **ARM64** jako **platformy rozwiązania,** zamiast **x86**.

   ![Konfiguracja programu Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i podłącz urządzenie do komputera za pomocą kabla USB.

Wybierz **debugowanie** > **Rozpocznij debugowanie,** aby wdrożyć aplikację i rozpocząć debugowanie.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

W programie Visual Studio zatrzymaj aplikację, wybierając **opcję Zatrzymaj debugowanie** lub Shift+F5.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unity-20193"></a>Jedność 2019.3

Ze względu na przełomowe zmiany, Unity 2019.3 nie jest obecnie obsługiwany. Prosimy o korzystanie z Unity 2019.1 lub 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
