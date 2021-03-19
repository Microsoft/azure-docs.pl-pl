---
title: Normalizacja tekstu dla filtrów, aspektów, sortowania
titleSuffix: Azure Cognitive Search
description: Określ normalizki do pól tekstowych w indeksie, aby dostosować zachowanie dopasowania słowa kluczowego Strict w filtrowanie, tworzenie aspektów i sortowanie.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609527"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalizacja tekstu dla filtrowania, aspektów i sortowania bez uwzględniania wielkości liter

 > [!IMPORTANT]
 > Normalizer jest w publicznej wersji zapoznawczej, dostępny za pomocą **interfejsu API REST 2020-06-30-Preview**. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się dodatkowe warunki użytkowania.

Wyszukiwanie i pobieranie dokumentów z indeksu Wyszukiwanie poznawcze platformy Azure wymaga dopasowania zapytania do zawartości dokumentu. Zawartość można analizować, aby utworzyć tokeny do dopasowania, tak jak w przypadku `search` użycia parametru lub może być używany jako-jest dla ścisłego dopasowania słowa kluczowego, który jest widoczny w `$filter` , `facets` , i `$orderby` . Takie podejście "all-or-Nothing" obejmuje większość scenariuszy, ale jest krótkie, gdzie proste przetwarzanie wstępne, takie jak wielkość liter, usuwanie akcentów, asciifolding i tak dalej jest wymagane bez przechodzenia przez cały łańcuch analizy.

Rozważmy następujące przykłady:

+ `$filter=City eq 'Las Vegas'` zwróci tylko dokumenty zawierające dokładnie tekst "Las Vegas" i wykluczają dokumenty z "LAS VEGAS" i "Las Vegas", które są nieodpowiednie, gdy przypadek użycia wymaga wszystkich dokumentów niezależnie od wielkości liter.

+ `search=*&facet=City,count:5` zwróci "Las Vegas", "LAS VEGAS" i "Las Vegas" jako różne wartości, mimo że jest to samo miasto.

+ `search=usa&$orderby=City` zwróci miasta w kolejności lexicographical: "Las Vegas", "Seattle", "Las Vegas", nawet jeśli celem jest zamówienie tych samych miejscowości łącznie niezależnie od wielkości liter. 

## <a name="normalizers"></a>Normalizer

*Normalizer* to składnik aparatu wyszukiwania odpowiedzialny za wstępne przetwarzanie tekstu dla dopasowania słowa kluczowego. Normalizki są podobne do analizatorów, z wyjątkiem tego, że nie tokenize zapytania. Niektóre przekształcenia, które można osiągnąć przy użyciu normalizacji są następujące:

+ Konwertuj na małe litery lub wielkie litery.
+ Normalizowanie akcentów i znaków diakrytycznych, takich jak ö lub ê, do znaków równoważnych ASCII "o" i "e".
+ Mapuj znaki takie jak `-` i odstępy do znaku określonego przez użytkownika.

Normalizer można określić w polach tekstowych w indeksie i są stosowane zarówno podczas indeksowania, jak i wykonywania zapytań.

## <a name="predefined-and-custom-normalizers"></a>Wstępnie zdefiniowane i niestandardowa Normalizer 

Usługa Azure Wyszukiwanie poznawcze obsługuje wstępnie zdefiniowane normalizy dla typowych przypadków użycia oraz możliwość dostosowywania w miarę potrzeb.

