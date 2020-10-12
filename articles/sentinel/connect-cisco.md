---
title: Łączenie danych Cisco z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć urządzenie Cisco ASA z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85566044"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Łączenie programu Cisco ASA z platformą Azure — wskaźnikiem



W tym artykule wyjaśniono, jak połączyć urządzenie Cisco ASA z platformą Azure — wskaźnikiem. Łącznik danych Cisco ASA pozwala łatwo połączyć dzienniki programu Cisco ASA z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Korzystanie z programu Cisco ASA na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji dotyczących użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Przekazywanie dzienników Cisco ASA do agenta dziennika systemu

Cisco ASA nie obsługuje CEF, więc dzienniki są wysyłane jako dziennik systemowy, a agent wskaźnikowego platformy Azure wie, jak przeanalizować je tak, jakby znajdowały się w dziennikach CEF. Skonfiguruj Cisco ASA do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu:

1. Przejdź do obszaru [wysyłanie komunikatów dziennika systemowego do zewnętrznego serwera dziennika](https://aka.ms/asi-syslog-cisco-forwarding)systemu i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie. Użyj tych parametrów po wyświetleniu monitu:
    - Ustaw **port** na 514 lub port ustawiony w agencie.
    - Ustaw **syslog_ip** na adres IP agenta.

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Cisco, wyszukaj ciąg `CommonSecurityLog` .

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń z programem Cisco ASA z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


