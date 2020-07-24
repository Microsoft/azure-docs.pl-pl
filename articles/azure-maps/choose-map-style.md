---
title: Zmień styl mapy w Azure Maps | Mapy Microsoft Azure
description: W tym artykule opisano funkcje związane z stylem dostępne w Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 815b76f2a1c8872ff01439b126cb2ba1cdf27953
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133371"
---
# <a name="change-the-style-of-the-map"></a>Zmienianie stylu mapy

Mapa obsługuje kilka różnych [opcji stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) , które można ustawić podczas inicjowania mapy lub w późniejszym czasie za pomocą funkcji Maps `setStyle` . W tym artykule pokazano, jak za pomocą tych opcji stylu dostosować wygląd map. Zapoznaj się z ustawieniem stylu podczas ładowania mapy i Dowiedz się, jak ustawić nowy styl mapy za pomocą kontrolki selektor stylów.

## <a name="set-the-style-options"></a>Ustawianie opcji stylu 

Opcje stylu można przesłać do mapy, gdy zostanie ona zainicjowana lub zaktualizowana później przy użyciu `setStyle` funkcji Maps.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Poniższe narzędzie pokazuje, jak różne opcje stylu zmieniają sposób renderowania mapy. Aby wyświetlić budynki 3W, Powiększ blisko miasta. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opcje stylu mapy</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Wybierz styl mapy podstawowej

Jedną z najczęściej używanych opcji stylu mapy jest zmiana stylu mapy podstawowej, która ma styl. Wiele [obsługiwanych stylów mapy w Azure Maps](supported-map-styles.md) są dostępne w zestawie SDK sieci Web. 

### <a name="set-base-map-style-on-map-load"></a>Ustaw styl mapy podstawowej podczas ładowania mapy


Styl mapy można określić podczas inicjowania mapy przez ustawienie `style` opcji. W poniższym kodzie `style` opcja mapy jest ustawiona na wartość `grayscale_dark` przy inicjacji.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ustawianie stylu podczas ładowania mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ustawianie stylu ładowania mapy</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizowanie stylu mapy podstawowej

 Styl mapy można zaktualizować przy użyciu `setStyle` funkcji i ustawiając `style` opcję na żądany styl mapy.

```javascript
map.setStyle({ style: 'satellite' });
```

W poniższym kodzie po załadowaniu wystąpienia mapy styl mapy zostanie zaktualizowany z `road` do `satellite` przy użyciu funkcji [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Aktualizowanie stylu</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Dodaj selektor stylów

Kontrolka selektora stylów oferuje łatwy do użycia przycisk z panelem wysuwanym, który może być używany przez użytkownika końcowego do zmiany stylu mapy. Selektor stylów ma dwie różne opcje układu. Domyślnie selektor stylu używa `icons` układu i wyświetla wszystkie style mapy jako poziomy wiersz ikon. 

<center>

![Układ ikony selektora stylów](media/choose-map-style/style-picker-icon-layout.png)</center>

Druga opcja układu jest wywoływana `list` i wyświetla listę stylów mapy, które można przewijać.  

<center>

![Układ listy selektora stylów](media/choose-map-style/style-picker-list-layout.png)</center>


Poniższy kod pokazuje, jak utworzyć wystąpienie kontrolki selektora stylów i dodać ją do prawego górnego rogu mapy. Selektor stylów ma ustawiony styl ciemny i pokazuje wybrane kilka stylów mapy przy użyciu warstwy listy.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Poniższy kod dodaje kontrolkę selektora stylu z ustawieniami domyślnymi do mapy, dzięki czemu użytkownik może łatwo przełączać się między różnymi stylami mapy. Przełączenie stylu mapy za pomocą kontrolki styl mapy w prawym górnym rogu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie selektora stylów' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>dodając selektor stylów</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie w przypadku korzystania z warstwy cenowej S0 Azure Maps w kontrolce wybór stylu są wyświetlane wszystkie dostępne style. Jeśli chcesz zmniejszyć liczbę stylów na tej liście, Przekaż tablicę stylów, które mają być wyświetlane na liście w `mapStyle` opcji selektora stylów. Jeśli używasz systemu S1 i chcesz wyświetlić wszystkie dostępne style, ustaw `mapStyles` opcję selektora stylów na `"all"` .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Dodawanie formantów do map:

> [!div class="nextstepaction"]
> [Dodawanie kontrolek mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Dodawanie numeru PIN](map-add-pin.md)
