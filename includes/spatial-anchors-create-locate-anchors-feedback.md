---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694516"
---
## <a name="provide-feedback-to-the-user"></a>Prześlij opinię użytkownikowi

Można napisać kod do obsługi zdarzenia zaktualizowanej sesji. To zdarzenie jest wyzwalane za każdym razem, gdy sesja poprawi swoje otoczenie. Dzięki temu można:

- Użyj `UserFeedback` klasy, aby przekazać użytkownikowi informacje zwrotne, gdy urządzenie jest przenoszone, a sesja aktualizuje swoją wiedzę o środowisku. W tym celu
- Określ, w jaki sposób ma być wystarczająco dużo śledzonych danych przestrzennych, aby utworzyć kotwice przestrzenne. Należy określić tę opcję `ReadyForCreateProgress` za pomocą `RecommendedForCreateProgress`lub. Gdy `ReadyForCreateProgress` jest więcej niż 1, mamy wystarczającą ilość danych, aby zapisać kotwicę przestrzenną w chmurze, ale zalecamy `RecommendedForCreateProgress` zaczekanie, aż przekroczy to 1.
