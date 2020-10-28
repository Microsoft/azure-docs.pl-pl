---
title: Ustawianie stylu mapy przy użyciu Azure Maps Android SDK | Mapy Microsoft Azure
description: Poznaj dwa sposoby ustawiania stylu mapy. Aby dostosować styl, zobacz jak używać Android SDK Azure Maps w pliku układu lub w klasie Activity.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 15dbe7d30652d0ace78bca4dc053757d57361c1a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895311"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ustawianie stylu mapy przy użyciu Azure Maps Android SDK

W tym artykule przedstawiono dwa sposoby ustawiania stylów mapy przy użyciu Android SDK Azure Maps. Azure Maps ma sześć różnych stylów map do wyboru. Aby uzyskać więcej informacji na temat obsługiwanych stylów mapy, zobacz [obsługiwane style mapy w Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć proces w tym artykule, należy zainstalować [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) w celu załadowania mapy.


## <a name="set-map-style-in-the-layout"></a>Ustawianie stylu mapy w układzie

Styl mapy można ustawić w pliku układu dla klasy Activity. Edytuj **> activity_main.xmlukładu > zasobów** , aby wyglądać następująco:

```XML
<?xml version="1.0" encoding="utf-8"?>
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

`mapcontrol_style`Powyższy atrybut ustawia styl mapy do **grayscale_dark** . 

<center>

![styl — grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ustaw styl mapy w klasie Activity

Styl mapy można ustawić w klasie Activity. Skopiuj poniższy fragment kodu do metody **OnCreate ()** `MainActivity.java` klasy. Ten kod ustawi styl mapy do **satellite_road_labels** .

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![styl — drogi satelitarne — etykiety](./media/set-android-map-styles/satellite-road-labels.png)</center>