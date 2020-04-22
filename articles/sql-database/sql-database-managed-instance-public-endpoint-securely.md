---
title: Bezpieczne publiczne punkty końcowe wystąpienia zarządzanego
description: Bezpieczne używanie publicznych punktów końcowych na platformie Azure przy użyciu wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684953"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpieczne korzystanie z wystąpienia zarządzanego usługi Azure SQL Database z publicznymi punktami końcowymi

Wystąpienia zarządzanej usługi Azure SQL Database mogą zapewnić łączność z użytkownikami za pośrednictwem [publicznych punktów końcowych.](../virtual-network/virtual-network-service-endpoints-overview.md) W tym artykule wyjaśniono, jak uczynić tę konfigurację bardziej bezpieczną.

## <a name="scenarios"></a>Scenariusze

Wystąpienie zarządzane bazy danych SQL zapewnia prywatny punkt końcowy, aby umożliwić łączność z wewnątrz sieci wirtualnej. Domyślną opcją jest zapewnienie maksymalnej izolacji. Istnieją jednak scenariusze, w których należy podać połączenie publicznego punktu końcowego:

- Wystąpienie zarządzane musi być zintegrowane z ofertami platformy tylko dla wielu dzierżawców (PaaS).
- Potrzebujesz wyższej przepustowości wymiany danych niż jest to możliwe, gdy używasz sieci VPN.
- Zasady firmy zabraniają paas wewnątrz sieci firmowych.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Wdrażanie wystąpienia zarządzanego dla publicznego dostępu do punktu końcowego

Chociaż nie jest to obowiązkowe, typowy model wdrażania dla wystąpienia zarządzanego z dostępem do publicznego punktu końcowego jest utworzenie wystąpienia w dedykowanej izolowanej sieci wirtualnej. W tej konfiguracji sieć wirtualna jest używana tylko do izolacji klastra wirtualnego. Nie ma znaczenia, czy przestrzeń adresowa adresu IP wystąpienia zarządzanego nakłada się na przestrzeń adresów IP sieci firmowej.

## <a name="secure-data-in-motion"></a>Bezpieczne dane w ruchu

Ruch danych wystąpienia zarządzanego jest zawsze szyfrowany, jeśli sterownik klienta obsługuje szyfrowanie. Dane wysyłane między wystąpieniem zarządzanym a innymi maszynami wirtualnymi platformy Azure lub usługami platformy Azure nigdy nie pozostawiają szkieletu platformy Azure. Jeśli istnieje połączenie między wystąpieniem zarządzanym a siecią lokalną, zaleca się użycie usługi Azure ExpressRoute. Usługa ExpressRoute pomaga uniknąć przenoszenia danych przez publiczny Internet. W przypadku łączności prywatnej wystąpienia zarządzanego można używać tylko prywatnej komunikacji równorzędnej.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Blokowanie połączeń przychodzących i wychodzących

Na poniższym diagramie przedstawiono zalecane konfiguracje zabezpieczeń:

![Konfiguracje zabezpieczeń do blokowania połączeń przychodzących i wychodzących](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Wystąpienie zarządzane ma [dedykowany publiczny adres końcowy](sql-database-managed-instance-find-management-endpoint-ip-address.md). W zaporze wychodzącej po stronie klienta i w regułach sieciowej grupy zabezpieczeń ustaw ten publiczny adres IP punktu końcowego, aby ograniczyć łączność wychodzącą.

Aby upewnić się, że ruch do wystąpienia zarządzanego pochodzi z zaufanych źródeł, zaleca się łączenie ze źródeł ze znanymi adresami IP. Użyj sieciowej grupy zabezpieczeń, aby ograniczyć dostęp do publicznego punktu końcowego wystąpienia zarządzanego na porcie 3342.

Gdy klienci muszą zainicjować połączenie z sieci lokalnej, upewnij się, że adres źródłowy jest tłumaczony na dobrze znany zestaw adresów IP. Jeśli nie możesz tego zrobić (na przykład pracownicy mobilni są typowym scenariuszem), zalecamy korzystanie z [połączeń sieci VPN typu punkt-lokacja i prywatnego punktu końcowego.](sql-database-managed-instance-configure-p2s.md)

Jeśli połączenia są uruchamiane z platformy Azure, zaleca się, że ruch pochodzi z dobrze znanego przypisanego [wirtualnego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md) (na przykład maszyny wirtualnej). Aby ułatwić zarządzanie wirtualnymi adresami IP (VIP), można użyć [prefiksów publicznego adresu IP](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować publiczny punkt końcowy do zarządzania wystąpieniami: [Konfigurowanie publicznego punktu końcowego](sql-database-managed-instance-public-endpoint-configure.md)
