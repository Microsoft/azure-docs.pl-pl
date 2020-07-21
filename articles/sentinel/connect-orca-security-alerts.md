---
title: Łączenie alertów programu Orca z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane alertów zabezpieczeń programu Orca z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532262"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Łączenie alertów programu Orca z platformą Azure — wskaźnikiem 

Łącznik alertów zabezpieczeń programu Orca umożliwia łatwe przełączenie alertów zabezpieczeń programu [Orca](https://orca.security/) do usługi Azure wskaźnikowych, aby można było wyświetlać je w skoroszytach, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja z alertami zabezpieczeń programu Orca i wskaźnikiem na platformie Azure oznacza korzystanie z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-orca-security-alerts"></a>Konfigurowanie i łączenie alertów zabezpieczeń Orca

Alerty zabezpieczeń programu Orca umożliwiają integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz opcję **alerty zabezpieczeń programu Orca** , a następnie **Otwórz stronę łącznik**.

2. Zapoznaj się z tematem https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration , aby zakończyć integrację z platformą Orca.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze **CustomLogs** w tabeli **OrcaAlerts_CL** .
Aby użyć odpowiedniego schematu w Log Analytics dla alertów programu Orca, wyszukaj ciąg `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia alertów zabezpieczeń programu Orca z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

