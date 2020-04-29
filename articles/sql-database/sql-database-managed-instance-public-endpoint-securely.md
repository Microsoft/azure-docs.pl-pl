---
title: Publiczne punkty końcowe wystąpienia zarządzania bezpiecznego
description: Bezpieczne używanie publicznych punktów końcowych na platformie Azure z wystąpieniami zarządzanymi
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684953"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpieczne używanie Azure SQL Database wystąpienia zarządzanego z publicznymi punktami końcowymi

Azure SQL Database wystąpieniami zarządzanymi mogą zapewnić łączność użytkowników za pośrednictwem [publicznych punktów końcowych](../virtual-network/virtual-network-service-endpoints-overview.md). W tym artykule wyjaśniono, jak zwiększyć bezpieczeństwo tej konfiguracji.

## <a name="scenarios"></a>Scenariusze

Wystąpienie zarządzane SQL Database udostępnia prywatny punkt końcowy, aby umożliwić łączność z wewnątrz sieci wirtualnej. Opcja domyślna to zapewnienie maksymalnej izolacji. Istnieją jednak scenariusze, w których należy podać połączenie z publicznym punktem końcowym:

- Wystąpienie zarządzane musi być zintegrowane z ofertą platformy jako usługi (PaaS) obsługującą wiele dzierżawców.
- Potrzebna jest większa przepływność wymiany danych niż jest to możliwe w przypadku korzystania z sieci VPN.
- Zasady firmowe nie zabraniają PaaS w sieciach firmowych.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Wdróż wystąpienie zarządzane dla publicznego dostępu do punktu końcowego

Chociaż nie jest to wymagane, typowym modelem wdrażania dla wystąpienia zarządzanego z publicznym dostępem do punktu końcowego jest utworzenie wystąpienia w dedykowanej izolowanej sieci wirtualnej. W tej konfiguracji Sieć wirtualna jest używana tylko do izolacji klastra wirtualnego. Nie ma znaczenia, czy przestrzeń adresów IP wystąpienia zarządzanego nakłada się na przestrzeń adresową IP sieci firmowej.

## <a name="secure-data-in-motion"></a>Zabezpieczanie danych w ruchu

Ruch danych wystąpienia zarządzanego jest zawsze szyfrowany, jeśli sterownik klienta obsługuje szyfrowanie. Dane wysyłane między wystąpieniem zarządzanym i innymi maszynami wirtualnymi platformy Azure lub usługami platformy Azure nigdy nie opuszczają szkieletu platformy Azure. Jeśli istnieje połączenie między wystąpieniem zarządzanym a siecią lokalną, zalecamy korzystanie z usługi Azure ExpressRoute. ExpressRoute pomaga uniknąć przeniesienia danych przez publiczny Internet. W przypadku połączenia prywatnego wystąpienia zarządzanego można używać tylko prywatnej komunikacji równorzędnej.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Zablokuj łączność ruchu przychodzącego i wychodzącego

Na poniższym diagramie przedstawiono zalecane konfiguracje zabezpieczeń:

![Konfiguracje zabezpieczeń służące do blokowania łączności przychodzącej i wychodzącej](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Wystąpienie zarządzane ma [dedykowany publiczny adres punktu końcowego](sql-database-managed-instance-find-management-endpoint-ip-address.md). W zaporze wychodzącej po stronie klienta i w regułach sieciowej grupy zabezpieczeń ustaw ten publiczny adres IP punktu końcowego, aby ograniczyć łączność wychodzącą.

Aby zapewnić, że ruch do wystąpienia zarządzanego pochodzi z zaufanych źródeł, zalecamy łączenie ze źródła przy użyciu dobrze znanych adresów IP. Użyj sieciowej grupy zabezpieczeń, aby ograniczyć dostęp do publicznego punktu końcowego wystąpienia zarządzanego na porcie 3342.

Gdy klienci muszą inicjować połączenie z sieci lokalnej, upewnij się, że adres źródłowy jest tłumaczony na dobrze znany zestaw adresów IP. Jeśli nie możesz tego zrobić (na przykład dla pracowników mobilnych jest typowy scenariusz), zalecamy użycie [połączeń sieci VPN typu punkt-lokacja i prywatnego punktu końcowego](sql-database-managed-instance-configure-p2s.md).

Jeśli połączenia są uruchamiane z platformy Azure, zalecamy, aby ruch pochodzi z dobrze znanego, przypisanego [wirtualnego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md) (na przykład maszyny wirtualnej). Aby łatwiej zarządzać wirtualnymi adresami IP (VIP), warto użyć [prefiksów publicznych adresów IP](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować publiczny punkt końcowy dla zarządzania wystąpieniami: [Skonfiguruj publiczny punkt końcowy](sql-database-managed-instance-public-endpoint-configure.md)
