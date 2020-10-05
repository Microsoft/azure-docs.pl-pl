---
title: What is Azure Service Health? (Co to jest usługa Azure Service Health?)
description: Spersonalizowane informacje o tym, w jaki sposób aplikacje platformy Azure wpływają na bieżące i przyszłe problemy z usługą i konserwację platformy Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87499776"
---
# <a name="what-is-azure-service-health"></a>What is Azure Service Health? (Co to jest usługa Azure Service Health?)

Platforma Azure oferuje pakiet środowisk, dzięki którym możesz uzyskać informacje o kondycji zasobów w chmurze. Te informacje obejmują bieżące i nadchodzące problemy, takie jak zdarzenia wpływające na usługę, planowana konserwacja i inne zmiany, które mogą mieć wpływ na dostępność.

Azure Service Health jest kombinacją trzech oddzielnych mniejszych usług.

[Stan platformy Azure](azure-status-overview.md) informuje o awarii usługi na platformie Azure na **[stronie stanu platformy Azure](https://status.azure.com)**. Strona to globalny widok kondycji wszystkich usług platformy Azure we wszystkich regionach świadczenia usługi Azure. Strona stanu jest dobrym odwołaniem do zdarzeń o szerokim wpływie, ale zdecydowanie zalecamy, aby bieżący użytkownicy platformy Azure korzystali z usługi Azure Service Health do uzyskiwania informacji o zdarzeniach i obsłudze platformy Azure.

[Usługa Service Health](service-health-overview.md) oferuje spersonalizowany widok kondycji usług i regionów platformy Azure, których używasz. Jest to najlepsze miejsce do wyszukiwania usług związanych z komunikacją na temat przestojów, planowanych działań konserwacyjnych i innych klasyfikatorów kondycji, ponieważ uwierzytelnione Service Health środowisko wie, które usługi i zasoby są obecnie używane. Najlepszym sposobem używania usługi Service Health jest skonfigurowanie alertów, aby otrzymywać powiadomienia za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów z usługą, planowanej konserwacji lub innych zmian, które mogą mieć wpływ na używane usługi platformy Azure i regiony.

Usługa [Resource Health](resource-health-overview.md) udostępnia informacje o kondycji poszczególnych zasobów w chmurze, takich jak określone wystąpienie maszyny wirtualnej. Za pomocą usługi Azure Monitor można również skonfigurować alerty informujące o zmianach dostępności zasobów w chmurze. Resource Health wraz z Azure Monitor powiadomieniami pomogą Ci pomóc Ci w lepszym informowaniu o dostępności zasobów przez minutę i szybko ocenić, czy problem jest spowodowany problemem po stronie użytkownika, czy też z wydarzeniem platformy Azure.

Razem te środowiska umożliwiają kompleksowy wgląd w kondycję platformy Azure, na poziomie szczegółowości najbardziej odpowiednim dla Ciebie.

**Zobacz omówienie strony stanu platformy Azure, usługi Azure Service Health i usługi Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]