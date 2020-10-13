---
title: Wstępnie utworzone modele — LUIS
titleSuffix: Azure Cognitive Services
description: Wbudowane modele zapewniają domeny, intencje, wyrażenia długości i jednostki. Możesz uruchomić aplikację z prezbudowaną domeną lub później dodać odpowiednią domenę do aplikacji.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 32755ea9dccd2a7d4a0e727c7a006491d5ea3521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535887"
---
# <a name="prebuilt-models"></a>Wstępnie utworzone modele

Wbudowane modele zapewniają domeny, intencje, wyrażenia długości i jednostki. Możesz uruchomić aplikację przy użyciu wstępnie skompilowanego modelu lub później dodać odpowiedni model do aplikacji. 

## <a name="types-of-prebuilt-models"></a>Typy wstępnie skompilowanych modeli

LUIS udostępnia trzy typy wstępnie skompilowanych modeli. Każdy model można dodać do aplikacji w dowolnym momencie. 

|Typ modelu|Zawiera|
|--|--|
|[Domeny](luis-reference-prebuilt-domains.md)|Intencje, wyrażenia długości, jednostki|
|Intencje|Intencje, wyrażenia długości|
|[Jednostki](luis-reference-prebuilt-entities.md)|Tylko jednostki| 

## <a name="prebuilt-domains"></a>Wstępnie utworzone domeny

Language Understanding (LUIS) zapewnia *prebudowane domeny*, które są wstępnie szkolonymi modelami [intencji](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) , które współpracują ze sobą w przypadku domen lub wspólnych kategorii aplikacji klienckich. 

Wstępnie skompilowane domeny są przeszkolone i gotowe do dodania do aplikacji LUIS. Intencje i jednostki prekompilowanej domeny są w pełni dostosowywane po dodaniu ich do aplikacji. 

> [!TIP]
> Intencje i jednostki we wstępnie skompilowanej domenie działają najlepiej wspólnie. Lepszym rozwiązaniem jest połączenie intencji i jednostek z tej samej domeny, jeśli jest to możliwe.
> Prebudowana domena narzędzi jest zamiarem, który można dostosować do użycia w dowolnej domenie. Można na przykład dodać `Utilities.Repeat` do swojej aplikacji i wyszkolić rozpoznawanie dowolnego działania, które użytkownik może chcieć powtórzyć w aplikacji. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania prekompilowanego zamiaru domeny

Może się okazać, że wbudowana domena zawiera zamiar, która jest podobna do zamiaru, który ma być używany w aplikacji LUIS, ale ma być zachowywać się inaczej. **Na przykład** prebudowana domena udostępnia `MakeReservation` zamiar tworzenia rezerwacji w restauracji, ale chcesz, aby Twoja aplikacja korzystała z tego celu do tworzenia rezerwacji hotelowych. W takim przypadku można zmodyfikować zachowanie tego celu przez dodanie przykładu wyrażenia długości do zamiaru dotyczącego tworzenia rezerwacji hotelowych, a następnie ponownego uczenia aplikacji. 

Pełną listę wstępnie skompilowanych domen można znaleźć w [dokumentacji prekompilowanych domen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Prebudowane intencje

LUIS zapewnia prebudowane intencje i ich wyrażenia długości dla każdej z wstępnie skompilowanych domen. Intencje mogą być dodawane bez dodawania całej domeny. Dodanie zamiaru to proces dodawania zamiaru i jego wyrażenia długości do aplikacji. Można modyfikować zarówno nazwę zamierzenia, jak i listę wypowiedź.  

## <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki

LUIS zawiera zestaw wstępnie utworzonych jednostek do rozpoznawania wspólnych typów informacji, takich jak daty, godziny, liczby, pomiary i waluta. Wbudowana obsługa jednostek jest różna w zależności od kultury aplikacji LUIS. Aby uzyskać pełną listę wstępnie utworzonych jednostek, które obsługuje LUIS, w tym obsługę według kultury, zobacz [prekompilowane odwołanie do jednostki](./luis-reference-prebuilt-entities.md).

Gdy wbudowana jednostka jest dołączona do aplikacji, jej przewidywania są zawarte w opublikowanej aplikacji. Zachowanie wbudowanych jednostek jest wstępnie nauczone i **nie można** go modyfikować. 

> [!NOTE]
> **element wbudowany. DateTime** jest przestarzały. Jest on zastępowany przez element [**wbudowany. datetimeV2**](luis-reference-prebuilt-datetimev2.md), który zapewnia rozpoznawanie zakresów dat i godzin, a także ulepszone rozpoznawanie niejednoznacznych dat i godzin.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [dodać wstępnie utworzone jednostki](luis-prebuilt-entities.md) do aplikacji.
