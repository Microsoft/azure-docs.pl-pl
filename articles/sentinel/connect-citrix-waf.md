---
title: Łączenie danych Citrix WAF z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika danych Citrix WAF do ściągania dzienników do platformy Azure. Wyświetlaj dane Citrix WAF w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93103125"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Łączenie rozwiązania Citrix WAF z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych usługi Citrix Web Application Firewall (WAF) w witrynie Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć urządzenie z zaporą aplikacji sieci Web firmy Citrix (WAF) z platformą Azure. Łącznik danych Citrix WAF umożliwia łatwe łączenie dzienników Citrix WAF z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Łącząc dzienniki CEF Citrix WAF z platformą Azure, możesz skorzystać z funkcji wyszukiwania oraz korelacji, alertów i wzbogacania analizy zagrożeń dla każdego dziennika.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Przekazywanie dzienników Citrix WAF do agenta dziennika systemu  

Program Citrix WAF wysyła komunikaty dziennika systemowego w formacie CEF do serwera usługi przekazywania dzienników opartego na systemie Linux (z systemem rsyslog lub dziennika systemowego-NG) z zainstalowanym agentem Log Analytics, który przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. Jeśli nie masz takiego serwera przesyłania dalej dzienników, zapoznaj się z [tymi instrukcjami](connect-cef-agent.md) , aby rozpocząć działanie.

1. Postępuj zgodnie z instrukcjami dostarczonymi przez Citrix, aby [skonfigurować WAF](https://support.citrix.com/article/CTX234174), [skonfigurować rejestrowanie CEF](https://support.citrix.com/article/CTX136146)i [skonfigurować wysyłanie dzienników do usługi przesyłania dalej dzienników](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Upewnij się, że dzienniki są wysyłane do portu TCP 514 na adres IP komputera usługi przesyłania dalej dzienników.

1. Sprawdź poprawność połączenia i sprawdź pozyskanie danych, korzystając z [tych instrukcji](connect-cef-verify.md). Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby wykonać zapytanie dotyczące dzienników Citrix WAF w Log Analytics, wprowadź `CommonSecurityLog` w górnej części okna zapytania.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie pokazano, jak połączyć program Citrix WAF z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.