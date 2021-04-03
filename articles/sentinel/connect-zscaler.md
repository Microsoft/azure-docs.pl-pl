---
title: Łączenie danych rozwiązania Zscaler z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane rozwiązania Zscaler z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655259"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Łączenie rozwiązania Zscaler dostępu do Internetu z platformą Azure — wskaźnik

W tym artykule wyjaśniono, jak połączyć urządzenie z dostępem do Internetu rozwiązania Zscaler z platformą Azure. Łącznik danych rozwiązania Zscaler umożliwia łatwe łączenie dzienników rozwiązania Zscaler dostępu do Internetu (ZIA) z danymi wskaźnikowymi platformy Azure w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Korzystanie z programu rozwiązania Zscaler na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji na temat użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurowanie rozwiązania Zscaler do wysyłania komunikatów CEF

1. Na urządzeniu rozwiązania Zscaler należy ustawić te wartości, aby urządzenie wysyła niezbędne dzienniki w niezbędnym formacie do agenta dziennika systemu Azure wskaźnikowego na podstawie agenta Log Analytics. Te parametry można modyfikować w urządzeniu, o ile są one również modyfikowane w demona dziennika systemowego w ramach agenta wskaźnikowego platformy Azure.
    - Protokół = TCP
    - Port = 514
    - Format = CEF
    - Adres IP — upewnij się, że wysłano komunikaty CEF na adres IP maszyny wirtualnej dedykowanej do tego celu.
 Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania programu rozwiązania Zscaler i platformy Azure](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > To rozwiązanie obsługuje dziennik systemowy RFC 3164 lub RFC 5424.


1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń CEF, wyszukaj ciąg `CommonSecurityLog` .
1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia rozwiązania Zscaler dostępu do Internetu z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.