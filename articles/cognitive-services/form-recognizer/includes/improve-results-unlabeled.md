---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "75379299"
---
Przeanalizuj `"confidence"` wartości dla każdego wyniku klucza/wartości pod `"pageResults"` węzłem. Należy również przyjrzeć się ocenom zaufania w `"readResults"` węźle, który odpowiada operacji odczytu tekstu. Zaufanie wyników odczytu nie wpływa na wiarygodność wyników wyodrębniania klucza/wartości, dlatego należy zaznaczyć oba te elementy.
* Jeśli oceny zaufania dla operacji odczytu są niskie, spróbuj poprawić jakość dokumentów wejściowych (zobacz [wymagania wejściowe](../overview.md#input-requirements)).
* Jeśli oceny zaufania dla operacji wyodrębniania klucza/wartości są niskie, upewnij się, że analizowane dokumenty są tego samego typu co dokumenty używane w zestawie szkoleniowym. Jeśli dokumenty w zestawie szkoleniowym mają różne wahania, należy podzielić je na różne foldery i przeanalizować jeden model dla każdej odmiany.

Uzyskane wyniki pewności będą zależeć od przypadku użycia, ale ogólnie jest to dobre rozwiązanie ukierunkowane na wynik 80% lub wyższy. W przypadku bardziej wrażliwych przypadków, takich jak odczytywanie rejestrów medycznych lub zestawień rozliczeniowych, zaleca się ocenę 100%.