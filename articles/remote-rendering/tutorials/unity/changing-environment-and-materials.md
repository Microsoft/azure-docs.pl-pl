---
title: Zmiana środowiska i materiałów
description: Samouczek pokazujący, jak modyfikować mapę i materiały obiektów w środowisku Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679618"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Samouczek: zmiana środowiska i materiałów

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Zmień mapę środowiska sceny.
> * Modyfikowanie parametrów materiału.
> * Załaduj tekstury niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że znasz [Samouczek: Praca z obiektami zdalnymi w środowisku Unity](working-with-remote-entities.md). Jednak potrzebny jest tylko projekt Unity, za pomocą którego można nawiązywać połączenie z sesjami i ładować model, jak pokazano w [samouczku: Konfigurowanie projektu Unity od podstaw](project-setup.md).

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków w folderze *Unity* , których można użyć jako odwołania.

## <a name="change-the-environment-map"></a>Zmień mapę środowiska

Zdalne renderowanie na platformie Azure obsługuje [skrzynki](../../overview/features/sky.md) pamięci (czasami nazywane również "mapami środowiskowymi"), aby symulować oświetlenie otoczenia. Jest to szczególnie przydatne, gdy obiekty używają *[renderowania opartego na fizycznie](../../overview/features/pbr-materials.md)*, jak nasze przykładowe modele. ARR zawiera również różnorodne tekstury wbudowanej przestrzeniki, które będą używane w tym samouczku.

Utwórz nowy skrypt o nazwie **RemoteSky** i dodaj go do nowej gry. Otwórz plik skryptu i zastąp go następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Należy zauważyć, `LoadTextureFromSASAsync` że wariant jest używany powyżej, ponieważ załadowano tekstury wbudowane. W przypadku ładowania z [połączonych magazynów obiektów BLOB](../../how-tos/create-an-account.md#link-storage-accounts)Użyj `LoadTextureAsync` wariantu. Przykładem tego, jak to działa dla modeli, można znaleźć w [sekcji Ładowanie modelu](../../concepts/models.md#loading-models).

Po uruchomieniu kodu i przełączeniu się przez mapy w przestrzenie czasu, zobaczysz radykalnie różne oświetlenie w modelu. Jednak tło pozostanie czarne i nie zobaczysz tekstury rzeczywistej wartości w tej samej wersji. Jest to zamierzone, ponieważ renderowanie tła byłoby rozpraszane z urządzeniem rzeczywistości rozszerzonej. W odpowiedniej aplikacji należy używać tekstur w formacie od sieci, które są podobne do rzeczywistego otoczenia świata, ponieważ ułatwiają one rzeczywiste Wyświetlanie obiektów.

## <a name="modify-materials"></a>Modyfikuj materiały

W poprzednim samouczku użyto [składników zastępujących stan](../../overview/features/override-hierarchical-state.md) , aby zmienić kolor odcienia wybranych obiektów. Teraz chcemy osiągnąć podobny efekt, ale należy to zrobić, modyfikując [materiał](../../concepts/materials.md) obiektu.

Najpierw potrzebujemy skryptu do wybierania obiektów, tak jak w [drugim samouczku](working-with-remote-entities.md). Jeśli nie masz jeszcze skryptu **RemoteRaycaster** , utwórz go teraz. Zastąp jego zawartość następującym kodem:

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
}
```

Dodaj składnik do obiektu gry *RemoteRendering* . Jest on odpowiedzialny za pobieranie obiektów pod myszą i Dodawanie składników *RemoteModelEntity* do pobranych obiektów. Ta klasa składnika to miejsce, w którym implementujemy rzeczywistą funkcjonalność zmiany materiału.

Jeśli nie masz jeszcze skryptu **RemoteModelEntity** , utwórz go i Zastąp jego zawartość tym kodem:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Gdy uruchamiasz ten kod, obiekty, które zostały aktywowane przy użyciu myszy, zostaną wyróżnione. Efekt jest podobny do tego, co zostało zrobione w samouczku 2, ale jego osiągnięcie jest inne. W tym miejscu zostanie wykorzystana Lista materiałów dla pobranego obiektu, a następnie zmodyfikowanie pierwszego koloru albedo.

> [!IMPORTANT]
> Należy pamiętać, że ta metoda podświetla poprawne części modelu, zależy od tego, jak model jest tworzony. Będzie to idealne działanie, jeśli każdy obiekt używa dokładnie jednego materiału. Jeśli jednak model nie ma relacji 1:1 między częściami i materiałami, powyższy kod algorytmie nie wykona odpowiednich czynności.

## <a name="use-a-different-texture"></a>Użyj innej tekstury

[Tekstury](../../concepts/textures.md) są zwykle częścią modelu źródłowego. Podczas [konwersji modelu](../../quickstarts/convert-model.md)wszystkie tekstury są konwertowane do niezbędnego formatu środowiska uruchomieniowego i pakowane w końcowym pliku modelu. Aby zastąpić teksturę w czasie wykonywania, należy ją zapisać w [formacie pliku DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) i przekazać do usługi Azure Blob Storage. Zapoznaj się z [tym przewodnikiem Szybki Start](../../quickstarts/convert-model.md) dotyczącym tworzenia kontenera obiektów blob platformy Azure. Po utworzeniu kontenera obiektów BLOB można go otworzyć w Eksplorator usługi Azure Storage i przekazać plik za pomocą przeciągania i upuszczania.

Po stronie środowiska uruchomieniowego można rozwiązać element zawartości tekstury w usłudze BLOB Storage na dwa różne sposoby:

* Tekstury adresów według identyfikatora URI sygnatury dostępu współdzielonego. W tym celu kliknij prawym przyciskiem myszy przekazany plik i wybierz polecenie "**Pobierz sygnaturę dostępu współdzielonego**" z menu kontekstowego. Użyj tego identyfikatora URI sygnatury `LoadTextureFromSASAsync` dostępu współdzielonego z wariantem funkcji (Zobacz przykładowy kod poniżej).
* Bezpośrednie rozwiązanie tekstury według parametrów magazynu obiektów BLOB w przypadku, gdy [Magazyn obiektów BLOB jest połączony z kontem](../../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania jest `LoadTextureAsync`w tym przypadku.

Teraz otwórz skrypt **RemoteModelEntity** , Dodaj następujący kod i Usuń zduplikowane funkcje:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Uruchom ten kod i umieść kursor nad modelem. Jeśli model ma prawidłowe współrzędne UV, powinna zostać wyświetlona tekstura. W przeciwnym razie zmiana koloru może być zauważalna.

## <a name="next-steps"></a>Następne kroki

Zakończymy nasze serie wprowadzające, jak używać zdalnego renderowania platformy Azure w środowisku Unity. W następnym kroku należy zapoznać się z najważniejszymi pojęciami dotyczącymi ARR, takimi jak [sesje](../../concepts/sessions.md), [jednostki](../../concepts/entities.md)i [modele](../../concepts/models.md) , aby skompilować bardziej szczegółowe informacje. Istnieją również różne funkcje, takie jak [sygnalizatory](../../overview/features/lights.md), [renderowanie konspektu](../../overview/features/outlines.md), [przesłaniania hierarchiczne](../../overview/features/override-hierarchical-state.md)i [materiały](../../concepts/materials.md) , które należy poznać w bardziej szczegółowy sposób.

> [!div class="nextstepaction"]
> [Obiekty i składniki gier Unity](../../how-tos/unity/objects-components.md)
