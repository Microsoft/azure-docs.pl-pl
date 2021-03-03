---
title: Łączenie VMware ESXi danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika danych VMware ESXi do ściągania dzienników ESXi do platformy Azure. Wyświetlaj dane ESXi w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700930"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Połącz VMware ESXi z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik VMware ESXi jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenie VMware ESXi z platformą Azure — wskaźnikiem. Łącznik danych VMware ESXi umożliwia łatwe pozyskiwanie dzienników VMware ESXi na platformie Azure, dzięki czemu możesz uzyskać więcej informacji na temat działań ESXi organizacji i pomóc w ulepszaniu możliwości operacji związanych z bezpieczeństwem. Integracja między usługami VMware ESXi i Azure — wskaźnikiem, umożliwia korzystanie z serwera dziennika systemowego z zainstalowanym agentem Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Twoje rozwiązanie VMware ESXi musi być skonfigurowane do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Wysyłanie dzienników VMware ESXi do agenta dziennika systemu  

Skonfiguruj VMware ESXi do przesyłania komunikatów dziennika systemowego do obszaru roboczego wskaźnikowego platformy Azure za pośrednictwem agenta dziennika systemowego.
3. Postępuj zgodnie z instrukcjami na stronie **VMware ESXi** .


1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz łącznik **VMware ESXi (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami na stronie **VMware ESXi** łącznika:

    1. Instalowanie i dołączanie agenta dla systemu Linux

        - Wybierz maszynę wirtualną z systemem Linux lub maszynę wirtualną w systemie innym niż Azure (fizyczna lub wirtualna).

    1. Konfigurowanie dzienników do zebrania

        - Wybierz funkcje i możliwości konfiguracji w obszarze **Konfiguracja agentów obszaru roboczego**.

    1. Konfigurowanie i łączenie VMware ESXi

        - Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować VMware ESXi do przesyłania dalej dziennika systemowego:
            - [VMware ESXi 3,5 i 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 i nowsze](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Na serwerze zdalnym Użyj adresu IP maszyny z systemem Linux, na której zainstalowano agenta systemu Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Weryfikowanie łączności i znajdowanie danych

Rozpoczęcie wyświetlania dzienników na platformie Azure wskaźnikowej może potrwać do 20 minut. 

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji *Zarządzanie dziennikami* *w tabeli* dziennik systemowy.

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. [Wykonaj następujące kroki](https://aka.ms/sentinel-vmwareesxi-parser) , aby utworzyć alias funkcji **VMwareESXi** Kusto. Następnie można wpisać `VMwareESXi` dowolne okno zapytania na platformie Azure do wykonywania zapytań dotyczących danych.

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne przykłady zapytań.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia VMware ESXi z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
