---
title: Praca z jednostkami zdalnymi w środowisku Unity
description: Samouczek pokazujący sposób pracy z jednostkami ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310206"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Samouczek: Praca z jednostkami zdalnymi w środowisku Unity

[Samouczek: Konfigurowanie projektu Unity od podstaw](project-setup.md) przedstawia sposób konfigurowania nowego projektu środowiska Unity do pracy z renderowaniem zdalnym platformy Azure. W tym samouczku Przyjrzyjmy się najbardziej typowym funkcjom, które wymagają każdemu użytkownikowi.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wybierz obiekty korzystające z Cast ray.
> * Przesłoń Stany obiektów, takie jak kolor tinty, stan zaznaczenia i widoczność.
> * Usuń jednostki zdalne.
> * Przenoszenie zdalnych obiektów.
> * Wykorzystaj płaszczyzny wycinania, aby wyszukiwać wewnątrz obiektów.

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek jest oparty na [samouczku: Konfigurowanie projektu Unity od podstaw](project-setup.md).

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków w folderze *Unity* , których można użyć jako odwołania.

## <a name="pick-objects"></a>Wybierz obiekty

Chcemy współdziałać z obiektami, więc najpierw potrzebujemy do wyboru obiektów w obszarze kursora myszy.

Utwórz [Nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) o nazwie **RemoteRaycaster** i Zastąp jego całą zawartość następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Dodaj ten składnik do obiektu *RemoteRendering* w scenie.

> [!WARNING]
>
> Składnik *RemoteRaycaster* wymaga dołączenia składnika *ARRServiceUnity* do tego samego obiektu. *ARRServiceUnity* to Klasa pomocnika, która ułatwia dostęp do niektórych funkcji arr. Jednak może istnieć tylko jedno wystąpienie tego składnika w scenie. W związku z tym należy dodać wszystkie składniki, które wymagają *ARRServiceUnity* do tego samego gry.
> Aby uzyskać dostęp do funkcji ARR z wielu obiektów gry, należy dodać składnik *ARRServiceUnity* tylko do jednego z nich i odwołać się do niego w innych skryptach lub bezpośrednio uzyskać dostęp do funkcji arr.

Naciśnij kolejno pozycje Odtwórz, Połącz z sesją i Załaduj model. Teraz wskaż obiekty w scenie i obejrzyj dane wyjściowe konsoli. Powinien drukować nazwę obiektu każdej części, którą umieścisz nad.

## <a name="highlight-objects"></a>Wyróżnianie obiektów

Następnym krokiem jest przedstawienie opinii wizualnych, które części modelu wskazują na użytkownika. Aby to osiągnąć, dołączymy [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) do jednostki, która została pobrana. Ten składnik może służyć do włączania lub wyłączania różnych funkcji w obiekcie. W tym miejscu używamy go do ustawienia koloru tinty i włączenia [renderowania konturu](../../overview/features/outlines.md).

Utwórz inny plik skryptu o nazwie **RemoteModelEntity** i Zastąp jego zawartość następującym kodem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Nie przypisuj tego skryptu do żadnego obiektu gry, ponieważ zostanie on przypisywany programowo przez Poniższy kod.

W następnej kolejności musimy rozciągnąć nasze *RemoteRaycaster* , aby dodać składnik *RemoteModelEntity* do obiektu, który właśnie został wybrany.

Dodaj następujący kod do implementacji **RemoteRaycaster** i Usuń zduplikowane funkcje:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Uruchom projekt i wskaż model, zobaczysz, że otrzymujesz czerwoną tintę i biały kontur zaznaczenia.

## <a name="isolate-the-selected-object"></a>Izoluj wybrany obiekt

Innym zastosowaniem [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) jest możliwość przesłonięcia widoczności. Umożliwia to wyizolowanie zaznaczonego obiektu od reszty modelu. Otwórz skrypt **RemoteModelEntity** , Dodaj następujący kod i Usuń zduplikowane funkcje:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Ten kod polega na wykorzystaniu składnika zastępującego stan w obiekcie najwyższego poziomu w hierarchii, co sprawia, że wszystkie obiekty są niewidoczne. Następnie zastępuje widoczność ponownie w wybranym obiekcie, aby można było wyświetlić jeden obiekt. W związku z tym musimy utworzyć składnik zastąpienia stanu w obiekcie głównym.

Otwórz skrypt **RemoteRendering** i Wstaw poniższy kod w górnej części funkcji *LoadModel* :

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Na koniec potrzebuję metody przełączania widoczności. Otwórz skrypt **RemoteRaycaster** i Zastąp funkcję *Update* :

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Uruchom kod i kliknij prawym przyciskiem myszy część modelu. Pozostała część modelu zniknie i tylko wyróżniony element pozostanie widoczny.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Usuń wystąpienia obiektu gameobject zdalnych obiektów

Być może zauważono, że kod ciągle tworzy obiekty, ale nigdy nie czyści ich. Jest to również widoczne w panelu Hierarchia obiektów. Po rozszerzeniu zdalnej hierarchii obiektów podczas symulacji zobaczysz więcej i więcej obiektów pojawiających się za każdym razem, gdy umieścisz na nowej części modelu.

Wiele obiektów w scenie ma negatywny wpływ na wydajność. Należy zawsze czyścić obiekty, które nie są już potrzebne.

Wstaw poniższy kod do skryptu **RemoteRaycaster** i Usuń zduplikowane funkcje:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Przenoszenie obiektów

Następnym krokiem jest przeniesienie zaznaczonego obiektu. W skrypcie **RemoteRaycaster** Wstaw ten kod i Usuń zduplikowaną funkcję:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> W przypadku uruchomienia tego kodu można zauważyć, że nic się nie dzieje. Dzieje się tak, ponieważ zmiana przekształcenia obiektu nie powoduje automatycznej synchronizacji zmiany stanu na serwerze, ze względu na wydajność. Zamiast tego należy wykonać ręcznie wypychanie zmiany stanu do serwera lub włączenie **SyncEveryFrame** na składniku *RemoteEntitySyncObject* .

Otwórz skrypt **RemoteModelEntity** i Dodaj następujący wiersz:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Aby ponownie uruchomić kod, powinno być możliwe kliknięcie obiektu lewym przyciskiem myszy i przeciągnięcie go.

## <a name="add-a-cut-plane"></a>Dodaj płaszczyznę wycinania

Ostatnia funkcja, którą chcemy wypróbować w tym samouczku, używa [wycinania płaszczyzn](../../overview/features/cut-planes.md). Wycięta płaszczyzna odcinania części renderowanych obiektów, takich jak można się zajrzeć.

Utwórz nową Gręobject w **CutPlane**sceny. Utwórz nowy skrypt i Wywołaj go **RemoteCutPlane**. Dodaj składnik do nowej gry.

Otwórz plik skryptu i Zastąp jego zawartość następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Gdy uruchamiasz teraz swój kod, powinieneś zobaczyć, jak model jest wycięty przez płaszczyznę. Można wybrać obiekt *CutPlane* i przenieść go i obrócić w oknie *sceny* . Możesz włączyć i wyłączyć wycinanie płaszczyzny, wyłączając obiekt płaszczyzny wycinania.

## <a name="next-steps"></a>Następne kroki

Znasz teraz najważniejsze funkcje współdziałania z obiektami zdalnymi. W następnym samouczku Przyjrzyjmy się dostosowaniu wyglądu sceny.

> [!div class="nextstepaction"]
> [Samouczek: zmiana środowiska i materiałów](changing-environment-and-materials.md)
