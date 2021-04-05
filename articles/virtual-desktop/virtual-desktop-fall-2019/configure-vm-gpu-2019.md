---
title: Konfigurowanie procesora GPU dla pulpitu wirtualnego systemu Windows (klasyczny) — Azure
description: Sposób włączania i kodowania procesora GPU w systemie Windows Virtual Desktop (klasyczny).
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: fa7b80b021e00d25dea4f96432ae922c15474058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023058"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Konfigurowanie przyspieszenia procesora graficznego (GPU) dla pulpitu wirtualnego systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../configure-vm-gpu.md).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w celu zwiększenia wydajności i skalowalności aplikacji. Przyspieszenie GPU jest szczególnie istotne dla aplikacji intensywnie korzystających z grafiki.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora GPU, dodać ją do puli hostów i skonfigurować do używania przyspieszenia procesora GPU na potrzeby renderowania i kodowania. W tym artykule przyjęto założenie, że masz już skonfigurowaną dzierżawę pulpitu wirtualnego systemu Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Wybieranie rozmiaru maszyny wirtualnej platformy Azure zoptymalizowanej pod kątem procesora GPU

Platforma Azure oferuje wiele [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../../virtual-machines/sizes-gpu.md). Wybór właściwy dla puli hostów zależy od wielu czynników, w tym konkretnych obciążeń aplikacji, odpowiedniej jakości środowiska użytkownika i kosztów. Ogólnie rzecz biorąc, większe i wydajniejsze procesory GPU oferują lepsze środowisko użytkownika w danej gęstości użytkownika.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Tworzenie puli hostów, Inicjowanie obsługi administracyjnej maszyny wirtualnej i Konfigurowanie grupy aplikacji

Utwórz nową pulę hostów przy użyciu maszyny wirtualnej o wybranym rozmiarze. Aby uzyskać instrukcje, zobacz [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](../create-host-pools-azure-marketplace.md).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w następujących systemach operacyjnych:

* Windows 10 w wersji 1511 lub nowszej
* Windows Server 2016 lub nowszy

