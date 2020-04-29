---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122894"
---
## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Konfiguracja** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Ten krótki czas będzie szybko szkolić usługę, co pozwoli zobaczyć, w jaki sposób Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](../media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.