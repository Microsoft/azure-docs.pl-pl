---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76545220"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Skonfiguruj sesję zakotwiczenia w chmurze

Zajmiemy się konfiguracją następnej sesji zakotwiczenia w chmurze. W pierwszym wierszu ustawiamy dostawcę czujnika w sesji. Od teraz wszystkie kotwice tworzone w trakcie sesji zostaną skojarzone z zestawem odczytów czujników. Następnie Utwórz wystąpienie kryteriów lokalizowania blisko urządzenia i zainicjuj je w celu dopasowania do wymagań aplikacji. Na koniec poinstruujemy, aby sesja korzystała z danych czujników podczas lokalizowania kotwic przez utworzenie obserwatora z naszych kryteriów w najbliższej lokalizacji.