---
title: Co nowego w agencie serwerów z włączonym usługą Azure Arc
description: W tym artykule znajdują się informacje o wersji agenta usługi Azure ARC z obsługą serwerów. W przypadku wielu problemów podsumowujących istnieją linki do dodatkowych szczegółów.
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 3a01113d0d6416fb050f5c66191d5c420b7ac137
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505651"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Co nowego w agencie serwerów z włączonym usługą Azure Arc

Agent połączonej maszyny z włączonym usługą Azure Arc odbiera usprawnienia w sposób ciągły. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="new-feature"></a>Nowa funkcja

Dodano obsługę systemu Windows Server 2008 R2

### <a name="fixed"></a>Stałe

Rozwiązano problem uniemożliwiający pomyślne zainstalowanie niestandardowego rozszerzenia skryptu w systemie Linux.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="fixed"></a>Stałe

Rozwiązano problem polegający na tym, że można utracić konfigurację serwera proxy po uaktualnieniu do dystrybucji opartych na RPM.

## <a name="october-2020"></a>Październik 2020 r.

### <a name="fixed"></a>Stałe

- Stały skrypt serwera proxy obsługujący alternatywną lokalizację pliku jednostki demona GC.
- Zmiany niezawodności agenta GuestConfig.
- Obsługa agenta GuestConfig dla regionu US Gov Wirginia.
- Komunikaty o rozszerzeniu agenta GuestConfig w celu uzyskania bardziej szczegółowych informacji w przypadku awarii.

## <a name="september-2020"></a>Wrzesień 2020

Wersja: 1,0 (ogólna dostępność)

### <a name="plan-for-change"></a>Planowanie zmian

- Obsługa agentów w wersji zapoznawczej (wszystkie wersje starsze niż 1,0) zostaną usunięte w przyszłej aktualizacji usługi.
- Usunięto obsługę rezerwowego punktu końcowego `.azure-automation.net` . Jeśli masz serwer proxy, musisz zezwolić na punkt końcowy `*.his.arc.azure.com` .
- Jeśli Agent połączonej maszyny jest zainstalowany na maszynie wirtualnej hostowanej na platformie Azure, nie można zainstalować ani zmodyfikować rozszerzeń maszyny wirtualnej z poziomu zasobu z włączonymi Łukiemmi serwerami. Ma to na celu uniknięcie konfliktu operacji rozszerzenia wykonywanych z zasobów **Microsoft. COMPUTE** i **Microsoft. HybridCompute** maszyny wirtualnej. Użyj zasobu **Microsoft. COMPUTE** dla maszyny dla wszystkich operacji rozszerzenia.
- Zmieniono nazwę procesu konfiguracji gościa, od *GCD* do *Gcad* w systemie Linux i *Gcservice* do *gcarcservice* w systemie Windows.

### <a name="new-feature"></a>Nowa funkcja

- Dodano `azcmagent logs` opcję zbierania informacji o pomocy technicznej.
- Dodano `azcmagent license` opcję wyświetlania umowy EULA.
- Dodano `azcmagent show --json` opcję do stanu agenta wyjściowego w łatwym do przeanalizowaniu formacie.
- Dodano flagę w `azcmagent show` danych wyjściowych, aby wskazać, czy serwer znajduje się na maszynie wirtualnej hostowanej na platformie Azure.
- Dodano `azcmagent disconnect --force-local-only` opcję umożliwiającą resetowanie stanu lokalnego agenta, gdy nie można nawiązać połączenia z usługą platformy Azure.
- Dodano `azcmagent connect --cloud` opcję obsługi dodatkowych chmur. W tej wersji tylko platforma Azure jest obsługiwana przez usługę w momencie wydania agenta.
- Agent został zlokalizowany w językach obsługiwanych przez platformę Azure.

### <a name="fixed"></a>Stałe

- Ulepszenia kontroli łączności.
- Rozwiązano problem z utratam ustawień serwera proxy podczas uaktualniania agenta w systemie Linux.
- Rozwiązano problemy podczas próby zainstalowania agenta na serwerze z systemem Windows Server 2012 R2.
- Ulepszenia niezawodności instalacji rozszerzenia

## <a name="august-2020"></a>Sierpień 2020 r.

Wersja: 0,11

- W tej wersji ogłoszono wcześniej obsługę Ubuntu 20,04. Ponieważ niektóre rozszerzenia maszyny wirtualnej platformy Azure nie obsługują Ubuntu 20,04, jest usuwana obsługa tej wersji Ubuntu.

- Ulepszenia niezawodności wdrożeń rozszerzeń.

### <a name="known-issues"></a>Znane problemy

Jeśli używasz starszej wersji agenta systemu Linux i jest on skonfigurowany do korzystania z serwera proxy, należy ponownie skonfigurować ustawienie serwera proxy po uaktualnieniu. Aby to zrobić, uruchom `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Następne kroki

Przed przeszacowaniem lub włączeniem serwerów z obsługą łuku na wielu maszynach hybrydowych Przejrzyj [Omówienie agenta połączonej maszyny](agent-overview.md) , aby poznać wymagania, szczegóły techniczne dotyczące agenta i metod wdrażania.