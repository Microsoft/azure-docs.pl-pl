---
title: Szablony obrazów w zestawie SDK sieci Web Azure Maps | Mapy Microsoft Azure
description: Dowiedz się, jak dodać ikony obrazu i wielokąty ze wzorkami do map przy użyciu zestawu SDK sieci Web Azure Maps. Wyświetl dostępne szablony obrazu i wypełnienia.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895685"
---
# <a name="how-to-use-image-templates"></a>Jak używać szablonów obrazów

Obrazy mogą być używane ze znacznikami HTML i różnymi warstwami w Azure Maps Web SDK:

 - Warstwy symboli mogą renderować punkty na mapie za pomocą ikony obrazu. Symbole mogą być również renderowane wzdłuż ścieżki linii.
 - Warstwy wielokątów mogą być renderowane przy użyciu obrazu deseniu wypełnienia. 
 - Znaczniki HTML mogą renderować punkty przy użyciu obrazów i innych elementów HTML.

Aby zapewnić dobrą wydajność przy użyciu warstw, Załaduj obrazy do zasobu mapy Sprite Image przed renderowaniem. [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions), SymbolLayer, wstępnie ładuje kilka obrazów znaczników w kilkuie kolorów do ikon obrazu mapy. Te obrazy znaczników i inne są dostępne jako szablony SVG. Mogą służyć do tworzenia obrazów z niestandardowymi skalami lub używany jako kolor podstawowy i pomocniczy klienta. W sumie dostępne są 42 szablony obrazów: 27 ikon symboli i 15 wielokątów wypełnienia.

