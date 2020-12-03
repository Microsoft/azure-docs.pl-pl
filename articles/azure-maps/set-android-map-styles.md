---
title: Ustawianie stylu mapy przy użyciu Azure Maps Android SDK
description: Poznaj dwa sposoby ustawiania stylu mapy. Zapoznaj się z tematem jak używać Microsoft Azure Maps Android SDK w pliku układu lub klasie Activity, aby dostosować styl.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532487"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ustawianie stylu mapy przy użyciu Azure Maps Android SDK

W tym artykule opisano sposób ustawiania stylów mapy przy użyciu Android SDK Azure Maps. Azure Maps ma sześć różnych stylów map do wyboru. Aby uzyskać więcej informacji na temat obsługiwanych stylów mapy, zobacz [obsługiwane style mapy w Azure Maps](./supported-map-styles.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
3. Pobierz i zainstaluj [Android SDK Azure Maps](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Ustawianie stylu mapy w układzie

Styl mapy można ustawić w pliku układu dla klasy Activity. Edytuj `res > layout > activity_main.xml` , tak aby wyglądała następująco:

```XML
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`Powyższy atrybut ustawia styl mapy do **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, obraz mapy pokazujący styl jako grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Ustaw styl mapy w klasie MAINS

Styl mapy można również ustawić w klasie MAINS. Otwórz `java > com.example.myapplication > MainActivity.java` plik i Skopiuj poniższy fragment kodu do metody **OnCreate ()** . Ten kod ustawia styl mapy do **satellite_road_labels**.

>[!WARNING]
>Nie zaimportowano wymaganych klas Android Studio.  W związku z tym kod będzie zawierał pewne odwołania nierozpoznawalne. Aby zaimportować wymagane klasy, po prostu umieść kursor nad każdym nierozwiązanym odwołaniem i naciśnij klawisz `Alt + Enter` (Option + Return na komputerze Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, obraz mapy pokazujący styl jako satellite_road_labels":::