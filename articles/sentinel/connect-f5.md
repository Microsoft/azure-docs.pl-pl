---
title: Łączenie danych F5 z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane F5 z platformą Azure wskaźnikiem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588267"
---
# <a name="connect-f5-to-azure-sentinel"></a>Połącz F5 z platformą Azure wskaźnikowego

W tym artykule wyjaśniono, jak podłączyć urządzenie F5 do usługi Azure wskaźnikowej. Łącznik danych F5 umożliwia łatwe łączenie dzienników F5 z danymi wskaźnikowymi platformy Azure, Wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Użycie klawisza F5 na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji na temat użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurowanie klawisza F5 w celu wysyłania komunikatów CEF

1. Przejdź do pozycji [F5 Konfigurowanie rejestrowania zdarzeń zabezpieczeń aplikacji](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)i postępuj zgodnie z instrukcjami, aby skonfigurować rejestrowanie zdalne, korzystając z następujących wskazówek:
   - Ustaw **Typ magazynu zdalnego** na **CEF**.
   - Ustaw **Protokół** na **TCP**.
   - Ustaw **adres IP** na adres IP serwera dziennika systemowego.
   - Ustaw **numer portu** na **514**lub port, który ma być używany przez agenta.
   - **Maksymalny rozmiar ciągu zapytania** można ustawić na rozmiar ustawiony w agencie.

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń CEF, wyszukaj ciąg `CommonSecurityLog`.

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z klawiszem F5 z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

