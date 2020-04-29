---
title: Łączenie danych Fortinet z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane Fortinet z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588199"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Łączenie Fortinet z platformą Azure — wskaźnik



W tym artykule wyjaśniono, jak połączyć urządzenie Fortinet z platformą Azure. Program Fortinet Data Connector umożliwia łatwe łączenie dzienników Fortinet z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i ulepszanie badania. Korzystanie z platformy Fortinet na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji dotyczących użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Przekazywanie dzienników firmy Fortinet do agenta dziennika systemu

Skonfiguruj konfigurację Fortinet do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Otwórz interfejs wiersza polecenia na urządzeniu Fortinet i uruchom następujące polecenia:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Zastąp **adres IP** serwera adresem IP agenta.
    - Ustaw wartość w polu **port dziennika** systemowego na **514** lub port ustawiony w agencie.
    - Aby włączyć format CEF w wersjach wczesnego FortiOS, może być konieczne uruchomienie opcji **wyłączania CSV**zestawu poleceń.
 
   > [!NOTE] 
   > Aby uzyskać więcej informacji, przejdź do [biblioteki dokumentów Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wybierz swoją wersję, a następnie użyj dokumentacji **podręcznika** i **komunikatu dziennika**.

1. Aby użyć odpowiedniego schematu w Azure Monitor Log Analytics dla imprez Fortinet, wyszukaj ciąg `CommonSecurityLog`.

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób łączenia urządzeń Fortinet z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


