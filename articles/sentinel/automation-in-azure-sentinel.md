---
title: Wprowadzenie do automatyzacji w usłudze Azure — Wskaźnikowanie | Microsoft Docs
description: W tym artykule wprowadzono funkcje aranżacji zabezpieczeń, automatyzacji i odpowiedzi (o) platformy Azure, a następnie opisano jej składniki o — reguły automatyzacji i elementy PlayBook.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609802"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Organizowanie, Automatyzacja i reagowanie w zabezpieczeniach (o) na platformie Azure — wskaźnik

W tym artykule opisano funkcje aranżacji zabezpieczeń, automatyzacji i odpowiedzi (o) na platformie Azure, a ponadto przedstawiono sposób używania reguł automatyzacji i elementy PlayBook w odpowiedzi na zagrożenia bezpieczeństwa, które zwiększają efektywność SOC i oszczędzają czas i zasoby.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure — Wskaźnikowanie jako rozwiązanie o

### <a name="the-problem"></a>Problem

Zespoły SIEM/SOC są zwykle zasypaniu z alertami i zdarzeniami zabezpieczeń w regularnych odstępach czasu, dzięki czemu w dużej ilości dostępnego personelu są przeważnie. Wynika to również z tego, że wiele alertów jest ignorowanych i wiele incydentów nie jest badanych, a organizacja jest narażona na ataki niezauważalne.

### <a name="the-solution"></a>Rozwiązanie

Na platformie Azure — oprócz systemu zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), jest również platforma umożliwiająca organizowanie, automatyzację i reagowanie w zabezpieczeniach (o). Jednym z podstawowych celów jest Automatyzowanie wszelkich cyklicznych i przewidywalnych zadań związanych z wzbogacaniem, odpowiedzią i korygowaniem, które są odpowiedzialne za działania centrum operacji w zakresie zabezpieczeń i personel (SOC/SecOP), zwalnianie czasu i zasobów na potrzeby bardziej szczegółowego badania i polowania dla, zaawansowanych zagrożeń. Automatyzacja pobiera kilka różnych formularzy na platformie Azure — od reguł automatyzacji, które centralnie zarządzają automatyzacją obsługi zdarzeń i odpowiedzi, aby elementy PlayBook, które uruchamiają wstępnie określone sekwencje akcji, aby zapewnić zaawansowane i elastyczne zaawansowane automatyzacje zadań odpowiedzi na zagrożenia.

## <a name="automation-rules"></a>Reguły automatyzacji

Reguły automatyzacji są nową koncepcją na platformie Azure — wskaźnikiem. Ta funkcja umożliwia użytkownikom centralne zarządzanie automatyzacją obsługi zdarzeń. Oprócz tego, że przypiszesz elementy PlayBook do zdarzeń (nie tylko dla alertów, jak poprzednio), reguły automatyzacji umożliwiają również Automatyzowanie odpowiedzi dla wielu reguł analizy jednocześnie, automatyczne oznaczanie tagów, przypisywanie lub zamykanie incydentów bez potrzeby elementy playbook i sterowanie kolejnością wykonywanych akcji. Reguły automatyzacji usprawnią korzystanie z usługi Automation na platformie Azure i umożliwią uproszczenie złożonych przepływów pracy dla procesów aranżacji zdarzeń.

Dowiedz się więcej, korzystając z tego [kompletnego wyjaśnienia reguł automatyzacji](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Funkcja **reguł automatyzacji** jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="playbooks"></a>Podręczniki

Element PlayBook to zbiór akcji odpowiedzi i korygowania, które można uruchamiać z poziomu platformy Azure jako procedury. Element PlayBook może pomóc w automatyzowaniu i koordynowaniu odpowiedzi na zagrożenia, która może być zintegrowana z innymi systemami wewnętrznymi i zewnętrznymi, i można ją skonfigurować tak, aby uruchamiała się automatycznie w odpowiedzi na określone alerty lub incydenty, w przypadku wyzwolenia odpowiednio przez regułę analizy lub regułę automatyzacji. Można go również uruchomić ręcznie na żądanie, w odpowiedzi na alerty, na stronie zdarzenia.

Elementy PlayBook na platformie Azure — na podstawie przepływów pracy wbudowanych w [Azure Logic Apps](../logic-apps/logic-apps-overview.md), usługi w chmurze, która ułatwia planowanie, Automatyzowanie i organizowanie zadań i przepływów pracy w różnych systemach w całym przedsiębiorstwie. Oznacza to, że elementy PlayBook może korzystać z zalet możliwości integracji i organizowania Logic Apps "oraz łatwych w użyciu narzędzi do projektowania i skalowalności, niezawodności i poziomu usług dla usługi platformy Azure w warstwie 1.

Dowiedz się więcej, korzystając z tego [kompletnego wyjaśnienia elementy PlayBook](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób, w jaki usługa Azure wskaźnikowa korzysta z automatyzacji, aby ułatwić wydajniejsze i wydajne działanie SOC.

- Aby dowiedzieć się więcej o automatyzacji obsługi zdarzeń, zobacz [Automatyzacja obsługi zdarzeń na platformie Azure — wskaźnik](automate-incident-handling-with-automation-rules.md).
- Aby dowiedzieć się więcej na temat zaawansowanych opcji automatyzacji, zobacz [Automatyzowanie odpowiedzi na zagrożenia z elementy PlayBook na platformie Azure](automate-responses-with-playbooks.md).
- Aby uzyskać pomoc w implementacji reguł i elementy PlayBook automatyzacji, zobacz [Samouczek: używanie elementy PlayBook do automatyzowania odpowiedzi na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
