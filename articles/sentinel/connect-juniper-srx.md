---
title: Łączenie Juniper sieci SRX danych z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika danych Juniper SRX do ściągania dzienników Juniper SRX do platformy Azure. Wyświetlaj dane Juniper SRX w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530641"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Połącz zaporę Juniper SRX z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Juniper SRX jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak podłączyć urządzenie zapory Juniper SRX do usługi Azure wskaźnikowej. Łącznik danych Juniper SRX umożliwia łatwe łączenie dzienników SRX z platformą Azure, dzięki czemu można wyświetlać dane ze skoroszytów, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między programem Juniper SRX i platformą Azure ze wskaźnikami korzysta z dziennika systemowego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Rozwiązanie Juniper SRX musi być skonfigurowane do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Przekazywanie dzienników usługi Juniper SRX do agenta dziennika systemu  

Skonfiguruj Juniper SRX do przesyłania komunikatów dziennika systemowego do obszaru roboczego wskaźnikowego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz łącznik **Juniper SRX (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika **Juniper SRX** :

    1. Instalowanie i dołączanie agenta dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna).

    1. Konfigurowanie dzienników do zebrania

        - Wybierz funkcje i możliwości konfiguracji w obszarze Konfiguracja agentów obszaru roboczego.

    1. Konfigurowanie i łączenie Juniper SRX

        - Postępuj zgodnie z tymi instrukcjami, aby skonfigurować Juniper SRX do przesyłania dalej dziennika systemowego.
            - [Dzienniki ruchu (dzienniki zasad zabezpieczeń)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Dzienniki systemu](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Na serwerze zdalnym Użyj adresu IP maszyny z systemem Linux, na której zainstalowano agenta systemu Linux.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze Dziennik systemu.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Juniper SRX z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
