---
title: 'Szybki Start: Tworzenie aplikacji HoloLens przy użyciu aparatu Unity'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając aparatu Unity i usługi Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c0cce7717dda73a381ec977c3bf520bf8db5bbb3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105854"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Szybki Start: Tworzenie aplikacji HoloLens Unity wykorzystującej kotwice przestrzenne platformy Azure

W tym przewodniku szybki start utworzysz aplikację HoloLens Unity korzystającą z [kotwic przestrzennych platformy Azure](../overview.md). Kotwice przestrzenne to wieloplatformowa usługa dla deweloperów, która umożliwia tworzenie środowisk o rzeczywistości mieszanej z obiektami, które utrzymują swoją lokalizację na różnych urządzeniach w miarę upływu czasu. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

- Utwórz konto zakotwiczeń przestrzennych.
- Przygotuj ustawienia kompilacji aparatu Unity.
- Skonfiguruj identyfikator konta zakotwiczeń przestrzennych i klucz konta.
- Wyeksportuj projekt HoloLens programu Visual Studio.
- Wdróż aplikację i uruchom ją na urządzeniu HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- Wymagany jest komputer z systemem Windows z systemami <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> i <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> lub nowszymi. Użyj **aparatu unity 2020 LTS** z zestawem SDK w wersji 2,9 lub nowszej (korzystającej z [struktury wtyczki XR Unity](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) lub **aparatu Unity 2019 LTS** za pomocą zestawu ASA SDK w wersji 2,8 lub starszej. Instalacja programu Visual Studio musi zawierać **platforma uniwersalna systemu Windows obciążenie programowaniem** i składnik **zestawu Windows 10 SDK (10.0.18362.0 lub nowszy)** . Należy również zainstalować narzędzie <a href="https://git-scm.com/download/win" target="_blank">git dla systemu Windows</a> i <a href="https://git-lfs.github.com/">narzędzia Git LFS</a>.
- Potrzebujesz urządzenia HoloLens, na którym włączono [Tryb dewelopera](/windows/mixed-reality/using-visual-studio) . Na urządzeniu musi być zainstalowana [Aktualizacja systemu Windows 10 maja 2020](/windows/mixed-reality/whats-new/release-notes-may-2020) . Aby zaktualizować do najnowszej wersji na serwerze HoloLens, Otwórz aplikację **Ustawienia** , przejdź do pozycji **Aktualizacja & zabezpieczenia**, a następnie wybierz pozycję **Sprawdź aktualizacje**.
- W aplikacji należy włączyć funkcję **SpatialPerception** . To ustawienie znajduje się w obszarze **Ustawienia kompilacji** ustawienia  >  **odtwarzacza**  >  **Publikowanie**  >  **ustawień**.
- W Twojej aplikacji należy włączyć opcję **wirtualnej rzeczywistość obsługiwaną** z **zestawem SDK rzeczywistości systemu Windows**. To ustawienie jest w obszarze Ustawienia **kompilacji** ustawienia  >  **odtwarzacza** ustawienia  >  **XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu środowiska Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](../how-tos/setup-unity-project.md#download-asa-packages) , aby pobrać i ZAIMPORTOWAĆ pakiety SDK ASA wymagane przez platformę HoloLens.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Zmień **urządzenie docelowe** na **HoloLens**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Aparat Unity może monitować o zainstalowanie składników obsługi platformy UWP, jeśli nie są one obecne.

![Okno ustawień kompilacji aparatu Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Eksportowanie projektu programu Visual Studio dla urządzenia HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz pozycję **kompilacja**. W oknie dialogowym Wybierz folder, w którym chcesz wyeksportować projekt HoloLens programu Visual Studio.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt HoloLens.

## <a name="deploy-the-hololens-application"></a>Wdrażanie aplikacji dla urządzenia HoloLens

W folderze kliknij dwukrotnie plik **HELLOAR U3D. sln** , aby otworzyć projekt w programie Visual Studio.

Zmień **konfigurację rozwiązania** na **Zwolnij**, Zmień **platformę rozwiązania** na **x86**, a następnie wybierz pozycję **urządzenie** z opcji cel wdrożenia.

W przypadku korzystania z urządzenia HoloLens 2 Użyj **arm64** jako **platformy rozwiązania** zamiast **architektury x86**.

   ![Konfiguracja programu Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i podłącz urządzenie do komputera za pomocą kabla USB.

Wybierz kolejno opcje **Debuguj**  >  **Rozpocznij debugowanie** , aby wdrożyć aplikację i rozpocząć debugowanie.

W aplikacji wybierz pozycję **BasicDemo** za pomocą strzałek, a następnie naciśnij pozycję **Przejdź!** przycisk służący do uruchamiania demonstracji. Postępuj zgodnie z instrukcjami, aby umieścić i odwołać kotwicę.

![Zrzut ekranu 1 zrzut ekranu 2 zrzut ekranu ](./media/get-started-unity-hololens/screenshot-1.jpg)
 ![ ](./media/get-started-unity-hololens/screenshot-2.jpg)
 ![ 3 zrzut ](./media/get-started-unity-hololens/screenshot-3.jpg)
 ![ ekranu 4](./media/get-started-unity-hololens/screenshot-4.jpg)

W programie Visual Studio Zatrzymaj aplikację, wybierając pozycję **Zatrzymaj debugowanie** lub naciśnij klawisze Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Instrukcje: Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity](../how-tos/setup-unity-project.md)