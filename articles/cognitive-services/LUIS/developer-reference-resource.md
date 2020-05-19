---
title: Zasoby dla deweloperów — Language Understanding
description: Zestawy SDK, interfejsy API REST, interfejs wiersza polecenia ułatwiają tworzenie aplikacji Language Understanding (LUIS) w języku programowania. Zarządzaj zasobami platformy Azure i przewidywaniami LUIS.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 820811c53f143c9747cd11f45cafb075398b080b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589010"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Zasoby deweloperskie zestawu SDK, REST i interfejsu wiersza polecenia dla Language Understanding (LUIS)

Zestawy SDK, interfejsy API REST, interfejs wiersza polecenia ułatwiają tworzenie aplikacji Language Understanding (LUIS) w języku programowania. Zarządzaj zasobami platformy Azure i przewidywaniami LUIS.

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Korzystając z warstwy zarządzania usługi Azure Cognitive Services, można tworzyć, edytować, wyświetlać i usuwać zasoby Language Understanding lub usług poznawczych.

Zapoznaj się z dokumentacją referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding tworzenia i przewidywania żądań

Usługa Language Understanding jest dostępna z zasobów platformy Azure, które należy utworzyć. Istnieją dwa zasoby:

* Użyj zasobu **tworzenia** do szkolenia do tworzenia, edytowania, uczenia i publikowania.
* Użyj **prognozowania** dla środowiska uruchomieniowego, aby wysłać tekst użytkownika i otrzymać prognozę.

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Użyj [Cognitive Services przykładowego kodu](https://github.com/Azure-Samples/cognitive-services-quickstart-code) , aby poznać i korzystać z najbardziej typowych zadań.

### <a name="rest-specifications"></a>Specyfikacje REST

[Specyfikacje REST Luis](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)wraz ze wszystkimi [specyfikacjami REST platformy Azure](https://github.com/Azure/azure-rest-api-specs)są dostępne publicznie w witrynie GitHub.

### <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API tworzenia i przewidywania są dostępne w interfejsach API REST:

|Typ|Wersja|
|--|--|
|Tworzenie|[V](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[wersja zapoznawcza v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prediction (Prognoza)|[V](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Czytanie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Punkty końcowe REST

LUIS ma obecnie 2 typy punktów końcowych:

* Tworzenie w punkcie końcowym szkolenia
* przewidywanie zapytania w punkcie końcowym środowiska uruchomieniowego.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie w punkcie końcowym szkolenia|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Środowisko uruchomieniowe v2 — wszystkie przewidywania w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime — przewidywanie wersji w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime — przewidywanie gniazda w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

W poniższej tabeli objaśniono parametry, które są oznaczone nawiasami klamrowymi `{}` w poprzedniej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|`your-resource-name`|Nazwa zasobu platformy Azure|
|`q` lub `query`|wypowiedź tekst wysłany z aplikacji klienckiej, na przykład Chat bot|
|`version`|10-znakowa Nazwa wersji|
|`slot`| `production` lub `staging`|

## <a name="app-schema"></a>Schemat aplikacji

[Schemat aplikacji](app-schema-definition.md) jest importowany i eksportowany w `.json` `.lu` formacie lub.

### <a name="language-based-sdks"></a>Zestawy SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Samples|Przewodniki Szybki start|
|--|--|--|--|--|
|C#|[Tworzenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Przewidując](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Tworzenie pakietów NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Prognoza NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Przykłady zestawu SDK dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Tworzenie aplikacji i zarządzanie nią](sdk-authoring.md?pivots=programming-language-csharp)<br>[Punkt końcowy przewidywania zapytania](sdk-query-prediction-endpoint.md)|
|Go|[Tworzenie i prognozowanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Przewidując](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Tworzenie i prognozowanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Tworzenie i prognozowanie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Tworzenie Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywania Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Przewidując](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Tworzenie i prognozowanie](luis-get-started-get-intent-from-rest.md)
|Node.js|[Tworzenie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Przewidując](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Tworzenie NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Przewidywania NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Przewidując](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Tworzenie i prognozowanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Tworzenie i prognozowanie](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tworzenie](sdk-authoring.md?pivots=programming-language-python)<br>[Prognoza przy użyciu REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

Language Understanding (LUIS) udostępnia [kontener](luis-container-howto.md) do udostępniania lokalnych i zawartych w nim wersji aplikacji.

### <a name="export-and-import-formats"></a>Formaty eksportu i importu

Language Understanding zapewnia możliwość zarządzania aplikacją i jej modelami w formacie JSON, `.LU` formacie ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) i skompresowanym pakiecie dla kontenera Language Understanding.

Importowanie i eksportowanie tych formatów jest dostępne z interfejsów API i portalu LUIS. Portal umożliwia importowanie i eksportowanie w ramach listy aplikacji i wersji.

## <a name="other-tools-and-sdks"></a>Inne narzędzia i zestawy SDK

Bot Framework jest dostępny jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa korzystająca [Azure bot Service](https://dev.botframework.com/).

Bot Framework udostępnia [kilka narzędzi](https://github.com/microsoft/botbuilder-tools) , które ułatwiają Language Understanding, w tym:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) — Kompilowanie modeli Luis, przy użyciu plików z promocji
* [Interfejs wiersza polecenia Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) — tworzenie aplikacji Luis.AI i zarządzanie nimi
* [Wysyłanie](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Zarządzanie aplikacjami nadrzędnymi i podrzędnymi
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — automatycznie Generuj kopie zapasowe klas/TypeScript w języku C# dla Twoich LUISów i jednostek.
* [Emulator bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) — aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów utworzonych przy użyciu zestawu SDK bot Framework
* [Układacz bot Framework](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) — zintegrowane narzędzie programistyczne dla deweloperów i zespołów wielodyscyplinarnych do kompilowania botów i konwersacji z platformą Microsoft bot Framework
* [Microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) — narzędzia obsługujące ciągłą integrację i wdrażanie usług NLU Services.

## <a name="next-steps"></a>Następne kroki

* Informacje o typowych [kodach błędów HTTP](luis-reference-response-codes.md)
* [Dokumentacja referencyjna](https://docs.microsoft.com/azure/index) dla wszystkich interfejsów API i zestawów SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) i [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kontenery poznawcze](../cognitive-services-container-support.md)
