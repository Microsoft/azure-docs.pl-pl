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
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038121"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Połącz system zarządzania atakami Illusive z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych systemu ataku Illusive na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć [system zarządzania atakami Illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) z platformą Azure. Łącznik danych systemu Illusiveego zarządzania atakami umożliwia udostępnianie Illusiveych danych analizy obszaru i dzienników zdarzeń na platformie Azure, a następnie wyświetlanie tych informacji na dedykowanych pulpitach nawigacyjnych, które oferują wgląd w informacje o zagrożeniu narażonym na ataki w organizacji (pulpit nawigacyjny ASM) i śledzenie nieautoryzowanego ruchu bocznego w sieci organizacji (pulpit nawigacyjny AD).

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Przekaż Dzienniki systemu do zarządzania atakami Illusive do agenta dziennika systemowego  

Skonfiguruj system zarządzania atakami do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Zaloguj się do konsoli Illusive i przejdź do ustawień — >raportowanie.

1. Znajdź serwery dziennika systemowego.

1. Wprowadź następujące informacje:
   - Nazwa hosta: adres IP lub nazwa FQDN agenta dziennika systemu Linux
   - Port: 514
   - Protokół: TCP
   - Komunikaty inspekcji: wysyłanie komunikatów inspekcji do serwera

1. Aby dodać serwer dziennika systemu, kliknij przycisk Dodaj.

1. Aby użyć odpowiedniego schematu w **dziennikach** dla systemu Illusiveego zarządzania atakami, Wyszukaj pozycję **CommonSecurityLog**.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z systemem zarządzania atakami Illusive na platformę Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
