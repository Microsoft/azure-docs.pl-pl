---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71180065"
---
Po utworzeniu obserwatora `AnchorLocated` zdarzenie zostanie wyzwolone dla każdej zażądanej zakotwiczenia. To zdarzenie jest wyzwalane, gdy zakotwiczenie jest zlokalizowane lub nie można zlokalizować zakotwiczenia. W takiej sytuacji przyczyna zostanie określona w stanie. Gdy wszystkie kotwice obserwatora zostaną przetworzone, znaleziono lub nie zostaną znalezione, `LocateAnchorsCompleted` zdarzenie zostanie wyzwolone. Istnieje limit 35 identyfikatorów dla obserwatora. 
