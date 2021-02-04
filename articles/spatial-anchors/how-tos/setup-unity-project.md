---
title: Zainstaluj kotwice przestrzenne platformy Azure dla aparatu Unity
description: Konfigurowanie projektu środowiska Unity do korzystania z kotwic przestrzennych platformy Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550627"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity

W tym przewodniku pokazano, jak rozpocząć pracę z zestawem SDK kotwic przestrzennych platformy Azure w projekcie aparatu Unity.

## <a name="requirements"></a>Wymagania

Kotwice przestrzenne platformy Azure obecnie obsługują technologię Unity 2019,4 (LTS) z następującymi konfiguracjami.

* Środowisko Unity 2019,4 z AR Foundation 3,1 jest obsługiwane przez kotwice przestrzenne platformy Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Konfigurowanie projektu

### <a name="download-packages"></a>Pobierz pakiety
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Importowanie pakietów
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Tylko system Android: Konfigurowanie pliku mainTemplate. Gradle

1. Przejdź do pozycji **Edytuj**  >  **Ustawienia projektu**  >  **odtwarzacz**.
2. W **panelu Inspektora** **Ustawienia odtwarzacza** wybierz ikonę **systemu Android** .
3. W sekcji **kompilacja** zaznacz pole wyboru **niestandardowy główny szablon Gradle** , aby wygenerować niestandardowy szablon Gradle w lokalizacji `Assets\Plugins\Android\mainTemplate.gradle` .
4. Otwórz `mainTemplate.gradle` plik w edytorze tekstu.
5. W `dependencies` sekcji Wklej następujące zależności:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Gdy wszystko będzie gotowe, Twoja `dependencies` sekcja powinna wyglądać następująco:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Tworzenie i lokalizowanie kotwic w środowisku Unity](./create-locate-anchors-unity.md)
