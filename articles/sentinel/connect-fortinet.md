---
title: Łączenie danych Fortinet z platformą Azure — wskaźnikiem Microsoft Docs
description: Połącz urządzenie Fortinet z platformą Azure wskaźnikiem wydajności, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie.
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
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913997"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Łączenie Fortinet z platformą Azure — wskaźnik



W tym artykule wyjaśniono, jak połączyć urządzenie Fortinet z platformą Azure. Program Fortinet Data Connector umożliwia łatwe łączenie dzienników Fortinet z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i ulepszanie badania. Korzystanie z platformy Fortinet na platformie Azure wskaźnikowej zapewnia więcej szczegółowych informacji dotyczących użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Przekazywanie dzienników firmy Fortinet do agenta dziennika systemu

Skonfiguruj konfigurację Fortinet do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Otwórz interfejs wiersza polecenia na urządzeniu Fortinet i uruchom następujące polecenia:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Zastąp **adres IP** serwera adresem IP agenta.
    - Ustaw wartość w polu **port dziennika** systemowego na **514** lub port ustawiony w agencie.
    - Aby włączyć format CEF w wersjach wczesnego FortiOS, może być konieczne uruchomienie opcji **wyłączania CSV** zestawu poleceń.
 
   > [!NOTE] 
   > Aby uzyskać więcej informacji, przejdź do [biblioteki dokumentów Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wybierz swoją wersję, a następnie użyj dokumentacji **podręcznika** i **komunikatu dziennika** .

1. Aby użyć odpowiedniego schematu w Azure Monitor Log Analytics dla imprez Fortinet, wyszukaj ciąg `CommonSecurityLog` .

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób łączenia urządzeń Fortinet z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


