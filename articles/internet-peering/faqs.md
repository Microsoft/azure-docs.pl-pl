---
title: Internet Komunikacja równorzędna — często zadawane pytania
titleSuffix: Azure
description: Internet Komunikacja równorzędna — często zadawane pytania
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775500"
---
# <a name="internet-peering---faqs"></a>Internet Komunikacja równorzędna — często zadawane pytania

Poniżej znajdziesz poniższe informacje.

**Jaka jest różnica między usługą internetowej komunikacji równorzędnej i komunikacji równorzędnej?**

Usługa komunikacji równorzędnej to usługa, która zamierza zapewnić firmie Microsoft łączność publiczną IP klasy korporacyjnej dla klientów korporacyjnych. Platforma internetowa klasy korporacyjnej obejmuje łączność za pośrednictwem usługodawców internetowych mających wysoką przepływność połączenia z firmą Microsoft i nadmiarowością w celu uzyskania łączności z HA. Ponadto ruch użytkowników jest zoptymalizowany pod kątem opóźnień do najbliższej przeglądarki Microsoft Edge. Usługa komunikacji równorzędnej jest oparta na łączności komunikacji równorzędnej z przewoźnikiem partnerskim. Komunikacja równorzędna z partnerem musi być bezpośrednią komunikację równorzędną w przeciwieństwie do komunikacji równorzędnej programu Exchange. Bezpośrednia Komunikacja równorzędna musi mieć lokalną i geograficzną nadmiarowość.

**Co to jest starsza Komunikacja równorzędna?**

Połączenie komunikacji równorzędnej skonfigurowane przy użyciu Azure PowerShell jest zarządzane jako zasób platformy Azure. Połączenia komunikacji równorzędnej skonfigurowane w przeszłości są przechowywane w naszym systemie jako Starsza Komunikacja równorzędna, którą można skonwertować do zarządzania jako zasób platformy Azure.

**Kiedy New-AzPeeringDirectConnectionObject jest wywoływana, jakie adresy IP są przyznawane urządzeniom firmy Microsoft i równorzędnym?**

Podczas wywoływania polecenia cmdlet New-AzPeeringDirectConnectionObject jest wprowadzony adres/31 (a. b. c. d/31) lub adres/30 (a. b. c. d/30). Pierwszy adres IP (a. b. c. d + 0) jest przyznany do urządzenia równorzędnego i drugiego adresu IP (a. b. c. d + 1).

**Co to są parametry MaxPrefixesAdvertisedIPv4 i MaxPrefixesAdvertisedIPv6 w New-AzPeeringDirectConnectionObject polecenie cmdlet?**

Parametry MaxPrefixesAdvertisedIPv4 i MaxPrefixesAdvertisedIPv6 przedstawiają maksymalną liczbę prefiksów IPv4 i IPv6, które firma Microsoft chce zaakceptować. Te parametry można modyfikować w dowolnym momencie.