---
title: Wskazówki dotyczące nazewnictwa słów kluczowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: Tworzenie efektywnego słowa kluczowego jest niezbędne do zapewnienia spójnej i dokładnej reakcji urządzenia.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399843"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Wskazówki dotyczące tworzenia efektywnego słowa kluczowego

Tworzenie efektywnego słowa kluczowego jest niezbędne do zapewnienia spójnej i dokładnej reakcji urządzenia. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania. W tym artykule omówiono niektóre zasady dotyczące tworzenia obowiązującego słowa kluczowego.

## <a name="choose-an-effective-keyword"></a>Wybierz efektywne słowo kluczowe

Podczas wybierania słowa kluczowego należy wziąć pod uwagę następujące wytyczne:

> [!div class="checklist"]
> * Słowo kluczowe powinno być słowem lub frazą w języku angielskim.
> * Nie może upłynąć dłużej niż dwa sekundy.
> * Słowa z 4 do 7 sylab działają najlepiej. Na przykład "Hej, Computer" jest dobrym słowem kluczowym. Tylko "Hej" jest słabo.
> * Słowa kluczowe powinny być zgodne z typowymi regułami wymowy w języku angielskim
> * Unikatowy lub nawet utworzony wyraz, który jest zgodny z typowymi regułami wymowy w języku angielskim, może obniżyć liczbę fałszywie dodatnich. Na przykład "computerama" może być dobrym słowem kluczowym.
> * Nie wybieraj wspólnego wyrazu. Na przykład "Eat" i "go" są słowami, które ludzie często mówią w zwykłych konwersacjach. Mogą to być fałszywe wyzwalacze dla Twojego urządzenia.
> * Unikaj używania słowa kluczowego, które może mieć alternatywne wymowy. Użytkownicy będą musieli znać wymowę "Right", aby uzyskać odpowiedzi na swoje urządzenia. Na przykład "509" może być wymawiane "5 0 9", "5 0 9" lub "509". "R.E.I." może być wymawiany "r-e-i" lub "ray". "Na żywo" może być wymawiane "/līv/" lub "/Liv/".
> * Nie należy używać znaków specjalnych, symboli ani cyfr. Na przykład "go #" i "20 + koty" mogą być problematycznymi słowami kluczowymi. Jednak może się zdarzyć, że "nie tylko" lub "dwadzieścia i koty". Można nadal używać symboli w znakowaniu i korzystać z marketingu i dokumentacji, aby wzmocnić odpowiednią wymowę.

> [!NOTE]
> Jeśli wybierzesz słowo znakowe jako słowo kluczowe, upewnij się, że jesteś właścicielem tego znaku towarowego lub że masz uprawnienia od właściciela znaku towarowego do korzystania z tego słowa. Firma Microsoft nie ponosi odpowiedzialności za wszelkie problemy prawne, które mogą wystąpić przy wyborze słowa kluczowego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć niestandardowe słowo kluczowe przy użyciu programu Speech Studio](speech-devices-sdk-create-kws.md).
