---
title: Jak wyróżnić tekst interfejs API wyszukiwania w sieci Web Bing przy użyciu znaczników dekoracji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać dekoracji tekstu i wyróżniania trafień w wynikach wyszukiwania przy użyciu interfejs API wyszukiwania w sieci Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854007"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Używanie znaczników dekoracji do wyróżniania tekstu

Bing obsługuje wyróżnianie trafień, co oznacza terminy zapytania (lub inne warunki, które znajdują się w usłudze Bing) w ciągu wyświetlania ciągów niektórych odpowiedzi. Na przykład, `displayUrl`, i `snippet` pola w `name`postaci strony sieci Web mogą zawierać oznaczone terminy zapytania. 

Domyślnie usługi Bing nie uwzględniają znaczników wyróżniania w ciągach wyświetlanych. Aby włączyć znaczniki, Dołącz parametr `textDecorations` zapytania w żądaniu i ustaw go na `true`wartość.

## <a name="hit-highlighting-example"></a>Przykład wyróżniania trafień

Poniższy przykład pokazuje wynik sieci Web dla `Sailing Dinghy`. Usługi Bing oznaczono początkową i końcową okresu kwerendy przy użyciu znaków Unicode E000 i E001.
  
![Podświetlanie trafień](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Przed wyświetleniem wyniku w interfejsie użytkownika, należy zamienić znaki Unicode na te, które są odpowiednie dla Twojego formatu wyświetlania.

## <a name="marker-formatting"></a>Formatowanie znaczników

Bing oferuje opcję użycia znaków Unicode lub tagów HTML jako znaczników. Aby określić, które znaczniki mają być używane, Dołącz parametr zapytania [TextFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) : 

| Wartość             | Obrys                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Znaki Unicode (domyślnie) |
| `textFormat=HTML` | Znaki HTML              |

## <a name="additional-text-decorations"></a>Dodatkowe dekoracje tekstu

Bing może zwracać kilka różnych dekoracji tekstu. Na przykład `Computation` odpowiedź może zawierać znaczniki indeksu dolnego dla terminu `log(2)` zapytania w `expression` polu.

![znaczniki obliczeń](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Jeśli żądanie nie określiło dekoracji, `expression` pole będzie zawierać. `log10(2)` 

Jeśli `textDecorations` jest `true`, Bing może zawierać następujące znaczniki w wyświetlanych ciągach odpowiedzi. Jeśli nie ma odpowiedniego tagu HTML, komórka tabeli jest pusta.

|Unicode|HTML|Opis
|-|-|-
|U + E000|\<b>|Oznacza początek okresu kwerendy (wyróżnianie trafień)
|U + E001|\</b>|Oznacza koniec terminu zapytania
|U + E002|\<i>|Oznacza początek zawartości z kursywą 
|U + E003|\</i>|Oznacza koniec zawartości z kursywą
|U + E004|\<br/>|Oznacza podział wiersza
|U + E005||Oznacza początek numeru telefonu
|U + E006||Oznacza koniec numeru telefonu
|U + E007||Oznacza początek adresu
|U + E008||Oznacza koniec adresu
|U + E009|\&nbsp;|Oznacza nieprzerwaną przestrzeń
|U + E00C|\<mocne>|Oznacza początek pogrubionej zawartości
|U + E00D|\</Strong>|Oznacza koniec pogrubionej zawartości
|U + E00E||Oznacza początek zawartości, której tło powinno być jaśniejsze niż otaczające tło
|U + E00F||Oznacza koniec zawartości, której tło powinno być jaśniejsze niż otaczające tło.
|U + E010||Oznacza początek zawartości, której tło powinno być ciemne niż otaczające tło
|U + E011||Oznacza koniec zawartości, której tło powinno być ciemne niż otaczające tło
|U + E012|\<del>|Oznacza początek zawartości, która powinna zostać przekreślona
|U + E013|\</del>|Oznacza koniec zawartości, która powinna zostać przekreślona
|U + E016|\<> podrzędne|Oznacza początek zawartości indeksu dolnego
|U + E017|\</Sub.>|Oznacza koniec zawartości indeksu dolnego
|U + E018|\<SUP>|Oznacza początek zawartości indeksu górnego
|U + E019|\</SUP>|Oznacza koniec zawartości indeksu górnego

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API wyszukiwania w sieci Web Bing?](overview.md) 
* [Zmiana rozmiaru i przycinanie miniatur](resize-and-crop-thumbnails.md)