| Kategoria | Opis |
|----------|-------------|
| [Wstępnie zdefiniowane Normalizer](#predefined-normalizers) | Dostarczone jako dostępne i mogą być używane bez żadnej konfiguracji. |
|[Niestandardowi Normalizer](#add-custom-normalizers) | W przypadku zaawansowanych scenariuszy. Wymaga zdefiniowanej przez użytkownika konfiguracji kombinacji istniejących elementów składających się z filtrów char i token. <sup>1</sup>|

<sup>(1)</sup> niestandardowe normalizacje nie określają tokenizatory, ponieważ Normalizer zawsze tworzy pojedynczy token.

## <a name="how-to-specify-normalizers"></a>Jak określić normalizki

Normalizer można określić dla pól tekstowych ( `Edm.String` i `Collection(Edm.String)` ), które mają co najmniej jedną `filterable` `sortable` Właściwość, lub `facetable` wartość true. Ustawienie Normalizer jest opcjonalne i jest `null` domyślnie. Zalecamy przeprowadzenie oceny wstępnie zdefiniowanych normalizek przed skonfigurowaniem niestandardowej usługi, aby ułatwić korzystanie z nich. Wypróbuj inny normaliz, jeśli wyniki nie są oczekiwane.

Normalizer można określić tylko wtedy, gdy nowe pole zostanie dodane do indeksu. Zachęca się do oceny normalizacji wymaga z góry i przydzielenia normalizacji w początkowym etapie opracowywania, gdy porzucenie i ponowne utworzenie indeksów jest rutynowe. Nie można określić Normalizer dla pola, które zostało już utworzone.

1. Podczas tworzenia definicji pola w [indeksie](/rest/api/searchservice/create-index)ustaw właściwość  **Normalizer** na jedną z następujących wartości: [wstępnie zdefiniowany Normalizer](#predefined-normalizers) , taki jak `lowercase` lub niestandardowy Normalizer (zdefiniowany w tym samym schemacie indeksu).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Niestandardowe normalizacji muszą być zdefiniowane w sekcji **[Normalizer]** indeksu jako pierwsze, a następnie przypisane do definicji pola, jak pokazano w poprzednim kroku. Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu](/rest/api/searchservice/create-index) i [Dodawanie niestandardowych normalizacji](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Aby zmienić Normalizer istniejącego pola, należy całkowicie ponownie skompilować indeks (nie można ponownie skompilować pojedynczych pól).

Dobrym rozwiązaniem w przypadku indeksów produkcyjnych, gdzie ponowne kompilowanie indeksów jest kosztowne, jest utworzenie nowego pola identycznego ze starym, ale przy użyciu nowego Normalizer i użycie go zamiast Starego. Użyj [indeksu aktualizacji](/rest/api/searchservice/update-index) , aby uwzględnić nowe pole i [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) w celu wypełnienia. Później w ramach planowanej obsługi indeksów można wyczyścić indeks w celu usunięcia przestarzałych pól.

## <a name="add-custom-normalizers"></a>Dodawanie niestandardowych normalizacji

Niestandardowe normalizacji są zdefiniowane w schemacie indeksu i można je określić przy użyciu właściwości Field. Definicja niestandardowego Normalizer zawiera nazwę, typ, jeden lub więcej filtrów char i filtrów tokenu. Filtry znaków i filtry tokenów są blokami konstrukcyjnymi dla niestandardowego normalizki i są odpowiedzialne za przetwarzanie tekstu. Te filtry są stosowane od lewej do prawej.

 `token_filter_name_1`Jest nazwą filtru tokenów, a `char_filter_name_1` i `char_filter_name_2` są nazwami filtrów znaków (zobacz [obsługiwane filtry tokenów](#supported-token-filters) i tabele filtry znaków poniżej dla prawidłowych wartości).

Definicja Normalizer jest częścią większego indeksu. Aby uzyskać informacje o pozostałej części indeksu, zobacz [create index API](/rest/api/searchservice/create-index) .

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Niestandardowe normalizki można dodać podczas tworzenia indeksu lub później, aktualizując istniejące. Dodanie niestandardowego Normalizer do istniejącego indeksu wymaga określenia flagi **allowIndexDowntime** w [indeksie Update](/rest/api/searchservice/update-index) i spowoduje, że indeks będzie niedostępny przez kilka sekund.

## <a name="normalizers-reference"></a>Dokumentacja Normalizer

### <a name="predefined-normalizers"></a>Wstępnie zdefiniowane Normalizer
|**Nazwa**|**Opis i opcje**|  
|-|-|  
|Standardowa| Małe litery tekstu, po którym następuje asciifolding.|  
|dużych| Przekształca znaki na małe litery.|
|znaki| Przekształca znaki na wielkie litery.|
|asciifolding| Transformuje znaki, które nie znajdują się w bloku Basic Unicode do ich odpowiedników ASCII, jeśli takie istnieją. Na przykład zmiana usługi w.|
|dla koprocedury| Usuwa dla koprocedury z początku tokenów.|

### <a name="supported-char-filters"></a>Obsługiwane filtry znaków
Więcej informacji o filtrach char można znaleźć w [dokumentacji filtrów znaków](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Obsługiwane filtry tokenów
Na poniższej liście przedstawiono filtry tokenu obsługiwane przez Normalizer i są podzbiorem ogólnych filtrów tokenów związanych z analizą leksykalną. Więcej szczegółowych informacji na temat filtrów można znaleźć w [dokumentacji dotyczącej filtrów tokenów](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [dla koprocedury](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [dużych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [znaki](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Przykład niestandardowego normalizacji

Poniższy przykład ilustruje niestandardową definicję normalizacji z odpowiednimi filtrami znaków i filtrami tokenów. Opcje niestandardowe filtrów char i filtrów tokenów są określane oddzielnie jako nazwane konstrukcje, a następnie przywoływane w definicji Normalizer, jak pokazano poniżej.

* Niestandardowy Normalizer "my_custom_normalizer" jest zdefiniowany w `normalizers` sekcji definicji indeksu.
* Normalizer składa się z dwóch filtrów char i trzech filtrów tokenów: dla koprocedury, małe litery i dostosowany filtr asciifolding "my_asciifolding".
* Pierwszy filtr znaków "map_dash" zastępuje wszystkie kreski znakami podkreślenia, podczas gdy druga "remove_whitespace" usuwa wszystkie spacje.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Zobacz też
+ [Analizatory dla przetwarzania lingwistycznego i tekstu](search-analyzers.md)

+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents) 
