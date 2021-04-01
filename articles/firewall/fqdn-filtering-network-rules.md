---
title: Filtrowanie nazw FQDN zapory platformy Azure w regułach sieci
description: Jak używać filtrowania nazw FQDN zapory platformy Azure w regułach sieci
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695949"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Użyj filtrowania nazw FQDN w regułach sieci

W pełni kwalifikowana nazwa domeny (FQDN) reprezentuje nazwę domeny hosta lub adresy IP. W regułach sieci można używać nazw FQDN opartych na rozpoznawaniu nazw DNS w zaporze platformy Azure i zasadach zapory. Ta funkcja umożliwia filtrowanie ruchu wychodzącego za pomocą dowolnego protokołu TCP/UDP (w tym NTP, SSH, RDP i innych). Aby używać nazw FQDN w regułach sieci, należy włączyć serwer proxy DNS. Aby uzyskać więcej informacji, zobacz [Ustawienia usługi DNS zapory platformy Azure](dns-settings.md).

> [!NOTE]
> Zgodnie z projektem filtrowanie nazw FQDN nie obsługuje symboli wieloznacznych.

## <a name="how-it-works"></a>Jak to działa

Po zdefiniowaniu serwera DNS potrzebnego w organizacji (Azure DNS lub własnego niestandardowego DNS) Zapora platformy Azure tłumaczy nazwę FQDN na adresy IP na podstawie wybranego serwera DNS. To tłumaczenie występuje zarówno w przypadku przetwarzania reguł aplikacji, jak i sieci.

Gdy nowe rozwiązanie DNS ma miejsce, nowe adresy IP są dodawane do reguł zapory. Stare adresy IP, które nie są już zwracane przez serwer DNS, wygaśnie w ciągu 15 minut. Reguły zapory platformy Azure są aktualizowane co 15 sekund od rozpoznawania DNS nazw FQDN w regułach sieci.

### <a name="differences-in-application-rules-vs-network-rules"></a>Różnice w regułach aplikacji a regułach sieci

- Filtrowanie nazw FQDN w regułach aplikacji dla protokołów HTTP/S i MSSQL jest oparte na przezroczystym serwerze proxy na poziomie aplikacji i nagłówku SNI. W związku z tym może rozpoznać między dwiema nazwami FQDN, które są rozpoznawane jako ten sam adres IP. Nie jest to przypadek z filtrowaniem nazw FQDN w regułach sieci. 

   Zawsze używaj reguł aplikacji, gdy jest to możliwe:
     - Jeśli protokół to HTTP/S lub MSSQL, Użyj reguł aplikacji do filtrowania nazw FQDN.
   - W przypadku innych protokołów oprócz protokołu HTTP/S lub MSSQL można użyć reguł aplikacji lub sieci do filtrowania nazw FQDN.

## <a name="next-steps"></a>Następne kroki

[Ustawienia usługi DNS zapory platformy Azure](dns-settings.md)
