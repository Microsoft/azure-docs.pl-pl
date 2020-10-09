---
title: Łączenie funkcji DLP Forcepoint z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć funkcję DLP Forcepoint z platformą Azure.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588250"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Łączenie funkcji DLP Forcepoint z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych Forcepoint ochrony przed utratą danych (DLP) na platformie Azure jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Łącznik DLP Forcepoint umożliwia automatyczne wyeksportowanie danych zdarzenia DLP do platformy Azure. Można jej użyć do uzyskania wglądu w działania użytkownika i zdarzenia utraty danych. Umożliwia również korelację z danymi z obciążeń platformy Azure i innymi źródłami danych oraz zwiększa możliwości monitorowania ze skoroszytami w ramach platformy Azure.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurowanie i łączenie funkcji DLP Forcepoint

Skonfiguruj funkcję DLP Forcepoint, aby przesłać dane zdarzenia w formacie JSON do obszaru roboczego platformy Azure za pośrednictwem interfejsu API REST, jak wyjaśniono w [przewodniku integracji FORCEPOINT DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Znajdowanie danych

Po skonfigurowaniu łącznika DLP Forcepoint dane są wyświetlane w Log Analytics w obszarze CustomLogs **ForcepointDLPEvents_CL**.


Aby użyć odpowiedniego schematu w Log Analytics Forcepoint DLP, wyszukaj ciąg **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia funkcji DLP Forcepoint z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.