---
title: Często zadawane pytania — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejs API przetwarzania obrazów w usłudze Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68564600"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>interfejs API przetwarzania obrazów często zadawane pytania

> [!TIP]
> Jeśli nie możesz znaleźć odpowiedzi na pytania w tym często zadawane pytania, spróbuj zadać interfejs API przetwarzania obrazów społeczność [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z pomocą [techniczną w witrynie UserVoice](https://cognitive.uservoice.com/)

---

**Pytanie**: *Czy mogę wyszkolić interfejs API przetwarzania obrazów, aby używać tagów niestandardowych?  Na przykład chcę uzyskać strumieniowe źródło danych z zdjęć Cat do "uczenia" AI, a następnie odebrać wartość rasy w żądaniu AI.*

**Odpowiedź**: Ta funkcja jest obecnie niedostępna. Jednak nasze inżynierowie pracują nad tym, aby skorzystać z tej funkcji przetwarzanie obrazów.

---

**Pytanie**: *czy przetwarzanie obrazów można używać lokalnie bez połączenia z Internetem?*

**Odpowiedź**: obecnie nie oferujemy lokalnego lub własnego rozwiązania.

---

**Pytanie**: *czy przetwarzanie obrazów można używać do odczytywania tablic licencji?*

**Odpowiedź**: interfejs API obsługi zapewnia dobre wykrywanie tekstu przy użyciu OCR, ale nie jest obecnie zoptymalizowany pod kątem płyt licencyjnych. Ciągle próbujemy ulepszyć nasze usługi i dodałeś OCR do rozpoznawania płyt z licencją Autotekstu do naszej listy żądań funkcji.

---

**Pytanie**: *jakie typy powierzchni pisania są obsługiwane na potrzeby rozpoznawania pisma ręcznego?*

**Odpowiedź**: Technologia działa z różnymi rodzajami powierzchni, takimi jak tablice, oficjalny dokument i żółte notatki.

---

**Pytanie**: *jak długo trwa operacja rozpoznawania pisma ręcznego?*

**Odpowiedź**: ilość czasu, jaką zajmie, zależy od długości tekstu. W przypadku dłuższych tekstów może upłynąć do kilku sekund. W związku z tym po zakończeniu operacji rozpoznawania tekstu napisanego ręcznie może być konieczne poczekanie przed pobraniem wyników przy użyciu operacji pobierania tekstu odręcznego.

---

**Pytanie**: *w jaki sposób technologia rozpoznawania pisma ręcznego obsługuje tekst, który został wstawiony przy użyciu karetki w środku linii?*

**Odpowiedź**: taki tekst jest zwracany jako osobny wiersz przez operację rozpoznawania pisma ręcznego.

---

**Pytanie**: *w jaki sposób usługa rozpoznawania pisma ręcznego przekroczy wykreślone wyrazy lub linie?*

**Odpowiedź**: Jeśli słowa są przekreślone z wieloma wierszami w celu renderowania ich nierozpoznawalności, operacja rozpoznawania pisma ręcznego nie zostanie wybrana. Jeśli jednak słowa są przekreślone przy użyciu jednego wiersza, przekroczenia są traktowane jako szum, a wyrazy nadal są pobierane przez operację rozpoznawania pisma ręcznego.

---

**Pytanie**: *jakie orientacje tekstu są obsługiwane przez technologię rozpoznawania pisma ręcznego?*

**Odpowiedź**: tekst zorientowany na wartości do 30 stopni do 40 stopni może być pobierany przez operację rozpoznawania pisma ręcznego.

---
