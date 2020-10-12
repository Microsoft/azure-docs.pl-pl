---
title: Łączenie danych Symantec ProxySG z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z danymi firmy Symantec ProxySG z platformą Azure.
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
ms.openlocfilehash: fe49481357b542ce52de5fc9dab3059cb584c229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083909"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Połącz swoją firmę Symantec ProxySG z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych firmy Symantec ProxySG na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć urządzenie z [firmą Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) z platformą Azure. Łącznik danych firmy Symantec ProxySG umożliwia łatwe łączenie dzienników programu Symantec ProxySG z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Integracja między firmą Symantec ProxySG i platformą Azure wskaźnikiem wykorzystuje dziennik systemowy.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Przekazywanie dzienników ProxySG firmy Symantec do agenta dziennika systemu  

Skonfiguruj ProxySG firmy Symantec do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **Symantec ProxySG** Connector.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **Symantec ProxySG** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z firmą Symantec ProxySG z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.