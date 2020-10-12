---
title: Łączenie danych DNS na platformie Azure — wskaźnikowa | Microsoft Docs
description: Dowiedz się, jak połączyć wszystkie serwery nazw domen (DNS) działające w systemie Windows z platformą Azure, instalując agenta na maszynie DNS.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: a88696ba69fdf53f5c7e15d174b126d69f4230ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85555431"
---
# <a name="connect-your-domain-name-server"></a>Łączenie serwera nazw domen

> [!IMPORTANT]
> Łącznik danych DNS na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można połączyć wszystkie serwery nazw domen (DNS) działające w systemie Windows z platformą Azure. W tym celu należy zainstalować agenta na maszynie DNS. Korzystając z dzienników DNS, można uzyskać szczegółowe informacje dotyczące zabezpieczeń, wydajności i operacji w infrastrukturze DNS organizacji przez zbieranie, analizowanie i skorelowanie dzienników analitycznych i inspekcji oraz innych powiązanych danych z serwerów DNS.

Po włączeniu połączenia dziennika DNS można:
- Identyfikowanie klientów próbujących rozpoznać złośliwe nazwy domen
- Identyfikowanie starych rekordów zasobów
- Zidentyfikuj często badane nazwy domen i próg klientów DNS
- Wyświetlanie obciążenia żądaniami na serwerach DNS
- Wyświetl błędy rejestracji dynamicznej usługi DNS

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../azure-monitor/platform/agent-windows.md) | Tak | Rozwiązanie zbiera informacje DNS z agentów systemu Windows. |
| [Agenci dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji DNS z bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Tak | Rozwiązanie zbiera informacje DNS z agentów w połączonej grupie zarządzania Operations Manager. Bezpośrednie połączenie z agenta Operations Manager do Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego Log Analytics. |
| [Konto usługi Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Storage nie jest używana przez rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły zbierania danych

Rozwiązanie zbiera dane dotyczące spisu DNS i zdarzeń DNS z serwerów DNS, na których zainstalowano agenta Log Analytics. Dane dotyczące spisu, takie jak liczba serwerów DNS, stref i rekordów zasobów, są zbierane przez uruchomienie poleceń cmdlet programu PowerShell dla usługi DNS. Dane są aktualizowane co dwa dni. Dane związane ze zdarzeniami są zbierane niemal w czasie rzeczywistym z [dzienników analitycznych i inspekcji](https://technet.microsoft.com/library/dn800669.aspx#enhanc) dostarczonych przez ulepszone rejestrowanie i diagnostykę DNS w systemie Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Połącz urządzenie DNS

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** i wybierz kafelek **DNS (wersja zapoznawcza)** .
1. Jeśli Twoje maszyny DNS znajdują się na platformie Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynie wirtualnej platformy Azure systemu Windows**.
    1. Na liście **maszyny wirtualne** wybierz MASZYNę DNS, która ma zostać przesłana do usługi Azure wskaźnikowej. Upewnij się, że jest to maszyna wirtualna z systemem Windows.
    1. W oknie otwartym dla tej maszyny wirtualnej kliknij pozycję **Połącz**.  
    1. W oknie **łącznika usługi DNS** kliknij pozycję **Włącz** . 

2. Jeśli maszyna DNS nie jest maszyną wirtualną platformy Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynach spoza platformy Azure**.
    1. W oknie **agenta bezpośredniego** wybierz pozycję **Pobierz agenta systemu Windows (64 bit)** lub **pobierz agenta systemu Windows (32 bit)**.
    1. Zainstaluj agenta na maszynie DNS. Skopiuj **Identyfikator obszaru roboczego**, **klucz podstawowy**i **klucz pomocniczy** , a następnie użyj ich po wyświetleniu monitu podczas instalacji.

3. Aby użyć odpowiedniego schematu w Log Analytics dla dzienników DNS, wyszukaj ciąg **DnsEvents**.

## <a name="validate"></a>Walidacja 

W Log Analytics Wyszukaj schemat **DnsEvents** i upewnij się, że istnieją zdarzenia.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zapytania wyszukiwania nie są wyświetlane na platformie Azure, wykonaj następujące kroki, aby zapytania były wyświetlane prawidłowo:
1. Włącz [dzienniki DNS Analytics na serwerach](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Upewnij się, że DNSEvents pojawia się na liście kolekcji Log Analytics.
3. Włącz [Azure DNS analizy](../azure-monitor/insights/dns-analytics.md).
4. W Azure DNS Analytics w obszarze **Konfiguracja**zmień dowolne ustawienia, Zapisz je, a następnie zmień je ponownie, jeśli zachodzi taka potrzeba, a następnie Zapisz je ponownie.
5. Sprawdź Azure DNS analizy, aby upewnić się, że zapytania są teraz wyświetlane.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z lokalnymi urządzeniami DNS z systemem Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
