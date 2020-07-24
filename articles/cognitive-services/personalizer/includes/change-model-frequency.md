---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133882"
---
## <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Konfiguracja** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Ten krótki czas będzie szybko szkolić usługę, co pozwoli zobaczyć, w jaki sposób Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](../media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.