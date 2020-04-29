---
title: Szablony obrazów w zestawie SDK sieci Web Azure Maps | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać szablonów obrazów ze znacznikami HTML i różnymi warstwami w Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804813"
---
# <a name="how-to-use-image-templates"></a>Jak używać szablonów obrazów

Obrazy mogą być używane ze znacznikami HTML i różnymi warstwami w Azure Maps Web SDK:

 - Warstwy symboli mogą renderować punkty na mapie za pomocą ikony obrazu. Symbole mogą być również renderowane wzdłuż ścieżki linii.
 - Warstwy wielokątów mogą być renderowane przy użyciu obrazu deseniu wypełnienia. 
 - Znaczniki HTML mogą renderować punkty przy użyciu obrazów i innych elementów HTML.

Aby zapewnić dobrą wydajność przy użyciu warstw, Załaduj obrazy do zasobu mapy Sprite Image przed renderowaniem. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), SymbolLayer, wstępnie ładuje kilka obrazów znaczników w kilkuie kolorów do ikon obrazu mapy. Te obrazy znaczników i inne są dostępne jako szablony SVG. Mogą służyć do tworzenia obrazów z niestandardowymi skalami lub używany jako kolor podstawowy i pomocniczy klienta. W sumie dostępne są 42 szablony obrazów: 27 ikon symboli i 15 wielokątów wypełnienia.

