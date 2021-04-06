---
title: Przykłady dla interfejsu REST
titleSuffix: Azure Cognitive Search
description: Znajdź przykłady kodu REST platformy Azure Wyszukiwanie poznawcze, które używają interfejsów API REST wyszukiwania lub zarządzania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98956528"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Przykłady kodu REST dla usługi Azure Wyszukiwanie poznawcze

Zapoznaj się z przykładami interfejsu API REST, które demonstrują funkcje i przepływ pracy rozwiązania Wyszukiwanie poznawcze platformy Azure. Te przykłady używają [**interfejsów API REST wyszukiwania**](/rest/api/searchservice).

REST to ostateczny interfejs programistyczny dla usługi Azure Wyszukiwanie poznawcze, a wszystkie operacje, które mogą być wywoływane programowo, są dostępne jako pierwsze w spoczynku, a następnie w zestawach SDK. Z tego powodu większość przykładów w dokumentacji wykorzystuje interfejsy API REST, aby przedstawić lub wyjaśnić ważne pojęcia.

Próbki REST są zwykle opracowywane i testowane w programie Poster, ale można użyć dowolnego klienta, który obsługuje wywołania HTTP:

+ Zacznij od [szybkiego startu: Utwórz indeks wyszukiwanie poznawcze platformy Azure przy użyciu interfejsów API REST](search-get-started-rest.md) , aby uzyskać pomoc w tworzeniu połączeń HTTP.
+ Wypróbuj [Visual Studio Code rozszerzenie dla wyszukiwanie poznawcze platformy Azure](search-get-started-vs-code.md), obecnie w wersji zapoznawczej, jeśli pracujesz w Visual Studio Code.

## <a name="doc-samples"></a>Przykłady doc

Przykłady kodu z zespołu Wyszukiwanie poznawcze przedstawiają funkcje i przepływy pracy. Wiele z tych przykładów odwołuje się do samouczków, przewodników Szybki Start i artykułów z krokami. Te przykłady można znaleźć na [**platformie Azure — przykłady/Azure-Search-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) w witrynie GitHub.

| Samples | Artykuł |
|---------|---------|
| [Szybki start](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Kod źródłowy do [szybkiego startu: Tworzenie indeksu wyszukiwania przy użyciu interfejsów API REST](search-get-started-rest.md). W tym artykule opisano podstawowy przepływ pracy służący do tworzenia, ładowania i wykonywania zapytań dotyczących indeksu wyszukiwania przy użyciu przykładowych danych. |
| [Samouczek](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Kod źródłowy [samouczka: Użyj REST i AI, aby wygenerować zawartość do przeszukiwania z obiektów blob platformy Azure](cognitive-search-tutorial-blob.md). W tym artykule opisano sposób tworzenia zestawu umiejętności, który iteruje za pośrednictwem obiektów blob platformy Azure w celu wyodrębnienia informacji i wywnioskowania struktury.|
| [Debugowanie — sesje](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Kod źródłowy [samouczka: diagnozowanie, naprawianie i zatwierdzanie zmian w zestawu umiejętności](cognitive-search-tutorial-debug-sessions.md). W tym artykule pokazano, jak używać sesji debugowania zestawu umiejętności w Azure Portal. REST służy do tworzenia obiektów używanych podczas debugowania.|
| [Analizatory niestandardowe](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Kod źródłowy [samouczka: Tworzenie niestandardowego analizatora dla numerów telefonów](tutorial-create-custom-analyzer.md). W tym artykule wyjaśniono, jak używać analizatorów do zachowywania wzorców i znaków specjalnych w zawartości z możliwością wyszukiwania.|
| [Magazyn wiedzy](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Kod źródłowy służący do [tworzenia sklepu z bazami danych przy użyciu REST i programu Poster](knowledge-store-create-rest.md). W tym artykule opisano kroki niezbędne do wypełniania sklepu wiedzy używanego na potrzeby przepływów pracy wyszukiwania w bazie wiedzy. |
| [projekcje](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Kod źródłowy służący [do tworzenia kształtów i eksportowania wzbogacania](knowledge-store-projections-examples.md). W tym artykule wyjaśniono, jak określić fizyczne struktury danych w sklepie z bazami wiedzy.|
| [index-Encrypted-obiekty blob](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Kod źródłowy służący [do indeksowania zaszyfrowanych obiektów BLOB przy użyciu indeksatorów obiektów blob i umiejętności](search-howto-index-encrypted-blobs.md). W tym artykule pokazano, jak indeksować dokumenty w usłudze Azure Blob Storage, które zostały wcześniej zaszyfrowane przy użyciu Azure Key Vault. |

> [!Tip]
> Wypróbuj [przeglądarkę przykładów](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) , aby wyszukać przykłady kodu firmy Microsoft w witrynie GitHub, filtrować według produktu, usługi i języka.

## <a name="other-samples"></a>Inne przykłady

Poniższe przykłady są również publikowane przez zespół Wyszukiwanie poznawcze, ale nie są przywoływane w dokumentacji. Skojarzone pliki Readme zawierają instrukcje dotyczące użycia.

| Samples | Opis |
|---------|-------------|
| [Zapytanie — przykłady](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Kolekcje publikowania pokazujące różne techniki zapytań, w tym Wyszukiwanie rozmyte, wyrażenie regularne i wyszukiwanie przy użyciu symboli wieloznacznych, Autouzupełnianie itd. |