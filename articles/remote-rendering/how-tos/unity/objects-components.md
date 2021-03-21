---
title: Obiekty i składniki gier Unity
description: Opisuje metody specyficzne dla aparatu Unity do pracy z obiektami i składnikami renderowania zdalnego.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594150"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interakcja ze składnikami i obiektami gier środowiska Unity

Renderowanie zdalne na platformie Azure jest zoptymalizowane pod kątem ogromnej liczby obiektów (zobacz [ograniczenia](../../reference/limits.md)). Chociaż istnieje możliwość zarządzania dużymi i złożonymi hierarchiami na hoście, replikacja wszystkich w aparacie Unity na urządzeniach z niską ilością danych jest niemożliwa.

W związku z tym, gdy model jest ładowany na hoście, zdalne renderowanie na platformie Azure odzwierciedla informacje o strukturze modelu na urządzeniu klienckim (który będzie ponosić ruch sieciowy), ale nie replikuje obiektów i składników w aparacie Unity. Zamiast tego oczekuje na ręczne zażądanie wymaganych obiektów i składników aparatu Unity, aby można było ograniczyć obciążenie do tego, co jest rzeczywiście potrzebne. Dzięki temu masz większą kontrolę nad wydajnością po stronie klienta.

W związku z tym integracja aparatu Unity na potrzeby zdalnego renderowania na platformie Azure obejmuje dodatkowe funkcje umożliwiające replikację struktury renderowania zdalnego na żądanie.

## <a name="load-a-model-in-unity"></a>Ładowanie modelu w środowisku Unity

Podczas ładowania modelu otrzymujesz odwołanie do obiektu głównego załadowanego modelu. To odwołanie nie jest obiektem gier Unity, ale można je przekształcić w jeden przy użyciu metody rozszerzenia `Entity.GetOrCreateGameObject()` . Ta funkcja oczekuje argumentu typu `UnityCreationMode` . Jeśli zostanie przekazany `CreateUnityComponents` , nowo utworzony obiekt gier Unity zostanie również wypełniony składnikami serwera proxy dla wszystkich składników renderowania zdalnego, które istnieją na hoście. Zalecane jest, aby zapewnić, że `DoNotCreateUnityComponents` obciążenie jest minimalne.

### <a name="load-model-with-unity-coroutines"></a>Załaduj model przy użyciu procedur wspólnych dla aparatu Unity

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Załaduj model ze wzorcem await

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Powyższe przykłady kodu używały ścieżki ładowania modelu za pośrednictwem sygnatury dostępu współdzielonego, ponieważ jest ładowany wbudowany model. Adresowanie modelu za pośrednictwem kontenerów obiektów BLOB (przy użyciu `LoadModelAsync` i `LoadModelOptions` ) działa w sposób analogiczny.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Tworzenie obiektu gry Unity niejawnie dodaje `RemoteEntitySyncObject` składnik do obiektu Game. Ten składnik służy do synchronizowania transformacji jednostki z serwerem. Domyślnie program `RemoteEntitySyncObject` wymaga, aby użytkownik jawnie wywoływał `SyncToRemote()` synchronizację stanu lokalnego aparatu Unity z serwerem. Włączenie `SyncEveryFrame` spowoduje automatyczne zsynchronizowanie obiektu.

Obiekty z obiektami `RemoteEntitySyncObject` może mieć swoje zdalne elementy podrzędne, które są tworzone i wyświetlane w edytorze Unity za pomocą **:::no-loc text="Show children":::** przycisku.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Składniki otoki

[Składniki](../../concepts/components.md) dołączone do zdalnych obiektów renderowania są ujawniane w środowisku Unity za poorednictwem proxy `MonoBehavior` s. Te serwery proxy reprezentują składnik zdalny w środowisku Unity i przekazują wszystkie modyfikacje do hosta.

Aby utworzyć składniki zdalnego renderowania serwera proxy, użyj metody rozszerzenia `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Połączone okresy istnienia

Okres istnienia [jednostki](../../concepts/entities.md) zdalnej i obiektu gry Unity jest sprzężony, gdy są one powiązane przez `RemoteEntitySyncObject` . Jeśli wywołasz `UnityEngine.Object.Destroy(...)` taki obiekt gry, zdalna jednostka zostanie również usunięta.

Aby zniszczyć obiekt gier Unity bez wpływu na jednostkę zdalną, należy najpierw wywołać metodę `Unbind()` `RemoteEntitySyncObject` .

Ta sama wartość dotyczy wszystkich składników serwera proxy. Aby zniszczyć tylko reprezentację po stronie klienta, należy najpierw wywołać `Unbind()` składnik serwera proxy:

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
* [Samouczek: manipulowanie obiektami zdalnymi w środowisku Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)
