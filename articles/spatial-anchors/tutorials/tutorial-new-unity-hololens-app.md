---
title: 'Samouczek: Tworzenie nowej aplikacji HoloLens Unity'
description: W ramach tego samouczka dowiesz się, jak utworzyć nową aplikację aparatu HoloLens Unity przy użyciu kotwic przestrzennych platformy Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee8b8c2931d006dbb3d472b545030d3aff79c56a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297991"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Samouczek: instrukcje krok po kroku dotyczące tworzenia nowej aplikacji HoloLens Unity przy użyciu kotwic przestrzennych platformy Azure

W tym samouczku pokazano, jak utworzyć nową aplikację usługi HoloLens Unity z zakotwiczeniami przestrzennymi platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

1. Maszyna z systemem Windows z programem <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> zainstalowana z **platforma uniwersalna systemu Windows obciążenie programowaniem** oraz składnikiem **Windows 10 SDK (10.0.18362.0 lub nowszym)** i <a href="https://git-scm.com/download/win" target="_blank">git dla systemu Windows</a>.
2. Powinno być zainstalowane rozszerzenie [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) dla programu Visual Studio z usługi [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Urządzenie HoloLens z włączonym [trybem dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows z 10 października 2018 r.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (znaną także jako RS5). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.

## <a name="getting-started"></a>Wprowadzenie

Najpierw skonfigurujemy nasze sceny projektowe i Unity:
1. Uruchom środowisko Unity.
2. Wybierz pozycję **Nowy**.
4. Upewnij się, że wybrano **3W** .
5. Nazwij projekt i wprowadź **lokalizację**zapisywania.
6. Kliknij pozycję **Utwórz projekt**.
7. Zapisz pustą domyślną scenę w nowym pliku przy użyciu polecenia: **plik**  >  **Zapisz jako**.
8. Nazwij nową scenę **główną** i naciśnij przycisk **Zapisz** .

**Konfigurowanie ustawień projektu**

Teraz ustawimy ustawienia projektu środowiska Unity, które pomagają nam określić zestaw SDK systemu Windows Holographic na potrzeby programowania.

Po pierwsze, program umożliwia ustawianie ustawień jakości dla naszej aplikacji.
1. Wybierz pozycję **Edytuj**  >  **Ustawienia projektu**  >  **jakość**
2. W kolumnie pod logo **sklepu Windows** kliknij strzałkę w wierszu **domyślny** i wybierz opcję **bardzo niska**. Wiadomo, że ustawienie jest stosowane poprawnie, gdy pole w kolumnie **Sklep Windows** i **bardzo niski** wiersz jest zielony.

Musimy poinformować aparat Unity, że aplikacja, którą próbujesz wyeksportować, powinna utworzyć widok immersyjny zamiast widoku 2D. Tworzymy widok immersyjny, włączając obsługę wirtualnej rzeczywistości w aparacie Unity przeznaczonym dla zestawu SDK systemu Windows 10.

1. Przejdź do pozycji **Edytuj**  >  **Ustawienia projektu**  >  **odtwarzacz**.
2. W **panelu Inspektora** **Ustawienia odtwarzacza**wybierz ikonę **sklepu Windows** .
3. Rozwiń grupę **ustawień XR** .
4. W sekcji **renderowanie** zaznacz pole wyboru zapoznaj **wirtualną rzeczywistość** , aby dodać nową listę **SDK rzeczywistości wirtualnej** .
5. Sprawdź, czy na liście pojawi się wartość **Windows Mixed Reality** . W przeciwnym razie wybierz **+** przycisk w dolnej części listy i wybierz pozycję **Windows Mixed Reality**.

> [!NOTE]
> Jeśli ikona sklepu Windows nie jest widoczna, należy zaznaczyć pole wyboru Sprawdź, czy przed instalacją została wybrana wewnętrzna baza danych do obsługi skryptów .NET sklepu Windows. W przeciwnym razie może być konieczne ponowne zainstalowanie aparatu Unity przy użyciu poprawnej instalacji systemu Windows.

**Weryfikowanie konfiguracji zaplecza skryptów**
1. Przejdź do pozycji **Edytuj**  >  **Ustawienia projektu**  >  **odtwarzacz** (nadal może być otwarty **odtwarzacz** z poprzedniego kroku).
2. W **panelu Inspektora** **Ustawienia odtwarzacza**wybierz ikonę **sklepu Windows** .
3. Upewnij **się, że** w sekcji Konfiguracja **innych ustawień** jest ustawiona wartość **IL2CPP**.

**Ustaw możliwości**
1. Przejdź do pozycji **Edytuj**  >  **Ustawienia projektu**  >  **odtwarzacz** (nadal może być otwarty **odtwarzacz** z poprzedniego kroku).
2. W **panelu Inspektora** **Ustawienia odtwarzacza**wybierz ikonę **sklepu Windows** .
3. W sekcji Konfiguracja **ustawień publikowania** Sprawdź **InternetClientServer** i **SpatialPerception**.

**Skonfiguruj główną kamerę wirtualną**
1. W **panelu hierarchia**wybierz pozycję **główny aparat**.
2. W **Inspektorze**ustaw jego pozycję przekształcenia na **0, 0, 0**.
3. Znajdź właściwość **Clear flags** i zmień listę rozwijaną z **skybox** na **Kolor kryjący**.
4. Kliknij pole **tło** , aby otworzyć selektor kolorów.
5. Ustaw wartość **R, G, B i a** na **0**.
6. Wybierz pozycję **Dodaj składnik** i Wyszukaj i Dodaj element **koliduje mapowania przestrzennego**.

**Utwórz nasz skrypt**
1. W okienku **projekt** Utwórz nowy folder ( **skrypty**) w folderze **Assets (zasoby** ).
2. Kliknij prawym przyciskiem myszy folder, a następnie wybierz polecenie **utwórz >**, **skrypt C#**. Tytuł IT **AzureSpatialAnchorsScript**.
3. Przejdź do pozycji **gameobject**  ->  **Utwórz pustą**.
4. Wybierz go, a w **Inspektorze** zmień jego nazwę **z** **MixedRealityCloud**. Wybierz pozycję **Dodaj składnik** i Wyszukaj i Dodaj **AzureSpatialAnchorsScript**.

**Utwórz sferę PREFAB**
1. Przejdź do **GameObject**  ->  sfery**obiektu 3D gry 3W**  ->  **Sphere**.
2. W **Inspektorze**ustaw jej skalę na **0,25, 0,25, 0,25**.
3. Znajdź obiekt **Sphere** w okienku **Hierarchia** . Kliknij go i przeciągnij do folderu **Assets** w okienku **projektu** .
4. Kliknij prawym przyciskiem myszy i **Usuń** oryginalną sferę utworzoną w okienku **Hierarchia** .

W okienku **projektu** powinna być teraz dostępna sfera PREFAB.

## <a name="trying-it-out"></a>Trwa próba
Aby sprawdzić, czy wszystko działa, skompiluj swoją aplikację w aparacie **Unity** i Wdróż ją z poziomu **programu Visual Studio**. Obserwuj rozdział 6 od firmy [ **MR podstawy 100: wprowadzenie** ](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) do środowiska Unity. Powinien zostać wyświetlony ekran startowy środowiska Unity, a następnie przycisk Wyczyść.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w świecie rzeczywistym
Utwórzmy & umieścić obiekt przy użyciu aplikacji. Otwórz rozwiązanie programu Visual Studio, które zostało utworzone podczas [wdrażania naszej aplikacji](#trying-it-out).

Najpierw Dodaj następujące Importy do `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Następnie Dodaj następujące zmienne elementów członkowskich do `AzureSpatialAnchorsScript` klasy:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Przed kontynuowaniem musimy ustawić PREFAB sferę utworzoną na naszej zmiennej składowej spherePrefab. Wróć do **aparatu Unity**.
1. W aparacie **Unity**wybierz obiekt **MixedRealityCloud** w okienku **Hierarchia** .
2. Kliknij PREFAB **sferę** , która została zapisana w okienku **projektu** . Przeciągnij **sferę** klikniętą w obszarze **sfera PREFAB** w obszarze **skrypt zakotwiczenia przestrzenne platformy Azure** w okienku **Inspektor** .

Teraz należy mieć rolę PREFAB **Sphere** w skrypcie. Skompiluj z **aparatu Unity** , a następnie ponownie otwórz powstałe rozwiązanie **programu Visual Studio** , tak jak właśnie [było to możliwe.](#trying-it-out)

W programie **Visual Studio**ponownie otwórz program `AzureSpatialAnchorsScript.cs` . Dodaj następujący kod do `Start()` metody. Ten kod zostanie poddany `GestureRecognizer` , który zostanie wykryty w przypadku naciskania i wyłączania powietrza `HandleTap` .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Teraz musimy dodać następującą `HandleTap()` metodę poniżej `Update()` . Spowoduje to rzutowanie promienia i uzyskanie punktu trafień, w którym należy umieścić sferę.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

Teraz musimy utworzyć sferę. Sfera będzie początkowo Biała, ale ta wartość zostanie później zmieniona na. Dodaj następującą `CreateAndSaveSphere()` metodę:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Uruchom aplikację z **programu Visual Studio** , aby zweryfikować ją jeszcze raz. Tym razem naciśnij ekran, aby utworzyć & umieścić białą sferę na wybranej powierzchni.

## <a name="set-up-the-dispatcher-pattern"></a>Konfigurowanie wzorca dyspozytora

Podczas pracy z systemem Unity wszystkie interfejsy API aparatu Unity, na przykład interfejsy API używane do wykonywania aktualizacji interfejsu użytkownika, muszą wystąpić w wątku głównym. W kodzie, który zapisząmy, otrzymamy wywołania zwrotne w innych wątkach. Chcemy zaktualizować interfejs użytkownika w tych wywołaniach zwrotnych, aby trzeba było przejść z wątku bocznego do wątku głównego. Aby wykonać kod w wątku głównym z wątku bocznego, użyjemy wzorca dyspozytora.

Dodajmy zmienną członkowską dispatchQueue, która jest kolejką akcji. Będziemy wypychać akcje do kolejki, a następnie dekolejkować i uruchamiać akcje w głównym wątku.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Następnie Dodajmy sposób dodawania akcji do kolejki. Dodaj `QueueOnUpdate()` bezpośrednio po `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Teraz użyjemy pętli Update (), aby sprawdzić, czy w kolejce występuje akcja. W takim przypadku zostanie on usunięty z kolejki i zostanie uruchomiony.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Pobierz zestaw SDK zakotwiczeń przestrzennych platformy Azure

## <a name="via-unity-package"></a>[Za pośrednictwem pakietu Unity](#tab/UnityPackage)

Teraz pobieramy zestaw SDK kotwic przestrzennych platformy Azure. Przejdź do [strony usługi GitHub dotyczącej zakotwiczenia przestrzennego platformy Azure](https://github.com/Azure/azure-spatial-anchors-samples/releases). W obszarze zasoby Pobierz **AzureSpatialAnchors. UNITYPACKAGE**. W środowisku Unity przejdź do pozycji **zasoby**, kliknij pozycję **Importuj**pakiet  >  **niestandardowy pakietu.** Przejdź do pakietu i wybierz pozycję **Otwórz**.

W oknie Nowy **Importuj pakiet Unity** , który zostanie wystawiony, usuń zaznaczenie **wtyczek** , a następnie kliknij przycisk **Importuj** w prawym dolnym rogu.

Teraz należy przywrócić pakiety NuGet, aby uzyskać zestaw SDK zakotwiczenia przestrzennego platformy Azure. Skompiluj z **aparatu Unity** , a następnie otwórz i Skompiluj ponownie powstałe rozwiązanie **programu Visual Studio** , jak to opisano w dalszej [próbie](#trying-it-out).

## <a name="via-nugetforunity"></a>[Za pośrednictwem NuGetForUnity](#tab/NuGetForUnity)

Najpierw musimy zainstalować NuGetForUnity. Przejdź do [strony NuGetForUnity releases (wersje GitHub](https://github.com/GlitchEnzo/NuGetForUnity/releases)). W obszarze zasoby Pobierz ostatni **NuGetForUnity. UNITYPACKAGE**. W środowisku Unity przejdź do pozycji **zasoby**, kliknij pozycję **Importuj**pakiet  >  **niestandardowy pakietu.** Przejdź do pakietu i wybierz pozycję **Otwórz**. Aparat Unity zainstaluje teraz NugetForUnity. Jeśli nie widzisz nowej listy rozwijanej **NuGet** w aparacie Unity, może być konieczne kliknięcie prawym przyciskiem myszy w obszarze **Projects**  >  **zasoby**projektu. Następnie wybierz pozycję **zaimportuj ponownie wszystko**.

Po zainstalowaniu programu NuGetForUnity wybierz pozycję **NuGet**  >  **Zarządzaj pakietami NuGet**. Wyszukaj pozycję Microsoft. Azure. SpatialAnchors. Unity i wybierz pozycję **Zainstaluj**.

Teraz musimy skompilować, aby uzyskać rzeczywiste ZESTAWIEnie zakotwiczeń przestrzennych platformy Azure, ponieważ właśnie pobrany pakiet NuGet zawiera tylko skrypty pomocnika. Skompiluj z **aparatu Unity** , a następnie otwórz i Skompiluj ponownie powstałe rozwiązanie **programu Visual Studio** , jak to opisano w dalszej [próbie](#trying-it-out).

---

W rozwiązaniu **programu Visual Studio** Dodaj następujący import do programu `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Następnie Dodaj następujące zmienne członkowskie do `AzureSpatialAnchorsScript` klasy:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Dołącz lokalną kotwicę przestrzenną platformy Azure do lokalnej kotwicy

Skonfigurujmy CloudSpatialAnchorSession zakotwiczenia przestrzennego platformy Azure. Zaczniemy od dodania następującej `InitializeSession()` metody wewnątrz `AzureSpatialAnchorsScript` klasy. Po wywołaniu zagwarantuje, że sesja kotwic Azure przestrzenny zostanie utworzona i poprawnie zainicjowana podczas uruchamiania aplikacji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

Teraz musimy napisać kod obsługujący wywołania delegatów. Będziemy w dalszym ciągu dodawać do nich dodatkowe elementy.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Teraz przejdźmy `initializeSession()` do metody `Start()` .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Na koniec Dodaj następujący kod do `CreateAndSaveSphere()` metody. Dołączy lokalną kotwicę platformy Azure do sfery, która jest umieszczana w świecie rzeczywistym.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Przed przeprowadzeniem dalszych dalszych czynności należy utworzyć konto zakotwiczeń przestrzennych platformy Azure, aby uzyskać identyfikator konta, klucz i domenę, jeśli nie zostały one jeszcze zainstalowane. Aby je uzyskać, postępuj zgodnie z poniższą sekcją.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przekaż lokalne zakotwiczenie do chmury

Gdy masz swój identyfikator konta, klucz i domenę usługi Azure przestrzenny, przejdź i wklej do `Account Id` `SpatialAnchorsAccountId` , do `Account Key` `SpatialAnchorsAccountKey` , i do `Account Domain` `SpatialAnchorsAccountDomain` .

Na koniec przychodźmy wszystko razem. W `SpawnNewAnchoredObject()` metodzie Dodaj następujący kod. Wywołanie metody będzie możliwe zaraz po `CreateAnchorAsync()` utworzeniu sfery. Po powrocie metody kod poniżej wykona jedną aktualizację ostateczną do sfery, zmieniając jej kolor na niebieską.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Uruchom aplikację z **programu Visual Studio** jeszcze raz. Poruszaj się po głowie, a następnie naciśnij pozycję Air, aby umieścić swoją sferę. Gdy mamy wystarczającą liczbę ramek, sfera zmieni się na żółtą i rozpocznie się przekazywanie do chmury. Po zakończeniu przekazywania sfera zmieni kolor na niebieski. Opcjonalnie można również użyć okna danych wyjściowych w **programie Visual Studio** do monitorowania komunikatów dziennika wysyłanych przez aplikację. Będziesz w stanie obejrzeć zalecenia dotyczące tworzenia postępów, a także identyfikator kotwicy zwracanej przez chmurę po zakończeniu przekazywania.

> [!NOTE]
> Jeśli zostanie wyświetlony element "DllNotFoundException —: nie można załadować biblioteki DLL" AzureSpatialAnchors ": nie można odnaleźć określonego modułu." należy **wyczyścić** i **skompilować** rozwiązanie ponownie.

## <a name="locate-your-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Jedno zakotwiczenie zostanie przekazane do chmury. wszystko jest gotowe do ponownego zlokalizowania. Dodajmy do metody następujący kod `HandleTap()` . Ten kod będzie:

* Wywołanie `ResetSession()` , które spowoduje zatrzymanie `CloudSpatialAnchorSession` i usunięcie istniejącej niebieskie sfery z ekranu.
* Zainicjuj `CloudSpatialAnchorSession` ponownie. Możemy to zrobić, aby upewnić się, że zakotwiczenie, które zamierzamy znaleźć, pochodzi z chmury, a nie jako zakotwiczenia lokalnego.
* Utwórz **obserwatora** , który będzie szukać zakotwiczenia przekazanego do zakotwiczeń przestrzennych platformy Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Dodajmy teraz nasze `ResetSession()` metody i `CleanupObjects()` . Można je umieścić poniżej`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Teraz musimy podłączyć kod, który zostanie wywołany, gdy zakotwiczenie zakotwiczenia dla programu znajduje się w lokalizacji. W programie `InitializeSession()` Dodaj następujące wywołania zwrotne:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Teraz pozwala dodać kod, który zostanie utworzony, & umieścić zieloną sferę po zlokalizowaniu CloudSpatialAnchor. Spowoduje to również ponowne włączenie ekranu, aby można było wielokrotnie powtórzyć cały scenariusz: Utwórz kolejną kotwicę lokalną, przekaż ją i Znajdź ponownie.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

To wszystko. Uruchom aplikację z **programu Visual Studio** po raz ostatni, aby wypróbować cały scenariusz do końca. Poruszaj się po urządzeniu i umieść swoją białą sferę. Następnie kontynuuj przesuwanie głowy, aby przechwycić dane środowiska do momentu, gdy sfera zmieni kolor na żółty. Twoje lokalne zakotwiczenie zostanie przekazane, a SFERA zmieni kolor na niebiesko. Na koniec naciśnij swój ekran jeszcze raz, aby lokalne zakotwiczenie zostało usunięte, a następnie będziemy wysyłać zapytania o jego odpowiednik w chmurze. Kontynuuj przenoszenie urządzenia do momentu, gdy zakotwiczenie chmury nie zostanie umieszczone. Zielona kula powinna pojawić się w poprawnej lokalizacji i można wypłukać & powtórzyć cały scenariusz ponownie.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]