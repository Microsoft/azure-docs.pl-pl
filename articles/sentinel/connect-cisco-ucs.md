---
title: Łączenie danych Cisco Unified Computing System (UCS) z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika danych Cisco UCS do ściągania dzienników Cisco UCS do platformy Azure. Wyświetlaj dane Cisco UCS w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: caa83b9149f39f69d0cbf44a2d6cb01fdaf29721
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567919"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Łączenie systemu Cisco Unified Computing System (UCS) z platformą Azure — wskaźnik

> [!IMPORTANT]
> Łącznik Cisco UCS jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak podłączyć urządzenie Cisco Unified Computing System (UCS) do kontrolki wskaźnikowej platformy Azure. Łącznik danych Cisco UCS umożliwia łatwe łączenie dzienników UCS z platformą Azure, dzięki czemu można wyświetlać dane ze skoroszytów, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między Cisco UCS i Azure wskaźnikiem jest używana przez dziennik systemowy.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Twoje rozwiązanie Cisco UCS musi być skonfigurowane do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Przekazywanie dzienników Cisco UCS do agenta dziennika systemu  

Skonfiguruj Cisco UCS do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego wskaźnikowego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. Z galerii **łączników danych** wybierz łącznik **Cisco UCS (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika **Cisco UCS** :

    1. Instalowanie i dołączanie agenta dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna).

    1. Konfigurowanie dzienników do zebrania

        - Wybierz obiekty i zwolnienia w konfiguracji zaawansowane ustawienia obszaru roboczego

    1. Konfigurowanie i łączenie programu Cisco UCS

        - Postępuj zgodnie z [tymi instrukcjami](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) , aby skonfigurować Cisco UCS do przesyłania dalej dziennika systemowego. Na serwerze zdalnym Użyj adresu IP maszyny z systemem Linux, na której zainstalowano agenta systemu Linux.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z programem Cisco UCS z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
