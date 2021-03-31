---
title: Łączenie danych adresu VIP firmy Symantec z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane adresu VIP firmy Symantec z platformą Azure — wskaźnikiem.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090415"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Łączenie adresu VIP firmy Symantec z platformą Azure wskaźnikowego

> [!IMPORTANT]
> Łącznik danych firmy Symantec na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć urządzenie z [adresem VIP firmy Symantec](https://vip.symantec.com/) z platformą Azure — wskaźnikiem. Łącznik danych firmy Symantec umożliwia łatwe łączenie dzienników adresów VIP firmy Symantec z danymi wskaźnikowymi platformy Azure w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między adresami VIP firmy Symantec i platformą Azure wskaźnikowego korzysta z dziennika systemowego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Przekazuj dzienniki adresów VIP firmy Symantec do agenta dziennika systemu  

Skonfiguruj adres VIP firmy Symantec do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję Łącznik usługi **Symantec VIP** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **adresu VIP firmy Symantec** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dzienników firmy Symantec z usługą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.