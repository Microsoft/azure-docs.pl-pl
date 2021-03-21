---
title: Zmień styl kontrolka mapy Azure Maps sieci Web
description: Dowiedz się, jak zmienić styl i opcje mapy. Zobacz, jak dodać kontrolkę selektora stylów do mapy w Azure Maps, aby użytkownicy mogli przełączać się między różnymi stylami.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680030"
---
# <a name="change-the-style-of-the-map"></a>Zmienianie stylu mapy

Formant mapy obsługuje kilka różnych [opcji stylu](/javascript/api/azure-maps-control/atlas.styleoptions) mapy i [stylów mapy podstawowej](supported-map-styles.md). Wszystkie style można ustawić, gdy kontrolka mapy jest inicjowana. Lub można ustawić style przy użyciu funkcji formantu mapy `setStyle` . W tym artykule pokazano, jak za pomocą tych opcji stylu dostosować wygląd mapy. Ponadto dowiesz się, jak zaimplementować kontrolkę selektora stylów na mapie. Kontrolka selektora stylów umożliwia użytkownikowi przełączanie się między różnymi stylami bazowymi.

## <a name="set-map-style-options"></a>Ustawianie opcji stylu mapy

Opcje stylu można ustawić podczas inicjowania kontrolki sieci Web. Lub można zaktualizować opcje stylu, wywołując funkcję kontrolki mapy `setStyle` . Aby wyświetlić wszystkie dostępne opcje stylu, zobacz [Opcje stylu](/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

Poniższe narzędzie pokazuje, jak różne opcje stylu zmieniają sposób renderowania mapy. Aby wyświetlić budynki 3W, Powiększ blisko miasta.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opcje stylu mapy</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Ustawianie stylu mapy podstawowej

Możesz również zainicjować formant mapy przy użyciu jednego z [stylów mapy podstawowej](supported-map-styles.md) , które są dostępne w zestawie SDK sieci Web. Następnie można użyć funkcji, `setStyle` Aby zaktualizować styl bazowy przy użyciu innego stylu mapy.

### <a name="set-a-base-map-style-on-initialization"></a>Ustaw styl mapy podstawowej podczas inicjowania

Style podstawowe kontrolki mapy można ustawić podczas inicjacji. W poniższym kodzie `style` opcja kontrolki mapy jest ustawiana na [ `grayscale_dark` styl mapy podstawowej](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ustawianie stylu podczas ładowania mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ustawianie stylu ładowania mapy</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizowanie stylu mapy podstawowej

Styl mapy bazowej można zaktualizować przy użyciu `setStyle` funkcji i ustawiając `style` opcję, aby zmienić na inny styl mapy podstawowej lub dodać dodatkowe opcje stylu.

```javascript
map.setStyle({ style: 'satellite' });
```

W poniższym kodzie po załadowaniu wystąpienia mapy styl mapy zostanie zaktualizowany z `grayscale_dark` do `satellite` przy użyciu funkcji [SetStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Aktualizowanie stylu</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Dodaj kontrolkę selektora stylów

Kontrolka selektora stylów oferuje łatwy do użycia przycisk z panelem wysuwania, który może być używany przez użytkownika końcowego do przełączania się między stylami bazowymi.

Selektor stylów ma dwie różne opcje układu: `icon` i `list` . Ponadto selektor stylów pozwala wybrać dwie różne opcje kontrolki selektora stylów `style` : `light` i `dark` . W tym przykładzie selektor stylu używa `icon` układu i wyświetla listę wybranych stylów mapy podstawowej w postaci ikon. Selektor kontrolki stylu zawiera następujący podstawowy zestaw stylów: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Aby uzyskać więcej informacji na temat opcji sterowania selektorem stylów, zobacz [Opcje kontrolki styl](/javascript/api/azure-maps-control/atlas.stylecontroloptions).

Poniższy obraz pokazuje kontrolkę selektora stylu wyświetlaną w `icon` układzie.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Układ ikony selektora stylów":::

Poniższy obraz pokazuje kontrolkę selektora stylu wyświetlaną w `list` układzie.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Układ listy selektora stylów":::

> [!IMPORTANT]
> Domyślnie kontrolka selektora stylów wyświetla listę wszystkich stylów dostępnych w warstwie cenowej S0 Azure Maps. Jeśli chcesz zmniejszyć liczbę stylów na tej liście, Przekaż tablicę stylów, które mają być wyświetlane na liście w `mapStyle` opcji selektora stylów. Jeśli używasz systemu S1 i chcesz wyświetlić wszystkie dostępne style, ustaw `mapStyles` opcję selektora stylów na `"all"` .

Poniższy kod pokazuje, jak zastąpić domyślną `mapStyles` listę stylów bazowych. W tym przykładzie ustawiamy `mapStyles` opcję wyświetlania stylów bazowych, które mają być wyświetlane w kontrolce selektora stylów.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie selektora stylów' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>dodając selektor stylów</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie kontrolek mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)
