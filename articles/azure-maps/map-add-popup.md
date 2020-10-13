---
title: Dodawanie okna podręcznego do punktu na mapie | Mapy Microsoft Azure
description: Informacje na temat okien podręcznych, szablonów podręcznych i zdarzeń podręcznych w programie Azure Maps. Zobacz, jak dodać okno podręczne do punktu na mapie oraz jak ponownie używać i dostosowywać okna podręczne.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: a67fad5d0980730eb73f4219f12eaf3a7b9ec7dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310479"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie okna podręcznego do mapy

W tym artykule opisano sposób dodawania okna podręcznego do punktu na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

Poniższy kod dodaje funkcję punktu, która ma `name` i `description` właściwości, do mapy za pomocą warstwy symboli. Wystąpienie [klasy popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) jest tworzone, ale nie jest wyświetlane. Zdarzenia myszy są dodawane do warstwy symboli, aby wyzwolić otwieranie i zamykanie okna podręcznego. Gdy symbol znacznika jest aktywowany, właściwość podręcznej `position` jest aktualizowana przy użyciu pozycji znacznika, a `content` opcja jest aktualizowana przy użyciu kodu HTML, który otacza  `name` właściwości i funkcji punktu, który jest `description` aktywowany. Następnie zostanie wyświetlone okno podręczne na mapie przy użyciu `open` funkcji.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wyskakujących okienek przy użyciu Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Dodaj wyskakujące okienko przy użyciu Azure Maps</a> przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Używanie okna podręcznego z wieloma punktami

Istnieją przypadki, w których najlepszym rozwiązaniem jest utworzenie jednego okna podręcznego i jego ponowne użycie. Na przykład możesz mieć dużą liczbę punktów i chcesz wyświetlić tylko jedno podręczne. Za pomocą tego okna podręcznego, liczba elementów DOM utworzonych przez aplikację jest znacznie zmniejszona, co może zapewnić lepszą wydajność. Poniższy przykład tworzy funkcje 3-punktowe. Po kliknięciu dowolnego z nich zostanie wyświetlone okno podręczne z zawartością dla tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='Używanie podręcznego z wieloma numerami PIN' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobaczysz piórem, <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>korzystając z okna podręcznego z wieloma</a> numerami pin przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Dostosowywanie okna podręcznego

Domyślnie okno podręczne ma białe tło, strzałkę wskaźnika na dole i przycisk Zamknij w prawym górnym rogu. Poniższy przykład zmienia kolor tła na czarny przy użyciu `fillColor` opcji menu podręcznego. Przycisk Zamknij jest usuwany przez ustawienie `CloseButton` opcji na false. Zawartość HTML z menu podręcznego jest uzupełniona o 10 pikseli od krawędzi okienka podręcznego. Tekst jest biały, więc pokazuje dobrze na czarnym tle.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Niestandardowe okno podręczne" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>niestandardowe podręczne</a> pióro, Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Dodawanie szablonów podręcznych do mapy

Szablony wyskakujące ułatwiają tworzenie układów opartych na danych dla okien podręcznych. W poniższych sekcjach przedstawiono użycie różnych szablonów podręcznych w celu wygenerowania sformatowanej zawartości przy użyciu właściwości funkcji.

> [!NOTE]
> Domyślnie cała zawartość renderowana przy użyciu szablonu popup zostanie przeizolowana wewnątrz elementu IFRAME jako funkcja zabezpieczeń. Istnieją jednak ograniczenia:
>
> - Wszystkie skrypty, formularze, blokada wskaźnika i najważniejsze funkcje nawigacji są wyłączone. Po kliknięciu łącza mogą zostać otwarte na nowej karcie. 
> - Starsze przeglądarki, które nie obsługują `srcdoc` parametru w iframeach, będą ograniczone do renderowania niewielkiej ilości zawartości.
> 
> Jeśli ufasz danych ładowanych do okien podręcznych i potencjalnie chcesz, aby te skrypty były ładowane do okien podręcznych, można je wyłączyć, ustawiając opcję szablony wyskakujące `sandboxContent` na false. 

### <a name="string-template"></a>Szablon ciągu

Szablon ciągu zastępuje symbole zastępcze wartościami właściwości funkcji. Właściwościom funkcji nie trzeba przypisywać wartości typu String. Na przykład `value1` przechowuje liczbę całkowitą. Te wartości są następnie przesyłane do właściwości content obiektu `popupTemplate` . 

