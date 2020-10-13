---
title: Używanie prywatnych punktów końcowych dla konfiguracji aplikacji platformy Azure
description: Zabezpiecz swój magazyn konfiguracji aplikacji za pomocą prywatnych punktów końcowych
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 3ec2a0e38f3bead5fbab8a119099bb5bbc3ded2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042126"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Używanie prywatnych punktów końcowych dla konfiguracji aplikacji platformy Azure

Możesz użyć [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla konfiguracji aplikacji platformy Azure, aby umożliwić klientom w sieci wirtualnej (VNET) bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla magazynu konfiguracji aplikacji. Ruch sieciowy między klientami sieci wirtualnej a magazynem konfiguracji aplikacji przechodzi przez sieć wirtualną przy użyciu prywatnego linku w sieci szkieletowej firmy Microsoft, eliminując narażenie na publiczny Internet.

Używanie prywatnych punktów końcowych dla magazynu konfiguracji aplikacji pozwala:
- Zabezpiecz szczegóły konfiguracji aplikacji, konfigurując zaporę do blokowania wszystkich połączeń z konfiguracją aplikacji w publicznym punkcie końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej (VNet), zapewniając, że dane nie są wyprowadzane przez sieć wirtualną.
- Bezpiecznie łącz się z magazynem konfiguracji aplikacji z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNET). Utworzenie prywatnego punktu końcowego dla magazynu konfiguracji aplikacji zapewnia bezpieczną łączność między klientami w sieci wirtualnej i magazynem konfiguracji. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym i magazynem konfiguracji używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą łączyć się z magazynem konfiguracji przez prywatny punkt końcowy **przy użyciu tych samych parametrów połączenia i mechanizmów autoryzacji, których mogą używać w przeciwnym razie**. Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez magazyn konfiguracji aplikacji.

Mimo że konfiguracja aplikacji nie obsługuje punktów końcowych usługi, można utworzyć prywatne punkty końcowe w podsieciach, które używają [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md). Klienci w podsieci mogą bezpiecznie łączyć się z magazynem konfiguracji aplikacji przy użyciu prywatnego punktu końcowego podczas korzystania z punktów końcowych usługi, aby uzyskiwać dostęp do innych osób.  

Podczas tworzenia prywatnego punktu końcowego dla usługi w sieci wirtualnej do zatwierdzenia dla właściciela konta usługi jest wysyłane żądanie zgody. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele kont usługi mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą `Private Endpoints` karty magazynu konfiguracji w [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Prywatne punkty końcowe dla konfiguracji aplikacji 

Podczas tworzenia prywatnego punktu końcowego należy określić magazyn konfiguracji aplikacji, z którym jest nawiązywane połączenie. Jeśli masz wiele wystąpień konfiguracji aplikacji w ramach konta, potrzebujesz oddzielnego prywatnego punktu końcowego dla każdego sklepu.

### <a name="connecting-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi

Platforma Azure korzysta z rozpoznawania nazw DNS w celu kierowania połączeń z sieci wirtualnej do magazynu konfiguracji za pośrednictwem prywatnego linku. Ciągi połączeń w Azure Portal można szybko znaleźć, wybierając swój magazyn konfiguracji aplikacji, a następnie wybierając pozycję **Ustawienia**  >  **klucze dostępu**.  

> [!IMPORTANT]
> Użyj tych samych parametrów połączenia, aby nawiązać połączenie z magazynem konfiguracji aplikacji przy użyciu prywatnych punktów końcowych, jak ma być używany dla publicznego punktu końcowego. Nie łącz się z magazynem przy użyciu `privatelink` adresu URL jego domeny podrzędnej.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla magazynu konfiguracji jest aktualizowany do aliasu w poddomenie z prefiksem `privatelink` . Platforma Azure tworzy również [prywatną strefę DNS](../dns/private-dns-overview.md) odpowiadającą `privatelink` poddomeny, z rekordem zasobów DNS a dla prywatnych punktów końcowych.

W przypadku rozpoznania adresu URL punktu końcowego z poziomu sieci wirtualnej hostującym prywatny punkt końcowy jest on rozpoznawany jako prywatny punkt końcowy magazynu. Po rozwiązaniu z zewnątrz sieci wirtualnej adres URL punktu końcowego jest rozpoznawany jako publiczny punkt końcowy. W przypadku tworzenia prywatnego punktu końcowego publiczny punkt końcowy jest wyłączony.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą być w stanie rozpoznać w pełni kwalifikowaną nazwę domeny (FQDN) dla punktu końcowego usługi jako prywatny adres IP punktu końcowego. Skonfiguruj serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfiguruj rekordy A dla `AppConfigInstanceA.privatelink.azconfig.io` za pomocą prywatnego adresu IP punktu końcowego.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer DNS w celu rozpoznania nazwy magazynu w `privatelink` poddomenie na adres IP prywatnego punktu końcowego. Można to zrobić przez delegowanie `privatelink` poddomeny do prywatnej strefy DNS sieci wirtualnej lub skonfigurowanie strefy DNS na serwerze DNS i dodanie rekordu A DNS a.

## <a name="pricing"></a>Cennik

Włączenie prywatnych punktów końcowych wymaga magazynu konfiguracji aplikacji [warstwy Standardowa](https://azure.microsoft.com/pricing/details/app-configuration/) .  Aby dowiedzieć się więcej o cenach łączy prywatnych, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tworzeniu prywatnego punktu końcowego dla magazynu konfiguracji aplikacji, zapoznaj się z następującymi artykułami:

- [Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Dowiedz się, jak skonfigurować serwer DNS za pomocą prywatnych punktów końcowych:

- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](/azure/private-link/private-endpoint-overview#dns-configuration)
