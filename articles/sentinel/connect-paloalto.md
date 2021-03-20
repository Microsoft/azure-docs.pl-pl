---
title: Łączenie danych Palo Alto Networks z platformą Azure Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych Palo Alto Networks do łatwego łączenia dzienników Palo Alto Networks z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85564558"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Łączenie Palo Alto z platformą Azure — wskaźnik



W tym artykule wyjaśniono, jak połączyć urządzenie z programem Palo Alto Networks z platformą Azure. Łącznik danych Palo Alto Networks umożliwia łatwe łączenie dzienników Palo Alto Networks z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Korzystanie z Palo Alto Networks na platformie Azure wskaźnikowa obejmuje więcej szczegółowych informacji na temat użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Przekazywanie dzienników Palo Alto Networks do agenta dziennika systemu

Skonfiguruj Palo Alto Networks do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu:
1.  Przejdź do [przewodnika konfiguracji usługi Common Event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) i Pobierz plik PDF dla danego typu urządzenia. Postępuj zgodnie ze wszystkimi instrukcjami w przewodniku, aby skonfigurować urządzenie Palo Alto Networks do zbierania zdarzeń CEF. 

1.  Przejdź do sekcji [konfigurowanie monitorowania dziennika](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) systemowego i wykonaj kroki 2 i 3, aby skonfigurować przekazywanie zdarzeń CEF z urządzenia Palo Alto Networks do usługi Azure wskaźnikowej.

    1. Upewnij się, że **Format serwera dziennika** systemu jest ustawiony na **BSD**.

       > [!NOTE]
       > Operacje kopiowania/wklejania z pliku PDF mogą zmieniać tekst i wstawiać losowe znaki. Aby tego uniknąć, skopiuj tekst do edytora i Usuń wszystkie znaki, które mogą spowodować przerwanie formatowania dziennika przed jego wklejeniem, jak widać w tym przykładzie.
 
        ![Problem z kopiowaniem tekstu CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Palo Alto Networks, wyszukaj ciąg **CommonSecurityLog**.

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączeń z urządzeniami Palo Alto Networks z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


