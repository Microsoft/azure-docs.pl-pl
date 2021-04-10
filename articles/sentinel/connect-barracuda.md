---
title: Łączenie danych Barracuda z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika Barracuda aplikacji sieci Web (WAF) do łączenia dzienników Barracuda z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e1462246b95da67591cbdfd1f9ed819220de5764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633065"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Łączenie urządzenia z programem Barracuda WAF 

Łącznik zapory aplikacji sieci Web Barracuda (WAF) umożliwia łatwe łączenie dzienników Barracuda z platformą Azure, w celu wyświetlania pulpitów nawigacyjnych, tworzenia alertów niestandardowych i ulepszania badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Korzystanie z platformy Azure w celu zapewnienia bezproblemowej integracji obejmuje natywną integrację między **Barracuda** i agentem log Analytics. 

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurowanie i łączenie Barracuda WAF

Zapora aplikacji sieci Web Barracuda umożliwia integrację i eksportowanie dzienników bezpośrednio do funkcji wskaźnikowej platformy Azure za pośrednictwem agenta Log Analytics.

1. Przejdź do [przepływu konfiguracji usługi Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie przy użyciu następujących parametrów:

    - **Identyfikator obszaru roboczego**: Skopiuj wartość identyfikatora obszaru roboczego ze strony łącznika usługi Azure nazwa_obszaru_roboczego Barracuda.

    - **Klucz podstawowy**: Skopiuj wartość klucza podstawowego ze strony łącznika usługi Azure wskaźnik Barracuda.

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Barracuda, Wyszukaj **CommonSecurityLog** i **barracuda_CL**.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń Barracuda z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


