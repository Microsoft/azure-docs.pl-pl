---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004356"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się stanie z moją aplikacją podczas wdrażania?

Wszystkie oficjalnie obsługiwane metody wdrażania wprowadzają zmiany w plikach w `/home/site/wwwroot` folderze aplikacji. Te pliki są używane do uruchamiania aplikacji. W związku z tym wdrożenie może zakończyć się niepowodzeniem ze względu na zablokowane pliki. Aplikacja może również zachowywać się nieprzewidywalne podczas wdrażania, ponieważ nie wszystkie pliki zostały zaktualizowane w tym samym czasie. Jest to niepożądane dla aplikacji klienta. Istnieje kilka różnych sposobów, aby uniknąć następujących problemów:

- [Uruchom aplikację bezpośrednio z pakietu zip](../articles/app-service/deploy-run-package.md) bez jej rozpakowania.
- Zatrzymaj aplikację lub Włącz tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz temat [postępowanie z zablokowanymi plikami podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdróż w [miejscu przejściowym](../articles/app-service/deploy-staging-slots.md) z włączonym funkcją [autozamieniania](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
