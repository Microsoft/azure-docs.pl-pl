---
title: Poprawianie materiałów, oświetlenia i efektów
description: Modyfikuj materiały i oświetlenie modelu. Dodawanie dodatkowych efektów, takich jak konspekty i płaszczyzny wycinania.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 0580614468d4003b3640fd4df08ff02f3a1c8476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021072"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Samouczek: poprawianie materiałów, oświetlenia i efektów

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyróżnij i Kreśl modele oraz składniki modelu
> * Zastosuj różne materiały do modeli
> * Wycinek poprzez modele z wyciętymi płaszczyznami
> * Dodawanie prostych animacji dla zdalnie renderowanych obiektów

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek kompiluje się w [samouczku: manipulowanie modelami](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Podświetlanie i tworzenie konspektu

Udostępnienie wizualnej opinii użytkownikowi jest ważnym elementem środowiska użytkownika w dowolnej aplikacji. Zdalne renderowanie na platformie Azure udostępnia mechanizmy przesyłania opinii wizualnych za pomocą [zastąpień stanu hierarchicznego](../../../overview/features/override-hierarchical-state.md). Zastąpienia stanu hierarchicznego są implementowane za pomocą składników dołączonych do lokalnych wystąpień modeli. Dowiesz się, jak utworzyć te wystąpienia lokalne podczas [synchronizowania zdalnego grafu obiektów z hierarchią aparatu Unity](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Najpierw utworzymy otokę wokół składnika [**HierarchicalStateOverrideComponent**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) . **HierarchicalStateOverrideComponent** jest skryptem lokalnym, który kontroluje zastąpienia w jednostce zdalnej. [**Zasoby samouczka**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) obejmują abstrakcyjną klasę bazową o nazwie **BaseEntityOverrideController**, która zostanie rozszerzona o tworzenie otoki.

1. Utwórz nowy skrypt o nazwie **EntityOverrideController** i Zastąp jego zawartość następującym kodem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

Głównym zadaniem **LocalOverride**jest utworzenie połączenia między nim i `RemoteComponent` . **LocalOverride** następnie umożliwia ustawianie flag stanu w składniku lokalnym, które są powiązane z jednostką zdalną. Zastąpienia i ich Stany są opisane na stronie [zastąpienia stanu hierarchicznego](../../../overview/features/override-hierarchical-state.md) . 

Ta implementacja powoduje jedynie przełączenie jednego stanu w danym momencie. Jednak w całości można połączyć wiele zastąpień dla pojedynczych jednostek i utworzyć kombinacje na różnych poziomach w hierarchii. Na przykład łączenie `Selected` i `SeeThrough` na pojedynczym składniku daje mu konspekt, a także sprawia, że jest on przezroczysty. Można też ustawić `Hidden` przesłonięcie jednostki głównej, aby `ForceOn` podczas `Hidden` przesłonięcia jednostki podrzędnej `ForceOff` ukryć wszystko z wyjątkiem elementu podrzędnego z przesłonięciem.

Aby zastosować Stany do jednostek, można zmodyfikować **RemoteEntityHelper** utworzone wcześniej.

1. Zmodyfikuj klasę **RemoteEntityHelper** , aby zaimplementować klasę abstrakcyjną **BaseRemoteEntityHelper** . Ta modyfikacja umożliwi korzystanie z kontrolera widoku dostępnego w **zasobach samouczka**. Powinien wyglądać tak, gdy zmodyfikowano:

    ```csharp
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Zastąp metody abstrakcyjne przy użyciu następującego kodu:

    ```csharp
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Ten kod gwarantuje, że składnik **EntityOverrideController** jest dodawany do jednostki docelowej, a następnie wywołuje jedną z metod przełączania. Jeśli to konieczne, na **TestModel** gameobject można wykonać te metody pomocnika, dodając **RemoteEntityHelper** jako wywołanie zwrotne do `OnRemoteEntityClicked` zdarzenia w składniku **RemoteRayCastPointerHandler** .

![Wywołania zwrotne wskaźnika](./media/pointer-event-callbacks.png)

Teraz, gdy te skrypty zostały dodane do modelu po nawiązaniu połączenia ze środowiskiem uruchomieniowym, kontroler widoku **AppMenu** powinien mieć włączone dodatkowe interfejsy umożliwiające współdziałanie z skryptem **EntityOverrideController** . Sprawdź menu **Narzędzia modelu** , aby wyświetlić odblokowane kontrolery widoku.

W tym momencie składniki **TestModel** gameobject powinny wyglądać następująco:

![Model testowy z dodatkowymi skryptami](./media/test-model-updated.png)

Oto przykład zastąpień na stosie dla pojedynczej jednostki. Używamy `Select` i `Tint` do zapewnienia zarówno konspektu, jak i kolorowania:

![Wybór odwzorowania modelu testu](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Wycięte płaszczyzny

[Wycięte płaszczyzny](../../../overview/features/cut-planes.md) to funkcja, którą można dodać do dowolnej jednostki zdalnej. Najczęściej można utworzyć nową jednostkę zdalną, która nie jest skojarzona z żadnymi danymi siatki do przechowywania składnika płaszczyzny wycinania. Pozycja i orientacja płaszczyzny wycinania są określane przez położenie i orientację zdalnej jednostki, do której jest dołączona.

Utworzymy skrypt, który automatycznie tworzy jednostkę zdalną, dodaje składnik wycinania płaszczyzny i zsynchronizuje transformację obiektu lokalnego z jednostką wycinania. Następnie możemy użyć **CutPlaneViewController** do zawijania płaszczyzny wycinania w interfejsie, który umożliwi nam manipulowanie nim.

1. Utwórz nowy skrypt o nazwie **RemoteCutPlane** i Zastąp jego kod następującym kodem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Ten kod rozszerza klasę **BaseRemoteCutPlane** znajdującą się w **zasobach samouczka**. Podobnie jak w przypadku modelu renderowanego zdalnie, ten skrypt dołącza i nasłuchuje `RemoteRenderingState` zmian z koordynatora zdalnego. Gdy koordynator osiągnie `RuntimeConnected` stan, podejmie próbę automatycznego połączenia, jeśli jest to konieczne. Istnieje również zmienna, która będzie `CutPlaneComponent` śledzona. Jest to składnik renderowania zdalnego platformy Azure, który synchronizuje się z płaszczyzną wycinania w sesji zdalnej. Zapoznaj się z tym, co należy zrobić, aby utworzyć płaszczyznę wycinania.

2. Zastąp `CreateCutPlane()` metodę zakończono wersją poniżej:

    ```csharp
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Tutaj tworzymy jednostkę zdalną i powiążemy ją z lokalną grą. Upewnij się, że obiekt zdalny zostanie zsynchronizowany z lokalną transformację przez ustawienie `SyncEveryFrame` do `true` . Następnie użyjemy wywołania, `CreateComponent` Aby dodać `CutPlaneComponent` obiekt do obiektu zdalnego. Na koniec skonfigurujemy płaszczyznę wycinania z ustawieniami zdefiniowanymi w górnej części działania. Zapoznaj się z tym, co jest potrzebne, aby oczyścić płaszczyznę wycinania przez implementację `DestroyCutPlane()` metody.

3. Zastąp `DestroyCutPlane()` metodę zakończono wersją poniżej:

    ```csharp
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Ponieważ obiekt zdalny jest dość prosty i trwa oczyszczanie zdalnego zakończenia (i utrzymywanie obiektu lokalnego), jest to proste, aby po prostu wywołać `Destroy` obiekt zdalny i usunąć odwołanie do niego.

**AppMenu** obejmuje kontroler widoku, który zostanie automatycznie dołączony do płaszczyzny wycinania i umożliwi Ci korzystanie z niej. Nie jest wymagane użycie **AppMenu** lub któregokolwiek z kontrolerów widoku, ale sprawiają, że zapewniają lepsze środowisko pracy. Teraz Przetestuj płaszczyznę wycinania i jej kontroler widoku.

1. Utwórz nową, pustą grę gameobject w scenie i nadaj jej nazwę **CutPlane**.
1. Dodaj składnik **RemoteCutPlane** do **CutPlane** gameobject.

   ![Konfiguracja składnika wycinania płaszczyzny](./media/cut-plane-config.png)

1. Naciśnij pozycję Odtwórz w edytorze aparatu Unity, aby załadować i połączyć się z sesją zdalną.
1. Korzystając z symulacji MRTK, porusz i obróć (przytrzymaj klawisz CTRL, aby obrócić) CutPlane, aby przenieść go wokół sceny. Obejrzyj wycinek IT w **TestModel** , aby odsłonić wewnętrzne składniki.

![Przykład wycinania płaszczyzny](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Konfigurowanie zdalnego oświetlenia

Zdalna sesja renderowania obsługuje pełny zakres [opcji oświetlenia](../../../overview/features/lights.md). Utworzymy skrypty dla tekstury dla [przestrzeni powietrznej](../../../overview/features/sky.md) i prostą mapę dla dwóch typów oświetlenia aparatu Unity do użycia z renderowaniem zdalnym.

### <a name="sky-texture"></a>Tekstura przestrzeniki

Istnieje wiele wbudowanych CubeMaps do wyboru podczas zmieniania tekstury z przestrzenią czasu. Są one ładowane do sesji i stosowane do tekstury w przestrzenice. Istnieje również możliwość [załadowania własnych tekstur](../../../concepts/textures.md) do użycia jako światła w sieci.

Utworzymy skrypt **RemoteSky** , który zawiera listę wbudowanych CubeMaps w formie parametrów ładowania. Następnie zezwolisz użytkownikowi na wybranie i załadowanie jednej z opcji.

1. Utwórz nowy skrypt o nazwie **RemoteSky** i Zastąp jego całą zawartość następującym kodem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSASParams> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSASParams> builtInTextures = new Dictionary<string, LoadTextureFromSASParams>()
        {
            {"Autoshop",new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    Najważniejszym elementem tego kodu jest zaledwie kilka wierszy:

    ```csharp
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Tutaj uzyskasz odwołanie do tekstury, która będzie używana przez załadowanie go do sesji z wbudowanego magazynu obiektów BLOB. Następnie wystarczy przypisać tę teksturę do sesji, `SkyReflectionTexture` Aby ją zastosować.

1. Utwórz pustą grę gameobject w Twojej scenie i nadaj jej nazwę **świetlik**.

1. Dodaj skrypt **RemoteSky** do programu **świetlik** gameobject.

    Przełączenie między światłami z przestrzenią powietrzną może odbywać się przez wywołanie `SetSky` jednego z kluczy ciągu zdefiniowanych w `AvailableCubemaps` . Kontroler widoku wbudowany w  **AppMenu** automatycznie tworzy przyciski i łączy swoje zdarzenia w celu wywołania `SetSky` z odpowiednimi kluczami.
1. Naciśnij pozycję Odtwórz w edytorze aparatu Unity i Autoryzuj połączenie.
1. Po nawiązaniu połączenia lokalnego środowiska uruchomieniowego z sesją zdalną przejdź do strony **AppMenu-> Session Tools — > zdalnej przestrzeni** czasu, aby poznać różne opcje dotyczące przestrzeni powietrznej i zobaczyć, jak wpływają one na **TestModel**.

### <a name="scene-lights"></a>Sygnalizatory sceny

Zdalne sygnalizatory sceny obejmują: punkt, punkt i kierunek. Podobnie jak w przypadku powyższej płaszczyzny wycinania, te sygnalizatory sceny są zdalnymi jednostkami ze składnikami dołączonymi do nich. Ważne jest, że oświetlenie zdalnej sceny próbuje dopasować oświetlenie w lokalnej scenie. Ta strategia nie zawsze jest możliwa, ponieważ wiele aplikacji aparatu Unity 2 nie używa renderowania fizycznie dla lokalnie renderowanych obiektów. Jednak do pewnego poziomu możemy symulować prostsze domyślne oświetlenie aparatu Unity.

1. Utwórz nowy skrypt o nazwie **RemoteLight** i Zastąp jego kod następującym kodem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Ten skrypt tworzy różne typy świateł zdalnych w zależności od typu lokalnego aparatu Unity, do którego jest dołączony skrypt. Lampa zdalna poduplikuje lokalne światło w: położenie, obrót, kolor i intensywność. W miarę możliwości zdalne oświetlenie również ustawi dodatkową konfigurację. To nie jest idealne dopasowanie, ponieważ światła Unity nie są światłami PBR.

1. Znajdź **DirectionalLight** gameobject w scenie. Jeśli po usunięciu domyślnej **DirectionalLight** z sceny: z górnego paska menu wybierz polecenie *gameobject-> lekki > DirectionalLight* , aby utworzyć nowe światełko w scenie.

1. Wybierz **DirectionalLight** gameobject i użyj przycisku **Dodaj składnik** , Dodaj skrypt **RemoteLight** .

1. Ponieważ ten skrypt implementuje klasę bazową `BaseRemoteLight` , można użyć podanego kontrolera widoku **AppMenu** do korzystania z zdalnego światła. Przejdź do **AppMenu > narzędzia sesji — > światło kierunkowe**.

    > [!NOTE]
    > Interfejs użytkownika w **AppMenu** został ograniczony do pojedynczego światła kierunkowego dla uproszczenia. Jednak nadal jest możliwe i zachęcamy do dodawania świateł punktów i dołączania do nich skryptu **RemoteLight** . Te dodatkowe sygnalizatory mogą być modyfikowane przez edytowanie właściwości oświetlenia środowiska Unity w edytorze. Musisz ręcznie zsynchronizować zmiany lokalne ze zdalnym sygnalizatorem za pomocą menu kontekstowego **RemoteLight** w Inspektorze:
    >
    > ![Zdalna synchronizacja ręczna](./media/sync-remote-light.png)

1. Naciśnij pozycję Odtwórz w edytorze aparatu Unity i Autoryzuj połączenie.

1. Po nawiązaniu połączenia ze środowiskiem uruchomieniowym z sesją zdalną wybierz pozycję i Zastąp kamerę (Użyj WASD i kliknij prawym przyciskiem myszy pozycję przesuń mysz), aby wyświetlić kontroler widoku światła kierunkowego w widoku. 
1. Aby zmodyfikować właściwości jasne, Użyj zdalnego kontrolera widoku. Korzystając z symulacji dłoniowej MRTK, pociągnij i obróć (przytrzymaj klawisz CTRL, aby obrócić) światło kierunkowe, aby zobaczyć efekt oświetlenia sceny.

    ![Światło kierunkowe](./media/directional-light-test.png)

## <a name="editing-materials"></a>Edytowanie materiałów

Zdalnie renderowane [materiały](../../../concepts/materials.md) można modyfikować, aby zapewnić dodatkowe efekty wizualne, precyzyjnie dostosować wizualizacje renderowanych modeli lub przekazać użytkownikom dodatkowe opinie. Istnieje wiele sposobów i wiele przyczyn modyfikacji materiału. Tutaj pokażemy, w jaki sposób zmienić kolor albedoi materiału i zmienić surowość i niesztywność materiału PBR.

> [!NOTE]
> W wielu przypadkach, jeśli funkcja lub efekt można zaimplementować przy użyciu **HierarchicalStateOverrideComponent**, idealnym rozwiązaniem jest użycie go zamiast modyfikowania materiału.

Utworzymy skrypt akceptujący jednostkę docelową i skonfiguruje kilka `OverrideMaterialProperty` obiektów, aby zmienić właściwości materiału jednostki docelowej. Zaczynamy od pobrania [**MeshComponent**](../../../concepts/meshes.md#meshcomponent)jednostki docelowej, która zawiera listę materiałów użytych w siatce. Dla uproszczenia używamy tylko pierwszego znalezionego materiału. Ta strategia algorytmie może bardzo łatwo działać w zależności od sposobu, w jaki została utworzona zawartość, dlatego prawdopodobnie chcesz utworzyć bardziej złożone podejście do wybierania odpowiedniego materiału.

Z materiału możemy uzyskiwać dostęp do wspólnych wartości, takich jak albedo. Najpierw materiały muszą być rzutowane w ich odpowiednim typie `PbrMaterial` lub `ColorMaterial` , aby można było pobrać ich wartości, jak pokazano w metodzie **GetMaterialColor** . Gdy mamy odwołanie do żądanego materiału, po prostu ustaw wartości, a ARR będzie obsługiwać synchronizację między właściwościami materiału lokalnego i materiałem zdalnym.

1. Utwórz skrypt o nazwie **EntityMaterialController** i Zastąp jego zawartość następującym kodem:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

`OverrideMaterialProperty`Typ powinien być wystarczająco elastyczny, aby umożliwić zmianę kilku innych wartości materiałowych, jeśli jest to konieczne. `OverrideMaterialProperty`Typ śledzi stan przesłonięcia, utrzymuje starą i nową wartość i używa delegata do ustawienia przesłonięcia. Na przykład zapoznaj się z `ColorOverride` :

```csharp
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Powoduje to utworzenie nowego `OverrideMaterialProperty` miejsca, w którym przesłonięcie będzie zawijać typ `Color` . Bieżący lub oryginalny kolor jest udostępniany podczas tworzenia zastąpienia. Podajemy również materiał z GENOTYPem do działania. Na koniec jest dostarczany delegat, który będzie stosował zastąpienie. Delegat jest metodą, która akceptuje materiał ARR i typ, który zastępuje przesłonięcie. Ta metoda jest najważniejszym elementem opisującym, jak ARR dostosowuje wartości materiału.

`ColorOverride`Używa `ApplyMaterialColor` metody do wykonywania swojej pracy:

```csharp
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Ten kod akceptuje materiał i kolor. Sprawdza on, jakiego rodzaju materiału jest następnie rzutowanie materiału, aby zastosować kolor.

`RoughnessOverride`I `MetalnessOverride` działają podobnie — przy użyciu `ApplyRoughnessValue` metod i `ApplyMetalnessValue` do wykonywania swoich zadań.

Następnie Przetestujmy kontroler materiału.

1. Dodaj skrypt **EntityMaterialController** do **TestModel** gameobject.
1. Naciśnij pozycję Odtwórz w aparacie Unity, aby rozpocząć scenę i nawiązać połączenie z ARR.
1. Po połączeniu środowiska uruchomieniowego z sesją zdalną i załadowaniem modelu przejdź do **AppMenu-> Model Tools — > edytuj materiał**
1. Wybierz jednostkę z modelu, korzystając z symulowanych wskazówek, aby kliknąć **TestModel**.
1. Upewnij się, że kontroler widoku materiału (**Narzędzia modelu AppMenu->— >edytuj materiał**) został zaktualizowany do jednostki docelowego.
1. Użyj kontrolera widoku materiału, aby dostosować materiał do jednostki wskazanej.

Ponieważ modyfikujemy tylko pierwszy materiał siatki, może nie być widoczna zmiana materiału. Użyj **SeeThrough** przesłaniania hierarchicznego, aby sprawdzić, czy modyfikowany materiał znajduje się w obrębie siatki.

![Przykład edycji materiału](./media/material-edit-example.png)

## <a name="next-steps"></a>Następne kroki

Gratulacje! Wdrożono wszystkie podstawowe funkcje renderowania zdalnego na platformie Azure. W następnym rozdziale dowiesz się, jak zabezpieczać zdalne renderowanie i magazyn obiektów BLOB na platformie Azure. Poniżej przedstawiono pierwsze kroki umożliwiające wydanie komercyjnej aplikacji używającej renderowania zdalnego platformy Azure.

> [!div class="nextstepaction"]
> [Dalej: Zabezpieczanie zdalnego renderowania i magazynu modelu platformy Azure](../security/security.md)