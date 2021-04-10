---
title: Łączenie danych usługi Azure Defender z platformą Azure — wskaźnikiem
description: Dowiedz się, jak połączyć alerty usługi Azure Defender z Azure Security Center i przesyłać je strumieniowo do usługi Azure wskaźnikowej.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633048"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Połącz dane alertów usługi Azure Defender z Azure Security Center

Skorzystaj z łącznika alertu usługi Azure Defender, aby uzyskać alerty usługi Azure Defender z [Azure Security Center](../security-center/security-center-introduction.md) i przesyłać je strumieniowo do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć rolę czytelnik zabezpieczeń w subskrypcji przesyłanych strumieniowo dzienników.

- Należy włączyć usługę Azure Defender w ramach Azure Security Center. (Warstwa standardowa już nie istnieje i nie jest już wymaganie dotyczące licencji).

## <a name="connect-to-azure-defender"></a>Nawiązywanie połączenia z usługą Azure Defender

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Z galerii łączników danych wybierz pozycję **alerty usługi Azure Defender z poziomu ASC** (nadal może być wywoływana Azure Security Center), a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. W obszarze **Konfiguracja** kliknij pozycję **Połącz** obok każdej subskrypcji, której alerty chcesz przesłać do usługi Azure wskaźnikowej. Przycisk Połącz będzie dostępny tylko wtedy, gdy masz wymagane uprawnienia.

1. Możesz wybrać, czy alerty z usługi Azure Defender mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **włączone** , aby włączyć domyślną regułę analizy, która automatycznie tworzy zdarzenia z alertów. Następnie można edytować tę regułę w obszarze **Analiza** na karcie  **aktywne reguły** .

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Azure Defender, Wyszukaj pozycję **SecurityAlert**.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Azure Defender z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
