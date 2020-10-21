---
title: Informacje o sieci w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure z Azure Site Recovery
description: Zawiera omówienie sieci na potrzeby replikacji maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: 1189324cf0bb2731a100032058c7ba9ae4add758
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332046"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Informacje o sieci w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure



Ten artykuł zawiera wskazówki dotyczące sieci podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Site Recovery zapewnia odzyskiwanie po awarii w [tym scenariuszu](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typowa infrastruktura sieci

Na poniższym diagramie przedstawiono typowe środowisko platformy Azure dla aplikacji uruchamianych na maszynach wirtualnych platformy Azure:

![Diagram przedstawiający typowe środowisko platformy Azure dla aplikacji uruchamianych na maszynach wirtualnych platformy Azure.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Jeśli używasz usługi Azure ExpressRoute lub połączenia sieci VPN z sieci lokalnej z platformą Azure, środowisko jest następujące:

![Klient-środowisko](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Zwykle sieci są chronione za pomocą zapór i sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Do kontrolowania łączności sieciowej należy używać tagów usługi. Sieciowych grup zabezpieczeń powinna zezwalać na wiele tagów usługi do kontrolowania łączności wychodzącej.

>[!IMPORTANT]
> Używanie uwierzytelnionego serwera proxy do sterowania łącznością sieciową nie jest obsługiwane przez Site Recovery i nie można włączyć replikacji.

>[!NOTE]
>- Filtrowanie na podstawie adresów IP nie powinno być przeprowadzane w celu kontrolowania łączności wychodzącej.
>- Azure Site Recovery adresów IP nie należy dodawać w tabeli routingu platformy Azure w celu kontrolowania łączności wychodzącej.

## <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na następujące adresy URL Site Recovery:

**Adres URL** | **Szczegóły**
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z poziomu maszyny wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz zezwolić na dostęp do adresów URL określonych kont magazynu (np. cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *. blob.core.windows.net
login.microsoftonline.com | Wymagany do autoryzacji i uwierzytelniania do adresów URL usługi Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja z usługą Site Recovery mogła się odbywać z poziomu maszyny wirtualnej.
*.servicebus.windows.net | Wymagane, aby dane dotyczące monitorowania i diagnostyki Site Recovery mogły być zapisywane z poziomu maszyny wirtualnej.
*.vault.azure.net | Zezwala na dostęp do włączania replikacji dla maszyn wirtualnych z obsługą ADE za pośrednictwem portalu
*. automation.ext.azure.com | Umożliwia włączenie autouaktualnienia agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu

## <a name="outbound-connectivity-using-service-tags"></a>Łączność wychodząca przy użyciu tagów usługi

Korzystając z sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, te Tagi usług muszą być dozwolone.

- Dla kont magazynu w regionie źródłowym:
    - Utwórz opartą na [znaczniku usługi magazynu](../virtual-network/security-overview.md#service-tags) regułę sieciowej grupy zabezpieczeń dla regionu źródłowego.
    - Zezwalaj na te adresy, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej z poziomu maszyny wirtualnej.
- Utwórz opartą na [usłudze Azure Active Directory regułę sieciowej grupy zabezpieczeń (AAD)](../virtual-network/security-overview.md#service-tags) , aby umożliwić dostęp do wszystkich adresów IP odpowiadających usłudze AAD
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na znacznikach usługi EventsHub dla regionu docelowego, umożliwiając dostęp do monitorowania Site Recovery.
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na znacznikach usługi AzureSiteRecovery, aby umożliwić dostęp do usługi Site Recovery w dowolnym regionie.
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na znacznikach usługi AzureKeyVault. Jest to wymagane tylko w przypadku włączania replikacji maszyn wirtualnych z obsługą ADE za pośrednictwem portalu.
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na znacznikach usługi GuestAndHybridManagement. Jest to wymagane tylko do włączenia autouaktualnienia agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu.
- Zalecamy utworzenie wymaganych reguł sieciowej grupy zabezpieczeń na testowym sieciowej grupy zabezpieczeń i sprawdzenie, czy nie ma żadnych problemów przed utworzeniem reguł na sieciowej grupy zabezpieczeń produkcyjnej.

## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

Ten przykład pokazuje, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, Użyj reguł "Zezwalaj na ruch wychodzący protokołu HTTPS" na port: 443 dla wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacją źródłową maszyny wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "środkowe stany USA".

### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. Wschód" na sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![Zrzut ekranu przedstawia Dodawanie reguły zabezpieczeń ruchu wychodzącego dla sieciowej grupy zabezpieczeń dla miejsca do magazynowania, wschód U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![Zrzut ekranu przedstawia Dodawanie reguły zabezpieczeń ruchu wychodzącego dla sieciowej grupy zabezpieczeń dla platformy Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. Podobnie jak w przypadku powyższych reguł zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. środkowe" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji docelowej. Pozwala to na dostęp do monitorowania Site Recovery.

4. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Te reguły są wymagane, aby można było włączyć replikację z regionu docelowego do obszaru źródłowego po zakończeniu pracy w trybie failover:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. środkowe" w sieciowej grupy zabezpieczeń.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń.

3. Podobnie jak w przypadku powyższych reguł zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. Wschód" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji źródłowej. Pozwala to na dostęp do monitorowania Site Recovery.

4. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

## <a name="network-virtual-appliance-configuration"></a>Konfiguracja wirtualnego urządzenia sieciowego

Jeśli używasz wirtualnych urządzeń sieciowych (urządzeń WUS) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych, urządzenie może zostać ograniczone, jeśli cały ruch związany z replikacją przechodzi przez urządzenie WUS. Zalecamy utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie przechodził do urządzenie WUS.

### <a name="create-network-service-endpoint-for-storage"></a>Utwórz punkt końcowy usługi sieciowej dla magazynu
W sieci wirtualnej można utworzyć punkt końcowy usługi sieciowej dla magazynu, aby ruch związany z replikacją nie opuszcza granicy platformy Azure.

- Wybierz sieć wirtualną platformy Azure i kliknij pozycję "punkty końcowe usługi"

    ![Magazyn — punkt końcowy](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kliknij przycisk "Dodaj" i Otwórz kartę "Dodaj punkty końcowe usługi"
- Wybierz pozycję "Microsoft. Storage" w obszarze "Service" i wymagane podsieci w polu "podsieci" i kliknij pozycję "Dodaj".

>[!NOTE]
>Nie ograniczaj dostępu do sieci wirtualnej do kont magazynu używanych na potrzeby usługi ASR. Należy zezwolić na dostęp ze wszystkich sieci

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 z [trasą niestandardową](../virtual-network/virtual-networks-udr-overview.md#custom-routes) i przekierować ruch maszyny wirtualnej do lokalnego urządzenia sieciowego (urządzenie WUS), ale ta konfiguracja nie jest zalecana w przypadku replikacji Site Recovery. Jeśli używasz tras niestandardowych, należy [utworzyć punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie opuszcza granicy platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Rozpocznij ochronę obciążeń przez [replikowanie maszyn wirtualnych platformy Azure](./azure-to-azure-quickstart.md).
- Dowiedz się więcej o [przechowywaniu adresów IP](site-recovery-retain-ip-azure-vm-failover.md) w trybie failover dla maszyny wirtualnej platformy Azure.
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