Szablony obrazów można dodawać do zasobów obrazu mapy Sprite przy użyciu `map.imageSprite.createFromTemplate` funkcji. Ta funkcja umożliwia przekazanie maksymalnie pięciu parametrów;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`Jest to unikatowy identyfikator, który tworzysz. `id`Element jest przypisywany do obrazu, gdy zostanie dodany do ikon obrazu mapy. Użyj tego identyfikatora w warstwach, aby określić, który zasób obrazu ma być renderowany. `templateName`Określa szablon obrazu, który ma być używany. `color`Opcja ustawia kolor podstawowy obrazu, a `secondaryColor` Opcje ustawia kolor pomocniczy obrazu. `scale`Opcja skaluje szablon obrazu przed zastosowaniem go do ikonki obrazu. Gdy obraz zostanie zastosowany do ikon obrazu, jest konwertowany na PNG. Aby zapewnić wyraźne renderowanie, lepiej jest skalować szablon obrazu przed dodaniem go do Sprite, niż w celu skalowania go w warstwie.

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

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako symbol w warstwie symboli, odwołując się do identyfikatora zasobu obrazu w `image` opcji `iconOptions` .

Poniższy przykład renderuje warstwę symboli przy użyciu `marker-flat` szablonu obrazu z pierwotnym kolorem podstawowym i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa symboli z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>warstwę symboli pióra przy użyciu wbudowanego szablonu ikony</a> , Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Używanie szablonu obrazu wzdłuż ścieżki linii

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować wzdłuż ścieżki linii przez dodanie LineString do źródła danych i użycie warstwy symbol z `lineSpacing` opcją oraz przez odwołanie się do identyfikatora zasobu obrazu w `image` opcji th `iconOptions` . 

Poniższy przykład renderuje różowy wiersz na mapie i używa warstwy symboli przy użyciu `car` szablonu obrazu z niebieski Dodger niebieskim kolorem podstawowym i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa linii z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>warstwę linii pióra przy użyciu wbudowanego szablonu ikony</a> , Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Jeśli szablon obrazu zostanie utworzony, ustaw `rotation` opcję ikona warstwy symboli na 90, jeśli chcesz, aby wskazywała ona ten sam kierunek co linia.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Używanie szablonu obrazu z warstwą wielokątów

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako wzór wypełnienia w warstwie wielokąta, odwołując się do identyfikatora zasobu obrazu w `fillPattern` opcji warstwy.

Poniższy przykład renderuje warstwę wielokąta przy użyciu `dot` szablonu obrazu z czerwonym kolorem podstawowym i przezroczystym kolorem pomocniczym.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wypełnij Wielokąt przy użyciu wbudowanego szablonu ikony" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Wielokąt wypełnienia piórem za pomocą wbudowanego szablonu ikony</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ustawienie koloru pomocniczego deseni wypełnienia ułatwia wyświetlenie podstawowej mapy, która będzie nadal udostępniać wzorzec podstawowy. 

## <a name="use-an-image-template-with-an-html-marker"></a>Używanie szablonu obrazu z znacznikiem HTML

Szablon obrazu można pobrać przy użyciu `altas.getImageTemplate` funkcji i używane jako zawartość znacznika HTML. Szablon może być przekazywać do `htmlContent` opcji znacznika, a następnie dostosowany przy użyciu `color` `secondaryColor` opcji, i `text` .

Poniższy przykład używa `marker-arrow` szablonu z czerwonym kolorem podstawowym, różowym kolorem pomocniczym i wartością tekstową "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Znacznik HTML z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>znacznik HTML pióra przy użyciu wbudowanego szablonu ikony</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Szablony obrazów można używać poza mapą. GetImageTemplate ATANH zwraca ciąg SVG, który ma symbole zastępcze; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Zamień te wartości symboli zastępczych, aby utworzyć prawidłowy ciąg SVG. Następnie można dodać ciąg SVG bezpośrednio do modelu DOM HTML lub przekonwertować go na identyfikator URI danych i wstawić go do tagu obrazu. Na przykład:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

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

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodaj niestandardowy szablon ikon do obszaru nazw szczytu" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Dodaj niestandardowy szablon ikony do obszaru nazw szczytu</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista szablonów obrazów

Ta tabela zawiera listę wszystkich szablonów obrazów dostępnych obecnie w Azure Maps Web SDK. Nazwa szablonu znajduje się powyżej każdego obrazu. Domyślnie kolor podstawowy jest niebieski i kolor pomocniczy jest biały. Aby ułatwić wyświetlanie koloru pomocniczego na białym tle, kolor pomocniczy jest ustawiany na czarnym.

**Szablony ikon symboli**

:::row:::
   :::column span="":::
      obrys
   :::column-end:::
   :::column span="":::
      znacznik — gruby
   :::column-end:::
   :::column span="":::
      znacznik — okrąg
   :::column-end:::
   :::column span="":::
      znacznik — Flat
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona znacznika](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![ikona o szerokim znaczniku](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![znacznik — ikona okręgu](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![znacznik — ikona płaska](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      znacznik — kwadrat
   :::column-end:::
   :::column span="":::
      znacznik — kwadrat — klaster
   :::column-end:::
   :::column span="":::
      znacznik — strzałka
   :::column-end:::
   :::column span="":::
      znacznik — piłka — numer PIN
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![znacznik — ikona kwadratu](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![znacznik — prostokąt — ikona klastra](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![ikona strzałki w dół](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![znacznik — piłka — ikona pinezki](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      znacznik — zaokrąglony kwadrat
   :::column-end:::
   :::column span="":::
      znacznik — zaokrąglony do kwadratu — klaster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      Flaga-Trójkąt
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![znacznik — ikona zaokrąglona kwadratowo](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![znacznik — zaokrąglony prostokąt-ikona klastra](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![ikona flagi](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![ikona trójkąta flaga](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      trójkąt
   :::column-end:::
   :::column span="":::
      Trójkąt — gruby
   :::column-end:::
   :::column span="":::
      Trójkąt — Strzałka w górę
   :::column-end:::
   :::column span="":::
      Trójkąt — Strzałka w lewo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona trójkąta](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![ikona o szerokim trójkącie](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![Trójkąt — ikona strzałki w górę](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![Trójkąt-Strzałka w lewo](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      sześciokąt
   :::column-end:::
   :::column span="":::
      sześciokątny
   :::column-end:::
   :::column span="":::
      zaokrąglony
   :::column-end:::
   :::column span="":::
      sześciokąt — zaokrąglony
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona sześciokątna](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![ikona o grubości sześciokątnej](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![ikona z zaokrągleniem sześciokątnym](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![ikona z zaokrągleniem do grubości](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      przypinanie
   :::column-end:::
   :::column span="":::
      kod PIN — Zaokrąglij
   :::column-end:::
   :::column span="":::
      zaokrąglony kwadrat
   :::column-end:::
   :::column span="":::
      zaokrąglona-kwadratowa
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona przypinania](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![ikona z zaokrągleniem numeru PIN](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![ikona zaokrąglony kwadrat](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![ikona o zaokrąglonej grubości kwadratowej](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Strzałka w górę
   :::column-end:::
   :::column span="":::
      Strzałka w górę — cienka
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona strzałki w górę](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![Strzałka w górę — ikona cienkiej](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![ikona samochodu](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Szablony deseni wypełnienia wielokąta**

:::row:::
   :::column span="":::
      kwestionuj
   :::column-end:::
   :::column span="":::
      pion — obrócony
   :::column-end:::
   :::column span="":::
      kółek
   :::column-end:::
   :::column span="":::
      koła — w odstępach
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Ikona narzędzia do sprawdzania](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![Narzędzie sprawdzania — obrócona ikona](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![ikona okręgów](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![kółka — ikona odstępu](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      ukośne linie — w górę
   :::column-end:::
   :::column span="":::
      ukośne linie — w dół
   :::column-end:::
   :::column span="":::
      ukośne paski — w górę
   :::column-end:::
   :::column span="":::
      ukośne paski — w dół
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona ukośna w górę](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![ikona ukośna w dół](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![ikona przekątna](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![Przekątna — ikona w dół](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      linie siatki
   :::column-end:::
   :::column span="":::
      obrócone — linie siatki
   :::column-end:::
   :::column span="":::
      obrócony — paski siatki
   :::column-end:::
   :::column span="":::
      x — wypełnienie
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona linii siatki](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ikona obrócona — linia siatki](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ikona obrócona-siatka](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![ikona wypełnienia x](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Zygzak
   :::column-end:::
   :::column span="":::
      Zygzak — pionowa
   :::column-end:::
   :::column span="":::
      kropk
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona zygzaka](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![Zygzak — ikona pionowa](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![ikona kropek](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Wstępnie załadowanych ikon obrazu**

Mapa wstępnie ładuje zestaw ikon do ikon obrazu mapy Sprite przy użyciu `marker` `pin` szablonów, i `pin-round` . Te nazwy ikon i ich wartości kolorów są wymienione w poniższej tabeli.

| Nazwa ikony | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Wypróbuj teraz narzędzie

Korzystając z następującego narzędzia, można renderować różne wbudowane szablony obrazów na różne sposoby i dostosować podstawowe i pomocnicze kolory i skalę.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opcje szablonu ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opcje szablonu ikony</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Przestrzeń nazw atlasu](/javascript/api/azure-maps-control/atlas#functions
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