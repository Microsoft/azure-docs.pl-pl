---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467242"
---
Przeanalizuj `"confidence"` wartości dla każdego wyniku klucza/wartości pod `"pageResults"` węzłem. Należy również przyjrzeć się ocenom zaufania w `"readResults"` węźle, który odpowiada operacji odczytu tekstu. Zaufanie wyników odczytu nie wpływa na wiarygodność wyników wyodrębniania klucza/wartości, dlatego należy zaznaczyć oba te elementy.
* Jeśli oceny zaufania dla operacji odczytu są niskie, spróbuj poprawić jakość dokumentów wejściowych (zobacz [wymagania wejściowe](../overview.md#input-requirements)).
* Jeśli oceny zaufania dla operacji wyodrębniania klucza/wartości są niskie, upewnij się, że analizowane dokumenty są tego samego typu co dokumenty używane w zestawie szkoleniowym. Jeśli dokumenty w zestawie szkoleniowym mają różne wahania, należy podzielić je na różne foldery i przeanalizować jeden model dla każdej odmiany.

Uzyskane wyniki pewności będą zależeć od przypadku użycia, ale ogólnie jest to dobre rozwiązanie ukierunkowane na wynik 80% lub wyższy. W przypadku bardziej wrażliwych przypadków, takich jak odczytywanie rejestrów medycznych lub zestawień rozliczeniowych, zaleca się ocenę 100%.