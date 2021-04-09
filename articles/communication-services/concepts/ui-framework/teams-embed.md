---
title: Zespoły struktury interfejsu użytkownika — Osadź
titleSuffix: An Azure Communication Services quickstart
description: W tym dokumencie dowiesz się, w jaki sposób można osadzać zespoły struktury interfejsu użytkownika usługi Azure Communication Services w celu tworzenia gotoweych środowisk wywoływania.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e55cfb1a4dff7bfda2323e68777d6f50514b1608
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930238"
---
# <a name="teams-embed"></a>Osadź zespoły

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Osadź zespoły to funkcja usług komunikacyjnych platformy Azure, która koncentruje się na typowych interakcjach połączeń między firmami i biznesem. Rdzeń systemu osadzania zespołów to [wideo i głosowe połączenie](../voice-video-calling/calling-sdk-features.md), ale zespoły są osadzane przez system na platformie Azure wywołujące elementy podstawowe, aby zapewnić pełne środowisko użytkownika na podstawie spotkań Microsoft Teams.

Zespoły Osadź zestawy SDK są zamknięte jako źródło i udostępniaj je w gotowe, w formacie złożonym. Zespoły są osadzane na kanwie swojej aplikacji, a zestaw SDK generuje pełne środowisko użytkownika. Ze względu na to, że środowisko użytkownika jest bardzo podobne do spotkań firmy Microsoft Teams, możesz skorzystać z zalet:

- Skrócenie czasu projektowania i złożoności inżynieryjnej
- Znajomość zespołów przez użytkowników końcowych
- Możliwość ponownego wykorzystania zespołów do [szkolenia użytkowników końcowych](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Osadź zespoły oferuje większość funkcji obsługiwanych w ramach spotkań zespołów, w tym:

- Środowisko przed spotkaniem, w którym użytkownik konfiguruje urządzenia audio i wideo
- Środowisko w spotkaniu do konfigurowania urządzeń audio i wideo
- [Tła wideo](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): umożliwienie uczestnikom rozmycia lub zastępowania tła
- [Wiele opcji dla dużej galerii filmów wideo](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) , tryb razem, fokus, przypinanie i Spotlight
- [Udostępnianie zawartości](https://support.microsoft.comoffice/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8#ID0EABAAA=Mobile): umożliwienie uczestnikom udostępniania ich ekranu

Aby uzyskać więcej informacji o interfejsie użytkownika w porównaniu z innymi zestawami SDK usługi Azure Communication, zobacz [wprowadzenie koncepcji zestawu interfejsu użytkownika](ui-sdk-overview.md). 
