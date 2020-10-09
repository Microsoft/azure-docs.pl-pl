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
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76845010"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Wprowadzenie do weryfikacji przepływu IP na platformie Azure Network Watcher

Sprawdzenie przepływu IP sprawdza, czy pakiet jest dozwolony, czy odrzucany do lub z maszyny wirtualnej. Informacje składają się z kierunku, protokołu, lokalnego adresu IP, zdalnego adresu IP, portu lokalnego i portu zdalnego. Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń, zostanie zwrócona nazwa reguły, która odrzuciła pakiet. Gdy można wybrać dowolny źródłowy lub docelowy adres IP, weryfikacja przepływu IP ułatwia administratorom Szybkie diagnozowanie problemów z łącznością z lub do Internetu oraz z lub do środowiska lokalnego.

Weryfikacja przepływu IP sprawdza reguły dla wszystkich sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) stosowanych w interfejsie sieciowym, takich jak podsieć lub karta sieciowa maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie skonfigurowanych ustawień do lub z tego interfejsu sieciowego. Sprawdzenie przepływu IP jest przydatne w przypadku potwierdzania, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wyjściowy do lub z maszyny wirtualnej.

Wystąpienie Network Watcher należy utworzyć we wszystkich regionach, w których planujesz uruchomić weryfikację przepływu IP. Network Watcher jest usługą regionalną i można ją wykonać tylko w odniesieniu do zasobów w tym samym regionie. Używane wystąpienie nie ma wpływu na wyniki weryfikacji przepływu IP, ponieważ jest nadal zwracana jakakolwiek trasa skojarzona z kartą sieciową lub podsieć.

![1][1]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się, czy pakiet jest dozwolony lub odrzucany dla określonej maszyny wirtualnej za pomocą portalu. [Sprawdź, czy ruch jest dozwolony na maszynie wirtualnej przy użyciu funkcji weryfikowania przepływu IP za pomocą portalu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

