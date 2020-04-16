---
title: Obiekty i komponenty gry Unity
description: W tym artykule opisano metody specyficzne dla unity do pracy z jednostkami i składnikami renderowania zdalnego.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409870"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interakcja ze składnikami i obiektami gier środowiska Unity

Renderowanie zdalne platformy Azure (ARR) jest zoptymalizowane pod kątem ogromnej liczby obiektów (patrz [Ograniczenia).](../../reference/limits.md) Chociaż można zarządzać dużymi i złożonymi hierarchiami na hoście, replikowanie ich wszystkich w unity na urządzeniach o niskim pozycjnym jest niewykonalne.

W związku z tym gdy model jest ładowany na hoście, azure remote rendering dubluje informacje o strukturze modelu na urządzeniu klienckim (który spowoduje ruch sieciowy), ale nie replikuje obiektów i składników w Unity. Zamiast tego oczekuje się, że można zażądać potrzebnych obiektów gry Unity i składników ręcznie, tak, że można ograniczyć obciążenie do tego, co jest rzeczywiście potrzebne. W ten sposób masz większą kontrolę nad wydajnością po stronie klienta.

W związku z tym integracja Unity usługi Azure Remote Rendering jest wyposażony w dodatkowe funkcje do replikowania struktury renderowania zdalnego na żądanie.

## <a name="load-a-model-in-unity"></a>Ładowanie modelu w unity

Podczas ładowania modelu, można uzyskać odwołanie do obiektu głównego załadowanego modelu. To odwołanie nie jest obiektem gry Unity, ale można `Entity.GetOrCreateGameObject()`przekształcić go w jeden przy użyciu metody rozszerzenia . Ta funkcja oczekuje argumentu `UnityCreationMode`typu . Jeśli przejdziesz, `CreateUnityComponents`nowo utworzony obiekt gry Unity zostanie dodatkowo wypełniony składnikami serwera proxy dla wszystkich składników renderowania zdalnego, które istnieją na hoście. Zaleca się jednak, aby `DoNotCreateUnityComponents`preferować , aby utrzymać minimalne nadgłowe.

### <a name="load-model-with-task"></a>Załaduj model z zadaniem

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Model ładowania z coroutines Unity

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Załaduj model z wzorem await

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Powyższe przykłady kodu używane ścieżki ładowania modelu za pośrednictwem sygnatury dostępu Współdzielonego, ponieważ model wbudowany jest ładowany. Adresowanie modelu za pośrednictwem kontenerów obiektów blob (przy użyciu `LoadModelAsync` i) `LoadModelParams`działa w pełni analogicznie.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Tworzenie obiektu gry Unity niejawnie dodaje `RemoteEntitySyncObject` składnik do obiektu gry. Ten składnik jest używany do synchronizowania transformacji jednostki z serwerem. Domyślnie `RemoteEntitySyncObject` wymaga od użytkownika `SyncToRemote()` jawnego wywołania, aby zsynchronizować lokalny stan Unity z serwerem. Włączenie `SyncEveryFrame` spowoduje automatyczną synchronizację obiektu.

Obiekty z `RemoteEntitySyncObject` może ich zdalnych dzieci wystąpienia i wyświetlane w edytorze Unity za pomocą przycisku **Pokaż dzieci.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Składniki otoki

[Składniki](../../concepts/components.md) dołączone do jednostek renderowania zdalnego `MonoBehavior`są udostępniane unity za pośrednictwem serwera proxy s. Te serwery proxy reprezentują składnik zdalny w unity i przekazuje wszystkie modyfikacje do hosta.

Aby utworzyć składniki zdalnego renderowania `GetOrCreateArrComponent`serwera proxy, użyj metody rozszerzenia:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Okresy istnienia sprzężonych

Okres istnienia [jednostki](../../concepts/entities.md) zdalnej i obiektu gry Unity jest `RemoteEntitySyncObject`połączony, gdy są one powiązane przez . Jeśli zadzwonisz `UnityEngine.Object.Destroy(...)` z takim obiektem gry, jednostka zdalna również zostanie usunięta.

Aby zniszczyć obiekt gry Unity, bez wpływu na `Unbind()` jednostkę `RemoteEntitySyncObject`zdalną, musisz najpierw wywołać plik .

To samo dotyczy wszystkich składników serwera proxy. Aby zniszczyć tylko reprezentację po stronie `Unbind()` klienta, należy najpierw wywołać składnik serwera proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie usługi Remote Rendering dla środowiska Unity](unity-setup.md)
* [Samouczek: Praca z jednostkami zdalnymi w unity](../../tutorials/unity/working-with-remote-entities.md)
