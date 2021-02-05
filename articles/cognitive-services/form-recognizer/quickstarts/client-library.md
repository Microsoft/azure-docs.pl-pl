---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy lub interfejs API REST'
titleSuffix: Azure Cognitive Services
description: Za pomocą biblioteki klienta aparatu rozpoznawania formularzy lub interfejsu API REST można utworzyć aplikację przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: przetwarzanie formularzy, automatyczne przetwarzanie danych
ms.openlocfilehash: d468f40d9de7fd7efb4ac7d8021a667e0c6c31f6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584612"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Szybki Start: korzystanie z biblioteki klienckiej aparatu rozpoznawania formularzy lub interfejsu API REST

Rozpocznij pracę z aparatem rozpoznawania formularzy przy użyciu wybranego języka programowania. Aparat rozpoznawania formularzy platformy Azure to usługa poznawcze, która umożliwia tworzenie automatycznego oprogramowania do przetwarzania danych przy użyciu technologii uczenia maszynowego. Zidentyfikuj i Wyodrębnij tekst, pary klucz/wartość, znaczniki wyboru, dane tabeli i inne z formularza dokumenty &mdash; , które zawierają dane z danymi strukturalnymi, które obejmują relacje w oryginalnym pliku. Aparat rozpoznawania formularzy można używać za pośrednictwem interfejsu API REST lub zestawu SDK. Wykonaj następujące kroki, aby zainstalować pakiet SDK i wypróbować przykładowy kod dla podstawowych zadań. 

Użyj aparatu rozpoznawania formularzy do:

* [Analizowanie układu](#analyze-layout)
* [Analizuj faktury](#analyze-invoices)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Analizuj potwierdzenia](#analyze-receipts)
* [Analizowanie kart służbowych](#analyze-business-cards)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
