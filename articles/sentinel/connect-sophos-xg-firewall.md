---
title: Łączenie danych zapory Sophos XG z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane zapory Sophos XG z platformą Azure.
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
ms.openlocfilehash: 0b8b6247a735bceaf98029740bf9d4f7e233069d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097572"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Połącz zaporę Sophos XG z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych zapory Sophos XG w usłudze Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak podłączyć urządzenie [zapory Sophos XG](https://www.sophos.com/products/next-gen-firewall.aspx) do usługi Azure wskaźnikowej. Łącznik danych zapory Sophos XG umożliwia łatwe łączenie dzienników zapory Sophos XG z platformą Azure, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i usprawnić badanie. Integracja między zaporą Sophos XG a usługą Azure wskaźnikiem umożliwia korzystanie z dziennika systemowego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Przekazywanie dzienników zapory Sophos XG do agenta dziennika systemu  

Skonfiguruj zaporę Sophos XG do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **Sophos XG firewall** Connector.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **zapory Sophos XG** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia zapory Sophos XG z usługą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.