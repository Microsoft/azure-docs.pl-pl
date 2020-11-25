---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999760"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Skonfiguruj sesję zakotwiczenia w chmurze

Zajmiemy się konfiguracją następnej sesji zakotwiczenia w chmurze. W pierwszym wierszu ustawiamy dostawcę czujnika w sesji. Od teraz wszystkie kotwice tworzone w trakcie sesji zostaną skojarzone z zestawem odczytów czujników. Następnie Utwórz wystąpienie kryteriów lokalizowania blisko urządzenia i zainicjuj je w celu dopasowania do wymagań aplikacji. Na koniec poinstruujemy, aby sesja korzystała z danych czujników podczas lokalizowania kotwic przez utworzenie obserwatora z naszych kryteriów w najbliższej lokalizacji.