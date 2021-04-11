---
title: Dane punktu klastrowania na mapie | Mapy Microsoft Azure
description: Dowiedz się, jak Klastrować dane punktów na mapach. Zobacz, jak używać zestawu SDK sieci Web Azure Maps do klastrowania danych, reagowania na zdarzenia myszy klastra i wyświetlania zagregowanych klastrów.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: f798b4a7964eba112ed9948b2db4f6d5c3edd1e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608361"
---
# <a name="clustering-point-data"></a>Dane punktu klastrowania

Podczas wizualizacji wielu punktów danych na mapie punkty danych mogą się nakładać. Nakładanie może spowodować, że mapa może stać się nieczytelna i trudne do użycia. Dane punktu klastrowania to proces łączenia danych punktów, które są blisko siebie i reprezentowania ich na mapie jako pojedynczy klastrowany punkt danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych. Podczas pracy z dużą liczbą punktów danych Użyj procesów klastrowania, aby poprawić środowisko użytkownika.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="enabling-clustering-on-a-data-source"></a>Włączanie klastrowania w źródle danych

Włącz klastrowanie w `DataSource` klasie, ustawiając `cluster` opcję na `true` . Ustaw `clusterRadius` , aby wybrać punkty w pobliżu i połączyć je z klastrem. Wartość `clusterRadius` jest w pikselach. Użyj `clusterMaxZoom` , aby określić poziom powiększenia, dla którego ma zostać wyłączona logika klastrowania. Oto przykład sposobu włączania klastrowania w źródle danych.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,
    
    //The radius in pixels to cluster points together.
    clusterRadius: 45,
    
    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Jeśli dwa punkty danych są blisko siebie, możliwe, że klaster nigdy nie ulegnie przerwie, niezależnie od tego, jak zamyka się użytkownik. Aby rozwiązać ten potrzeba, można ustawić `clusterMaxZoom` opcję wyłączania logiki klastrowania i po prostu wyświetlić wszystko.

`DataSource`Klasa zawiera również następujące metody związane z klastrem.

| Metoda | Typ zwracany | Opis |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | &lt; &lt; Geometria funkcji macierzy Promise &lt; , dowolny &gt; \| kształt&gt;&gt; | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i podklastrów. Podklastry będą funkcjami o właściwościach pasujących do ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Numer obietnicy &lt;&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub przerywanie. |
| getClusterLeaves (clusterId: Number, limit: Number, offset: Number) | &lt; &lt; Geometria funkcji macierzy Promise &lt; , dowolny &gt; \| kształt&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw wartość `limit` na, aby zwracała podzestaw punktów, i Użyj `offset` strony do za pomocą punktów. |

## <a name="display-clusters-using-a-bubble-layer"></a>Wyświetlanie klastrów przy użyciu warstwy bąbelkowej

Warstwa bąbelków to doskonały sposób renderowania punktów klastrowanych. Użyj wyrażeń, aby skalować promień i zmienić kolor na podstawie liczby punktów w klastrze. W przypadku wyświetlania klastrów przy użyciu warstwy bąbelków należy użyć osobnej warstwy do renderowania nieklastrowanych punktów danych.

Aby wyświetlić rozmiar klastra w górnej części bąbelka, użyj warstwy symbol z tekstem i nie używaj ikony.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe klastrowanie warstwy bąbelków" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>warstwą pęcherzykową bąbelki w warstwie Podstawowa</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Wyświetlanie klastrów przy użyciu warstwy symboli

Podczas wizualizacji punktów danych warstwa symboli automatycznie ukrywa symbole, które nakładają się na siebie, aby zapewnić oczyszczarkę interfejsu użytkownika. To zachowanie domyślne może być niepożądane, jeśli chcesz pokazać gęstość punktów danych na mapie. Można jednak zmienić te ustawienia. Aby wyświetlić wszystkie symbole, ustaw `allowOverlap` opcję Właściwości warstwy symboli `iconOptions` na `true` . 

