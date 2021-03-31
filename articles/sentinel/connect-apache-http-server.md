---
title: Połącz serwer Apache HTTP z platformą Azure — wskaźnikiem Microsoft Docs
description: Informacje na temat używania łącznika serwera Apache HTTP do ściągania dzienników oprogramowania Apache do platformy Azure — wskaźnik. Wyświetlaj dane Apache w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096858"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Połącz serwer Apache HTTP z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik serwera Apache HTTP jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć serwer Apache HTTP z platformą Azure. Łącznik serwera Apache HTTP umożliwia łatwe pozyskiwanie dzienników serwera HTTP platformy Apache w systemie Azure, dzięki czemu można wyświetlać dane w skoroszytach, wysyłać zapytania do tworzenia niestandardowych alertów i uwzględniać je w celu usprawnienia badania. Integracja między programem Apache HTTP Server i wskaźnikiem na platformie Azure oznacza korzystanie z lokalnego przetwarzania plików przez agenta Log Analytics.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienie do zapisu w obszarze roboczym wskaźnik platformy Azure.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Konfigurowanie i integrowanie dzienników serwera HTTP Apache za pośrednictwem agenta Log Analytics

Skonfiguruj serwer Apache HTTP do wysyłania plików dziennika do obszaru roboczego platformy Azure za pośrednictwem agenta Log Analytics.
Skonfiguruj agenta Log Analytics do odczytywania plików dziennika serwera Apache HTTP.

1. Postępuj zgodnie z instrukcjami w temacie https://httpd.apache.org/docs/2.4/logs.html , aby skonfigurować lokalizację plików dziennika w programie Apache HTTP Server.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję **Apache HTTP Server (wersja zapoznawcza)**.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie serwer Apache HTTP.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z serwerem Apache HTTP z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
