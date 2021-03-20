---
title: Skonfiguruj zasady replikacji dla odzyskiwania po awarii programu VMware przy użyciu Azure Site Recovery | Microsoft Docs
description: Opisuje sposób konfigurowania ustawień replikacji dla odzyskiwania po awarii programu VMware na platformie Azure przy użyciu Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84699604"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurowanie zasad replikacji dla odzyskiwania po awarii środowiska VMware i zarządzanie nimi

W tym artykule opisano sposób konfigurowania zasad replikacji w przypadku replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Tworzenie zasad

1. Wybierz pozycję **Zarządzaj**  >  **infrastrukturą Site Recovery**.
2. W programie **dla oprogramowania VMware i maszyn fizycznych** wybierz pozycję **zasady replikacji**.
3. Kliknij pozycję **+ zasady replikacji**, a następnie określ nazwę zasad.
4. W polu **próg punktu odzyskiwania** Określ limit celu punktu odzyskiwania. Alerty są generowane, gdy Ciągła replikacja przekracza ten limit.
5. W obszarze **Przechowywanie punktów odzyskiwania** określ w godzinach, jak długie będzie okno przechowywania dla każdego punktu odzyskiwania. Chronione maszyny można odzyskać do dowolnego punktu w tym oknie przechowywania. W przypadku maszyn replikowanych do magazynu w warstwie Premium jest obsługiwany czas przechowywania do 24 godzin. Do 72 godzin jest obsługiwana w przypadku magazynu w warstwie Standardowa.
6. W obszarze **częstotliwość migawek spójnych na poziomie aplikacji** wybierz z listy rozwijanej, jak często (w godzinach) punkty odzyskiwania zawierające migawki spójne z aplikacjami mają być tworzone. Jeśli chcesz wyłączyć generowanie punktów spójności aplikacji, wybierz wartość "wyłączone" na liście rozwijanej.
7. Kliknij przycisk **OK**. Tworzenie zasad powinno potrwać od 30 do 60 sekund.

Podczas tworzenia zasad replikacji zostanie automatycznie utworzona zgodna zasada replikacji w przypadku powrotu po awarii z sufiksem "powrót po awarii". Po utworzeniu zasad można je edytować, wybierając ją > **Edytuj ustawienia**.

## <a name="associate-a-configuration-server"></a>Kojarzenie serwera konfiguracji

Skojarz zasady replikacji z lokalnym serwerem konfiguracji.

1. Kliknij pozycję **Skojarz** i wybierz serwer konfiguracji.

    ![Kojarzenie serwera konfiguracji](./media/vmware-azure-set-up-replication/associate1.png)
2. Kliknij przycisk **OK**. Kojarzenie serwera konfiguracji powinno potrwać od 1 do 2 minut.

    ![Kojarzenie serwera konfiguracji](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Edytowanie zasad

Możesz zmodyfikować zasady replikacji po jej utworzeniu.

- Zmiany wprowadzone w zasadach są stosowane do wszystkich maszyn przy użyciu zasad.
- Jeśli chcesz skojarzyć zreplikowane maszyny z innymi zasadami replikacji, musisz wyłączyć i ponownie włączyć ochronę odpowiednich maszyn.

Edytuj zasady w następujący sposób:
1. Wybierz pozycję **Zarządzaj**  >    >  **zasadami replikacji** Site Recovery infrastruktura.
2. Wybierz zasady replikacji, które chcesz zmodyfikować.
3. Kliknij pozycję **Edytuj ustawienia**, a następnie w razie potrzeby zaktualizuj odpowiednie pola próg/czas przechowywania punktu odzyskiwania/częstotliwość migawek dla aplikacji.
4. Jeśli chcesz wyłączyć generowanie punktów spójności aplikacji, wybierz wartość "off" na liście rozwijanej **częstotliwości migawek spójnej na poziomie aplikacji**.
5. Kliknij pozycję **Zapisz**. Zasady powinny zostać zaktualizowane w ciągu 30 do 60 sekund.



## <a name="disassociate-or-delete-a-replication-policy"></a>Skojarzenie lub usuwanie zasad replikacji

1. Wybierz zasady replikacji.
    a. Aby usunąć skojarzenie zasad z serwera konfiguracji, upewnij się, że żadne zreplikowane maszyny nie korzystają z zasad. Następnie kliknij przycisk **Usuń skojarzenie**.
    b. Aby usunąć zasady, upewnij się, że nie jest ona skojarzona z serwerem konfiguracji. Następnie kliknij pozycję **Usuń**. Usunięcie może zająć od 30-60 sekund.
2. Kliknij przycisk **OK**.
