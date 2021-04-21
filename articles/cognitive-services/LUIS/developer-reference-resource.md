---
title: Zasoby dla deweloperów — Language Understanding
description: Zestawy SDK, interfejsy API REST i interfejs wiersza polecenia ułatwiają tworzenie Language Understanding (LUIS) w języku programowania. Zarządzanie zasobami platformy Azure i przewidywaniami usługi LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787111"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Zasoby dla deweloperów zestawu SDK, interfejsu REST i interfejsu wiersza Language Understanding (LUIS)

Zestawy SDK, interfejsy API REST i interfejs wiersza polecenia ułatwiają tworzenie Language Understanding (LUIS) w języku programowania. Zarządzanie zasobami platformy Azure i przewidywaniami usługi LUIS.

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Warstwa zarządzania Azure Cognitive Services umożliwia tworzenie, edytowanie, tworzenie listy i usuwanie zasobu Language Understanding usług Cognitive Service.

Znajdź dokumentację referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding tworzenia i przewidywania

Dostęp do Language Understanding jest uzyskiwany z zasobu platformy Azure, który należy utworzyć. Istnieją dwa zasoby:

* Użyj zasobu **tworzenia do szkolenia** w celu tworzenia, edytowania, trenowania i publikowania.
* Użyj przewidywania **dla** środowiska uruchomieniowego, aby wysłać tekst użytkownika i otrzymać przewidywanie.

Dowiedz się więcej o [punkcie końcowym przewidywania w wersji 3.](luis-migration-api-v3.md)

Użyj [Cognitive Services przykładowego kodu,](https://github.com/Azure-Samples/cognitive-services-quickstart-code) aby nauczyć się i korzystać z najczęściej wykonywanych zadań.

### <a name="rest-specifications"></a>Specyfikacje REST

Specyfikacje [REST usługi LUIS](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)wraz ze wszystkimi specyfikacjami REST platformy [Azure](https://github.com/Azure/azure-rest-api-specs)są publicznie dostępne w witrynie GitHub.

### <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API punktu końcowego tworzenia i przewidywania są dostępne z interfejsów API REST:

|Typ|Wersja|
|--|--|
|Tworzenie|[Wersja 2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[wersja zapoznawcza 3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Przewidywanie|[Wersja 2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Wersja 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Punkty końcowe REST

Usługa LUIS ma obecnie 2 typy punktów końcowych:

* **tworzenie w** punkcie końcowym szkolenia
* przewidywanie **zapytań** w punkcie końcowym środowiska uruchomieniowego.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie w wersji 2 w punkcie końcowym trenowania|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Tworzenie w wersji 3 w punkcie końcowym szkolenia|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Przewidywanie w wersji 2 — wszystkie przewidywania w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Przewidywanie wersji 3 — przewidywanie wersji w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Przewidywanie w wersji 3 — przewidywanie miejsca w punkcie końcowym środowiska uruchomieniowego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

W poniższej tabeli wyjaśniono parametry oznaczone nawiasami `{}` klamrowymi w poprzedniej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|`your-resource-name`|Nazwa zasobu platformy Azure|
|`q` lub `query`|tekst wypowiedzi wysyłany z aplikacji klienckiej, takiej jak czatbot|
|`version`|10-znakowa nazwa wersji|
|`slot`| `production` lub `staging`|

### <a name="rest-query-string-parameters"></a>Parametry ciągu zapytania REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Schemat aplikacji

Schemat [aplikacji jest](app-schema-definition.md) importowany i eksportowany w `.json` formacie lub `.lu` .

### <a name="language-based-sdks"></a>Zestawy SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Przewodniki Szybki start|
|--|--|--|--|
|C#|[Tworzenie](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Przewidywania](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[Tworzenie nuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Przewidywanie nuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Przewidywanie zapytań](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Tworzenie i przewidywanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Tworzenie i przewidywanie](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Tworzenie w maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywanie maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Tworzenie](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Przewidywania](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[Tworzenie npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Przewidywanie NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Przewidywania](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Tworzenie i przewidywanie](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](./client-libraries-rest-api.md?pivots=rest-api)<br>[Przewidywania](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Kontenery

Language Understanding (LUIS) udostępnia [kontener](luis-container-howto.md) w celu zapewnienia lokalnych i zawartych wersji aplikacji.

### <a name="export-and-import-formats"></a>Formaty eksportu i importu

Language Understanding umożliwia zarządzanie aplikacją i jej modelami w formacie JSON, w formacie `.LU` ([LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)i skompresowanym pakietem dla Language Understanding kontenera.

Importowanie i eksportowanie tych formatów jest dostępne z interfejsów API i portalu usługi LUIS. Portal umożliwia importowanie i eksportowanie w ramach listy Aplikacje i Wersje.

## <a name="workshops"></a>Warsztaty

* GitHub: (Workshop) [Conversational-AI: NLU using LUIS (Konwersacyjna-AI: NLU przy użyciu usługi LUIS)](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Narzędzia do ciągłej integracji

* GitHub: (wersja zapoznawcza) [Tworzenie aplikacji LUIS przy użyciu praktyk Metodyki DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) — narzędzia do ciągłej integracji i ciągłego wdrażania dla usług NLU.

## <a name="bot-framework-tools"></a>Bot Framework narzędzi

Bot Framework jest dostępny jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa przy użyciu [Azure Bot Service.](https://dev.botframework.com/)

Bot Framework udostępnia [kilka narzędzi,](https://github.com/microsoft/botbuilder-tools) które ułatwiają Language Understanding, w tym:
* [Bot Framework — aplikacja](https://github.com/Microsoft/BotFramework-Emulator/releases) desktopowa, która umożliwia deweloperom botów testowanie i debugowanie botów budowaną przy użyciu zestawu BOT FRAMEWORK SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) — zintegrowane narzędzie deweloperacyjne dla deweloperów i wielodyscyplinarnych zespołów do tworzenia botów i konwersacyjne środowisko pracy z Microsoft Bot Framework
* [Bot Framework przykłady —](https://github.com/microsoft/botbuilder-samples) w językach #C, JavaScript, TypeScript i Python

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o typowych [kodach błędów HTTP](luis-reference-response-codes.md)
* [Dokumentacja referencyjna](../../index.yml) dla wszystkich interfejsów API i zestawów SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) i [Azure Bot Service](https://dev.botframework.com/)
* [LuDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kontenery cognitive](../cognitive-services-container-support.md)
