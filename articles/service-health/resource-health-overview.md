---
title: Przegląd Azure Resource Health
description: Dowiedz się, jak Azure Resource Health pomaga zdiagnozować i uzyskać pomoc techniczną dotyczącą problemów z usługami, które mają wpływ na zasoby platformy Azure.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90967880"
---
# <a name="resource-health-overview"></a>Przegląd Resource Health
 
Azure Resource Health pomaga zdiagnozować i uzyskać pomoc techniczną dotyczącą problemów z usługami, które mają wpływ na zasoby platformy Azure. Raporty IT są raportowane w bieżącej i wcześniejszej kondycji zasobów.

Raporty o [stanie platformy Azure](https://status.azure.com) dotyczące problemów z usługami, które mają wpływ na szeroki zestaw klientów platformy Azure. Resource Health zapewnia spersonalizowany pulpit nawigacyjny kondycji zasobów. Resource Health przedstawia wszystkie czasy braku dostępu do zasobów z powodu problemów z usługą platformy Azure. Te dane ułatwiają sprawdzenie, czy umowa SLA została naruszona.

## <a name="resource-definition-and-health-assessment"></a>Definicja zasobu i Ocena kondycji

*Zasób* to określone wystąpienie usługi platformy Azure, takie jak maszyna wirtualna, aplikacja sieci web lub SQL Database. Resource Health opiera się na sygnałach z różnych usług platformy Azure w celu oceny, czy zasób jest w dobrej kondycji. Jeśli zasób jest w złej kondycji, Resource Health analizuje dodatkowe informacje w celu ustalenia źródła problemu. Raport zawiera również informacje o działaniach podejmowanych przez firmę Microsoft w celu rozwiązania problemu i identyfikacji rzeczy, które można zrobić, aby rozwiązać ten problem.

Aby uzyskać więcej informacji na temat oceny kondycji, zobacz listę typów zasobów i kontroli kondycji w [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stan kondycji

Kondycja zasobu jest wyświetlana jako jeden z następujących stanów.

### <a name="available"></a>Dostępne

*Dostępne* oznacza, że nie wykryto zdarzeń, które mają wpływ na kondycję zasobu. W przypadku, gdy zasób odzyskał sprawność z nieplanowanego przestoju w ciągu ostatnich 24 godzin, zobaczysz powiadomienie "ostatnio rozwiązane".

![Stan * dostępny * dla maszyny wirtualnej, która ma powiadomienie "ostatnio rozwiązane"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niedostępny

*Niedostępne* oznacza, że usługa wykryła trwającą platformę lub zdarzenie niebędące platformą, które ma wpływ na kondycję zasobu.

#### <a name="platform-events"></a>Zdarzenia platformy

Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowane akcje (na przykład zaplanowaną konserwację), jak i nieoczekiwane zdarzenia (na przykład nieplanowany ponowny rozruch hosta lub niedziałający sprzęt hosta przewidywany niepowodzenie po określonym przedziale czasu).

Resource Health zawiera dodatkowe szczegóły dotyczące zdarzenia i procesu odzyskiwania. Pozwala również kontaktować się z usługą pomoc techniczna firmy Microsoft, nawet jeśli nie masz aktywnej umowy pomocy technicznej.

![Stan * niedostępne * dla maszyny wirtualnej z powodu zdarzenia platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Zdarzenia niezwiązane z platformą

Zdarzenia inne niż platformy są wyzwalane przez akcje użytkownika. Przykłady obejmują zatrzymywanie maszyny wirtualnej lub osiągnięcie maksymalnej liczby połączeń z usługą Azure cache for Redis.

![Stan "niedostępne" dla maszyny wirtualnej z powodu zdarzenia niezwiązanego z platformą](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Nieznane

*Nieznany* oznacza, że Resource Health nie otrzymał informacji o zasobie przez więcej niż 10 minut. Mimo że ten stan nie jest ostatecznym oznaczeniem stanu zasobu, jest to ważny punkt danych na potrzeby rozwiązywania problemów.

Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na *dostępny* po kilku minutach.

W przypadku wystąpienia problemów z zasobem *nieznany* stan kondycji może oznaczać, że zdarzenie na platformie ma wpływ na zasób.

![Stan * nieznane * dla maszyny wirtualnej](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Obniżona wydajność

*Zdegradowane* oznacza, że zasób wykrył utratę wydajności, chociaż nadal jest dostępny do użycia.

Różne zasoby mają własne kryteria w przypadku zgłaszania nieprawidłowej wydajności.

![Stan * obniżone * dla maszyny wirtualnej](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Zgłaszanie nieprawidłowego stanu

Jeśli uważasz, że bieżący stan kondycji jest nieprawidłowy, możesz powiedzieć nam, wybierając pozycję **Zgłoś nieprawidłowy stan kondycji**. W przypadkach, gdy problem z platformą Azure wpływa na Ciebie, zachęcamy do skontaktowania się z pomocą techniczną Resource Health.

![Formularz do przesyłania informacji o nieprawidłowym stanie](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informacje historyczne

Możesz uzyskać dostęp do 30-dniowej historii w sekcji **historia kondycji** Resource Health.

![Lista zdarzeń Resource Health w ciągu ostatnich dwóch tygodni](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Rozpoczęcie pracy

Aby otworzyć Resource Health dla jednego zasobu:

1. Zaloguj się w witrynie Azure Portal.
2. Przejdź do zasobu.
3. W menu zasób w okienku po lewej stronie wybierz pozycję **kondycja zasobu**.

![Otwieranie Resource Health z widoku zasobów](./media/resource-health-overview/from-resource-blade.png)

Dostęp do Resource Health można również uzyskać, wybierając pozycję **wszystkie usługi** i wpisując w polu tekstowym wartość **kondycja zasobu** . W okienku **Pomoc i obsługa techniczna** wybierz pozycję [kondycja zasobu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otwieranie Resource Health ze wszystkich usług](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi odwołaniami, aby dowiedzieć się więcej na temat Resource Health:
-  [Typy zasobów i kontrole kondycji w Azure Resource Health](resource-health-checks-resource-types.md)
-  [Często zadawane pytania dotyczące Azure Resource Health](resource-health-faq.md)