Szablony obrazów można dodawać do zasobów obrazu mapy Sprite przy użyciu `map.imageSprite.createFromTemplate` funkcji. Ta funkcja umożliwia przekazanie maksymalnie pięciu parametrów;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` Jest to unikatowy identyfikator, który tworzysz. Element `id` jest przypisywany do obrazu, gdy zostanie dodany do ikon obrazu mapy. Użyj tego identyfikatora w warstwach, aby określić, który zasób obrazu ma być renderowany. `templateName` Określa szablon obrazu, który ma być używany. `color` Opcja ustawia kolor podstawowy obrazu, a `secondaryColor` opcje ustawia kolor pomocniczy obrazu. `scale` Opcja skaluje szablon obrazu przed zastosowaniem go do ikonki obrazu. Gdy obraz zostanie zastosowany do ikon obrazu, jest konwertowany na PNG. Aby zapewnić wyraźne renderowanie, lepiej jest skalować szablon obrazu przed dodaniem go do Sprite, niż w celu skalowania go w warstwie.

Ta funkcja asynchronicznie ładuje obraz do Sprite ikon obrazu. W ten sposób zwraca obietnicę, która może poczekać na ukończenie tej funkcji.

Poniższy kod przedstawia sposób tworzenia obrazu z jednego z wbudowanych szablonów i używania go z warstwą symboli.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Używanie szablonu obrazu z warstwą symboli

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako symbol w warstwie symboli, odwołując się do identyfikatora zasobu obrazu w `image` opcji. `iconOptions`

Poniższy przykład renderuje warstwę symboli przy użyciu szablonu `marker-flat` obrazu z pierwotnym kolorem podstawowym i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa symboli z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>warstwę symboli pióra przy użyciu wbudowanego szablonu ikony</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Używanie szablonu obrazu wzdłuż ścieżki linii

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować wzdłuż ścieżki linii przez dodanie LineString do źródła danych i użycie warstwy symbol z `lineSpacing`opcją oraz przez odwołanie się do identyfikatora zasobu obrazu w `image` opcji th. `iconOptions` 

Poniższy przykład renderuje różowy wiersz na mapie i używa warstwy symboli przy użyciu szablonu `car` obrazu z niebieski Dodger niebieskim kolorem podstawowym i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa linii z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>warstwę linii pióra przy użyciu wbudowanego szablonu ikony</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Jeśli szablon obrazu zostanie utworzony, ustaw opcję `rotation` ikona warstwy symboli na 90, jeśli chcesz, aby wskazywała ona ten sam kierunek co linia.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Używanie szablonu obrazu z warstwą wielokątów

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako wzór wypełnienia w warstwie wielokąta, odwołując się do identyfikatora zasobu obrazu w `fillPattern` opcji warstwy.

Poniższy przykład renderuje warstwę wielokąta przy użyciu `dot` szablonu obrazu z czerwonym kolorem podstawowym i przezroczystym kolorem pomocniczym.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wypełnij Wielokąt przy użyciu wbudowanego szablonu ikony" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Wielokąt wypełnienia piórem za pomocą wbudowanego szablonu ikony</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ustawienie koloru pomocniczego deseni wypełnienia ułatwia wyświetlenie podstawowej mapy, która będzie nadal udostępniać wzorzec podstawowy. 

## <a name="use-an-image-template-with-an-html-marker"></a>Używanie szablonu obrazu z znacznikiem HTML

Szablon obrazu można pobrać przy użyciu `altas.getImageTemplate` funkcji i używane jako zawartość znacznika HTML. `htmlContent` Szablon może być przekazywać do opcji znacznika, a następnie dostosowany przy użyciu opcji `color`, `secondaryColor`i. `text`

Poniższy przykład używa `marker-arrow` szablonu z czerwonym kolorem podstawowym, różowym kolorem pomocniczym i wartością tekstową "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Znacznik HTML z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>znacznik HTML pióra przy użyciu wbudowanego szablonu ikony</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Tworzenie niestandardowych szablonów do wielokrotnego użytku

Jeśli aplikacja używa tej samej ikony z różnymi ikonami lub jeśli tworzysz moduł, który dodaje dodatkowe szablony obrazów, możesz łatwo dodać te ikony i pobrać je z zestawu Azure Maps Web SDK. Użyj następujących funkcji statycznych w `atlas` przestrzeni nazw.

| Nazwa | Typ zwracany | Opis | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Dodaje niestandardowy szablon obrazu SVG do przestrzeni nazw szczytu. |
|  `getImageTemplate(templateName: string, scale?: number)`| ciąg | Pobiera szablon SVG według nazwy. |
| `getAllImageTemplateNames()` | ciąg [] |  Pobiera szablon SVG według nazwy. |

Szablony obrazów SVG obsługują następujące wartości zastępcze:

| Symbol zastępczy | Opis |
|-|-|
| `{color}` | Kolor podstawowy. | 
| `{secondaryColor}` | Kolor pomocniczy. | 
| `{scale}` | Obraz SVG zostanie przekonwertowany na obraz PNG, gdy zostanie dodany do ikon obrazu mapy. Ten symbol zastępczy może służyć do skalowania szablonu przed jego konwersją w celu zapewnienia jasnego renderowania. | 
| `{text}` | Lokalizacja, w której ma być renderowany tekst, gdy jest używany z znacznikiem HTML. |

Poniższy przykład pokazuje, jak zastosować szablon SVG i dodać go do Azure Maps Web SDK jako szablonu ikony wielokrotnego użytku. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodaj niestandardowy szablon ikon do obszaru nazw szczytu" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Dodaj niestandardowy szablon ikony do obszaru nazw szczytu</a> według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista szablonów obrazów

Ta tabela zawiera listę wszystkich szablonów obrazów dostępnych obecnie w Azure Maps Web SDK. Nazwa szablonu znajduje się powyżej każdego obrazu. Domyślnie kolor podstawowy jest niebieski i kolor pomocniczy jest biały. Aby ułatwić wyświetlanie koloru pomocniczego na białym tle, kolor pomocniczy jest ustawiany na czarnym.

**Szablony ikon symboli**

|||||
|:-:|:-:|:-:|:-:|
| obrys | znacznik — gruby | znacznik — okrąg | znacznik — Flat |
|![ikona znacznika](./media/image-templates/marker.png)|![ikona o szerokim znaczniku](./media/image-templates/marker-thick.png)|![znacznik — ikona okręgu](./media/image-templates/marker-circle.png)|![znacznik — ikona płaska](./media/image-templates/marker-flat.png)|
||||
| znacznik — kwadrat | znacznik — kwadrat — klaster | znacznik — strzałka | znacznik — piłka — numer PIN | 
|![znacznik — ikona kwadratu](./media/image-templates/marker-square.png)|![znacznik — prostokąt — ikona klastra](./media/image-templates/marker-square-cluster.png)|![ikona strzałki w dół](./media/image-templates/marker-arrow.png)|![znacznik — piłka — ikona pinezki](./media/image-templates/marker-ball-pin.png)|
||||
| znacznik — zaokrąglony kwadrat | znacznik — zaokrąglony do kwadratu — klaster | flag | Flaga-Trójkąt |
| ![znacznik — ikona zaokrąglona kwadratowo](./media/image-templates/marker-square-rounded.png) | ![znacznik — zaokrąglony prostokąt-ikona klastra](./media/image-templates/marker-square-rounded-cluster.png) | ![ikona flagi](./media/image-templates/flag.png) | ![ikona trójkąta flaga](./media/image-templates/flag-triangle.png) |
||||
| trójkąt | Trójkąt — gruby | Trójkąt — Strzałka w górę | Trójkąt — Strzałka w lewo |
| ![ikona trójkąta](./media/image-templates/triangle.png) | ![ikona o szerokim trójkącie](./media/image-templates/triangle-thick.png) | ![Trójkąt — ikona strzałki w górę](./media/image-templates/triangle-arrow-up.png) | ![Trójkąt-Strzałka w lewo](./media/image-templates/triangle-arrow-left.png) |
||||
| sześciokąt | sześciokątny | zaokrąglony | sześciokąt — zaokrąglony |
| ![ikona sześciokątna](./media/image-templates/hexagon.png) | ![ikona o grubości sześciokątnej](./media/image-templates/hexagon-thick.png) | ![ikona z zaokrągleniem sześciokątnym](./media/image-templates/hexagon-rounded.png) | ![ikona z zaokrągleniem do grubości](./media/image-templates/hexagon-rounded-thick.png) |
||||
| przypinanie | kod PIN — Zaokrąglij | zaokrąglony kwadrat | zaokrąglona-kwadratowa |
| ![ikona przypinania](./media/image-templates/pin.png) | ![ikona z zaokrągleniem numeru PIN](./media/image-templates/pin-round.png) | ![ikona zaokrąglony kwadrat](./media/image-templates/rounded-square.png) | ![ikona o zaokrąglonej grubości kwadratowej](./media/image-templates/rounded-square-thick.png) |
||||
| Strzałka w górę | Strzałka w górę — cienka | car ||
| ![ikona strzałki w górę](./media/image-templates/arrow-up.png) | ![Strzałka w górę — ikona cienkiej](./media/image-templates/arrow-up-thin.png) | ![ikona samochodu](./media/image-templates/car.png) | |

**Szablony deseni wypełnienia wielokąta**

|||||
|:-:|:-:|:-:|:-:|
| kwestionuj | pion — obrócony | kółek | koła — w odstępach |
| ![Ikona narzędzia do sprawdzania](./media/image-templates/checker.png) | ![Narzędzie sprawdzania — obrócona ikona](./media/image-templates/checker-rotated.png) | ![ikona okręgów](./media/image-templates/circles.png) | ![kółka — ikona odstępu](./media/image-templates/circles-spaced.png) |
|||||
| ukośne linie — w górę | ukośne linie — w dół | ukośne paski — w górę | ukośne paski — w dół |
| ![ikona ukośna w górę](./media/image-templates/diagonal-lines-up.png) | ![ikona ukośna w dół](./media/image-templates/diagonal-lines-down.png) | ![ikona przekątna](./media/image-templates/diagonal-stripes-up.png) | ![Przekątna — ikona w dół](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linie siatki | obrócone — linie siatki | obrócony — paski siatki | x — wypełnienie |
| ![ikona linii siatki](./media/image-templates/grid-lines.png) | ![ikona obrócona — linia siatki](./media/image-templates/rotated-grid-lines.png) | ![ikona obrócona-siatka](./media/image-templates/rotated-grid-stripes.png) | ![ikona wypełnienia x](./media/image-templates/x-fill.png) |
|||||
| Zygzak | Zygzak — pionowa | kropk |  |
| ![ikona zygzaka](./media/image-templates/zig-zag.png) | ![Zygzak — ikona pionowa](./media/image-templates/zig-zag-vertical.png) | ![ikona kropek](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Wypróbuj teraz narzędzie

Korzystając z następującego narzędzia, można renderować różne wbudowane szablony obrazów na różne sposoby i dostosować podstawowe i pomocnicze kolory i skalę.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opcje szablonu ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opcje szablonu ikony</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Przestrzeń nazw atlasu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, w których można używać szablonów obrazów:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodaj marki HTML](map-add-bubble-layer.md)
