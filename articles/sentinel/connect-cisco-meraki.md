---
title: Łączenie danych Cisco Meraki z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika Cisco Meraki do ściągania dzienników Cisco Meraki do usługi Azure wskaźnikowej. Wyświetlaj dane Cisco Meraki w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747087"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Łączenie rozwiązania Cisco Meraki z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Cisco Meraki jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenia Cisco Meraki (MX/MS/Pan) z platformą Azure. Łącznik danych Cisco Meraki umożliwia łatwe łączenie dzienników Cisco Meraki z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Integracja między programem Cisco Meraki i platformą Azure wskaźnikowego umożliwia korzystanie z serwera dziennika systemowego z zainstalowanym agentem Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Rozwiązanie Cisco Meraki musi być skonfigurowane do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Wysyłanie dzienników Cisco Meraki do usługi Azure wskaźnikowej za pośrednictwem agenta dziennika systemu  

Skonfiguruj Cisco Meraki do przekazywania komunikatów dziennika systemowego do obszaru roboczego wskaźnikowego platformy Azure za pośrednictwem agenta dziennika systemowego.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. Z galerii **łączników danych** wybierz łącznik **Cisco Meraki (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie łącznika **Cisco Meraki** :

    1. Instalowanie i dołączanie agenta dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna).

    1. Konfigurowanie dzienników do zebrania

        - Wybierz funkcje i możliwości konfiguracji w obszarze **Konfiguracja agentów obszaru roboczego**.

    1. Konfigurowanie i łączenie urządzeń Cisco Meraki

        - Postępuj zgodnie z [tymi instrukcjami](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) w celu skonfigurowania urządzeń Cisco Meraki do przesyłania dalej dziennika systemowego. Na serwerze zdalnym Użyj adresu IP maszyny z systemem Linux, na której zainstalowano agenta systemu Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Weryfikowanie łączności i znajdowanie danych

Rozpoczęcie wyświetlania dzienników na platformie Azure wskaźnikowej może potrwać do 20 minut. 

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji *Zarządzanie dziennikami* *w tabeli* dziennik systemowy.

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. [Wykonaj następujące kroki](https://aka.ms/sentinel-ciscomeraki-parser) , aby utworzyć alias funkcji **CiscoMeraki** Kusto. Następnie można wpisać `CiscoMeraki` w oknie zapytania, aby wykonać zapytanie dotyczące danych.

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne przykłady zapytań.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z programem Cisco Meraki z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
