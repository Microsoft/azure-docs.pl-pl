---
title: Infrastruktura i łączność do SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Skonfiguruj wymaganą infrastrukturę łączności, aby używać SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27dc2f6c647308984a02f2cf52dbc0213abad088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676839"
---
# <a name="sap-hana-large-instances-deployment"></a>Wdrożenie SAP HANA (duże wystąpienia) 

W tym artykule przyjęto założenie, że zakończono kupowanie SAP HANA na platformie Azure (duże wystąpienia) od firmy Microsoft. Aby zapoznać się z tym artykułem, zobacz sekcję " [duże wystąpienia usługi Hana — typowe terminy](hana-know-terms.md) i [jednostki SKU dużych wystąpień usługi Hana](hana-available-skus.md)".


Firma Microsoft wymaga następujących informacji w celu wdrożenia jednostek dużego wystąpienia HANA:

- Nazwa klienta.
- Informacje kontaktowe firmy (w tym adres e-mail i numer telefonu).
- Informacje o kontakcie technicznym (w tym adres e-mail i numer telefonu).
- Informacje kontaktowe sieci technicznej (w tym adres e-mail i numer telefonu).
- Region wdrożenia platformy Azure (na przykład zachodnie stany USA, Australia Wschodnia i Europa Północna).
- SAP HANA na platformie Azure (duże wystąpienia) jednostka SKU (Konfiguracja).
- Dla każdego regionu wdrożenia platformy Azure:
    - Zakres adresów IP/29 dla połączeń ER-P2P łączących sieci wirtualne platformy Azure z wystąpieniami programu HANA.
    - Blok CIDR/24, używany dla puli adresów IP serwera duże wystąpienia HANA.
    - Opcjonalny w przypadku używania [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md) do włączenia bezpośredniego routingu ze środowisk lokalnych do Hana jednostek wystąpienia dużych wystąpień lub routingu między jednostkami dużej instancji Hana w różnych regionach świadczenia usługi Azure należy zarezerwować inny zakres adresów IP/29. Ten konkretny zakres nie może nakładać się na żadne inne zdefiniowane wcześniej zakresy adresów IP.
- Wartości zakresu adresów IP używane w atrybucie przestrzeń adresów sieci wirtualnej dla każdej sieci wirtualnej platformy Azure, która łączy się z dużymi wystąpieniami HANA.
- Dane dla każdego systemu dużego wystąpienia HANA:
  - Wymagana nazwa hosta, najlepiej z w pełni kwalifikowaną nazwą domeny.
  - Żądany adres IP dla jednostki dużego wystąpienia HANA poza zakresem adresów puli adresów IP serwera. (Pierwsze 30 adresów IP z zakresu adresów puli adresów IP serwera są zarezerwowane do użytku wewnętrznego w dużych wystąpieniach HANA).
  - SAP HANA nazwę SID dla wystąpienia SAP HANA (wymagane do utworzenia niezbędnych SAP HANA woluminów dysków). Firma Microsoft potrzebuje identyfikatora SID platformy HANA do tworzenia uprawnień dla sidadm na woluminach systemu plików NFS. Te woluminy dołączają do jednostki dużego wystąpienia HANA. Identyfikator SID HANA jest również używany jako jeden ze składników nazw woluminów dysku, które mają zostać zainstalowane. Jeśli chcesz uruchomić więcej niż jedno wystąpienie HANA w jednostce, należy wyświetlić listę wielu identyfikatorów SID HANA. Każdy z nich otrzymuje oddzielny zestaw woluminów.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator grupy. Ten identyfikator jest wymagany do utworzenia niezbędnych woluminów dyskowych powiązanych SAP HANA. Instalacja SAP HANA zwykle tworzy grupę sapsys z IDENTYFIKATORem grupy 1001. Użytkownik sidadm jest częścią tej grupy.
  - W systemie operacyjnym Linux użytkownik sidadm ma identyfikator użytkownika. Ten identyfikator jest wymagany do utworzenia niezbędnych woluminów dyskowych powiązanych SAP HANA. Jeśli używasz kilku wystąpień HANA w jednostce, Wyświetl wszystkich użytkowników sidadm. 
- Identyfikator subskrypcji platformy Azure dla subskrypcji platformy Azure, która SAP HANA w dużych wystąpieniach usługi Azure HANA, zostanie połączony bezpośrednio. Ten identyfikator subskrypcji odwołuje się do subskrypcji platformy Azure, która będzie obciążana jednostką lub jednostkami dużej instancji HANA.

Po podaniu powyższych informacji firma Microsoft zainicjuje SAP HANA na platformie Azure (duże wystąpienia). Firma Microsoft wysyła informacje, aby połączyć sieci wirtualne platformy Azure z dużymi wystąpieniami HANA. Możesz również uzyskać dostęp do jednostek dużych wystąpień platformy HANA.

Użyj następującej sekwencji, aby nawiązać połączenie z dużymi wystąpieniami HANA po wdrożeniu go przez firmę Microsoft:

1. [Łączenie maszyn wirtualnych platformy Azure z dużymi wystąpieniami HANA](hana-connect-azure-vm-large-instances.md)
2. [Łączenie sieci wirtualnej z dużymi wystąpieniami platformy HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Dodatkowe wymagania dotyczące sieci (opcjonalnie)](hana-additional-network-requirements.md)
