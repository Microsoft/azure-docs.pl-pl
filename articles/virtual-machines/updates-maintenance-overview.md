---
title: Omówienie aktualizacji i konserwacji
description: Informacje na temat aktualizacji i opcji konserwacji dostępnych w przypadku maszyn wirtualnych na platformie Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022297"
---
# <a name="updates-and-maintenance-overview"></a>Omówienie aktualizacji i konserwacji
Ten artykuł zawiera omówienie różnych opcji aktualizacji i konserwacji dla maszyn wirtualnych platformy Azure.

## <a name="automatic-os-image-upgrade"></a>Automatyczne uaktualnianie obrazu systemu operacyjnego

Włączenie [automatycznych uaktualnień obrazu systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) w zestawie skalowania ułatwia zarządzanie aktualizacjami, bezpiecznie i automatycznie uaktualnia dysk systemu operacyjnego dla wszystkich wystąpień w zestawie skalowania.

[Automatyczne uaktualnianie systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) ma następującą charakterystykę:

- Po skonfigurowaniu najnowszy obraz systemu operacyjnego Opublikowany przez wydawców obrazów jest automatycznie stosowany do zestawu skalowania bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób ciągły za każdym razem, gdy nowy obraz jest publikowany przez wydawcę.
- Integruje się z sondami kondycji aplikacji i [rozszerzeniem kondycji aplikacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz obrazów systemów Windows i Linux.
- Możesz zrezygnować z automatycznych uaktualnień w dowolnym momencie (uaktualnienia systemu operacyjnego można także zainicjować ręcznie).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowym dyskiem systemu operacyjnego utworzonym przy użyciu najnowszej wersji obrazu. Skonfigurowane rozszerzenia i niestandardowe skrypty danych są uruchamiane, podczas gdy utrwalane dyski danych są zachowywane.
- [Sekwencjonowanie rozszerzeń](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) jest obsługiwane.
- Automatyczne uaktualnianie obrazu systemu operacyjnego można włączyć w zestawie skalowania o dowolnym rozmiarze.


## <a name="automatic-vm-guest-patching-preview"></a>Automatyczna poprawka gościa maszyny wirtualnej (wersja zapoznawcza)

Włączenie [automatycznej poprawki gościa maszyny wirtualnej](automatic-vm-guest-patching.md) dla maszyn wirtualnych platformy Azure ułatwia zarządzanie aktualizacjami przez bezpieczne i automatyczne poprawianie maszyn wirtualnych w celu zachowania zgodności z zabezpieczeniami.

[Automatyczna poprawka gościa maszyny wirtualnej](automatic-vm-guest-patching.md) ma następującą charakterystykę:
- Poprawki sklasyfikowane jako *krytyczne* lub *zabezpieczenia* są automatycznie pobierane i stosowane na maszynie wirtualnej.
- Poprawki są stosowane w godzinach poza godzinami szczytu w strefie czasowej maszyny wirtualnej.
- Aranżacja poprawek jest zarządzana przez platformę Azure, a poprawki są stosowane po [pierwszej zasadzie dostępności](automatic-vm-guest-patching.md#availability-first-patching).
- Kondycja maszyny wirtualnej określona za pośrednictwem sygnałów kondycji platformy jest monitorowana w celu wykrywania niepowodzeń poprawek.
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych.


## <a name="automatic-extension-upgrade-preview"></a>Automatyczne uaktualnianie rozszerzeń (wersja zapoznawcza)

[Automatyczne uaktualnianie rozszerzeń](automatic-extension-upgrade.md) jest dostępne w wersji zapoznawczej dla maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Jeśli automatyczne uaktualnianie rozszerzeń jest włączone dla maszyny wirtualnej lub zestawu skalowania, rozszerzenie zostanie uaktualnione automatycznie za każdym razem, gdy Wydawca rozszerzenia zwolni nową wersję dla tego rozszerzenia.

 Automatyczne uaktualnianie rozszerzeń ma następujące funkcje:
- Obsługiwane w przypadku maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Virtual Machine Scale Sets Service Fabric nie są obecnie obsługiwane.
- Uaktualnienia są stosowane w modelu wdrożenia pierwszego dostępności.
- W przypadku zestawu skalowania maszyn wirtualnych nie można uaktualnić więcej niż 20% maszyn wirtualnych zestawu skalowania w jednej partii. Minimalny rozmiar wsadu to jedna maszyna wirtualna.
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz dla rozszerzeń systemu Windows i Linux.
- W dowolnym momencie możesz zrezygnować z automatycznych uaktualnień.
- Automatyczne uaktualnianie rozszerzeń można włączyć na Virtual Machine Scale Sets dowolnego rozmiaru.
- Każde obsługiwane rozszerzenie jest rejestrowane osobno i można wybrać rozszerzenia, które mają zostać uaktualnione automatycznie.
- Obsługiwane we wszystkich regionach chmury publicznej.

## <a name="hotpatch"></a>Hotpatch 

Funkcja [HotPatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) to nowy sposób instalowania aktualizacji na nowych maszynach wirtualnych systemu Windows Server Azure Edition (VM), które nie wymagają ponownego uruchomienia po instalacji. Hotpatch dla maszyn wirtualnych z systemem Windows Server Azure Edition ma następujące zalety:

- Dolny wpływ na obciążenie z mniejszą ilością ponownych uruchomień
- Szybsze wdrażanie aktualizacji w miarę mniejszych pakietów, instalowanie szybszych i łatwiejsze organizowanie poprawek za pomocą usługi Azure Update Manager
- Lepsza ochrona, ponieważ pakiety aktualizacji hotpatch są objęte zakresem aktualizacji zabezpieczeń systemu Windows, które instalują się szybciej bez ponownego uruchamiania


## <a name="azure-update-management"></a>Zarządzanie aktualizacjami platformy Azure

[Update Management w Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych z systemami Windows i Linux na platformie Azure, w środowiskach lokalnych i w innych środowiskach w chmurze. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów.

## <a name="maintenance-control"></a>Sterowanie konserwacją

Zarządzaj aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu funkcji [kontroli konserwacji](maintenance-control.md). Platforma Azure często aktualizuje swoją infrastrukturę w celu poprawy niezawodności, wydajności, zabezpieczeń lub uruchamiania nowych funkcji. Większość aktualizacji jest niewidoczna dla użytkowników. Niektóre wrażliwe obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie mogą tolerować nawet kilku sekund zamarzania maszyny wirtualnej lub odłączenia jej do konserwacji. Sterowanie konserwacją umożliwia zaczekanie na aktualizacje platformy i stosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure.

Za pomocą [kontrolki konserwacji](maintenance-control.md)można:
- Aktualizacje wsadowe w jednym pakiecie aktualizacji.
- Zaczekaj do 35 dni, aby zastosować aktualizacje. 
- Automatyzowanie aktualizacji platformy przez skonfigurowanie harmonogramu konserwacji lub przy użyciu [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Konfiguracje konserwacji działają w ramach subskrypcji i grup zasobów. 


## <a name="scheduled-events"></a>Zaplanowane zdarzenia

Scheduled Events to Metadata Service platformy Azure, który umożliwia aplikacji przygotowanie czasu do konserwacji maszyn wirtualnych. Zawiera informacje dotyczące nadchodzących zdarzeń konserwacyjnych (na przykład ponownego uruchomienia), aby aplikacja mogła je przygotować i ograniczyć zakłócenia. Jest ona dostępna dla wszystkich typów Virtual Machines platformy Azure, w tym PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje na temat Scheduled Events, zobacz [Scheduled Events maszyn wirtualnych z systemem Windows](./windows/scheduled-events.md) i [Scheduled Events dla systemu Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją dotyczącą [dostępności i skalowania](availability.md) , aby dowiedzieć się więcej o tym, jak zwiększyć czas pracy aplikacji i usług. 