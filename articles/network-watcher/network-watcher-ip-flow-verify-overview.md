---
title: Wprowadzenie do weryfikacji przepływu IP na platformie Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie możliwości zweryfikowania przepływu adresu IP Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: d6f6fe81c121f547f79fa34be77aab1fb6c0875a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899358"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Wprowadzenie do weryfikacji przepływu IP na platformie Azure Network Watcher

Sprawdzenie przepływu IP sprawdza, czy pakiet jest dozwolony, czy odrzucany do lub z maszyny wirtualnej. Informacje składają się z kierunku, protokołu, lokalnego adresu IP, zdalnego adresu IP, portu lokalnego i portu zdalnego. Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń, zostanie zwrócona nazwa reguły, która odrzuciła pakiet. Gdy można wybrać dowolny źródłowy lub docelowy adres IP, weryfikacja przepływu IP ułatwia administratorom Szybkie diagnozowanie problemów z łącznością z lub do Internetu oraz z lub do środowiska lokalnego.

Weryfikacja przepływu IP sprawdza reguły dla wszystkich sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) stosowanych w interfejsie sieciowym, takich jak podsieć lub karta sieciowa maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie skonfigurowanych ustawień do lub z tego interfejsu sieciowego. Sprawdzenie przepływu IP jest przydatne w przypadku potwierdzania, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wyjściowy do lub z maszyny wirtualnej.

Wystąpienie Network Watcher należy utworzyć we wszystkich regionach, w których planujesz uruchomić weryfikację przepływu IP. Network Watcher jest usługą regionalną i można ją wykonać tylko w odniesieniu do zasobów w tym samym regionie. Używane wystąpienie nie ma wpływu na wyniki weryfikacji przepływu IP, ponieważ jest nadal zwracana jakakolwiek trasa skojarzona z kartą sieciową lub podsieć.

![1][1]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się, czy pakiet jest dozwolony lub odrzucany dla określonej maszyny wirtualnej za pomocą portalu. [Sprawdź, czy ruch jest dozwolony na maszynie wirtualnej przy użyciu funkcji weryfikowania przepływu IP za pomocą portalu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

