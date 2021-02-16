---
title: Łączenie danych CyberArk EPV z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika danych CyberArk Enterprise Password (EPV) do ściągania swoich dzienników do usługi Azure wskaźnikowej. Wyświetlaj dane CyberArk EPV w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530505"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Łączenie magazynu CyberArk Enterprise Passwords (EPV) z platformą Azure — wskaźnik

> [!IMPORTANT]
> Łącznik danych CyberArk przedsiębiorstwa z magazynem haseł (EPV) na platformie Azure jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik dziennika systemowego CyberArk umożliwia łatwe łączenie dzienników własnych rozwiązań zabezpieczeń CyberArk z badaniem wskaźnikowym platformy Azure w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania badania. Integracja między programami CyberArk i Azure — wskaźnikiem, korzysta z łącznika danych CEF, aby prawidłowo analizować i wyświetlać komunikaty dziennika systemu CyberArk.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-cyberark-epv"></a>Konfigurowanie i łączenie CyberArk EPV

Dzienniki EPV CyberArk są wysyłane z magazynu na serwer z systemem Linux (z systemem rsyslog lub dziennikiem systemu), z zainstalowanym agentem Log Analytics, który eksportuje dzienniki do usługi Azure wskaźnikowej. Jeśli nie masz takiego serwera przesyłania dalej dzienników, zapoznaj się z [tymi instrukcjami](connect-cef-agent.md) , aby rozpocząć działanie.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych**, wybierz pozycję **CYBERARK Enterprise Password EPV (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z [instrukcjami CYBERARK EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) , aby skonfigurować wysyłanie danych dziennika systemowego do serwera przesyłania dalej dzienników.

1. Sprawdź poprawność połączenia i sprawdź pozyskanie danych, korzystając z [tych instrukcji](connect-cef-verify.md). Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby wykonać zapytanie dotyczące dzienników CyberArk EPV w Log Analytics, wprowadź `CommonSecurityLog` w górnej części okna zapytania.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dzienników magazynu CyberArk Enterprise Password z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
