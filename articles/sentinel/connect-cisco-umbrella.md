---
title: Łączenie Cisco parasol z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika danych Cisco parasol do ściągania danych parasol do platformy Azure. Wyświetlaj dane parasolowe w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99566900"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Łączenie Cisco parasol z platformą Azure wskaźnikiem

> [!IMPORTANT]
> Łącznik Cisco parasol jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik Cisco parasol pozwala łatwo połączyć wszystkie dzienniki rozwiązań zabezpieczeń Cisco parasol z Twoim wskaźnikiem kontrolnym platformy Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Integracja między programem Cisco parasol i platformą Azure wskaźnikiem wykorzystuje interfejs API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-cisco-umbrella"></a>Konfigurowanie i łączenie Cisco parasol

Cisco parasol mogą integrować i eksportować dzienniki bezpośrednio do platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. Z galerii **łączników danych** wybierz pozycję **Cisco parasol (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Wykonaj kroki opisane w sekcji **Konfiguracja** na stronie łącznik.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w co najmniej jednej z następujących tabel:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia danych Cisco parasol z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
