---
title: Zasoby dla deweloperów — Language Understanding
description: Zestawy SDK, interfejsy API REST, interfejs wiersza polecenia ułatwiają tworzenie aplikacji Language Understanding (LUIS) w języku programowania. Zarządzaj zasobami platformy Azure i przewidywaniami LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946170"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Zasoby deweloperskie zestawu SDK, REST i interfejsu wiersza polecenia dla Language Understanding (LUIS)

Zestawy SDK, interfejsy API REST, interfejs wiersza polecenia ułatwiają tworzenie aplikacji Language Understanding (LUIS) w języku programowania. Zarządzaj zasobami platformy Azure i przewidywaniami LUIS.

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Korzystając z warstwy zarządzania usługi Azure Cognitive Services, można tworzyć, edytować, wyświetlać i usuwać zasoby Language Understanding lub usług poznawczych.

Zapoznaj się z dokumentacją referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


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
|Przewidywanie|[V](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Czytanie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Punkty końcowe REST

LUIS ma obecnie 2 typy punktów końcowych:

* **Tworzenie** w punkcie końcowym szkolenia
* **przewidywanie** zapytania w punkcie końcowym środowiska uruchomieniowego.

|Przeznaczenie|Adres URL|
|--|--|
|Wersja 2 autorstwa w punkcie końcowym szkolenia|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Wersja 3 autorstwa w punkcie końcowym szkolenia|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Przewidywanie v2 — wszystkie przewidywania w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Prognozowana wersja v3 — przewidywanie wersji w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Prognozowana wersja v3 — przewidywanie gniazda w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

W poniższej tabeli objaśniono parametry, które są oznaczone nawiasami klamrowymi `{}` w poprzedniej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|`your-resource-name`|Nazwa zasobu platformy Azure|
|`q` lub `query`|wypowiedź tekst wysłany z aplikacji klienckiej, na przykład Chat bot|
|`version`|10-znakowa Nazwa wersji|
|`slot`| `production` lub `staging`|

### <a name="rest-query-string-parameters"></a>Parametry ciągu zapytania REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Schemat aplikacji

[Schemat aplikacji](app-schema-definition.md) jest importowany i eksportowany w `.json` `.lu` formacie lub.

### <a name="language-based-sdks"></a>Zestawy SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Przewodniki Szybki start|
|--|--|--|--|
|C#|[Tworzenie](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Przewidując](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[Tworzenie pakietów NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Prognoza NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Prognozowanie zapytań](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Tworzenie i prognozowanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Tworzenie i prognozowanie](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Tworzenie Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywania Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Tworzenie](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Przewidując](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[Tworzenie NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Przewidywania NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Przewidując](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Tworzenie i prognozowanie](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Przewidując](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Kontenery

Language Understanding (LUIS) udostępnia [kontener](luis-container-howto.md) do udostępniania lokalnych i zawartych w nim wersji aplikacji.

### <a name="export-and-import-formats"></a>Formaty eksportu i importu

Language Understanding zapewnia możliwość zarządzania aplikacją i jej modelami w formacie JSON, `.LU` formacie ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) i skompresowanym pakiecie dla kontenera Language Understanding.

Importowanie i eksportowanie tych formatów jest dostępne z interfejsów API i portalu LUIS. Portal umożliwia importowanie i eksportowanie w ramach listy aplikacji i wersji.

## <a name="workshops"></a>Warsztatów

* GitHub: (warsztat) [konwersacja-AI: NLU przy użyciu Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Narzędzia do ciągłej integracji

* GitHub: (wersja zapoznawcza) [opracowywanie aplikacji Luis przy użyciu praktyk DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) — narzędzia obsługujące ciągłą integrację i wdrażanie usług NLU Services.

## <a name="bot-framework-tools"></a>Narzędzia bot Framework

Bot Framework jest dostępny jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa korzystająca [Azure bot Service](https://dev.botframework.com/).

Bot Framework udostępnia [kilka narzędzi](https://github.com/microsoft/botbuilder-tools) , które ułatwiają Language Understanding, w tym:
* [Emulator bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) — aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów utworzonych przy użyciu zestawu SDK bot Framework
* [Układacz bot Framework](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) — zintegrowane narzędzie programistyczne dla deweloperów i zespołów wielodyscyplinarnych do kompilowania botów i konwersacji z platformą Microsoft bot Framework
* [Przykłady platformy bot Framework](https://github.com/microsoft/botbuilder-samples) — w #C, JavaScript, TypeScript i Python

## <a name="next-steps"></a>Następne kroki

* Informacje o typowych [kodach błędów HTTP](luis-reference-response-codes.md)
* [Dokumentacja referencyjna](../../index.yml) dla wszystkich interfejsów API i zestawów SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) i [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kontenery poznawcze](../cognitive-services-container-support.md)
