---
title: 'Samouczek JavaScript: najważniejsze informacje o integracji wyszukiwania'
titleSuffix: Azure Cognitive Search
description: Informacje o zapytaniach wyszukiwania zestawu SDK języka JavaScript używanych w witrynie sieci Web z obsługą wyszukiwania
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726877"
---
# <a name="4---search-integration-highlights"></a>4 — Wyszukiwanie najważniejszych narzędzi Integration

W poprzednich lekcjach dodano wyszukiwanie do statycznej aplikacji sieci Web. W tej lekcji przedstawiono podstawowe kroki, które nawiązują integrację. Jeśli szukasz arkusza Ściągawka na temat integrowania wyszukiwania w aplikacji JavaScript, w tym artykule wyjaśniono, co należy wiedzieć.

## <a name="azure-sdk-azuresearch-documents"></a>Zestaw Azure SDK @azure/search-documents 

Aplikacja funkcji używa zestawu Azure SDK dla Wyszukiwanie poznawcze:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Dokumentacja referencyjna: [Biblioteka kliencka](/javascript/api/overview/azure/search-documents-readme)

Aplikacja funkcji jest uwierzytelniana za pośrednictwem zestawu SDK do Wyszukiwanie poznawcze interfejsu API opartego na chmurze przy użyciu nazwy zasobu, klucza zasobu i nazwy indeksu. Wpisy tajne są przechowywane w ustawieniach statycznej aplikacji sieci Web i ściągane do funkcji jako zmienne środowiskowe. 

## <a name="configure-secrets-in-a-configuration-file"></a>Skonfiguruj wpisy tajne w pliku konfiguracji

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Funkcja platformy Azure: przeszukiwanie katalogu

`Search` [Interfejs API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) pobiera termin wyszukiwania i przeszukuje dokumenty w indeksie wyszukiwania, zwracając listę dopasowań. 

Routing dla interfejsu API wyszukiwania jest zawarty w [function.jsna](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) powiązaniach.

Funkcja platformy Azure pobiera informacje o konfiguracji wyszukiwania i spełnia zapytanie.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Klient: wyszukiwanie w wykazie

Wywołaj funkcję platformy Azure w kliencie z reagują na następujący kod. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Funkcja platformy Azure: sugestie z katalogu

`Suggest` [Interfejs API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) pobiera termin wyszukiwania, gdy użytkownik pisze i sugeruje terminy wyszukiwania, takie jak tytuły książek i autorów w dokumentach w indeksie wyszukiwania, zwracając niewielką listę dopasowań. 

Sugerowany wyszukiwania, `sg` , jest zdefiniowany w [pliku schematu](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) używanym podczas przekazywania zbiorczego.

Routing dla interfejsu API Sugeruj jest zawarty w [function.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) powiązań.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Klient: sugestie z katalogu

Ten interfejs API funkcji sugerował jest wywoływany w aplikacji reagującej w `\src\components\SearchBar\SearchBar.js` ramach inicjalizacji składnika:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Funkcja platformy Azure: pobieranie określonego dokumentu 

`Lookup` [Interfejs API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) przyjmuje identyfikator i zwraca obiekt dokumentu z indeksu wyszukiwania. 

Routing dla interfejsu API wyszukiwania jest zawarty w [function.jsna](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) powiązaniach.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Klient: pobieranie określonego dokumentu 

Ten interfejs API funkcji jest wywoływany w aplikacji reagującej w `\src\pages\Details\Detail.js` ramach inicjalizacji składnika:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Następne kroki

* [Indeksowanie danych usługi Azure SQL](search-indexer-tutorial.md)
