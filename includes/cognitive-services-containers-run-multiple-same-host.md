---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000553"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, że każdy kontener jest uruchamiany z innym uwidocznionym portem. Na przykład Uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Można korzystać z tego kontenera i innego kontenera Cognitive Services platformy Azure uruchomionego na HOŚCIE. Można również mieć wiele kontenerów tego samego kontenera Cognitive Services uruchomione.
