---
title: Połączenie pulsu Połącz zabezpieczone dane z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak połączyć puls Połącz zabezpieczone dane z platformą Azure — wskaźnikiem.
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
ms.openlocfilehash: 71e1c7787243713b29be9455fee966eff54f6d90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097657"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Łączenie pulsu z bezpiecznym połączeniem z platformą Azure

> [!IMPORTANT]
> Łącznik pulsu bezpiecznego połączenia z danymi na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć urządzenie [pulsu z bezpiecznym](https://www.pulsesecure.net/products/pulse-connect-secure/) urządzeniem z platformą Azure. Łącznik pulsu Secure data Connector umożliwia łatwe łączenie dzienników pulsu z bezpiecznymi dziennikami przy użyciu platformy Azure — do wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między usługą puls Connect Secure a wskaźnikiem na platformie Azure oznacza korzystanie z dziennika systemowego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Impulsowe łączenie bezpiecznych dzienników z agentem dziennika systemowego  

Skonfiguruj bezpieczne łączenie pulsu do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **Pulse Połącz bezpieczny** łącznik.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **bezpieczne łączenie pulsu** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia połączeń pulsu Secure z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.