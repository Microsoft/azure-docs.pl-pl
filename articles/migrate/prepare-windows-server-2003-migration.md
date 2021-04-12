---
title: Przygotowywanie serwerów z systemem Windows Server 2003 do migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować serwery z systemem Windows Server 2003 do migracji przy użyciu Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753755"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Przygotowywanie maszyn z systemem Windows Server 2003 do migracji

W tym artykule opisano sposób przygotowania maszyn z systemem Windows Server 2003 do migracji na platformę Azure. 


> [!NOTE]
> [Rozszerzona pomoc techniczna systemu Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) zakończyła się 14 lipca 2015.  Zespół pomocy technicznej systemu Azure nadal pomaga w rozwiązywaniu problemów związanych z uruchamianiem systemu Windows Server 2003 na platformie Azure. Ta obsługa jest jednak ograniczona do problemów, które nie wymagają rozwiązywania problemów lub poprawek na poziomie systemu operacyjnego. Zalecanym podejściem do migrowania aplikacji do wystąpień platformy Azure z nowszą wersją systemu Windows Server jest zapewnienie elastyczności i niezawodności chmury platformy Azure. Jeśli jednak nadal zdecydujesz się na migrację systemu Windows Server 2003 do platformy Azure, możesz użyć narzędzia do migracji serwera Azure Migrate, jeśli serwer systemu Windows jest maszyną wirtualną działającą w oprogramowaniu VMware lub funkcji Hyper-V.


- Do migracji [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) i [maszyn wirtualnych VMware](tutorial-migrate-vmware.md) na platformę Azure można użyć migracji bez agentów.
- Aby można było nawiązać połączenie z maszynami wirtualnymi platformy Azure po migracji, należy zainstalować usługi integracji funkcji Hyper-V na maszynie wirtualnej platformy Azure. Komputery z systemem Windows Server 2003 nie mają domyślnie zainstalowane.
- Nie ma bezpośredniego łącza do pobrania w celu zainstalowania usług integracji funkcji Hyper-V, więc należy wykonać następujące czynności:
    - W przypadku maszyn wirtualnych funkcji Hyper-V, które nie są zainstalowane, pliki instalacyjne usług Integration Services można wyodrębnić na komputerze z systemem Windows Server 2012 R2/Windows Server 2012 z rolą funkcji Hyper-V, a następnie skopiować Instalatora na komputer z systemem Windows Server 2003. Pliki instalacyjne nie są dostępne na komputerach z systemem Windows Server 2016.
    - W przypadku maszyn wirtualnych VMware można utworzyć zadanie uruchamiania, które instaluje usługi integracji po uruchomieniu maszyny wirtualnej platformy Azure po migracji.


## <a name="install-on-hyper-v-vms"></a>Instalowanie na maszynach wirtualnych funkcji Hyper-V

Przed rozpoczęciem migracji Sprawdź, czy usługi integracji funkcji Hyper-V są zainstalowane, a następnie zainstaluj je w razie konieczności.

1. Postępuj zgodnie z [tymi instrukcjami](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) , aby sprawdzić, czy jest zainstalowana.
2. Jeśli nie jest zainstalowana, zaloguj się do maszyny z systemem Windows Server 2012 R2/Windows Server 2012 z rolą funkcji Hyper-V.
3. Przejdź do pliku instalacyjnego w lokalizacji **C:\Windows\System32\vmguest.ISO** i Zainstaluj plik.
2. Skopiuj folder instalacyjny na komputer z systemem Windows Server 2003 i zainstaluj usługi integracji.
4. Po zakończeniu instalacji można pozostawić domyślne ustawienia w usługach Integration Services. 

## <a name="install-on-vmware-vms"></a>Zainstaluj na maszynach wirtualnych VMware

1. Zaloguj się do maszyny z systemem Windows Server 2012 R2/Windows Server 2012 z rolą funkcji Hyper-V.
2. Przejdź do pliku instalacyjnego w lokalizacji **C:\Windows\System32\vmguest.ISO** i Zainstaluj plik.
3. Skopiuj folder instalacyjny na maszynę wirtualną programu VMware.
4. W wierszu polecenia na maszynie wirtualnej Uruchom polecenie ```gpedit.msc``` .
5. Otwórz **konfigurację komputera**  >  skrypty **ustawień systemu Windows**  >  **(uruchamianie/zamykanie)**.
6. W polu **Start**  >  **Dodaj**  >  **nazwę skryptu** wpisz adres setup.exe.
7. Po migracji na platformę Azure skrypt jest uruchamiany podczas pierwszego uruchomienia maszyny wirtualnej platformy Azure.
8. Ręcznie uruchom ponownie maszynę wirtualną platformy Azure. W celu wskazania, że wymagane jest ponowne uruchomienie, znajduje się okno podręczne diagnostyki rozruchu.
9. Po uruchomieniu skryptu i usług integracji funkcji Hyper-V na maszynie wirtualnej platformy Azure można usunąć skrypt z uruchamiania.
10. Po zakończeniu instalacji można pozostawić domyślne ustawienia w usługach Integration Services. 

## <a name="next-steps"></a>Następne kroki

- Przejrzyj wymagania dotyczące migracji maszyn wirtualnych [VMware](migrate-support-matrix-vmware-migration.md) i [funkcji Hyper-V](migrate-support-matrix-hyper-v-migration.md) .
- Migrowanie maszyn wirtualnych [VMware](server-migrate-overview.md) i [funkcji Hyper-V](tutorial-migrate-hyper-v.md) .
