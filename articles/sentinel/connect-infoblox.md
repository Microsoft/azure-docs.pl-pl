---
title: Łączenie danych Infoblox Network Identity Operating System (NIOS) z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane usługi InfoBlox Network Identity Operating System (NIOS) z platformą Azure.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532172"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Połącz swoje Infoblox NIOS z platformą Azure — wskaźnikiem

W tym artykule wyjaśniono, jak połączyć [urządzenie z systemem operacyjnym Infoblox (Nios) tożsamości sieci](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) z platformą Azure. Łącznik danych Infoblox NIOS umożliwia łatwe łączenie dzienników Infoblox z platformą Azure, w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między programem Infoblox NIOS i platformą Azure ze wskaźnikami korzysta z dziennika systemowego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Przekazywanie dzienników Infoblox do agenta dziennika systemu  

Skonfiguruj Infoblox do przesyłania komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję Łącznik **Infoblox Nios** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie **INFOBLOX Nios** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Infoblox NIOS z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.