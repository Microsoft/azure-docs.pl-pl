---
title: 'Szybki Start: Tworzenie aplikacji HoloLens przy użyciu technologii DirectX'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację HoloLens przy użyciu kotwic obiektów.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 7e71719d83426a3444435ed78d0d63a2599a9157
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749056"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Szybki Start: Tworzenie aplikacji HoloLens przy użyciu kotwic obiektów platformy Azure w językach C++/WinRT i DirectX

W tym przewodniku szybki start opisano sposób tworzenia aplikacji HoloLens przy użyciu [kotwic obiektów platformy Azure](../overview.md) w językach C++/WinRT i DirectX. Kotwice obiektów platformy Azure to zarządzana usługa w chmurze, która konwertuje zasoby 3D na modele AI, które umożliwiają korzystanie z rzeczywistości mieszanej opartej na obiektach dla urządzenia HoloLens. Po zakończeniu będziesz mieć aplikację HoloLens, która może wykryć obiekt i jego ułożenie w aplikacji Holographic DirectX 11 (uniwersalnej systemu Windows).

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie i ładowanie aplikacji HoloLens
> * Wykrywanie obiektu i wizualizacja jego modelu
> * Przechwytywanie diagnostyki środowiska uruchomieniowego

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Obiekt fizyczny w środowisku i jego model 3W (CAD lub zeskanowany).
* Komputer z systemem Windows z zainstalowaną funkcją:
  * <a href="https://git-scm.com" target="_blank">Git dla systemu Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Program Visual Studio 2019</a> z obciążeniem **programowanie platforma uniwersalna systemu Windows** i składnikiem **Windows 10 SDK (10.0.18362.0 lub nowszym)**
* Urządzenie HoloLens 2, które jest aktualne i ma włączony [Tryb dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio#enabling-developer-mode) .
  * Aby zaktualizować do najnowszej wersji na serwerze HoloLens, Otwórz aplikację **Ustawienia** , przejdź do pozycji **Aktualizacja & zabezpieczenia**, a następnie wybierz pozycję **Sprawdź aktualizacje**.

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Otwórz plik `quickstarts/apps/directx/DirectXAoaSampleApp.sln` w programie Visual Studio.

Zmień **konfigurację rozwiązania** na **Zwolnij**, Zmień **platformę rozwiązania** na **arm64**, wybierz pozycję **urządzenie** z opcji cel wdrożenia. Następnie Skompiluj projekt **AoaSampleApp** , klikając prawym przyciskiem myszy projekt i wybierając opcję **Kompiluj**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Konfigurowanie projektu programu Visual Studio do wdrożenia":::

## <a name="deploy-the-app-to-hololens"></a>Wdrażanie aplikacji w usłudze HoloLens

Po pomyślnym skompilowaniu przykładowego projektu można wdrożyć aplikację w usłudze HoloLens.

Włącz urządzenie HoloLens, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB. Upewnij się, że **urządzenie** jest wybranym miejscem docelowym wdrożenia (Zobacz powyżej).

Kliknij prawym przyciskiem myszy projekt **AoaSampleApp** , a następnie kliknij polecenie **Wdróż** z menu podręcznego, aby zainstalować aplikację. Jeśli żaden błąd nie pojawia się w **okno dane wyjściowe** programu Visual Studio, aplikacja zostanie zainstalowana na serwerze HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Wdrażanie aplikacji w usłudze HoloLens":::

Przed uruchomieniem aplikacji należy przekazać model obiektu. Postępuj zgodnie z instrukcjami w temacie **model obiektów pozyskiwania i Wykryj** poniższe informacje o wystąpieniu.

Aby uruchomić i debugować aplikację, wybierz pozycję **debuguj > Rozpocznij debugowanie**. Aby zatrzymać aplikację, wybierz opcję **Zatrzymaj debugowanie** lub naciśnij klawisze **Shift + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Pozyskiwanie modelu obiektów i wykrywanie jego wystąpienia

Musisz utworzyć model obiektów, aby uruchomić przykładową aplikację. Załóżmy, że masz już program CAD lub zeskanowany model siatki 3W obiektu w miejscu. Zapoznaj się z [przewodnikiem Szybki Start: pozyskiwanie modelu 3D](./get-started-model-ingestion.md) na temat tworzenia modelu.

Pobierz ten **model w naszym** przypadku do komputera. Następnie w portalu urządzenia HoloLens wybierz pozycję **System > plików eksplorator > LocalAppData > AoaSampleApp > LocalState** i wybierz pozycję **Przeglądaj...**. Następnie wybierz plik modelu, **krzesło. OU** na przykład, a następnie wybierz pozycję **Przekaż**. Następnie należy wyświetlić plik modelu w lokalnej pamięci podręcznej.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Model przekazywania portalu":::

Na podstawie urządzenia HoloLens Uruchom aplikację **AoaSampleApp** (jeśli została już otwarta, zamknij ją i Otwórz ponownie). Spójrz blisko (w odległości 2-pomiarowej) na obiekt docelowy (krzesło) i przeskanuj go, patrząc na niego z wielu perspektyw. Powinno zostać wyświetlone różowe obramowanie wokół obiektu z niektórymi żółtymi punktami, które wskazują na powierzchnię obiektu, co oznacza, że zostało wykryte.

:::image type="content" source="./media/chair-detection.png" alt-text="Wykrywanie krzesła":::

Ilustracja: wykryty krzesło, które jest renderowane z obwiednią (różowe), w punkcie Cloud (żółty) i w obszarze wyszukiwania (duże żółte pole).

Możesz zdefiniować miejsce wyszukiwania dla obiektu w aplikacji, naciskając przycisk myszy w powietrzu z prawą lub lewą ręką. Obszar wyszukiwania przejdzie między sferą promienia 2-metrii, a 4 m ^ 3 obwiednią i widokiem frustum. W przypadku większych obiektów, takich jak samochody, najlepszym wyborem jest zwykle użycie frustum widoku, gdy jest on widoczny w górnym rogu obiektu.
Za każdym razem, gdy obszar wyszukiwania ulegnie zmianie, aplikacja usunie aktualnie śledzone wystąpienia, a następnie spróbuje je znaleźć ponownie w nowym obszarze wyszukiwania.

Ta aplikacja może śledzić wiele obiektów jednocześnie. W tym celu Przekaż wiele modeli do folderu **LocalState** i Ustaw obszar wyszukiwania obejmujący wszystkie obiekty docelowe. Wykrywanie i śledzenie wielu obiektów może trwać dłużej.

Aplikacja dopasowuje model 3D do jego fizycznego odpowiednika. Użytkownik może wybrać tryb śledzenia o wysokiej dokładności przy użyciu lewej strony, który oblicza dokładniejsze ułożenie. Nadal jest to funkcja eksperymentalna, która zużywa więcej zasobów systemowych i może powodować większe wahania w szacowanej pozycji. Naciśnij ponownie z lewej strony, aby przełączyć się z powrotem do normalnego trybu śledzenia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: pozyskiwanie modelu 3D](./get-started-model-ingestion.md)

> [!div class="nextstepaction"]
> [Pojęcia: Omówienie zestawu SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania](../faq.md)