Należy również skonfigurować grupę aplikacji lub użyć domyślnej grupy aplikacji pulpitu (o nazwie "aplikacja klasyczna"), która jest tworzona automatycznie podczas tworzenia nowej puli hostów. Aby uzyskać instrukcje, zobacz [Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](../manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instaluj obsługiwane sterowniki grafiki na maszynie wirtualnej

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N w systemie Windows, należy zainstalować odpowiednie sterowniki grafiki. Postępuj zgodnie z instrukcjami w obszarze [obsługiwane systemy operacyjne i sterowniki](../../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) , aby zainstalować sterowniki od odpowiedniego dostawcy grafiki, ręcznie lub przy użyciu rozszerzenia maszyny wirtualnej platformy Azure.

Dla pulpitu wirtualnego systemu Windows są obsługiwane tylko sterowniki dystrybuowane przez platformę Azure. Dodatkowo w przypadku maszyn wirtualnych platformy Azure z procesorami GPU w systemie Windows są obsługiwane tylko [sterowniki NVIDIA GRID](../../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers) .

Po zainstalowaniu sterownika wymagane jest ponowne uruchomienie maszyny wirtualnej. Wykonaj kroki weryfikacji opisane powyżej, aby potwierdzić, że sterowniki grafiki zostały pomyślnie zainstalowane.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurowanie renderowania aplikacji przyspieszonej przez procesor GPU

Domyślnie aplikacje i komputery stacjonarne działające w konfiguracjach wielosesyjnych są renderowane z użyciem procesora CPU i nie wykorzystują dostępnych procesorów GPU do renderowania. Skonfiguruj zasady grupy dla hosta sesji w celu włączenia renderowania przyspieszanego przez procesor GPU:

1. Połącz się z pulpitem maszyny wirtualnej przy użyciu konta z uprawnieniami administratora lokalnego.
2. Otwórz menu Start i wpisz "gpedit. msc", aby otworzyć Edytor zasady grupy.
3. Przejdź do węzła **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **środowisku sesji zdalnej** hosta sesji.
4. Wybierz pozycję zasady **Użyj domyślnej karty graficznej sprzętowej dla wszystkich sesji usługi pulpitu zdalnego** i **Ustaw dla tych** zasad włączenie renderowania procesora GPU w sesji zdalnej.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurowanie kodowania ramek z przyspieszeniem procesora GPU

Pulpit zdalny koduje wszystkie grafiki renderowane przez aplikacje i komputery stacjonarne (renderowane z procesorem GPU lub z procesorem CPU) do przesyłania do Pulpit zdalny klientów. Domyślnie Pulpit zdalny nie wykorzystuje dostępnych procesorów GPU dla tego kodowania. Skonfiguruj zasady grupy dla hosta sesji, aby umożliwić kodowanie ramek przez procesor GPU. Kontynuując powyższe kroki:

1. Wybierz pozycję zasady **ustalania priorytetów tryb grafiki h. 264/avc 444 dla połączeń pulpit zdalny** i ustaw te zasady tak **, aby** wymusić na koderze-dekoder H. 264/AVC 444 w sesji zdalnej.
2. Wybierz pozycję zasady **Konfiguruj kodowanie sprzętu H. 264/AVC dla połączeń pulpit zdalny** i ustaw te zasady na **włączone** , aby włączyć kodowanie sprzętu dla AVC/H. 264 w sesji zdalnej.

    >[!NOTE]
    >W systemie Windows Server 2016 ustaw opcję **Preferuj kodowanie sprzętu AVC** , aby **zawsze próbować**.

3. Teraz, gdy zasady grupy zostały edytowane, Wymuś aktualizację zasad grupy. Otwórz wiersz polecenia i wpisz:

    ```batch
    gpupdate.exe /force
    ```

4. Wyloguj się z sesji Pulpit zdalny.

## <a name="verify-gpu-accelerated-app-rendering"></a>Weryfikowanie renderowania aplikacji przyspieszonej przez procesor GPU

Aby sprawdzić, czy aplikacje używają procesora GPU do renderowania, spróbuj wykonać jedną z następujących czynności:

* W przypadku maszyn wirtualnych platformy Azure z procesorem GPU firmy NVIDIA Użyj `nvidia-smi` narzędzia zgodnie z opisem w temacie [Weryfikowanie instalacji sterowników](../../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) , aby sprawdzić użycie procesora GPU podczas uruchamiania aplikacji.
* W przypadku obsługiwanych wersji systemu operacyjnego można użyć Menedżera zadań do sprawdzenia użycia procesora GPU. Wybierz procesor GPU na karcie "Performance" (wydajność), aby sprawdzić, czy aplikacje korzystają z procesora GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Weryfikowanie przyspieszanego procesora GPU

Aby sprawdzić, czy Pulpit zdalny używa kodowania przyspieszanego przez procesor GPU:

1. Nawiąż połączenie z pulpitem maszyny wirtualnej przy użyciu klienta pulpitu wirtualnego systemu Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego węzła: **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operacyjnego**
3. Aby określić, czy jest używane kodowanie przyspieszone procesora GPU, poszukaj zdarzenia o IDENTYFIKATORze 170. Jeśli widzisz "koder sprzętowy AVC włączony: 1", używane jest kodowanie GPU.
4. Aby określić, czy jest używany tryb AVC 444, poszukaj zdarzenia o IDENTYFIKATORze 162. Jeśli widzisz wartość "dostępnego AVC: 1 początkowy profil: 2048", zostanie użyta wartość AVC 444.

## <a name="next-steps"></a>Następne kroki

Instrukcje te powinny obejmować Przyspieszenie GPU na jednym hoście sesji (jednej maszynie wirtualnej). Dodatkowe zagadnienia dotyczące włączania przyspieszenia procesora GPU w większej puli hostów:

* Rozważ użycie [rozszerzenia maszyny wirtualnej](../../virtual-machines/extensions/overview.md) w celu uproszczenia instalacji sterowników i aktualizacji na wielu maszynach wirtualnych. Użyj [rozszerzenia sterownika procesora GPU NVIDIA](../../virtual-machines/extensions/hpccompute-gpu-windows.md) dla maszyn wirtualnych z procesorami GPU firmy NVIDIA i Użyj rozszerzenia sterownika procesora GPU AMD dla maszyn wirtualnych z procesorami GPU AMD.
* Rozważ użycie zasady grupy Active Directory, aby uprościć konfigurację zasad grupy na wielu maszynach wirtualnych. Aby uzyskać informacje na temat wdrażania zasady grupy w domenie Active Directory, zobacz [Praca z obiektami zasady grupy](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).