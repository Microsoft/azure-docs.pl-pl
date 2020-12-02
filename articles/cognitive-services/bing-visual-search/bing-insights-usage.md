---
title: Przykłady usługi Bing Insights — wyszukiwanie wizualne Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera przykłady korzystania z wyszukiwanie wizualne Bing i wyświetlania szczegółowych informacji o obrazach w programie Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493359"
---
# <a name="examples-of-bing-insights-usage"></a>Przykłady użycia usługi Bing Insights

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

W tym artykule przedstawiono przykłady użycia usługi Bing i wyświetlania szczegółowych informacji o obrazach w programie Bing.com.

## <a name="pagesincluding-insight-example"></a>Przykład usługi PagesIncluding Insight

Poniżej znajduje się łącze do pierwszej strony sieci Web i umożliwia użytkownikowi rozwijanie i zwijanie listy innych stron internetowych, które zawierają obraz:

![Rozwinięte strony, w tym](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Przykład usługi ShoppingSources Insight

Poniżej przedstawiono sposób wyświetlania przez usługę Bing źródeł zakupów dla produktów widocznych na obrazie:

![Źródła zakupów](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Przykład usługi VisualSearch Insight

Poniżej przedstawiono sposób wyświetlania wizualnie podobnych obrazów w usłudze Bing (zobacz **powiązane obrazy** w przykładzie):

![Obrazy podobne wizualnie](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Przykład szczegółowych informacji

Poniżej przedstawiono sposób wyświetlania przez usługę Bing przepisów dotyczących żywności widocznej na obrazie. Przykład umożliwia użytkownikowi znane są następujące przepisy:

![Przepisy i strony, w tym](./media/recipes-pages-including.PNG)

 I udostępnia link do przepisów, gdy użytkownik rozwinie listę:

![Rozwinięte strony przepisowe, w tym](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Przykład usługi RelatedSearches Insight

Poniżej przedstawiono sposób wyświetlania przez usługę Bing pokrewnych wyszukiwań obrazów wykonywanych przez inne osoby. Jeśli użytkownik kliknie obraz, użytkownik zostanie przełączony do strony wyników wyszukiwania Bing.com/images dla tego powiązanego zapytania.

![Powiązane wyszukiwania obrazów](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Przykład usługi Entity Insight

Poniżej pokazano, w jaki sposób Bing może wyświetlać informacje o jednostce (osoba, miejsce lub rzecz) widocznej na obrazie. Jeśli użytkownik kliknie łącze jednostki, użytkownik zostanie przełączony do strony wyników wyszukiwania Bing.com dla jednostki:

![Jednostka pokazana w obrazie](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Wyświetlanie innych szczegółowych informacji, które użytkownik może zbadać

Poniżej pokazano, w jaki sposób Bing może wyświetlać inne informacje o obrazie, który użytkownik może zbadać.

![Poznaj inne szczegółowe informacje o obrazie](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Pola ograniczenia i punkty aktywne

Tagi inne niż domyślne zawierają pole ograniczenia, które identyfikuje obszar zainteresowania obrazu, do którego odnosi się tag. Jeśli pole ograniczenia nie identyfikuje całego obrazu, użyj pola ograniczenia, aby utworzyć gorącą plamę obrazu. Użytkownik może kliknąć punkt aktywny, aby uzyskać informacje dotyczące zawartości znalezionej pod punktem aktywnym (lub prostokątem). Na przykład, jeśli obraz jest obrazem wysokiej jakości, wyniki mogą zawierać Tagi (i pola ograniczające) dla akcesoriów widocznych na obrazie, takich jak okrężnica, biżuterii, scarfs i tak dalej. Poniższy przykład pokazuje prostokąt gorąca dla szklanych pokazanych na obrazie:

![Pole ograniczenia i punkt aktywny](./media/click-to-search.PNG)

## <a name="next-steps"></a>Następne kroki

Aby zacząć korzystać z pierwszego żądania, zobacz Przewodniki Szybki Start:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)