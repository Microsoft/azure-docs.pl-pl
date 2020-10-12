---
title: Filtrowanie nazw FQDN zapory platformy Azure w regułach sieci (wersja zapoznawcza)
description: Jak używać filtrowania nazw FQDN zapory platformy Azure w regułach sieci
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 1a35d9c48dd46d5d220699589f4ed758d21feca8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854277"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>Używanie filtrowania nazw FQDN w regułach sieci (wersja zapoznawcza)

> [!IMPORTANT]
> Filtrowanie nazw FQDN w regułach sieciowych jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W pełni kwalifikowana nazwa domeny (FQDN) reprezentuje nazwę domeny hosta lub adresy IP. W regułach sieci można używać nazw FQDN opartych na rozpoznawaniu nazw DNS w zaporze platformy Azure i zasadach zapory. Ta funkcja umożliwia filtrowanie ruchu wychodzącego za pomocą dowolnego protokołu TCP/UDP (w tym NTP, SSH, RDP i innych). Aby używać nazw FQDN w regułach sieci, należy włączyć serwer proxy DNS. Aby uzyskać więcej informacji, zobacz [Ustawienia usługi DNS zapory platformy Azure (wersja zapoznawcza)](dns-settings.md).

> [!NOTE]
> Zgodnie z projektem filtrowanie nazw FQDN nie obsługuje symboli wieloznacznych.

## <a name="how-it-works"></a>Jak to działa

Po zdefiniowaniu serwera DNS potrzebnego w organizacji (Azure DNS lub własnego niestandardowego DNS) Zapora platformy Azure tłumaczy nazwę FQDN na adresy IP na podstawie wybranego serwera DNS. To tłumaczenie występuje zarówno w przypadku przetwarzania reguł aplikacji, jak i sieci.

Jaka jest różnica między używaniem nazw domen w regułach aplikacji w porównaniu z regułami sieci? 

- Filtrowanie nazw FQDN w regułach aplikacji dla protokołów HTTP/S i MSSQL jest oparte na przezroczystym serwerze proxy na poziomie aplikacji i nagłówku SNI. W związku z tym może rozpoznać między dwiema nazwami FQDN, które są rozpoznawane jako ten sam adres IP. Nie jest to przypadek z filtrowaniem nazw FQDN w regułach sieci. Zawsze używaj reguł aplikacji, gdy jest to możliwe.
- W regułach aplikacji można używać protokołu HTTP/S i MSSQL jako wybranych protokołów. W regułach sieci można użyć dowolnego protokołu TCP/UDP z docelowymi nazwami FQDN.

## <a name="next-steps"></a>Następne kroki

[Ustawienia usługi DNS zapory platformy Azure](dns-settings.md)
