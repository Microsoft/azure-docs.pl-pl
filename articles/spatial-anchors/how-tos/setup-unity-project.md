---
title: Zainstaluj kotwice przestrzenne platformy Azure dla aparatu Unity
description: Konfigurowanie projektu środowiska Unity do korzystania z kotwic przestrzennych platformy Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: da983719dc66656aa28cab4aea0bae558c2a7162
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530424"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity

W tym przewodniku pokazano, jak rozpocząć pracę z zestawem SDK kotwic przestrzennych platformy Azure w projekcie aparatu Unity.

## <a name="requirements"></a>Wymagania

Kotwice przestrzenne platformy Azure obecnie obsługują technologię Unity 2019,4 (LTS) z następującymi konfiguracjami.

* Środowisko Unity 2019,4 z AR Foundation 3,1 jest obsługiwane przez kotwice przestrzenne platformy Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Konfigurowanie projektu

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Dodawanie pakietów Menedżera pakietów Unity do projektu](#tab/UPMPackage)

Kotwice przestrzenne platformy Azure dla aparatu Unity są obecnie dystrybuowane przy użyciu pakietów Menedżera pakietów Unity (UPM). Te pakiety można znaleźć w naszym [rejestrze npm](https://bintray.com/microsoft/AzureMixedReality-NPM). Aby dowiedzieć się więcej na temat pracy z rejestrami pakietów objętych zakresem w projekcie Unity, zobacz oficjalną dokumentację aparatu Unity [tutaj](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Dodawanie rejestru do projektu środowiska Unity

1. W Eksploratorze plików przejdź do folderu projektu aparatu Unity `Packages` . Otwórz plik manifestu projektu, `manifest.json` w edytorze tekstu.
2. W górnej części pliku, na tym samym poziomie, co `dependencies` sekcja, Dodaj następujący wpis, aby dołączyć rejestr zakotwiczeń przestrzennych platformy Azure do projektu. `scopedRegistries`Wpis informuje aparat Unity, gdzie należy szukać pakietów zestawu SDK kotwice przestrzenne platformy Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Dodawanie pakietów SDK do projektu środowiska Unity

| Platforma | Nazwa pakietu                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. przestrzenny — kotwice — SDK. Android |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-SDK. iOS     |
| HoloLens | com. Microsoft. Azure. przestrzenny — kotwice — SDK. Windows |

1. Dla każdej platformy (Android/iOS/HoloLens), którą chcesz obsłużyć w projekcie, Dodaj wpis z nazwą pakietu i wersją pakietu do `dependencies` sekcji w manifeście projektu. Zobacz przykład poniżej.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Zapisz i Zamknij `manifest.json` plik. Gdy wrócisz do aparatu Unity, aparat Unity powinien automatycznie wykryć zmiany manifestu projektu i pobrać określone pakiety. Można rozwinąć `Packages` folder w widoku projektu, aby sprawdzić, czy odpowiednie pakiety zostały zaimportowane.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Tylko system Android: Konfigurowanie pliku mainTemplate. Gradle

1. Przejdź do pozycji **Edytuj**  >  **Ustawienia projektu**  >  **odtwarzacz**.
2. W **panelu Inspektora** **Ustawienia odtwarzacza**wybierz ikonę **systemu Android** .
3. W sekcji **kompilacja** zaznacz pole wyboru **niestandardowy główny szablon Gradle** , aby wygenerować niestandardowy szablon Gradle w lokalizacji `Assets\Plugins\Android\mainTemplate.gradle` .
4. Otwórz `mainTemplate.gradle` plik w edytorze tekstu.
5. W `dependencies` sekcji Wklej następujące zależności:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Gdy wszystko będzie gotowe, Twoja `dependencies` sekcja powinna wyglądać następująco:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importuj pakiet zasobów](#tab/UnityAssetPackage)

> [!WARNING]
> Dystrybucja pakietu zasobów środowiska Unity zestawu Azure przestrzenny zakotwiczenia jest przestarzała w wersji 2.5.0 i nie jest już dostępna w 2.6.0.

1. Pobierz `AzureSpatialAnchors.unitypackage` plik dla wersji, która ma być docelowa z [wydań usługi GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.unity3d.com/Manual/AssetPackagesImport.html) , aby zaimportować pakiet zasobów aparatu Unity do projektu.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Tworzenie i lokalizowanie kotwic w środowisku Unity](./create-locate-anchors-unity.md)
