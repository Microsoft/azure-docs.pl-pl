---
title: Łączenie danych systemowych zarządzania atakami Illusive do usługi Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane systemu zarządzania Illusive ataków na platformę Azure.
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
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532169"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Połącz system zarządzania atakami Illusive z platformą Azure — wskaźnikiem

W tym artykule wyjaśniono, jak połączyć [system zarządzania atakami Illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) z platformą Azure. Łącznik danych systemu Illusiveego zarządzania atakami umożliwia udostępnianie Illusiveych danych analizy obszaru i dzienników zdarzeń na platformie Azure, a następnie wyświetlanie tych informacji na dedykowanych pulpitach nawigacyjnych, które oferują wgląd w informacje o zagrożeniu narażonym na ataki w organizacji (pulpit nawigacyjny ASM) i śledzenie nieautoryzowanego ruchu bocznego w sieci organizacji (pulpit nawigacyjny AD).

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Przekaż Dzienniki systemu do zarządzania atakami Illusive do agenta dziennika systemowego  

Skonfiguruj system zarządzania atakami do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Zaloguj się do konsoli Illusive i przejdź do ustawień — >raportowanie.

1. Znajdź dziennik systemowy Serversץ

1. Wprowadź następujące informacje:
   - Nazwa hosta: adres IP lub nazwa FQDN agenta dziennika systemu Linux
   - Port: 514
   - Protokół: TCP
   - Komunikaty inspekcji: wysyłanie komunikatów inspekcji do serwera

1. Aby dodać serwer dziennika systemu, kliknij przycisk Dodaj.

1. Aby użyć odpowiedniego schematu w Log Analytics w systemie zarządzania atakami Illusive, Wyszukaj pozycję CommonSecurityLog.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z systemem zarządzania atakami Illusive na platformę Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