Użyj klastrowania, aby pokazać gęstość punktów danych przy zachowaniu czystego interfejsu użytkownika. Poniższy przykład pokazuje, jak dodawać niestandardowe symbole i reprezentować klastry oraz poszczególne punkty danych przy użyciu warstwy symboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klastrowana warstwa symboli" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>warstwę symboli grupowanych</a> piórem według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klastrowanie i warstwa mapy cieplnej

Mapy cieplne to doskonały sposób wyświetlania gęstości danych na mapie. Ta metoda wizualizacji może samodzielnie obsłużyć dużą liczbę punktów danych. Jeśli punkty danych są klastrowane i rozmiar klastra jest używany jako waga mapy cieplnej, Mapa cieplna może obsłużyć jeszcze więcej danych. Aby osiągnąć tę opcję, należy ustawić `weight` opcję warstwy mapy cieplnej na `['get', 'point_count']` . Gdy promień klastra jest mały, Mapa cieplna będzie wyglądać niemal identycznie jak mapa cieplna korzystająca z nieklastrowanych punktów danych, ale będzie działać znacznie lepiej. Jednak im mniejsza jest wartość promienia klastra, tym dokładniejsza jest mapa cieplna, ale z mniejszą wydajnością.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa cieplna ważona klastra" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>z Azure Maps</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Zdarzenia myszy w klastrowanych punktach danych

Gdy zdarzenia myszy wystąpią na warstwie zawierającej klastrowane punkty danych, klastrowany punkt danych wraca do zdarzenia jako obiekt funkcji punktu GEOJSON. Ta funkcja wskazuje następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być używany ze źródłem danych `getClusterExpansionZoom` , `getClusterChildren` i `getClusterLeaves` metodami. |
| `point_count`             | liczba  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skraca wartość, `point_count` Jeśli jest długi. (na przykład 4 000 to 4K)  |

Ten przykład pobiera warstwę bąbelkową, która renderuje punkty klastra i dodaje zdarzenie kliknięcia. Gdy wyzwalane jest zdarzenie kliknięcia, kod oblicza i powiększa mapę do następnego stopnia powiększenia, przy czym klaster zostaje podzielony na siebie. Ta funkcja jest implementowana przy użyciu `getClusterExpansionZoom` metody `DataSource` klasy i `cluster_id` Właściwości klikniętego klastrowanego punktu danych.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom klastra" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/moZWeV/'>GetClusterExpansionZoom klastra</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Wyświetlanie obszaru klastra 

Dane punktów reprezentowane przez klaster są rozłożone na obszar. W tym przykładzie, gdy wskaźnik myszy jest aktywowany przez klaster, występują dwa główne zachowania. Najpierw poszczególne punkty danych zawarte w klastrze będą używane do obliczania wypukłych kadłubów. Następnie na mapie zostanie wyświetlony wyświetlona kadłub, aby wyświetlić obszar.  Kadłub wypukły jest wielokątem, który otacza zestaw punktów jak elastyczną i może być obliczany przy użyciu `atlas.math.getConvexHull` metody. Wszystkie punkty zawarte w klastrze mogą być pobierane ze źródła danych przy użyciu `getClusterLeaves` metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kadłub wypukłych obszarów klastra" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kadłubem obszar klastra piórem wypukły</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregowanie danych w klastrach

Często klastry są reprezentowane przy użyciu symbolu z liczbą punktów znajdujących się w klastrze. Jednak czasami pożądane jest dostosowanie stylu klastrów przy użyciu dodatkowych metryk. W przypadku agregacji klastra właściwości niestandardowe można tworzyć i wypełniać przy użyciu obliczeń [wyrażeń agregujących](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Agregacje klastrów można definiować w `clusterProperties` opcjach `DataSource` .

W poniższym przykładzie zastosowano wyrażenie agregujące. Kod oblicza liczbę na podstawie właściwości typu jednostki każdego punktu danych w klastrze. Gdy użytkownik kliknie klaster, zostanie wyświetlone okno podręczne z dodatkowymi informacjami o klastrze.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregacje klastra" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregaty klastra</a> piórem według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource — Klasa](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Obiekt DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [Atlas. Math — przestrzeń nazw](/javascript/api/azure-maps-control/atlas.math)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)