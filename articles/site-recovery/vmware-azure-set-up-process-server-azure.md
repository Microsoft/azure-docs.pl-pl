---
title: Konfigurowanie serwera przetwarzania VMware/fizycznego powrotu po awarii w Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii maszyn wirtualnych platformy Azure do programu VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008491"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurowanie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii

Po przełączeniu w tryb failover maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure przy użyciu [Site Recovery](site-recovery-overview.md)można po awarii wrócić do lokacji lokalnej po jej ponownym uruchomieniu. Aby można było wrócić po awarii, musisz skonfigurować tymczasowy serwer przetwarzania na platformie Azure, aby obsługiwał replikację z platformy Azure do lokacji lokalnej. Tę maszynę wirtualną można usunąć po zakończeniu powrotu po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się więcej o procesie ponownego [zabezpieczania](vmware-azure-reprotect.md) i [powrotu po awarii](vmware-azure-failback.md) .

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. W obszarze magazyn > **Site Recovery infrastruktura** >  **Zarządzanie**  >  **serwerami konfiguracji** wybierz serwer konfiguracji.
2. Na stronie serwer kliknij pozycję **+ serwer przetwarzania**
3. Na stronie **Dodawanie serwera przetwarzania** i wybierz opcję wdrożenia serwera przetwarzania na platformie Azure.
4. Określ ustawienia platformy Azure, w tym subskrypcję używaną w trybie failover, grupę zasobów, region platformy Azure używany do pracy w trybie failover oraz sieć wirtualną, w której znajdują się maszyny wirtualne platformy Azure. Jeśli używasz wielu sieci platformy Azure, musisz mieć serwer przetwarzania w każdym z nich.

   ![Dodaj element galerii serwerów przetwarzania](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. W polu **Nazwa serwera**, **Nazwa użytkownika** i **hasło** Określ nazwę serwera przetwarzania i poświadczenia, które zostaną przypisane uprawnienia administratora na serwerze.
5. Określ konto magazynu, które będzie używane dla dysków maszyny wirtualnej serwera, podsieć, w której znajduje się maszyna wirtualna serwera przetwarzania, oraz adres IP serwera, który zostanie przypisany podczas uruchamiania maszyny wirtualnej.
6. Kliknij przycisk **OK** , aby rozpocząć wdrażanie maszyny wirtualnej serwera przetwarzania. Serwer przetwarzania zostanie wdrożony w Standard_A8_v2 jednostki SKU. Upewnij się, że ta jednostka SKU maszyny wirtualnej jest dostępna dla Twojej subskrypcji.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Rejestrowanie serwera przetwarzania (działającego na platformie Azure) na serwerze konfiguracji (działającego lokalnie)

Po uruchomieniu maszyny wirtualnej serwera przetwarzania należy zarejestrować ją na lokalnym serwerze konfiguracji w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


