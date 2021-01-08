---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: Za pomocą biblioteki klienta aparatu rozpoznawania formularzy można utworzyć aplikację przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: przetwarzanie formularzy, automatyczne przetwarzanie danych
ms.openlocfilehash: d099feff76d74f358a7d7958fb10406a5b8dc188
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029036"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>Szybki Start: korzystanie z biblioteki klienta aparatu rozpoznawania formularzy

Rozpocznij pracę z aparatem rozpoznawania formularzy przy użyciu wybranego języka. Aparat rozpoznawania formularzy platformy Azure to usługa poznawcze, która umożliwia tworzenie automatycznego oprogramowania do przetwarzania danych przy użyciu technologii uczenia maszynowego. Zidentyfikuj i Wyodrębnij wartości tekstowe, pary klucz/wartość oraz dane tabeli z formularza dokumenty &mdash; , które zawierają dane ze strukturą, zawierające relacje w oryginalnym pliku. Wykonaj następujące kroki, aby zainstalować pakiet SDK i wypróbować przykładowy kod dla podstawowych zadań. Biblioteka klienta aparatu rozpoznawania formularzy obecnie dotyczy wersji v 2.0 usługi aparat rozpoznawania.

Biblioteka klienta aparatu rozpoznawania formularzy umożliwia:

* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Rozpoznawanie wizytówek](#recognize-business-cards)
* [Rozpoznaj faktury](#recognize-invoices)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
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