`numberFormat`Opcja określa format liczby do wyświetlenia. Jeśli wartość `numberFormat` nie jest określona, kod będzie używać formatu daty szablonów podręcznych. `numberFormat`Opcja formatuje liczby przy użyciu funkcji [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Aby sformatować duże liczby, należy rozważyć użycie `numberFormat` opcji z funkcjami [NumberFormat. format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Na przykład poniższy fragment kodu używa, `maximumFractionDigits` Aby ograniczyć liczbę cyfr ułamków do dwóch.

> [!Note]
> Istnieje tylko jeden sposób renderowania obrazów przez szablon ciągu. Najpierw szablon ciągu musi mieć tag obrazu. Wartość, która jest przesyłana do znacznika obrazu, powinna być adresem URL obrazu. Następnie szablon ciągu musi mieć `isImage` ustawioną wartość true w elemencie `HyperLinkFormatOptions` . `isImage`Opcja określa, że hiperłącze jest dla obrazu, a hiperlink zostanie załadowany do tagu obrazu. Po kliknięciu hiperlinku zostanie otwarty obraz.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Szablon PropertyInfo

Szablon PropertyInfo wyświetla dostępne właściwości funkcji. `label`Opcja określa tekst, który ma być wyświetlany użytkownikowi. Jeśli `label` nie jest określony, zostanie wyświetlone hiperłącze. A jeśli hiperłącze jest obrazem, zostanie wyświetlona wartość przypisana do tagu "Alt". `dateFormat`Określa format daty, a jeśli format daty nie jest określony, Data będzie renderowana jako ciąg. `hyperlinkFormat`Opcja renderuje łącza z możliwością kliknięcia, podobnie jak w przypadku `email` renderowania adresów e-mail, które można wybrać.

Zanim szablon PropertyInfo wyświetli właściwości dla użytkownika końcowego, rekursywnie sprawdza, czy właściwości są rzeczywiście zdefiniowane dla tej funkcji. Ignoruje także wyświetlanie właściwości stylu i tytułu. Na przykład nie będzie wyświetlana `color` , `size` ,, `anchor` `strokeOpacity` , i `visibility` . Dlatego po zakończeniu sprawdzania ścieżki właściwości w zapleczu szablon PropertyInfo pokazuje zawartość w formacie tabeli.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Wiele szablonów zawartości

Funkcja może również wyświetlać zawartość przy użyciu kombinacji szablonu ciągu i szablonu PropertyInfo. W takim przypadku szablon ciągu renderuje wartości zastępcze na białym tle.  A, szablon PropertyInfo renderuje obraz pełnej szerokości wewnątrz tabeli. Właściwości w tym przykładzie są podobne do właściwości, które wyjaśniono w poprzednich przykładach.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punkty bez zdefiniowanego szablonu

Gdy szablon podręczny nie jest zdefiniowany jako szablon typu ciąg, szablon PropertyInfo lub kombinacji obu, wówczas używa ustawień domyślnych. Gdy `title` i `description` są jedynymi przypisanymi właściwościami, szablon podręczny pokazuje białe tło, przycisk Zamknij w prawym górnym rogu. Na małych i średnich ekranach widać strzałkę u dołu. Ustawienia domyślne są wyświetlane wewnątrz tabeli dla wszystkich właściwości innych niż `title` i `description` . Nawet w przypadku powrotu do ustawień domyślnych szablon podręczny może nadal być manipulowany programowo. Na przykład użytkownicy mogą wyłączyć wykrywanie hiperłączy, a ustawienia domyślne byłyby nadal stosowane do innych właściwości.

Kliknij punkty na mapie w CodePen. Istnieje punkt na mapie dla każdego z następujących szablonów podręcznych: szablon String, szablon PropertyInfo i szablon wielu zawartości. Istnieją również trzy punkty, w których można wyświetlać szablony renderowane przy użyciu ustawień domyślnych.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Ponownie Użyj szablonu popup

Podobnie jak w przypadku korzystania z menu podręcznego, można ponownie użyć szablonów podręcznych. Takie podejście jest przydatne, gdy chcesz wyświetlić tylko jeden szablon podręczny w danej chwili dla wielu punktów. Ponowne użycie szablonu podręcznego powoduje zmniejszenie liczby elementów DOM utworzonych przez aplikację, co następnie poprawi wydajność aplikacji. Poniższy przykład używa tego samego szablonu podręcznego dla trzech punktów. Po kliknięciu dowolnego z nich zostanie wyświetlone okno podręczne z zawartością dla tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Zdarzenia wyskakujące

Okienka wyskakujące mogą być otwierane, zamykane i przeciągane. Klasa popup udostępnia zdarzenia, aby pomóc deweloperom reagować na te zdarzenia. W poniższym przykładzie przedstawiono zdarzenia wyzwalane, gdy użytkownik otwiera, zamyka lub przeciąga okno podręczne. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia wyskakujące" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>zdarzenia wyskakujące</a> pióro według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Zobacz następujące wspaniałe artykuły dotyczące pełnych przykładów kodu:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie znacznika HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)
