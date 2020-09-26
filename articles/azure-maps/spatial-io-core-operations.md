---
title: Podstawowe operacje we/wy | Mapy Microsoft Azure
description: Dowiedz się, jak skutecznie odczytywać i zapisywać dane XML i rozdzielane przy użyciu bibliotek podstawowych z przestrzennego modułu we/wy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 1689ff4d24b3bf82298041fbb84d759b451d8eab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321768"
---
# <a name="core-io-operations"></a>Podstawowe operacje we/wy

Oprócz udostępniania narzędzi do odczytywania plików danych przestrzennych, w module we/wy przestrzennym są udostępniane podstawowe biblioteki podstawowe umożliwiające szybkie i wydajne odczytywanie i zapisywanie danych XML oraz ograniczanie ich.

`atlas.io.core`Przestrzeń nazw zawiera dwie klasy niskiego poziomu, które mogą szybko odczytywać i zapisywać dane CSV i XML. Te klasy bazowe zużywają czytelników i moduły zapisujące danych przestrzennych w module we/wy. Korzystaj z nich, aby dodać dodatkową obsługę odczytu i zapisu dla plików CSV lub XML.
 
## <a name="read-delimited-files"></a>Odczytuj Pliki rozdzielane

`atlas.io.core.CsvReader`Klasa odczytuje ciągi zawierające rozdzielane zestawy danych. Ta klasa udostępnia dwie metody odczytu danych:

- `read`Funkcja odczytaje pełny zestaw danych i zwróci dwuwymiarową tablicę ciągów reprezentujących wszystkie komórki rozdzielonego zestawu danych.
- `getNextRow`Funkcja odczytuje każdy wiersz tekstu w rozdzielonym zestawie danych i zwraca tablicę zawierającą ciąg reprezentujący wszystkie komórki w tym wierszu zestawu danych. Użytkownik może przetwarzać wiersz i usunąć niepotrzebną pamięć z tego wiersza przed przetworzeniem następnego wiersza. Dlatego funkcja ma więcej wydajnej pamięci.

Domyślnie czytnik będzie używał znaku przecinka jako ogranicznika. Ogranicznik można jednak zmienić na dowolny pojedynczy znak lub ustawić na `'auto'` . Po ustawieniu na `'auto'` , czytnik będzie analizować pierwszy wiersz tekstu w ciągu. Następnie wybierze najbardziej typowy znak z poniższej tabeli, aby użyć jako ogranicznika.

| Ogranicznik | Znak |
| :-- | :-- |
| Przecinek | `,` |
| Tab | `\t` |
| Wodzie | `|` |

Ten czytnik obsługuje również kwalifikatory tekstu, które są używane do obsługi komórek zawierających znak ogranicznika. Znak cudzysłowu ( `'"'` ) jest domyślnym kwalifikatorem tekstu, ale można go zmienić na pojedynczy znak.

## <a name="write-delimited-files"></a>Pliki rozdzielane zapisem

`atlas.io.core.CsvWriter`Zapisuje tablicę obiektów jako ciąg rozdzielany. Dowolny pojedynczy znak może być używany jako ogranicznik lub kwalifikator tekstu. Domyślny ogranicznik jest przecinkiem ( `','` ), a domyślny kwalifikator tekstu jest znakiem cudzysłowu ( `'"'` ).

Aby użyć tej klasy, wykonaj następujące czynności:

- Utwórz wystąpienie klasy i opcjonalnie Ustaw ogranicznik niestandardowy lub kwalifikator tekstu.
- Zapisz dane w klasie przy użyciu `write` funkcji lub `writeRow` funkcji. Dla `write` funkcji Przekaż dwuwymiarową tablicę obiektów reprezentujących wiele wierszy i komórek. Aby użyć `writeRow` funkcji, należy przekazać tablicę obiektów reprezentujących wiersz danych z wieloma kolumnami.
- Wywołaj `toString` funkcję, aby pobrać rozdzielany ciąg. 
- Opcjonalnie Wywołaj `clear` metodę, aby umożliwić ponowne użycie modułu zapisywania i zmniejszyć jego alokację zasobów, lub wywołaj `delete` metodę, aby usunąć wystąpienie składnika zapisywania.

> [!Note]
> Liczba zapisanych kolumn zostanie ograniczona do liczby komórek w pierwszym wierszu danych przesłanych do składnika zapisywania.

## <a name="read-xml-files"></a>Odczytywanie plików XML

`atlas.io.core.SimpleXmlReader`Klasa jest szybsza podczas analizowania plików XML niż `DOMParser` . Jednak `atlas.io.core.SimpleXmlReader` Klasa wymaga dobrze sformatowania plików XML. Pliki XML, które nie są poprawnie sformatowane, na przykład brakujące tagi zamykające prawdopodobnie spowodują wystąpienie błędu.

Poniższy kod ilustruje sposób użycia `SimpleXmlReader` klasy do analizy ciągu XML w obiekcie JSON i serializacji go w żądanym formacie.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Zapisz pliki XML

`atlas.io.core.SimpleXmlWriter`Klasa zapisuje poprawnie sformatowane dane XML w wydajny sposób pamięci.

Poniższy kod ilustruje sposób użycia `SimpleXmlWriter` klasy w celu wygenerowania dobrze sformatowanego ciągu XML.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Wygenerowany kod XML z powyższego kodu będzie wyglądać następująco.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

[CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

[Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)