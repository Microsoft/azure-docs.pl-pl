---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start interfejsu API REST dowiesz się, jak wywoływać interfejsy API REST platformy Azure Wyszukiwanie poznawcze przy użyciu programu Poster lub Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714185"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu interfejsów API REST

W tym artykule opisano sposób interaktywnego tworzenia żądań interfejsu API REST przy użyciu [interfejsów API REST usługi Azure wyszukiwanie poznawcze](/rest/api/searchservice) i klienta interfejsu API w celu wysyłania i otrzymywania żądań. Za pomocą klienta interfejsu API i tych instrukcji można wysyłać żądania i wyświetlać odpowiedzi przed zapisaniem kodu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wykonywać podstawowe zadania, możesz przejść do przodu z dodatkowymi wywołaniami interfejsu API REST, aby uzyskać bardziej zaawansowane funkcje, takie jak indeksatory, lub skonfigurować [potok wzbogacania](cognitive-search-tutorial-blob.md) , który dodaje przekształcenia zawartości do indeksowania. W następnym kroku zalecamy następujące łącze:

> [!div class="nextstepaction"]
> [Samouczek: używanie REST i AI do generowania zawartości z możliwością wyszukiwania z obiektów blob platformy Azure](cognitive-search-tutorial-blob.md)