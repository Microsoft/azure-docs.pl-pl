---
title: Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych z obsługą usługi Azure Arc
description: W tym artykule opisano sposób rozwiązywania problemów z rozszerzeniami maszyn wirtualnych platformy Azure, które powstają w przypadku serwerów z obsługą usługi Azure Arc.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100584672"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych z włączonym łukiem

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów, które mogą wystąpić podczas próby wdrożenia lub usunięcia rozszerzeń maszyny wirtualnej platformy Azure na serwerach z obsługą łuku. Aby uzyskać ogólne informacje, zobacz [Zarządzanie rozszerzeniami maszyn wirtualnych platformy Azure i korzystanie](./manage-vm-extensions.md)z nich.

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta gościa, należy zapoznać się z działaniem w przypadku aprowizacji rozszerzenia w `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` systemie Windows oraz dla systemu Linux `/var/lib/GuestConfig/ext_mgr_logs` .

2. Aby uzyskać więcej informacji na temat systemu Windows, Sprawdź dzienniki rozszerzeń pod kątem określonego rozszerzenia `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . Dane wyjściowe rozszerzenia są rejestrowane w pliku dla każdego rozszerzenia zainstalowanego w systemie Linux w systemie `/var/lib/GuestConfig/extension_logs` .

3. Sprawdź sekcje dotyczące rozwiązywania problemów z dokumentacją, aby zapoznać się z kodami błędów, znanymi problemami itp. Dodatkowe informacje dotyczące rozwiązywania problemów dla każdego rozszerzenia można znaleźć w sekcji **Rozwiązywanie problemów i pomoc techniczna** w temacie Omówienie rozszerzenia. Obejmuje to opis kodów błędów zapisywana w dzienniku. Artykuły o rozszerzeniu są połączone w [tabeli rozszerzeń](manage-vm-extensions.md#extensions).

4. Sprawdź dzienniki systemu. Sprawdź inne operacje, które mogły mieć wpływ na rozszerzenie, takie jak długotrwała instalacja innej aplikacji wymagającej wyłącznego dostępu do Menedżera pakietów.

## <a name="troubleshooting-specific-extension-scenarios"></a>Rozwiązywanie problemów z określonymi scenariuszami rozszerzeń

### <a name="vm-insights"></a>Szczegółowe informacje o maszynie wirtualnej

- Podczas włączania usługi VM Insights dla serwera z włączoną funkcją Azure Arc jest instalowany Agent zależności i Log Analytics. Na wolnej maszynie lub z wolnym połączeniem sieciowym można zobaczyć limity czasu podczas procesu instalacji. Firma Microsoft podejmuje kroki w celu rozwiązania tego problemu w agencie połączonej maszyny, aby pomóc w ulepszaniu tego stanu. W międzyczasie ponowna próba instalacji może się powieść.

### <a name="log-analytics-agent-for-linux"></a>Agent Log Analytics dla systemu Linux

- Agent Log Analytics w wersji 1.13.9 (odpowiadająca wersja rozszerzenia to 1.13.15) nie oznacza prawidłowo znaku przekazane dane z IDENTYFIKATORem zasobu serwera z włączonym łukiem platformy Azure. Mimo że dzienniki są wysyłane do usługi, podczas próby wyświetlenia danych z wybranego dostępnego serwera po wybraniu **dzienników** lub **szczegółowych** informacji nie są zwracane żadne dane. Dane można wyświetlić, uruchamiając zapytania z dzienników Azure Monitor lub z Azure Monitor dla maszyn wirtualnych, które są objęte zakresem obszaru roboczego.

- Niektóre dystrybucje nie są obecnie obsługiwane przez agenta Log Analytics dla systemu Linux. Agent wymaga zainstalowania dodatkowych zależności, w tym języka Python 2. Przejrzyj macierz pomocy technicznej i wymagania wstępne w [tym miejscu](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- Kod błędu 52 w komunikacie o stanie wskazuje brakującą zależność. Sprawdź dane wyjściowe i dzienniki, aby uzyskać więcej informacji o braku zależności.

- Jeśli instalacja nie powiedzie się, zapoznaj się z sekcją **Rozwiązywanie problemów i pomocą techniczną** w temacie Omówienie rozszerzenia. W większości przypadków w komunikacie o stanie występuje kod błędu. W przypadku agenta Log Analytics dla systemu Linux w [tym miejscu](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)są wyjaśnione komunikaty o stanie, a także ogólne informacje dotyczące rozwiązywania problemów dla tego rozszerzenia maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [Microsoft Q&A](/answers/topics/azure-arc.html).

- Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.

- Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
