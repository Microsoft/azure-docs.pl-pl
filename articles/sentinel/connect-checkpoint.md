---
title: Połącz dane usługi Check Point z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak połączyć dane punktów kontrolnych z platformą Azure.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588420"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Połącz punkt kontroli z platformą Azure — wskaźnik



W tym artykule wyjaśniono, jak połączyć urządzenie z punktem kontrolnym z platformą Azure. Łącznik danych Check Point umożliwia łatwe łączenie dzienników punktów kontrolnych z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Użycie funkcji Check Point na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji na temat użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Przekazywanie dzienników punktów kontrolnych do agenta dziennika systemu

Skonfiguruj urządzenie z punktem kontrolnym do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Przejdź do opcji [Eksportuj dziennik do dziennika](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Przewiń w dół do **podstawowego wdrożenia** i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie, korzystając z następujących wskazówek:
   - Ustaw wartość w polu **port dziennika** systemowego na **514** lub port ustawiony w agencie.
     - Zastąp **nazwę** i **adres IP serwera** w interfejsie wiersza polecenia nazwą i adresem IP agenta dziennika systemowego.
     - Ustaw format na **CEF**.
1. Jeśli używasz wersji R 77.30 lub R 80.10, przewiń do **instalacji** i postępuj zgodnie z instrukcjami, aby zainstalować eksportera dzienników dla używanej wersji.
1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).
 

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń Check Point z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- [Sprawdź poprawność łączności](connect-cef-verify.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


