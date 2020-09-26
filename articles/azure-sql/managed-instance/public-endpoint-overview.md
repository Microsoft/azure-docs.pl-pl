---
title: Bezpieczne publiczne punkty końcowe wystąpienia zarządzanego usługi Azure SQL
description: Bezpieczne używanie publicznych punktów końcowych w wystąpieniu zarządzanym usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332852"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Bezpieczne używanie wystąpienia zarządzanego usługi Azure SQL z publicznymi punktami końcowymi
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane usługi Azure SQL może zapewniać łączność użytkowników za pośrednictwem [publicznych punktów końcowych](../../virtual-network/virtual-network-service-endpoints-overview.md). W tym artykule wyjaśniono, jak zwiększyć bezpieczeństwo tej konfiguracji.

## <a name="scenarios"></a>Scenariusze

Wystąpienie zarządzane Azure SQL udostępnia prywatny punkt końcowy, który umożliwia połączenie z wewnątrz sieci wirtualnej. Opcja domyślna to zapewnienie maksymalnej izolacji. Istnieją jednak scenariusze, w których należy podać połączenie z publicznym punktem końcowym:

- Wystąpienie zarządzane musi być zintegrowane z ofertą platformy jako usługi (PaaS) obsługującą wiele dzierżawców.
- Potrzebna jest większa przepływność wymiany danych niż jest to możliwe w przypadku korzystania z sieci VPN.
- Zasady firmowe nie zabraniają PaaS w sieciach firmowych.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Wdróż wystąpienie zarządzane dla publicznego dostępu do punktu końcowego

Chociaż nie jest to wymagane, typowym modelem wdrażania dla wystąpienia zarządzanego z publicznym dostępem do punktu końcowego jest utworzenie wystąpienia w dedykowanej izolowanej sieci wirtualnej. W tej konfiguracji Sieć wirtualna jest używana tylko do izolacji klastra wirtualnego. Nie ma znaczenia, czy przestrzeń adresów IP wystąpienia zarządzanego nakłada się na przestrzeń adresową IP sieci firmowej.

## <a name="secure-data-in-motion"></a>Zabezpieczanie danych w ruchu

Ruch danych wystąpienia zarządzanego SQL jest zawsze szyfrowany, jeśli sterownik klienta obsługuje szyfrowanie. Dane wysyłane między wystąpieniem zarządzanym i innymi maszynami wirtualnymi platformy Azure lub usługami platformy Azure nigdy nie opuszczają szkieletu platformy Azure. Jeśli istnieje połączenie między wystąpieniem zarządzanym a siecią lokalną, zalecamy korzystanie z usługi Azure ExpressRoute. ExpressRoute pomaga uniknąć przeniesienia danych przez publiczny Internet. W przypadku połączenia prywatnego wystąpienia zarządzanego można używać tylko prywatnej komunikacji równorzędnej.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Zablokuj łączność ruchu przychodzącego i wychodzącego

Na poniższym diagramie przedstawiono zalecane konfiguracje zabezpieczeń:

![Konfiguracje zabezpieczeń służące do blokowania łączności przychodzącej i wychodzącej](./media/public-endpoint-overview/managed-instance-vnet.png)

Wystąpienie zarządzane ma [dedykowany publiczny adres punktu końcowego](management-endpoint-find-ip-address.md). W zaporze wychodzącej po stronie klienta i w regułach sieciowej grupy zabezpieczeń ustaw ten publiczny adres IP punktu końcowego, aby ograniczyć łączność wychodzącą.

Aby zapewnić, że ruch do wystąpienia zarządzanego pochodzi z zaufanych źródeł, zalecamy łączenie ze źródła przy użyciu dobrze znanych adresów IP. Użyj sieciowej grupy zabezpieczeń, aby ograniczyć dostęp do publicznego punktu końcowego wystąpienia zarządzanego na porcie 3342.

Gdy klienci muszą inicjować połączenie z sieci lokalnej, upewnij się, że adres źródłowy jest tłumaczony na dobrze znany zestaw adresów IP. Jeśli nie możesz tego zrobić (na przykład dla pracowników mobilnych jest typowy scenariusz), zalecamy użycie [połączeń sieci VPN typu punkt-lokacja i prywatnego punktu końcowego](point-to-site-p2s-configure.md).

Jeśli połączenia są uruchamiane z platformy Azure, zalecamy, aby ruch pochodzi z dobrze znanego, przypisanego [wirtualnego adresu IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (na przykład maszyny wirtualnej). Aby łatwiej zarządzać wirtualnymi adresami IP (VIP), warto użyć [prefiksów publicznych adresów IP](../../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować publiczny punkt końcowy dla zarządzania wystąpieniami: [Skonfiguruj publiczny punkt końcowy](public-endpoint-configure.md)
