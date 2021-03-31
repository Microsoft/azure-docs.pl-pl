---
title: Logika przetwarzania reguł Menedżera zapory platformy Azure
description: Dowiedz się więcej o logice przetwarzania reguł zapory platformy Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86512184"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall

Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.

## <a name="network-rules-and-applications-rules"></a>Reguły sieci i zasady dotyczące aplikacji

Reguły dotyczące sieci są stosowane najpierw, a następnie reguły aplikacji. Reguły są przerywane. Dlatego jeśli w regułach sieciowych zostanie znalezione dopasowanie, reguły aplikacji nie zostaną przetworzone.  Jeśli żadna Reguła sieciowa nie jest zgodna, a protokół pakietów to HTTP/HTTPS, pakiet jest następnie oceniany przez reguły aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet jest oceniany względem kolekcji reguł infrastruktury. Jeśli nadal nie ma dopasowania, pakiet zostanie domyślnie odrzucony.

## <a name="nat-rules"></a>Reguły NAT

Połączenie przychodzące można włączyć przez skonfigurowanie translacji docelowego adresu sieciowego (DNAT) zgodnie z opisem w [samouczku: filtrowanie ruchu przychodzącego za pomocą usługi Azure firewall DNAT przy użyciu Azure Portal](../firewall/tutorial-firewall-dnat.md). Reguły DNAT są stosowane jako pierwsze. Jeśli zostanie znalezione dopasowanie, niejawna odpowiadająca reguła sieci umożliwia dodanie przetłumaczonego ruchu. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Dla tych połączeń nie są stosowane żadne reguły aplikacji.

## <a name="inherited-rules"></a>Dziedziczone reguły

Kolekcje reguł sieciowych dziedziczone z zasad nadrzędnych są zawsze priorytetowe względem kolekcji reguł sieci, które są zdefiniowane w ramach nowych zasad. Ta sama logika dotyczy również kolekcji reguł aplikacji. Jednak kolekcje reguł sieci są zawsze przetwarzane przed kolekcjami reguł aplikacji niezależnie od dziedziczenia.

Domyślnie zasady dziedziczą tryb analizy zagrożeń zasad nadrzędnych. Można to zastąpić, ustawiając tryb analizy zagrożeń na inną wartość na stronie Ustawienia zasad. Można przesłonić tylko wartość bardziej rygorystyczną. Na przykład jeśli zasady nadrzędne są ustawione tylko na *alerty*, można skonfigurować te zasady lokalne w taki sposób, aby były *wyzwalane i odrzucane*, ale nie można ich wyłączyć.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o Menedżerze zapory platformy Azure](overview.md)