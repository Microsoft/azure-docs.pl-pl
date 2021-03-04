---
title: Wprowadzenie do kontrolki mapy systemu Android | Mapy Microsoft Azure
description: Zapoznaj się z Android SDK Azure Maps. Zobacz jak utworzyć projekt w Android Studio, zainstalować zestaw SDK i utworzyć mapę interaktywną.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5888a5f34ef65fc1015b6e73af1d03368a8329b2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098523"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Wprowadzenie do Azure Maps Android SDK

Android SDK Azure Maps jest biblioteką mapy wektorowej dla systemu Android. Ten artykuł przeprowadzi Cię przez proces instalowania Android SDK Azure Maps i ładowania mapy.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) .

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps Android SDK oferuje trzy różne sposoby ustawiania języka i widoku regionalnego mapy. Poniższy kod pokazuje, jak ustawić język na francuski ("fr-FR") i widok regionalny na "Auto".

Pierwsza opcja polega na przejściu języka i wyświetleniu informacji regionalnych do `AzureMaps` klasy przy użyciu metod statycznych `setLanguage` i `setView` globalnie. Spowoduje to ustawienie języka domyślnego i widoku regionalnego dla wszystkich kontrolek Azure Maps załadowanych w aplikacji.

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Alternatively use Azure Active Directory authenticate.
    //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

Druga opcja polega na przejściu języka i wyświetleniu informacji w kodzie XML kontrolki mapy.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Trzecia opcja polega na programowo ustawić język i widok regionalny mapy przy użyciu `setStyle` metody Maps. Można to zrobić w dowolnym momencie, aby zmienić język i widok regionalny mapy.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Oto przykład Azure Maps z językiem ustawionym na "fr-FR" i widoku regionalnym ustawionym na "Auto".

![Azure Maps, obraz mapy przedstawiający etykiety w języku francuskim](media/how-to-use-android-map-control-library/android-localization.png)

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="navigating-the-map"></a>Nawigowanie po mapie

Istnieje kilka różnych sposobów, w których mapowanie może być powiększane, przesuwane, obracane i mierzone. Poniżej przedstawiono szczegółowe informacje na temat różnych sposobów nawigowania po mapie.

**Powiększ mapę**

* Dotknij mapy dwoma palcami i szczypania razem, aby powiększyć lub rozłożyć palcami z zewnątrz, aby powiększyć.
* Naciśnij dwukrotnie mapę, aby powiększyć na jednym poziomie.
* Naciśnij dwukrotnie dwa palcami, aby powiększyć mapę o jeden poziom.
* Naciśnij dwukrotnie; po naciśnięciu klawisza, przytrzymaj palec na mapie i przeciągnij w górę, aby powiększyć, lub w dół, aby pomniejszyć.

**Panoramowanie mapy**

* Dotknij mapy i przeciągnij w dowolnym kierunku.

**Obróć mapę**

* Dotknij mapy dwoma palcami i Obróć.

**Wysokość mapy**

* Dotknij mapy dwoma palcami i przeciągnij je w górę lub w dół.

## <a name="azure-government-cloud-support"></a>Obsługa Azure Government w chmurze

Android SDK Azure Maps obsługuje chmurę Azure Government. Android SDK Azure Maps jest dostępny z tego samego repozytorium Maven. Aby nawiązać połączenie z wersją Azure Government w chmurze platformy Azure Maps, należy wykonać następujące zadania.

W tym samym miejscu, w którym są określone szczegóły uwierzytelniania Azure Maps, Dodaj następujący wiersz kodu, aby określić, że mapa ma korzystać z domeny chmury Azure Maps dla instytucji rządowych.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Podczas uwierzytelniania mapy i usług upewnij się, że Azure Maps szczegóły uwierzytelniania na platformie Azure Government Cloud.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dane nakładki na mapie:

> [!div class="nextstepaction"]
> [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Zmień style mapy w usłudze mapy systemu Android](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
