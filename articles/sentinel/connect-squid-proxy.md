---
title: Łączenie danych serwera proxy Squid z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak używać łącznika danych serwera proxy Squid do ściągania dzienników squid proxy do usługi Azure wskaźnikowej. Wyświetlaj dane serwera proxy Squid w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090449"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Połącz serwer proxy Squid z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik serwera proxy Squid jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenie proxy Squid z platformą Azure — wskaźnikiem. Łącznik danych serwera proxy Squid umożliwia łatwe łączenie dzienników squid z platformą Azure, dzięki czemu można wyświetlać dane ze skoroszytów, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między squid serwerem proxy i platformą Azure wskaźnikiem umożliwia korzystanie z lokalnego przetwarzania plików przez agenta Log Analytics.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Przekazywanie dzienników serwera proxy Squid do agenta Log Analytics  

Skonfiguruj serwer proxy Squid do wysyłania plików dziennika do obszaru roboczego platformy Azure za pośrednictwem agenta Log Analytics.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz łącznik **squid proxy (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika **serwera proxy Squid** :

    1. Instalowanie i dołączanie agenta dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna).

    1. Konfigurowanie dzienników do zebrania

        - W obszarze Ustawienia zaawansowane obszaru roboczego Dodaj niestandardowy typ dziennika, Przekaż przykładowy plik i skonfiguruj go jako kierowany.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w obszarze **dzienniki niestandardowe** w `SquidProxy_CL` tabeli.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z serwerem proxy Squid na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
