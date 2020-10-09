---
title: Współdzielona odpowiedzialność w chmurze — Microsoft Azure
description: Zapoznaj się z udostępnionym modelem odpowiedzialności i zadaniami zabezpieczeń obsługiwanymi przez dostawcę chmury i zadaniami, które są obsługiwane przez użytkownika.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72518371"
---
# <a name="shared-responsibility-in-the-cloud"></a>Wspólna odpowiedzialność w chmurze

Rozważane i oceniane usługi w chmurze publicznej są niezwykle ważne, aby zrozumieć współużytkowany model odpowiedzialności i jakie zadania zabezpieczeń są obsługiwane przez dostawcę chmury i które zadania są obsługiwane przez użytkownika. Obowiązki obciążeń różnią się w zależności od tego, czy obciążenie jest hostowane w ramach oprogramowania jako usługi (SaaS), platformy jako usługi (PaaS), infrastruktury jako usługi (IaaS), czy w lokalnym centrum danych

## <a name="division-of-responsibility"></a>Dział odpowiedzialności
W lokalnym centrum danych jesteś własnością całego stosu. Podczas przechodzenia do chmury niektóre odpowiedzialności są przekazywane do firmy Microsoft. Na poniższym diagramie przedstawiono obszary odpowiedzialności między ty i firmą Microsoft, zgodnie z typem wdrożenia stosu.

![Strefy odpowiedzialności](./media/shared-responsibility/shared-responsibility.png)

W przypadku wszystkich typów wdrożeń w chmurze posiadasz własne dane i tożsamości. Użytkownik jest odpowiedzialny za ochronę danych i tożsamości, zasobów lokalnych i kontrolowanych przez Ciebie składników w chmurze (które różnią się w zależności od typu usługi).

Niezależnie od typu wdrożenia następujące odpowiedzialności są zawsze zachowywane przez użytkownika:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

## <a name="cloud-security-advantages"></a>Zalety chmury
Chmura oferuje znaczące korzyści w zakresie rozwiązywania długotrwałych wyzwań związanych z bezpieczeństwem informacji. W środowisku lokalnym organizacje mogą korzystać z niewypełnienia obowiązków i ograniczonych zasobów w celu inwestowania w zabezpieczeniach, co tworzy środowisko, w którym atakujący mogą wykorzystać luki we wszystkich warstwach.

Na poniższym diagramie przedstawiono tradycyjne podejście, w którym wiele obowiązków związanych z zabezpieczeniami jest niewypełnienia z powodu ograniczonych zasobów. W ramach rozwiązania z obsługą chmury można zmienić zakres obowiązków bezpieczeństwa na dostawcę chmury i ponownie przydzielić zasoby.

![Zalety zabezpieczeń chmury era](./media/shared-responsibility/cloud-enabled-security.png)

W podejściu z obsługą chmury można także korzystać z funkcji zabezpieczeń opartych na chmurze w celu zwiększenia efektywności i korzystania z usługi Cloud Intelligence w celu ulepszania wykrywania zagrożeń i czasu odpowiedzi. Dzięki przesunięciu obowiązków do dostawcy usług w chmurze organizacje mogą uzyskać więcej informacji o zabezpieczeniach, co umożliwia im ponowne przydzielanie zasobów zabezpieczeń i budżetu do innych priorytetów firmy.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat działu odpowiedzialności między ty i firmą Microsoft w ramach wdrożenia SaaS, PaaS i IaaS, zobacz [udostępnianie obowiązków w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
