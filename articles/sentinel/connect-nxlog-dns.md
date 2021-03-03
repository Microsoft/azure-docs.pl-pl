---
title: Łączenie NXLog Windows DNS rejestruje dane do usługi Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak używać łącznika danych dzienników DNS NXLog do ściągania zdarzeń DNS systemu Windows do usługi Azure wskaźnikowej. Wyświetlanie danych DNS systemu Windows w skoroszytach, tworzenie alertów i ulepszanie badania.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747080"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Łączenie NXLog dzienników DNS systemu Windows z platformą Azure — wskaźnik

> [!IMPORTANT]
> Łącznik dzienników DNS NXLog jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik [dzienników DNS NXLog](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) umożliwia łatwe eksportowanie wszystkich zdarzeń serwera DNS systemu Windows do platformy Azure, w czasie rzeczywistym, dzięki czemu można uzyskać wgląd w działanie serwera nazw domen w całej organizacji. Wykorzystuje śledzenie zdarzeń o wysokiej wydajności [dla systemu Windows (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) do zbierania zdarzeń zarówno w czasie rzeczywistym, jak i analitycznego serwera DNS, i obsługuje wzbogacanie zdarzeń z polami niestandardowymi. Integracja między dziennikami DNS NXLog i wskaźnikiem "Azure" jest ułatwiona za pośrednictwem interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Konfigurowanie i łączenie dzienników DNS NXLog

NXLog można skonfigurować do wysyłania zdarzeń w formacie JSON bezpośrednio do platformy Azure.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **NXLog usługi Dzienniki DNS** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Aby skonfigurować przekazywanie za pośrednictwem interfejsu API REST, wykonaj instrukcje krok po kroku w temacie Integracja *podręcznika użytkownika NXLog* [Microsoft Azure wskaźnikiem](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji  **dzienniki niestandardowe** w tabeli *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z programu NXLog do pozyskiwania dzienników DNS systemu Windows w ramach platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
