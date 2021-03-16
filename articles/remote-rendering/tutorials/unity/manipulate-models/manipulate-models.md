---
title: Manipulowanie modelami
description: Manipuluj zdalnie renderowanymi modelami, przenosząc, obracając skalowanie i nie tylko
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 301d6eb0867604a6e780eb1d059eef0c153f246f
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574513"
---
# <a name="tutorial-manipulating-models"></a>Samouczek: manipulowanie modelami

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Dodawanie zakresu wizualizacji i manipulacji wokół zdalnych renderowanych modeli
> * Przenoszenie, obracanie i skalowanie
> * Raycast z zapytaniami przestrzennymi
> * Dodawanie prostych animacji dla zdalnie renderowanych obiektów

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek kompiluje się w [samouczku: interfejsy i modele niestandardowe](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Wykonaj zapytanie dotyczące powiązań obiektów zdalnych i Zastosuj do granic lokalnych

Aby można było korzystać z obiektów zdalnych, musimy mieć lokalną reprezentację w celu współpracy z pierwszym. [Granice obiektów](../../../concepts/object-bounds.md) są przydatne do szybkiego manipulowania obiektami zdalnymi. Do powiązań zdalnych można wykonywać zapytania z ARR, używając lokalnej jednostki jako odwołania. Powiązane są zapytania po załadowaniu modelu do sesji zdalnej.

Granice modelu są definiowane przez pole, które zawiera cały model — podobnie jak [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html)środowiska Unity, które ma wartość centrum i rozmiar zdefiniowany dla osi x, y, z. W rzeczywistości będziemy używać **BoxCollider** aparatu Unity do reprezentowania granic modelu zdalnego.

1. Utwórz nowy skrypt w tym samym katalogu, w którym znajduje się **RemoteRenderedModel** i nadaj mu nazwę **RemoteBounds**.
1. Zastąp zawartość skryptu następującym kodem:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Jeśli zobaczysz błąd w programie Visual Studio, *Funkcja "X" nie jest dostępna w języku C# 6. Użyj języka w wersji 7,0 lub nowszej*, ten błąd można bezpiecznie zignorować. Jest to związane z rozwiązaniem i generowaniem projektu środowiska Unity.

    Ten skrypt należy dodać do tego samego programu gameobject co skrypt implementujący  **BaseRemoteRenderedModel**. W takim przypadku oznacza to **RemoteRenderedModel**. Podobnie jak w przypadku poprzednich skryptów, ten kod początkowy będzie obsługiwał wszystkie zmiany stanu, zdarzenia i dane związane ze zdalnymi ograniczeniami.

    Istnieje tylko jedna metoda do zaimplementowania: **QueryBounds**. **QueryBounds** Pobiera granice asynchronicznie, pobiera wyniki zapytania i stosuje je do lokalnego **BoxCollider**.

    Metoda **QueryBounds** jest prosta: wysyła zapytanie do zdalnej sesji renderowania i oczekuje na wynik.

1. Zastąp metodę **QueryBounds** następującą ukończoną metodą:

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        var remoteBounds = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    Sprawdzimy wynik zapytania, aby sprawdzić, czy zakończyło się pomyślnie. Jeśli tak, Konwertuj i Zastosuj zwrócone powiązania w formacie, który może akceptować **BoxCollider** .

Teraz, gdy skrypt **RemoteBounds** jest dodawany do tego samego obiektu gry co **RemoteRenderedModel**, **BoxCollider** zostanie dodana w razie potrzeby i gdy model osiągnie swój `Loaded` stan, granice zostaną automatycznie zbadane i zastosowane do **BoxCollider**.

1. Korzystając z **TestModel** gameobject utworzonego wcześniej, Dodaj składnik **RemoteBounds** .
1. Upewnij się, że skrypt został dodany.

     ![Dodaj składnik RemoteBounds](./media/remote-bounds-script.png)

1. Uruchom ponownie aplikację. Wkrótce po załadowaniu modelu zostaną wyświetlone granice dla obiektu zdalnego. Zobaczysz coś podobne do poniższych wartości:

     ![Zrzut ekranu pokazujący przykład zdalnego powiązania obiektu.](./media/updated-bounds.png)

Teraz mamy lokalną **BoxCollider** skonfigurowaną z dokładnymi granicami obiektu Unity. Granice umożliwiają wizualizację i interakcję przy użyciu tych samych strategii, które są używane dla lokalnie renderowanego obiektu. Na przykład skrypty, które zmieniają transformację, fizyką i nie tylko.

## <a name="move-rotate-and-scale"></a>Przenoszenie, obracanie i skalowanie  

Przesuwanie, obracanie i skalowanie zdalnie renderowane obiekty działa tak samo jak każdy inny obiekt Unity. **RemoteRenderingCoordinator** w swojej `LateUpdate` metodzie wywołuje `Update` obecnie aktywną sesję. Część co `Update` to jest synchronizacja jednostki modelu lokalnego transformacje ze swoimi zdalnymi odpowiednikami. Aby przenieść, obrócić lub skalować zdalnie renderowany model, wystarczy przenieść, obrócić lub skalować transformację obiektu gameobject reprezentującego model zdalny. W tym miejscu zmienimy transformację nadrzędnego obiektu gameobject, do którego dołączono skrypt **RemoteRenderedModel** .

Ten samouczek używa MRTK do interakcji z obiektem. Większość implementacji konkretnych MRTK na potrzeby przesuwania, obracania i skalowania obiektu jest poza zakresem tego samouczka. Istnieje kontroler widoku modelu, który jest wstępnie skonfigurowany w **AppMenu**, w menu **Narzędzia modeli** .

1. Upewnij się, że utworzono wcześniej **TestModel** gameobject w scenie.
1. Upewnij się, że **AppMenu** PREFAB znajduje się w scenie.
1. Naciśnij przycisk odtwarzania aparatu Unity, aby odtworzyć scenę i otworzyć menu **Narzędzia modeli** wewnątrz **AppMenu**.
![Kontroler widoku](./media/model-with-view-controller.png)

**AppMenu** zawiera **Narzędzia modelu** podmenu, które implementują kontroler widoku do powiązania z modelem. Gdy Graobject zawiera składnik **RemoteBounds** , kontroler widoku doda składnik [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) , który jest składnikiem MRTK, który renderuje pole ograniczenia wokół obiektu za pomocą **BoxCollider**. [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html), który jest odpowiedzialny za interakcje. Połączone skrypty umożliwią nam przenoszenie, obracanie i skalowanie zdalnie renderowanego modelu.

1. Przesuń wskaźnik myszy do panelu gry i kliknij wewnątrz niego, aby nadać fokus.
1. Korzystając z [symulacji MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation), naciśnij i przytrzymaj lewy klawisz Shift.
1. Przekierowania symulowanej dłoni tak, aby promień dłoni został skierowany do modelu testowego.

    ![Promień wskazywany](./media/handray-engine.png)

1. Przytrzymaj lewym przyciskiem myszy i przeciągnij model, aby go przenieść.

Powinna zostać wyświetlona przenoszona zdalnie zawartość przeniesiona wraz z obwiednią. Może być zauważalne pewne opóźnienie lub opóźnienie między obwiednią a zawartością zdalną. To opóźnienie będzie zależeć od opóźnienia i przepustowości Internetu.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Rzutowanie i zapytania przestrzenne z modelami zdalnymi

Obiekt koliduje wokół modeli jest odpowiedni do współdziałania z całym modelem, ale nie jest wystarczająco szczegółowy, aby można było korzystać z poszczególnych części modelu. Aby rozwiązać ten problem, można użyć [rzutowania zdalnego Ray](../../../overview/features/spatial-queries.md#ray-casts). Zdalne rzutowanie Ray jest interfejsem API udostępnianym przez renderowanie zdalne na platformie Azure, aby rzutować promienie na zdalną scenę i zwracać wyniki trafień lokalnie. Ta technika może służyć do wybierania jednostek podrzędnych dużego modelu lub uzyskiwania informacji o wynikach trafień, takich jak pozycja, normalne i odległość.

Model testowy ma wiele jednostek podrzędnych, które mogą być badane i wybierane. Na razie wybór będzie wyprowadzał nazwę wybranej jednostki do konsoli aparatu Unity. Zapoznaj się z rozdziałem [materiały, oświetlenie i efekty,](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) aby wyróżnić wybraną jednostkę.

Najpierw utwórz otokę statyczną wokół zapytań rzutowania zdalnego ray. Ten skrypt zaakceptuje pozycję i kierunek w środowisku Unity, przekonwertuje go na typy danych akceptowane przez rzutowanie zdalnego odbierania i zwróci wyniki. Skrypt użyje `RayCastQueryAsync` interfejsu API.

1. Utwórz nowy skrypt o nazwie **RemoteRayCaster** i Zastąp jego zawartość następującym kodem:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Aparat Unity ma klasę o nazwie [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html), a zdalne renderowanie na platformie Azure ma klasę o nazwie [**RaycastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). Wielkie litery **C** są istotną różnicą, aby uniknąć błędów kompilacji.

    **RemoteRayCaster** zapewnia wspólny punkt dostępu do rzutowania zdalnych promieni na bieżącą sesję. Aby uzyskać bardziej szczegółowe informacje, zaimplementujmy procedurę obsługi wskaźników MRTK. Skrypt zaimplementuje `IMixedRealityPointerHandler` interfejs, który będzie informować MRTK, że ten skrypt powinien nasłuchiwać zdarzeń [wskaźnika rzeczywistości mieszanej](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) .

1. Utwórz nowy skrypt o nazwie **RemoteRayCastPointerHandler** i Zastąp kod następującym kodem:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

Metoda RemoteRayCastPointerHandler `OnPointerClicked` jest wywoływana przez MRTK, gdy wskaźnik "klika" na kolizji, jak nasz obiekt koliduje. Następnie `PointerDataToRemoteRayCast` jest wywoływana, aby przekonwertować wynik wskaźnika w punkt i kierunek. Ten punkt i kierunek są następnie używane do rzutowania zdalnego promienia w sesji zdalnej.

![Powiązane aktualizacje](./media/raycast-local-remote.png)

Wysyłanie żądań dla rzutowania promieniowego w ramach kliknięcia jest wydajną strategią wykonywania zapytań dotyczących obiektów zdalnych. Nie jest to jednak idealne środowisko użytkownika, ponieważ kursor koliduje z kolizją pola, a nie z samym modelem.

Można również utworzyć nowy wskaźnik MRTK, który często rzutuje swoje promienie w sesji zdalnej. Chociaż jest to bardziej skomplikowane podejście, środowisko użytkownika będzie lepiej. Ta strategia jest poza zakresem tego samouczka, ale przykładem tego podejścia można zobaczyć w aplikacji pokazowej, która znajduje się w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase).

Po pomyślnym zakończeniu rzutowania promienia w **RemoteRayCastPointerHandler**, trafienie `Entity` jest emitowane ze `OnRemoteEntityClicked` zdarzenia aparatu Unity. Aby odpowiedzieć na to zdarzenie, utworzymy skrypt pomocnika, który akceptuje `Entity` i wykonuje akcję. Zacznijmy od pobrania skryptu w celu wydrukowania nazwy `Entity` do dziennika debugowania.

1. Utwórz nowy skrypt o nazwie **RemoteEntityHelper** i Zastąp jego zawartość następującym:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Na utworzonej wcześniej **TestModel** gameobject Dodaj składnik **RemoteRayCastPointerHandler** i składnik **RemoteEntityHelper** .
1. Przypisz `EntityToDebugLog` metodę do `OnRemoteEntityClicked` zdarzenia. Gdy typ danych wyjściowych zdarzenia i typ danych wejściowych metody są zgodne, możemy użyć dynamicznego zdarzenia podłączenie środowiska Unity, które automatycznie przekaże wartość zdarzenia do metody.
    1. Utwórz nowe pole wywołania zwrotnego ![ Dodaj wywołanie zwrotne](./media/add-callback-remote-entity-clicked.png)
    1. Przeciągnij składnik **pomocnika jednostki zdalnej** do pola Object, aby odwołać się do obiektu nadrzędnegoobject ![ Assign](./media/assign-object.png)
    1. Przypisywanie wywołania zwrotnego `EntityToDebugLog` przypisania wywołania zwrotnego ![](./media/remote-entity-event.png)
1. Naciśnij kolejno pozycje Odtwórz w edytorze aparatu Unity, aby rozpocząć scenę, nawiązać połączenie z sesją zdalną i załadować model testowy.
1. Za pomocą symulacji dłoni MRTK naciśnij i przytrzymaj lewy klawisz Shift.
1. Przekierowania symulowanej dłoni tak, aby promień dłoni został skierowany do modelu testowego.
1. Długa kliknij, aby symulować wybór powietrza, wykonując `OnPointerClicked` zdarzenie.
1. Zwróć uwagę na konsolę Unity dla komunikatu dziennika o nazwie wybranej jednostki podrzędnej. Przykład: obiekt ![ podrzędny](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Synchronizowanie grafu zdalnego obiektu z hierarchią aparatu Unity

Do tego momentu oglądamy tylko jeden lokalny obiekt gameobject reprezentujący cały model. Jest to dobre rozwiązanie w przypadku renderowania i manipulowania całym modelem. Jeśli jednak chcemy zastosować efekty lub manipulować określonymi jednostkami podrzędnymi, należy utworzyć lokalną GameObjects do reprezentowania tych jednostek. Po pierwsze możemy zbadać go ręcznie w modelu testowym.

1. Rozpocznij scenę i Załaduj model testowy.
1. Rozwiń elementy podrzędne **TestModel** gameobject w hierarchii aparatu Unity i wybierz **TestModel_Entity** gameobject.
1. W Inspektorze kliknij przycisk *Pokaż elementy podrzędne* .
![Pokaż elementy podrzędne](./media/show-remote-children.png)
1. Kontynuuj rozszerzanie elementów podrzędnych w hierarchii i klikanie pozycji *Pokaż elementy podrzędne* do momentu wyświetlenia dużej listy elementów podrzędnych.
![Wszystkie elementy podrzędne](./media/test-model-children.png)

Lista dziesiątek jednostek będzie teraz wypełniać hierarchię. Wybranie jednego z nich spowoduje wyświetlenie `Transform` `RemoteEntitySyncObject` składników i w Inspektorze. Domyślnie każda jednostka nie jest automatycznie synchronizowana z każdą ramką, więc lokalne zmiany `Transform` nie są synchronizowane z serwerem. Możesz sprawdzić *synchronizację każdej ramki* , a następnie przenieść, skalować lub obrócić transformację w widoku sceny, w widoku sceny nie zobaczysz renderowanego modelu, Obejrzyj widok gry, aby zobaczyć, jak zmienia się położenie i rotacja modelu.

Ten sam proces można wykonać programowo, a pierwszy krok polega na zmodyfikowaniu konkretnych jednostek zdalnych.

1. Zmodyfikuj skrypt **RemoteEntityHelper** , aby zawierał również następującą metodę:

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Dodaj dodatkowe wywołanie zwrotne do  zdarzenia RemoteRayCastPointerHandler `OnRemoteEntityClicked` , ustawiając je na `MakeSyncedGameObject` .
![Dodatkowe wywołanie zwrotne](./media/additional-callback.png)
1. Za pomocą symulacji dłoni MRTK naciśnij i przytrzymaj lewy klawisz Shift.
1. Przekierowania symulowanej dłoni tak, aby promień dłoni został skierowany do modelu testowego.
1. Długa kliknij, aby symulować wybór powietrza, wykonując `OnPointerClicked` zdarzenie.
1. Zaznacz i rozwiń hierarchię, aby wyświetlić nowy obiekt podrzędny reprezentujący klikniętą jednostkę.
![Reprezentacja gameobject](./media/gameobject-representing-entity.png)
1. Po zakończeniu testowania Usuń wywołanie zwrotne dla `MakeSyncedGameObject` , ponieważ zostanie ono dołączone jako część innych efektów później.

> [!NOTE]
> Synchronizacja każdej ramki jest wymagana tylko w przypadku konieczności synchronizacji danych transformacji. Istnieją pewne koszty związane z synchronizacją, więc powinny być używane oszczędnie.

Tworzenie wystąpienia lokalnego i automatyczne synchronizowanie to pierwszy krok w operacji manipulowania jednostkami podrzędnymi. Te same techniki, z których korzystamy do manipulowania modelem jako całości, można również użyć w jednostkach podrzędnych. Na przykład po utworzeniu zsynchronizowanego wystąpienia lokalnego jednostki można wykonać zapytanie dotyczące jego granic i dodać procedury obsługi manipulowania, aby umożliwić przeniesienie go między oddziałami użytkownika.

## <a name="next-steps"></a>Następne kroki

Teraz można manipulować z zdalnie renderowanymi modelami i korzystać z nich. W następnym samouczku zajmiemy się modyfikowaniem materiałów, zmianami oświetlenia i zastosowaniem efektów do zdalnych renderowanych modeli.

> [!div class="nextstepaction"]
> [Dalej: rafinacja materiałów, oświetlenia i efektów](../materials-lighting-effects/materials-lighting-effects.md)