---
title: Kody odpowiedzi HTTP interfejsu API — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jakie kody odpowiedzi HTTP są zwracane z interfejsów API tworzenia LUIS i punktu końcowego
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.openlocfilehash: 46a7fff86d3a8c4539b77a0f271179d68e7a26d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541683"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Typowe kody odpowiedzi interfejsu API i ich znaczenie

Interfejsy API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) i [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) zwracają kody odpowiedzi HTTP. Podczas gdy komunikaty odpowiedzi zawierają informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólny.

## <a name="common-status-codes"></a>Typowe kody stanu
W poniższej tabeli wymieniono najbardziej typowe kody stanu odpowiedzi HTTP dla interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) i [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) :

|Kod|Interfejs API|Objaśnienie|
|:--|--|--|
|400|Tworzenie, punkt końcowy|parametry żądania są niepoprawne, co oznacza, że brakuje wymaganych parametrów, są źle sformułowane lub zbyt duże|
|400|Tworzenie, punkt końcowy|treść żądania jest niepoprawna, co oznacza, że brakuje pliku JSON, został źle sformułowany lub jest zbyt duży|
|401|Tworzenie|użyty klucz subskrypcji punktu końcowego zamiast klucza tworzenia|
|401|Tworzenie, punkt końcowy|nieprawidłowy, źle sformułowany lub pusty klucz|
|401|Tworzenie, punkt końcowy| klucz nie jest zgodny z regionem|
|401|Tworzenie|nie jesteś właścicielem lub współpracownikiem|
|401|Tworzenie|Nieprawidłowa kolejność wywołań interfejsu API|
|403|Tworzenie, punkt końcowy|Przekroczono łączny limit przydziału miesięcznego klucza|
|409|Punkt końcowy|Aplikacja jest nadal ładowana|
|410|Punkt końcowy|Aplikacja musi być przeszkolna i ponownie opublikowana|
|414|Punkt końcowy|zapytanie przekracza maksymalny limit znaków|
|429|Tworzenie, punkt końcowy|Przekroczono limit szybkości (żądania/s)|

## <a name="next-steps"></a>Następne kroki

* [Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) interfejsu API REST i dokumentacja [punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
