---
title: plik dołączania
description: plik dołączania
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055400"
---
### <a name="change-the-model-update-frequency"></a>Zmień częstotliwość aktualizacji modelu

W Azure Portal na stronie **Konfiguracja** w zasobów personalizacji Zmień **częstotliwość aktualizacji modelu** na 10 sekund. Ten krótki czas będzie szybko szkolić usługę, co pozwoli zobaczyć, w jaki sposób Górna akcja zmienia się dla każdej iteracji.

![Zmień częstotliwość aktualizacji modelu](../media/settings/configure-model-update-frequency-settings.png)

W przypadku pierwszego wystąpienia pętli programu personalizacji nie istnieje model, ponieważ nie ma żadnych wywołań interfejsu API do uczenia się. Wywołania rangi będą zwracać równe prawdopodobieństwa dla każdego elementu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.