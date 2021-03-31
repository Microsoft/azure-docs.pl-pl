---
title: Filtrowanie Menedżera zapory platformy Azure w regułach sieci (wersja zapoznawcza)
description: Jak używać filtrowania nazw FQDN w regułach sieci
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460154"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtrowanie nazw FQDN w regułach sieci (wersja zapoznawcza)

> [!IMPORTANT]
> Filtrowanie nazw FQDN w regułach sieciowych jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W pełni kwalifikowana nazwa domeny (FQDN) reprezentuje nazwę domeny hosta lub adresy IP. W regułach sieci można używać nazw FQDN opartych na rozpoznawaniu nazw DNS w zaporze platformy Azure i zasadach zapory. Ta funkcja umożliwia filtrowanie ruchu wychodzącego za pomocą dowolnego protokołu TCP/UDP (w tym NTP, SSH, RDP i innych). Aby używać nazw FQDN w regułach sieci, należy włączyć serwer proxy DNS. Aby uzyskać więcej informacji, zobacz [Ustawienia usługi DNS zasad zapory platformy Azure (wersja zapoznawcza)](dns-settings.md).

## <a name="how-it-works"></a>Jak to działa

Po zdefiniowaniu serwera DNS potrzebnego w organizacji (Azure DNS lub własnego niestandardowego DNS) Zapora platformy Azure tłumaczy nazwę FQDN na adresy IP na podstawie wybranego serwera DNS. To tłumaczenie występuje zarówno w przypadku przetwarzania reguł aplikacji, jak i sieci.

Jaka jest różnica między używaniem nazw domen w regułach aplikacji w porównaniu z regułami sieci? 

- Filtrowanie nazw FQDN w regułach aplikacji dla protokołów HTTP/S i MSSQL jest oparte na przezroczystym serwerze proxy na poziomie aplikacji i nagłówku SNI. W związku z tym może rozpoznać między dwiema nazwami FQDN, które są rozpoznawane jako ten sam adres IP. Nie jest to przypadek z filtrowaniem nazw FQDN w regułach sieci. Zawsze używaj reguł aplikacji, gdy jest to możliwe.
- W regułach aplikacji można używać protokołu HTTP/S i MSSQL jako wybranych protokołów. W regułach sieci można użyć dowolnego protokołu TCP/UDP z docelowymi nazwami FQDN.

## <a name="next-steps"></a>Następne kroki

[Ustawienia usługi DNS zapory platformy Azure](dns-settings.md)
