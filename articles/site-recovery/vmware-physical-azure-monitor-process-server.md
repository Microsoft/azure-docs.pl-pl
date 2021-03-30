---
title: Monitoruj serwer przetwarzania Azure Site Recovery
description: W tym artykule opisano sposób monitorowania Azure Site Recovery serwera przetwarzania używanego na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware/serwera fizycznego
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134732"
---
# <a name="monitor-the-process-server"></a>Monitorowanie serwera przetwarzania

W tym artykule opisano sposób monitorowania serwera przetwarzania [Site Recovery](site-recovery-overview.md) .

- Serwer przetwarzania jest używany podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure.
- Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. Jest ona instalowana domyślnie podczas wdrażania serwera konfiguracji.
- Opcjonalnie, aby skalować i obsłużyć większą liczbę replikowanych maszyn i wyższych woluminów ruchu związanego z replikacją, można wdrożyć dodatkowe serwery przetwarzania skalowalnego w poziomie.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) na temat roli i wdrożenia serwerów przetwarzania.

## <a name="monitoring-overview"></a>Omówienie monitorowania

Ponieważ serwer przetwarzania ma tak wiele ról, szczególnie w przypadku replikowanych pamięci podręcznej, kompresji i transferu danych na platformę Azure, ważne jest, aby regularnie monitorować kondycję serwera przetwarzania.

Istnieje wiele sytuacji, które zwykle wpływają na wydajność serwera przetwarzania. Problemy mające wpływ na wydajność będą mieć wpływ na kondycję maszyny wirtualnej, a ostatecznie wypychanie serwera przetwarzania i jego zreplikowanych maszyn w stan krytyczny. Sytuacje obejmują:

- Duża liczba maszyn wirtualnych korzysta z serwera przetwarzania, zbliżania lub przekroczenia zalecanych ograniczeń.
- Maszyny wirtualne korzystające z serwera przetwarzania mają wysoką szybkość zmian.
- Przepływność sieci między maszynami wirtualnymi i serwerem przetwarzania nie jest wystarczająca do przekazywania danych replikacji do serwera przetwarzania.
- Przepustowość sieci między serwerem przetwarzania i platformą Azure nie jest wystarczająca do przekazywania danych replikacji z serwera przetwarzania do platformy Azure.

Wszystkie te problemy mogą mieć wpływ na cel punktu odzyskiwania (RPO) maszyn wirtualnych. 

**Dlaczego?** Ponieważ generowanie punktu odzyskiwania dla maszyny wirtualnej wymaga, aby wszystkie dyski na maszynie wirtualnej miały wspólny punkt. Jeśli jeden dysk ma dużą wydajność, replikacja działa wolno lub serwer przetwarzania nie jest optymalny, ma wpływ na sposób tworzenia punktów odzyskiwania.

## <a name="monitor-proactively"></a>Aktywne monitorowanie

Aby uniknąć problemów z serwerem przetwarzania, należy wykonać następujące czynności:

