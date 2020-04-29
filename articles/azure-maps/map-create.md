---
title: Tworzenie mapy za pomocą Azure Maps | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować mapę na stronie sieci Web przy użyciu zestawu Microsoft Azure Web SDK mapy.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802335"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule pokazano, jak utworzyć mapę i animować mapę.  

## <a name="loading-a-map"></a>Ładowanie mapy

Aby załadować mapę, Utwórz nowe wystąpienie [klasy map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Podczas inicjowania mapy, Przekaż identyfikator elementu DIV, aby renderować mapę i przekazać zestaw opcji do użycia podczas ładowania mapy. Jeśli w `atlas` przestrzeni nazw nie określono domyślnych informacji o uwierzytelnianiu, te informacje muszą być określone w opcjach mapy podczas ładowania mapy. Mapa ładuje wiele zasobów asynchronicznie dla wydajności. W związku z tym po utworzeniu wystąpienia mapy należy dołączyć zdarzenie `ready` lub `load` do mapy, a następnie dodać kod, który współdziała z mapą do programu obsługi zdarzeń. `ready` Zdarzenie zostało wyzwolone, gdy tylko mapa ma wystarczającą liczbę zasobów, z których załadowano program programowo. `load` Zdarzenie wyzwalane po całkowitym załadowaniu widoku mapy początkowej. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe obciążenie mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>podstawowe mapowanie</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na tej samej stronie można ładować wiele map. Wiele map na tej samej stronie może korzystać z tych samych lub różnych ustawień uwierzytelniania i języka.

## <a name="show-a-single-copy-of-the-world"></a>Pokaż pojedynczą kopię świata

Gdy mapa zostanie powiększona na szerokim ekranie, wiele kopii świata będzie wyświetlanych w poziomie. Ta opcja doskonale nadaje się w niektórych scenariuszach, ale w przypadku innych aplikacji pożądane jest wyświetlenie pojedynczej kopii świata. To zachowanie jest implementowane przez ustawienie opcji `renderWorldCopies` Maps na `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = FAŁSZ" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opcje mapy

Podczas tworzenia mapy istnieje kilka różnych typów opcji, które można przekazywać, aby dostosować sposób, w jaki działa mapa, jak wymieniono poniżej.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) i [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) są używane do określania obszaru, który ma być wyświetlany w mapie.
- [Opcje serviceoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) służą do określania sposobu, w jaki mapa powinna współdziałać z usługami, które umożliwiają mapowanie.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) są używane do określenia, czy mapa powinna mieć styl i być renderowany.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) są używane do określenia sposobu, w jaki mapa powinna dotrzeć, gdy użytkownik korzysta z mapy. 

Te opcje można również zaktualizować po załadowaniu mapy za pomocą `setCamera`funkcji, `setServiceOptions`, `setStyle`i. `setUserInteraction` 

## <a name="controlling-the-map-camera"></a>Sterowanie kamerą mapy

Istnieją dwa sposoby ustawiania obszaru wyświetlania mapy przy użyciu aparatu mapy. Opcje aparatu można ustawić podczas ładowania mapy. Lub można wywołać opcję w `setCamera` dowolnym momencie po załadowaniu mapy, aby programowo zaktualizować widok mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustawianie aparatu

Aparat mapy kontroluje zawartość wyświetlaną w okienku ekranu kanwy mapy. Opcje aparatu fotograficznego mogą być przesyłane do opcji mapy podczas inicjowania lub przekazywania do funkcji Maps `setCamera` .

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

W poniższym kodzie tworzony jest [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) i opcje wyśrodkowania i powiększania są ustawione. Właściwości mapy, takie jak centrum i poziom powiększenia, są częścią [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Utwórz mapę `CameraOptions` </a>przez Azure Location based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustawianie granic kamery

Aby zaktualizować aparat mapy, można użyć pola ograniczenia. Jeśli pole ograniczenia zostało obliczone na podstawie danych punktu, często warto również określić wartość dopełnienia pikseli w opcjach aparatu, aby uwzględnić rozmiar ikony. Dzięki temu punkty nie będą znajdować się na krawędzi okienka ekranu mapy.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

W poniższym kodzie [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) jest konstruowany za pośrednictwem `new atlas.Map()`. Właściwości mapy, takie `CameraBoundsOptions` jak można zdefiniować za pomocą funkcji [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) klasy map. Właściwości Bound i uzupełnienie są ustawiane przy `setCamera`użyciu.

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Utwórz mapę `CameraBoundsOptions` </a>przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animuj widok mapy

Podczas ustawiania opcji kamery mapy można również ustawić [Opcje animacji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) . Te opcje określają typ animacji i czas trwania, który należy wykonać, aby przenieść aparat.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

W poniższym kodzie pierwszy blok kodu tworzy mapę i ustawia style Enter i zoom map. W drugim bloku kodu dla przycisku Animuj jest tworzony program obsługi zdarzeń kliknięcia. Po kliknięciu tego przycisku `setCamera` funkcja jest wywoływana z niektórymi wartościami losowymi dla [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) i [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animuj widok mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Obejrzyj <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Widok mapy animacji</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Wypróbuj kod

Zapoznaj się z przykładami kodu. Można edytować kod JavaScript wewnątrz **karty js** i zobaczyć zmiany widoku mapy na **karcie wynik**. Możesz również kliknąć pozycję **Edytuj na CodePen**, w prawym górnym rogu i zmodyfikować kod w CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Zmienianie stylu mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Dodawanie kontrolek do mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
