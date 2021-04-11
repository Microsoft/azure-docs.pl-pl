---
title: 'Szybki Start: Language Understanding (LUIS) Biblioteka klienta zestawu SDK i interfejs API REST'
description: Utwórz i Zbadaj aplikację LUIS za pomocą bibliotek klienckich LUIS SDK i interfejsu API REST.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, sztuczna inteligencja, AI, przetwarzanie języka naturalnego, NLP, LUIS, Azure Luis, zrozumienie języka naturalnego, AI chatbot, chatbot Maker, zrozumienie języka naturalnego
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278919"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Szybki Start: Language Understanding (LUIS) — biblioteki klienta i interfejs API REST

Utwórz i Zbadaj aplikację LUIS (AI) platformy Azure przy użyciu bibliotek klienckich LUIS SDK z tym przewodnikiem Szybki Start, korzystając z języka C#, Python lub JavaScript. Można również użyć zawieszania do wysyłania żądań przy użyciu interfejsu API REST.

Language Understanding (LUIS) umożliwia stosowanie przetwarzania języka naturalnego (NLP) do konwersacji użytkownika, tekstu języka naturalnego w celu przewidywania ogólnego znaczenia i ściągania odpowiednich szczegółowych informacji.

* Biblioteka klienta **tworzenia** i interfejs API REST umożliwiają tworzenie, edytowanie, uczenie i publikowanie aplikacji Luis.
* Biblioteka klienta **przewidywania środowiska uruchomieniowego** i interfejs API REST umożliwiają wykonywanie zapytań dotyczących opublikowanej aplikacji.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć aplikację z [portalu Luis](https://www.luis.ai) i usunąć zasoby platformy Azure z [Azure Portal](https://portal.azure.com/).

Jeśli używasz interfejsu API REST, Usuń `ExampleUtterances.JSON` plik z systemu plików, gdy skończysz korzystać z przewodnika Szybki Start.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Uwierzytelnianie w bibliotece klienta — błędy uwierzytelniania wskazują zwykle, że użyto nieprawidłowego punktu końcowego & klucza. Ten przewodnik Szybki Start używa klucza tworzenia i punktu końcowego dla środowiska uruchomieniowego przewidywania jako wygody, ale będzie działał tylko wtedy, gdy nie został jeszcze użyty miesięczny limit przydziału. Jeśli nie możesz użyć klucza tworzenia i punktu końcowego, musisz użyć klucza i punktu końcowego przewidywania w czasie wykonywania w celu uzyskania dostępu do biblioteki klienta zestawu SDK przewidywania środowiska uruchomieniowego.
* Tworzenie jednostek — Jeśli wystąpi błąd podczas tworzenia zagnieżdżonej jednostki uczenia maszynowego użytej w tym samouczku, upewnij się, że skopiowano kod i nie zmienił się kod w celu utworzenia innej jednostki.
* Tworzenie przykładowej wyrażenia długości — Jeśli wystąpi błąd podczas tworzenia przykładowego kodu wypowiedź użytego w tym samouczku, upewnij się, że skopiowano kod i nie zmienił się kod w celu utworzenia innego przykładowego kodu.
* Uczenie — w przypadku wystąpienia błędu szkoleniowego zwykle oznacza to pustą aplikację (nie ma intencji z przykładem wyrażenia długości) lub aplikację z intencjami lub nieprawidłowymi jednostkami.
* Różne błędy — ponieważ kod wywołuje się z bibliotekami klienckimi przy użyciu obiektów tekstowych i JSON, upewnij się, że kod nie został zmieniony.

Inne błędy — jeśli wystąpi błąd, który nie został objęty poprzednią listą, powiadom nas o przesłaniu opinii na końcu na tej stronie. Uwzględnienie języka programowania i wersji zainstalowanych bibliotek klienckich.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Iteracyjne opracowywanie aplikacji dla LUIS](./luis-concept-app-iteration.md)