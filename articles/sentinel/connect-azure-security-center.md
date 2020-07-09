---
title: Łączenie Azure Security Center danych z platformą Azure — wskaźnikiem
description: Dowiedz się, jak łączyć alerty z warstwy Standardowa Azure Security Center (ASC) i przesyłać strumieniowo do platformy Azure.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559151"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Połącz dane z Azure Security Center (ASC)

System Azure — wskaźnik produkcji umożliwia łączenie alertów z [Azure Security Center](../security-center/security-center-intro.md) i przesyłanie ich strumieniowo do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Aby wyeksportować alerty z Azure Security Center, musisz mieć rolę czytnika zabezpieczeń w subskrypcji przesyłanych strumieniowo dzienników.

- W ramach subskrypcji musi być uruchomiona [Azure Security Center warstwa standardowa](../security-center/security-center-pricing.md) . W przeciwnym razie [Uaktualnij subskrypcję do wersji Standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Połącz z Azure Security Center

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. W galerii łączniki danych wybierz pozycję **Azure Security Center**, a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. W obszarze **Konfiguracja**kliknij pozycję **Połącz** obok każdej subskrypcji, której alerty chcesz przesłać do usługi Azure wskaźnikowej. Przycisk Połącz będzie dostępny tylko wtedy, gdy masz wymagane uprawnienia i subskrypcję warstwy Standardowa ASC.

1. Możesz wybrać, czy alerty od Azure Security Center mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **włączone** , aby włączyć domyślną regułę analizy, która automatycznie tworzy zdarzenia z alertów. Następnie można edytować tę regułę w obszarze **Analiza**na karcie **aktywne reguły** .

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure Security Center, Wyszukaj pozycję **SecurityAlert**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Security Center z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
