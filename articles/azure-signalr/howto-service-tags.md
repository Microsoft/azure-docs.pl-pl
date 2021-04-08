---
title: Korzystanie z tagów usługi
titleSuffix: Azure SignalR Service
description: Użyj tagów usługi, aby zezwolić na ruch wychodzący do usługi Azure Signal
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152313"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Używanie tagów usługi dla usługi Azure Signal Service

Podczas konfigurowania [sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#network-security-groups)można używać [tagów usługi](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure Signal Service. Umożliwia zdefiniowanie reguły zabezpieczeń sieci wychodzącej dla punktów końcowych usługi Azure sygnalizujących bez konieczności umieszczaj adresów IP.

Usługa Azure sygnalizująca zarządza tymi tagami usług. Nie można utworzyć własnego tagu usługi ani zmodyfikować istniejącego. Firma Microsoft zarządza tymi prefiksami adresów, które pasują do znacznika usługi i automatycznie aktualizuje tag usługi jako adresy.

## <a name="use-service-tag-on-portal"></a>Korzystanie z tagu usługi w portalu

Możesz zezwolić na ruch wychodzący do usługi Azure Signal Service, dodając nową regułę zabezpieczeń sieci wychodzącej:

1. Przejdź do sieciowej grupy zabezpieczeń.

1. Kliknij menu Ustawienia o nazwie **reguły zabezpieczeń dla ruchu wychodzącego**.

1. Kliknij przycisk **+ Dodaj** na górze.

1. Wybierz pozycję **tag usługi** w obszarze **miejsce docelowe**.

1. Wybierz pozycję **AzureSignalR** w polu **tag usługi docelowej**.

1. Wypełnij **443** w **zakresach portów docelowych**.

    ![Tworzenie reguły zabezpieczeń dla ruchu wychodzącego](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Dostosuj inne pola zgodnie z potrzebami.

1. Kliknij pozycję **Dodaj**.


## <a name="next-steps"></a>Następne kroki

- [Sieciowe grupy zabezpieczeń: Tagi usług](../virtual-network/network-security-groups-overview.md#security-rules)