- Zapoznaj się z określonymi wymaganiami dotyczącymi serwerów przetwarzania przy użyciu [wskazówek dotyczących pojemności i rozmiarów](site-recovery-plan-capacity-vmware.md#capacity-considerations)oraz upewnij się, że serwery przetwarzania zostały wdrożone i uruchomione zgodnie z zaleceniami.
- Monitoruj alerty i rozwiązywaj problemy w miarę ich występowania, aby zapewnić wydajne działanie serwerów przetwarzania.


## <a name="process-server-alerts"></a>Alerty serwera przetwarzania

Serwer przetwarzania generuje wiele alertów dotyczących kondycji, które zostały podsumowane w poniższej tabeli.

**Typ alertu** | **Szczegóły**
--- | ---
![Dobra kondycja][green] | Serwer przetwarzania jest podłączony i jest w dobrej kondycji.
![Ostrzeżenie][yellow] | Użycie procesora CPU > 80% dla ostatnich 15 minut
![Ostrzeżenie][yellow] | Użycie pamięci > 80% dla ostatnich 15 minut
![Ostrzeżenie][yellow] | Wolne miejsce w folderze pamięci podręcznej < 30% dla ostatnich 15 minut
![Ostrzeżenie][yellow] | Site Recovery monitoruje dane oczekujące/wychodzące co pięć minut i szacuje, że dane w pamięci podręcznej serwera przetwarzania nie mogą być przekazywane do platformy Azure w ciągu 30 minut.
![Ostrzeżenie][yellow] | Usługi serwera przetwarzania nie są uruchomione w ciągu ostatnich 15 minut
![Krytyczne][red] | Użycie procesora CPU > 95% dla ostatnich 15 minut
![Krytyczne][red] | Użycie pamięci > 95% dla ostatnich 15 minut
![Krytyczne][red] | Wolne miejsce w folderze pamięci podręcznej < 25% dla ostatnich 15 minut
![Krytyczne][red] | Site Recovery monitoruje dane oczekujące/wychodzące co pięć minut i szacuje, że dane w pamięci podręcznej serwera przetwarzania nie mogą zostać przekazane do platformy Azure w ciągu 45 minut.
![Krytyczne][red] | Brak pulsu z serwera przetwarzania przez 15 minut.

![Klucz tabeli](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Ogólny stan kondycji serwera przetwarzania jest oparty na wygenerowanym najgorszym alercie.



## <a name="monitor-process-server-health"></a>Monitorowanie kondycji serwera przetwarzania

Stan kondycji serwerów przetwarzania można monitorować w następujący sposób: 

1. Aby monitorować kondycję i stan replikacji replikowanej maszyny oraz jej serwera przetwarzania, w magazynie > **zreplikowane elementy** kliknij maszynę, którą chcesz monitorować.
2. W obszarze **kondycja replikacji** można monitorować stan kondycji maszyny wirtualnej. Kliknij stan, aby przejść do szczegółów, aby uzyskać szczegółowe informacje o błędach.

    ![Kondycja serwera przetwarzania na pulpicie nawigacyjnym maszyny wirtualnej](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. W obszarze **kondycja serwera przetwarzania** można monitorować stan serwera przetwarzania. Szczegóły.

    ![Szczegóły serwera przetwarzania na pulpicie nawigacyjnym maszyny wirtualnej](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Kondycję można również monitorować przy użyciu graficznej reprezentacji na stronie maszyny wirtualnej.
    - Serwer przetwarzania skalowalnego w poziomie zostanie wyróżniony w kolorze pomarańczowym, jeśli są z nim skojarzone ostrzeżenia, a czerwona, jeśli ma jakiekolwiek krytyczne problemy. 
    - Jeśli serwer przetwarzania jest uruchomiony w ramach domyślnego wdrożenia na serwerze konfiguracji, serwer konfiguracji zostanie odpowiednio wyróżniony.
    - Aby przejść do szczegółów, kliknij serwer konfiguracji lub serwer przetwarzania. Zwróć uwagę na wszelkie problemy i wszelkie zalecenia dotyczące korygowania.

Możesz również monitorować serwery przetwarzania w magazynie w obszarze **Site Recovery infrastruktura**. W obszarze **Zarządzanie infrastrukturą Site Recovery** kliknij pozycję **serwery konfiguracji**. Wybierz serwer konfiguracji skojarzony z serwerem przetwarzania i przejdź do szczegółów serwera przetwarzania.


## <a name="next-steps"></a>Następne kroki

- Jeśli masz problemy z serwerami przetwarzania, postępuj zgodnie z naszymi [wskazówkami dotyczącymi rozwiązywania problemów](vmware-physical-azure-troubleshoot-process-server.md)
- Jeśli potrzebujesz więcej pomocy, Opublikuj swoje pytanie na [stronie pytania&](/answers/topics/azure-site-recovery.html)pytań i odpowiedzi dla Azure Site Recovery. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
