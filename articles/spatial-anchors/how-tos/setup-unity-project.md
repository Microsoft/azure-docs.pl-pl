---
title: Zainstaluj kotwice przestrzenne platformy Azure dla aparatu Unity
description: Konfigurowanie projektu środowiska Unity do korzystania z kotwic przestrzennych platformy Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076710"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurowanie kotwic przestrzennych platformy Azure w projekcie środowiska Unity

W tym przewodniku pokazano, jak rozpocząć pracę z zestawem SDK kotwic przestrzennych platformy Azure w projekcie aparatu Unity.

## <a name="project-requirements"></a>Wymagania dotyczące projektu

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Konfigurowanie projektu

Przed uwzględnieniem zestawu Azure przestrzenny zakotwiczenia w projekcie środowiska Unity należy zainstalować [wymagane](#project-requirements) pakiety za pomocą Menedżera pakietów aparatu Unity.

### <a name="download-asa-packages"></a>Pobierz pakiety ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importuj pakiety ASA
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
