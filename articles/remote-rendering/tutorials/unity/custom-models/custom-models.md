---
title: Interfejsy i modele niestandardowe
description: Dodawanie kontrolerów widoków i pozyskiwanie modeli niestandardowych, które mają być renderowane przez zdalne renderowanie na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591667"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Samouczek: interfejsy i modele niestandardowe

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Dodaj zestaw narzędzi rzeczywistości mieszanej do projektu
> * Zarządzanie stanem modelu
> * Konfigurowanie Blob Storage platformy Azure na potrzeby pozyskiwania modelu
> * Przekazywanie i przetwarzanie modeli do renderowania

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek kompiluje [się w samouczku: wyświetlanie zdalnie renderowanego modelu](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Wprowadzenie do zestawu narzędzi rzeczywistości mieszanej (MRTK)

Zestaw narzędzi rzeczywistość Mixed (MRTK) to Międzyplatformowy zestaw narzędzi do tworzenia środowisk o rzeczywistości mieszanej. Będziemy używać MRTK 2.5.1 na potrzeby funkcji interakcji i wizualizacji.

Aby dodać MRTK, wykonaj [wymagane kroki](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) opisane w [podręczniku instalacji MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

Kroki te są następujące:
 - [Pobierz najnowsze pakiety MRTK Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Mimo że mówi "Najnowsza", użyj wersji 2.5.1 ze strony MRTK Release.
     - W tym samouczku używamy pakietu *Foundation* . *Rozszerzenia*, *Narzędzia* i *przykłady* pakietów nie są wymagane.
 - [Importowanie pakietów MRTK do projektu Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Przełączanie projektu środowiska Unity na platformę docelową](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Ten krok należy wykonać już w pierwszym rozdziale, ale teraz jest to dobry czas na podwójne sprawdzenie.
 - [Dodawanie MRTK do nowej sceny lub nowego projektu](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Możesz dodać MRTK do nowej sceny i ponownie dodać swoje koordynator i obiekty modelu/skrypty lub dodać MRTK do istniejącej sceny przy użyciu *zestawu rzeczywistości mieszanej — > dodać do sceny i skonfigurować* polecenie menu.

## <a name="import-assets-used-by-this-tutorial"></a>Importowanie zasobów używanych przez ten samouczek

Począwszy od tego rozdziału, zaimplementujmy prosty [wzorzec Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) dla wielu pokrytych materiałów. Częścią *modelu* wzorca jest kod specyficzny dla zdalnego renderowania platformy Azure oraz zarządzanie stanem związane z renderowaniem zdalnym platformy Azure. Części *widoku* i *kontrolera* wzorca są implementowane przy użyciu zasobów MRTK i niektórych skryptów niestandardowych. Można korzystać z *modelu* w tym samouczku bez zaimplementowanego tutaj *kontrolera widoku* . Ta separacja pozwala łatwo zintegrować kod znaleziony w tym samouczku do własnej aplikacji, w której przejdzie ona do *części wzorca* projektowego.

Wraz z wprowadzeniem MRTK istnieje wiele skryptów, prefabs i zasobów, które można teraz dodać do projektu, aby obsługiwać interakcje i wizualną opinię. Te zasoby, określane jako **elementy zawartości samouczka**, są powiązane z [pakietem zasobów aparatu Unity](https://docs.unity3d.com/Manual/AssetPackages.html), który jest dołączony do [usługi Azure Remote rendering](https://github.com/Azure/azure-remote-rendering) w serwisie GitHub w witrynie "\Unity\TutorialAssets\TutorialAssets.UNITYPACKAGE".

1. Klonuj lub Pobierz repozytorium git [zdalne renderowanie Azure](https://github.com/Azure/azure-remote-rendering), jeśli pobieranie wyodrębni plik zip do znanej lokalizacji.
1. W projekcie środowiska Unity wybierz pozycję *zasoby — > Importuj pakiet — > pakiet niestandardowy*.
1. W Eksploratorze plików przejdź do katalogu, w którym zostało sklonowane lub rozpakowane zdalne repozytorium renderowania platformy Azure, a następnie wybierz pozycję. UNITYPACKAGE znalezioną w aparacie **Unity-> TutorialAssets-> TutorialAssets. UNITYPACKAGE**
1. Wybierz przycisk **Importuj** , aby zaimportować zawartość pakietu do projektu.
1. W edytorze aparatu Unity wybierz pozycję *Mixed Reality Toolkit — > narzędzia — > Uaktualnij program do cieniowania standardowego MRTK dla lekkiego potoku renderowania* z górnego paska menu i postępuj zgodnie z monitami, aby uaktualnić program do cieniowania.

Po MRTK i umieszczeniu w projekcie zasobów samouczka Przełączmy profil MRTK na jeden, który jest odpowiedni dla samouczka.

1. Wybierz **MixedRealityToolkit** gameobject w hierarchii sceny.
1. W Inspektorze pod składnikiem **MixedRealityToolkit** Przełącz profil konfiguracji na *ARRMixedRealityToolkitConfigurationProfile*.
1. Naciśnij *kombinację klawiszy Ctrl + S* , aby zapisać zmiany.

Spowoduje to skonfigurowanie MRTK, głównie przy użyciu domyślnych profilów HoloLens 2. Podane profile są wstępnie skonfigurowane w następujący sposób:
 - Wyłącz Profiler (naciśnij 9, aby go włączyć/wyłączyć, lub powiedz "Pokaż/Ukryj Profiler" na urządzeniu).
 - Wyłącz kursor urządzenia Gaze oczu.
 - Włącz kliknięcia myszą Unity, aby można było kliknąć MRTK elementy interfejsu użytkownika za pomocą myszy zamiast symulowanej dłoni.

## <a name="add-the-app-menu"></a>Dodaj menu aplikacji

Większość kontrolerów widoku w tym samouczku działa w oparciu o abstrakcyjne klasy bazowe zamiast dla konkretnych klas. Ten wzorzec zapewnia większą elastyczność i pozwala nam udostępnić kontrolerom widoku, jednocześnie ułatwiając naukę kodu renderowania zdalnego na platformie Azure. Dla uproszczenia Klasa **RemoteRenderingCoordinator** nie ma dostarczonej klasy abstrakcyjnej, a jej kontroler widoku działa bezpośrednio względem konkretnej klasy.

Teraz możesz dodać PREFAB **AppMenu** do sceny, aby uzyskać wizualną opinię o bieżącym stanie sesji. Ten kontroler widoku będzie "odblokować" więcej kontrolerów widoku podmenu podczas implementowania i integrowania większej liczby funkcji ARR z sceną. Na razie **AppMenu** będzie mieć oznaczenie wizualne stanu ARR i przedstawić panel modalny używany przez użytkownika do autoryzowania aplikacji do łączenia się z arr.

1. Znajdź **AppMenu** PREFAB w obszarze *Assets/RemoteRenderingTutorial/prefabs/AppMenu*
1. Przeciągnij PREFAB **AppMenu** do sceny.
1. Prawdopodobnie zobaczysz okno dialogowe dla **importera tmp**, ponieważ po raz pierwszy dołączymy zasoby w postaci *siatki tekstu Pro* w scenie. Postępuj zgodnie z monitami, aby zaimportować informacje o programie **tmp Essentials**. Następnie zamknij okno dialogowe importera, przykłady i dodatki nie są zbędne.
1. **AppMenu** jest skonfigurowany do automatycznego podłączania i zapewniania modalności do wyrażania zgody na łączenie się z sesją, dzięki czemu można usunąć obejście umieszczone wcześniej. Na **RemoteRenderingCoordinator** gameobject Usuń obejście dla autoryzacji, którą wprowadziliśmy wcześniej, naciskając przycisk "-" w zdarzeniu **żądanie autoryzacji** .
 ![Usuń obejście ](./media/remove-bypass-event.png) .
1. Przetestuj kontroler widoku, naciskając **Odtwórz** w edytorze aparatu Unity.
1. W edytorze teraz, gdy MRTK jest skonfigurowany, możesz użyć kluczy WASD, aby zmienić położenie widoku i trzymać prawy przycisk myszy, a następnie przenieść wskaźnik myszy, aby zmienić kierunek widoku. Wypróbuj "kierowanie", aby uzyskać działanie dla kontrolek.
1. Na urządzeniu możesz podnieść swój komputer, aby móc wymyślić **AppMenu**, w edytorze Unity, użyć klawisza skrótu.
1. Jeśli dowiesz się, jak uzyskać informacje o menu, naciśnij klawisz "'M", aby wymyślić menu. Menu zostanie umieszczone blisko aparatu, aby można było łatwo obsłużyć interakcję.
1. Autoryzacja będzie teraz widoczna jako prośba o prawo od **AppMenu**, od teraz do, zostanie użyta do autoryzowania aplikacji do zarządzania zdalnymi sesjami renderowania.
 ![Autoryzacja interfejsu użytkownika](./media/authorize-request-ui.png)
1. Zatrzymaj odtwarzanie aparatu Unity, aby kontynuować pracę z samouczkiem.

## <a name="manage-model-state"></a>Zarządzanie stanem modelu

Teraz zostanie wdrożony nowy skrypt **RemoteRenderedModel** , który służy do śledzenia stanu, reagowania na zdarzenia, uruchamiania zdarzeń i konfiguracji. Zasadniczo **RemoteRenderedModel** przechowuje ścieżkę zdalną dla danych modelu w `modelPath` . Nasłuchuje zmian stanu w **RemoteRenderingCoordinator** , aby sprawdzić, czy powinien on automatycznie ładować lub zwalniać zdefiniowany przez niego model. Obiekt gameobject, do którego dołączono **RemoteRenderedModel** , będzie lokalnym elementem nadrzędnym dla zawartości zdalnej.

Zwróć uwagę, że skrypt **RemoteRenderedModel** implementuje **BaseRemoteRenderedModel** z uwzględnieniem **zasobów samouczka**. Umożliwi to powiązanie zdalnego kontrolera widoku modelu z Twoim skryptem.

1. Utwórz nowy skrypt o nazwie **RemoteRenderedModel** w tym samym folderze, co **RemoteRenderingCoordinator**. Zastąp całą zawartość następującym kodem:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

W najbardziej podstawowych terminach **RemoteRenderedModel** przechowuje dane, które są konieczne do załadowania modelu (w tym przypadku identyfikator URI SAS lub *BUILTIN://* ) i śledzi stan modelu zdalnego. Gdy czas ładowania jest załadowana, `LoadModel` Metoda jest wywoływana w **RemoteRenderingCoordinator** , a jednostka zawierająca model jest zwracana w celu odwołania i wyładowania.

## <a name="load-the-test-model"></a>Załaduj model testu

Przetestujmy nowy skrypt, ponownie ładując model testowy. Utworzymy obiekt gry, aby zawierał skrypt i będzie elementem nadrzędnym dla modelu testowego.

1. Utwórz nowy pusty obiekt gry w scenie i nadaj mu nazwę **TestModel**.
1. Dodaj skrypt *RemoteRenderedModel* do **TestModel**.
![Dodaj składnik RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Wypełnij odpowiednio pola `Model Display Name` i `Model Path` z opcją "*TestModel*" i "*BUILTIN://Engine*".
![Określ szczegóły modelu](./media/add-model-script.png)
1. Umieść obiekt **TestModel** przed kamerą w pozycji **x = 0, y = 0, z = 3**.
![Position — obiekt](./media/test-model-position.png)
1. Upewnij się, że **AutomaticallyLoad** jest włączona.
1. Naciśnij pozycję **Odtwórz** w edytorze aparatu Unity, aby przetestować aplikację.
1. Przyznaj autoryzację, klikając przycisk *Połącz* , aby zezwolić aplikacji na tworzenie sesji i nawiązać połączenie z sesją i automatycznie załadować model.

Obejrzyj konsolę w miarę postępu aplikacji przez Stany. Należy pamiętać, że niektóre Stany mogą zająć trochę czasu i nie będą pokazywać postępu. Ostatecznie zobaczysz dzienniki z ładowania modelu, a następnie model testowy będzie renderowany w scenie.

Spróbuj przenieść i obrócić **TestModel** gameobject za pośrednictwem transformacji w Inspektorze lub w widoku sceny. Zostanie wyświetlony model Przenieś i obróć go w widoku gry.

![Dziennik aparatu Unity](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Inicjowanie obsługi administracyjnej Blob Storage na platformie Azure i pozyskiwanie modelu niestandardowego

Teraz możemy spróbować załadować własny model. W tym celu należy skonfigurować Blob Storage i na platformie Azure, przekazać i przekonwertować model, a następnie załadować model przy użyciu skryptu **RemoteRenderedModel** . Kroki ładowania modelu niestandardowego można bezpiecznie pominąć, jeśli nie masz własnego modelu do załadowania.

Wykonaj kroki opisane w [przewodniku szybki start: konwertowanie modelu do renderowania](../../../quickstarts/convert-model.md). Pomiń sekcję **wstawianie nowego modelu do przykładowej aplikacji szybkiego startu** na potrzeby tego samouczka. Gdy masz identyfikator URI *sygnatury dostępu współdzielonego* modelu, przejdź do następnego kroku poniżej.

## <a name="load-and-rendering-a-custom-model"></a>Ładowanie i renderowanie modelu niestandardowego

1. Utwórz nową pustą grę gameobject w scenie i nadaj jej nazwę podobną do modelu niestandardowego.
1. Dodaj skrypt *RemoteRenderedModel* do nowo utworzonej gry.
 ![Dodaj składnik RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Wypełnij `Model Display Name` odpowiednie nazwy dla modelu.
1. Wypełnij pole `Model Path` przy użyciu identyfikatora URI *sygnatury dostępu współdzielonego (SAS)* , który został utworzony w powyższym kroku pozyskiwania.
1. Umieśćobject przed kamerą na pozycji **x = 0, y = 0, z = 3.**
1. Upewnij się, że **AutomaticallyLoad** jest włączona.
1. Naciśnij pozycję **Odtwórz** w edytorze aparatu Unity, aby przetestować aplikację.

    Zostanie wyświetlona konsola, która zostanie wypełniona bieżącym stanem, a ostatecznie załadowanie przez model komunikatów o postępie. Model niestandardowy zostanie następnie załadowany do sceny.

1. Usuń niestandardowy obiekt modelu z sceny. Najlepszym doświadczeniem w tym samouczku będzie użycie modelu testowego. Chociaż wiele modeli jest na pewno obsługiwane w ARR, ten samouczek został zapisany w celu zapewnienia najlepszej obsługi pojedynczego modelu zdalnego w danym momencie.

## <a name="next-steps"></a>Następne kroki

Teraz możesz ładować własne modele do zdalnego renderowania platformy Azure i wyświetlać je w swojej aplikacji. Następnie przeprowadzimy Cię przez proces manipulowania modelami.

> [!div class="nextstepaction"]
> [Dalej: manipulowanie modelami](../manipulate-models/manipulate-models.md)
