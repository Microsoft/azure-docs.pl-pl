---
title: Konfigurowanie procesora GPU dla pulpitu wirtualnego systemu Windows — Azure
description: Jak włączyć procesor GPU w szybszym wyrenderowaniu i kodowaniu na pulpicie wirtualnym systemu Windows.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709467"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurowanie przyspieszania procesora graficznego (GPU) dla usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w celu zwiększenia wydajności i skalowalności aplikacji. Przyspieszenie GPU jest szczególnie istotne dla aplikacji intensywnie korzystających z grafiki.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora GPU, dodać ją do puli hostów i skonfigurować do używania przyspieszenia procesora GPU na potrzeby renderowania i kodowania. W tym artykule przyjęto założenie, że masz już skonfigurowaną dzierżawę pulpitu wirtualnego systemu Windows.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Wybierz odpowiedni rozmiar maszyny wirtualnej platformy Azure zoptymalizowany pod kątem procesora GPU

Wybierz jedną z rozmiarów maszyn wirtualnych [z serii](../virtual-machines/nv-series.md) [NVv3](../virtual-machines/nvv3-series.md)lub [NVv4](../virtual-machines/nvv4-series.md) . Są one dostosowane do wirtualizacji aplikacji i pulpitu oraz umożliwiają przyspieszenie większości aplikacji i interfejsu użytkownika systemu Windows. Wybór właściwy dla puli hostów zależy od wielu czynników, w tym konkretnych obciążeń aplikacji, odpowiedniej jakości środowiska użytkownika i kosztów. Ogólnie rzecz biorąc, większe i wydajniejsze procesory GPU oferują lepsze środowisko użytkownika w danej gęstości użytkownika, podczas gdy mniejsze i ułamkowe rozmiary procesora GPU umożliwiają dokładniejszą kontrolę nad kosztami i jakością.

>[!NOTE]
>Maszyny wirtualne z serii NC, NCv2, Seria NCV3, ND i NDv2 na platformie Azure zwykle nie są odpowiednie dla hostów sesji usług pulpitu wirtualnego systemu Windows. Te maszyny wirtualne są dostosowane do wyspecjalizowanych, wysoko wydajnych narzędzi obliczeniowych lub uczenia maszynowego, takich jak te utworzone za pomocą technologii NVIDIA CUDA. Nie obsługują przyspieszenia procesora GPU dla większości aplikacji lub interfejsu użytkownika systemu Windows.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Tworzenie puli hostów, Inicjowanie obsługi administracyjnej maszyny wirtualnej i Konfigurowanie grupy aplikacji

Utwórz nową pulę hostów przy użyciu maszyny wirtualnej o wybranym rozmiarze. Aby uzyskać instrukcje, zobacz [Samouczek: Tworzenie puli hostów przy użyciu Azure Portal](./create-host-pools-azure-marketplace.md).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w następujących systemach operacyjnych:

* Windows 10 w wersji 1511 lub nowszej
* Windows Server 2016 lub nowszy

Należy również skonfigurować grupę aplikacji lub użyć domyślnej grupy aplikacji pulpitu (o nazwie "aplikacja klasyczna"), która jest tworzona automatycznie podczas tworzenia nowej puli hostów. Aby uzyskać instrukcje, zobacz [Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instaluj obsługiwane sterowniki grafiki na maszynie wirtualnej

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N w systemie Windows, należy zainstalować odpowiednie sterowniki grafiki. Postępuj zgodnie z instrukcjami w obszarze [obsługiwane systemy operacyjne i sterowniki,](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) aby zainstalować sterowniki. Obsługiwane są tylko sterowniki dystrybuowane przez platformę Azure.

* W przypadku maszyn wirtualnych z serii NV lub serii NVv3, tylko sterowników NVIDIA GRID, a nie sterowników NVIDIA CUDA, obsługa przyspieszania GPU dla większości aplikacji i interfejsu użytkownika systemu Windows. W przypadku wybrania opcji ręcznego instalowania sterowników należy zainstalować sterowniki siatki. Jeśli zdecydujesz się zainstalować sterowniki przy użyciu rozszerzenia maszyny wirtualnej platformy Azure, Sterowniki siatki zostaną automatycznie zainstalowane dla tych rozmiarów maszyn wirtualnych.
* W przypadku maszyn wirtualnych z serii Azure NVv4 Zainstaluj sterowniki AMD dostarczone przez platformę Azure. Można je zainstalować automatycznie przy użyciu rozszerzenia maszyny wirtualnej platformy Azure lub można zainstalować je ręcznie.

Po zainstalowaniu sterownika wymagane jest ponowne uruchomienie maszyny wirtualnej. Wykonaj kroki weryfikacji opisane powyżej, aby potwierdzić, że sterowniki grafiki zostały pomyślnie zainstalowane.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurowanie renderowania aplikacji przyspieszonej przez procesor GPU

Domyślnie aplikacje i komputery stacjonarne działające w konfiguracjach wielosesyjnych są renderowane z użyciem procesora CPU i nie wykorzystują dostępnych procesorów GPU do renderowania. Skonfiguruj zasady grupy dla hosta sesji w celu włączenia renderowania przyspieszanego przez procesor GPU:

1. Połącz się z pulpitem maszyny wirtualnej przy użyciu konta z uprawnieniami administratora lokalnego.
2. Otwórz menu Start i wpisz "gpedit. msc", aby otworzyć Edytor zasady grupy.
3. Przejdź do węzła **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **środowisku sesji zdalnej** hosta sesji.
4. Wybierz pozycję zasady **Użyj sprzętowych kart graficznych dla wszystkich sesji usługi pulpitu zdalnego** i **Ustaw dla tych zasad włączenie renderowania** procesora GPU w sesji zdalnej.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurowanie kodowania ramek z przyspieszeniem procesora GPU

Pulpit zdalny koduje wszystkie grafiki renderowane przez aplikacje i komputery stacjonarne (renderowane z procesorem GPU lub z procesorem CPU) do przesyłania do Pulpit zdalny klientów. Gdy część ekranu jest często aktualizowana, ta część ekranu jest zaszyfrowana przy użyciu kodera-dekoder wideo (H. 264/AVC). Domyślnie Pulpit zdalny nie wykorzystuje dostępnych procesorów GPU dla tego kodowania. Skonfiguruj zasady grupy dla hosta sesji, aby umożliwić kodowanie ramek przez procesor GPU. Kontynuując powyższe kroki:

>[!NOTE]
>Przyspieszenie procesora GPU nie jest dostępne na maszynach wirtualnych z serii NVv4.

1. Wybierz pozycję zasady **Konfiguruj kodowanie sprzętu H. 264/AVC dla połączeń pulpit zdalny** i ustaw te zasady na **włączone** , aby włączyć kodowanie sprzętu dla AVC/H. 264 w sesji zdalnej.

    >[!NOTE]
    >W systemie Windows Server 2016 ustaw opcję **Preferuj kodowanie sprzętu AVC** , aby **zawsze próbować**.

2. Teraz, gdy zasady grupy zostały edytowane, Wymuś aktualizację zasad grupy. Otwórz wiersz polecenia i wpisz:

    ```cmd
    gpupdate.exe /force
    ```

3. Wyloguj się z sesji Pulpit zdalny.

## <a name="configure-fullscreen-video-encoding"></a>Konfigurowanie pełnoekranowego kodowania wideo

Jeśli często używasz aplikacji, które generują zawartość o wysokiej rozdzielczości, taką jak modelowanie 3W, programy CAD i wideo, możesz włączyć pełnoekranowe kodowanie wideo dla sesji zdalnej. Pełny profil wideo zapewnia wyższą szybkość klatek i lepszy komfort korzystania z takich aplikacji na koszt przepustowości sieci oraz zasobów hosta sesji i klienta. Zaleca się używanie przyspieszania procesora GPU dla kodowania wideo w trybie pełnoekranowym. Skonfiguruj zasady grupy dla hosta sesji, aby włączyć pełnoekranowe kodowanie wideo. Kontynuując powyższe kroki:

1. Wybierz pozycję zasady **ustalania priorytetów tryb grafiki h. 264/avc 444 dla połączeń pulpit zdalny** i ustaw te zasady tak **, aby** wymusić na koderze-dekoder H. 264/AVC 444 w sesji zdalnej.
2. Teraz, gdy zasady grupy zostały edytowane, Wymuś aktualizację zasad grupy. Otwórz wiersz polecenia i wpisz:

    ```cmd
    gpupdate.exe /force
    ```

3. Wyloguj się z sesji Pulpit zdalny.
## <a name="verify-gpu-accelerated-app-rendering"></a>Weryfikowanie renderowania aplikacji przyspieszonej przez procesor GPU

Aby sprawdzić, czy aplikacje używają procesora GPU do renderowania, spróbuj wykonać jedną z następujących czynności:

* W przypadku maszyn wirtualnych platformy Azure z procesorem GPU NVIDIA Użyj `nvidia-smi` narzędzia zgodnie z opisem w temacie [Weryfikowanie instalacji sterowników](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) , aby sprawdzić użycie procesora GPU podczas uruchamiania aplikacji.
* W przypadku obsługiwanych wersji systemu operacyjnego można użyć Menedżera zadań do sprawdzenia użycia procesora GPU. Wybierz procesor GPU na karcie "Performance" (wydajność), aby sprawdzić, czy aplikacje korzystają z procesora GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Weryfikowanie przyspieszanego procesora GPU

Aby sprawdzić, czy Pulpit zdalny używa kodowania przyspieszanego przez procesor GPU:

1. Nawiąż połączenie z pulpitem maszyny wirtualnej przy użyciu klienta pulpitu wirtualnego systemu Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego węzła: **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operacyjnego**
3. Aby określić, czy jest używane kodowanie przyspieszone procesora GPU, poszukaj zdarzenia o IDENTYFIKATORze 170. Jeśli widzisz "koder sprzętowy AVC włączony: 1", używane jest kodowanie GPU.

## <a name="verify-fullscreen-video-encoding"></a>Weryfikuj pełnoekranowe kodowanie wideo

Aby sprawdzić, czy Pulpit zdalny korzysta z pełnoekranowego kodowania wideo:

1. Nawiąż połączenie z pulpitem maszyny wirtualnej przy użyciu klienta pulpitu wirtualnego systemu Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego węzła: **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operacyjnego**
3. Aby określić, czy jest używane pełnoekranowe kodowanie wideo, poszukaj zdarzenia o IDENTYFIKATORze 162. Jeśli widzisz wartość "dostępnego AVC: 1 początkowy profil: 2048", zostanie użyta wartość AVC 444.

## <a name="next-steps"></a>Następne kroki

Instrukcje te powinny obejmować Przyspieszenie GPU na jednym hoście sesji (jednej maszynie wirtualnej). Dodatkowe zagadnienia dotyczące włączania przyspieszenia procesora GPU w większej puli hostów:

* Rozważ użycie [rozszerzenia maszyny wirtualnej](../virtual-machines/extensions/overview.md) w celu uproszczenia instalacji sterowników i aktualizacji na wielu maszynach wirtualnych. Użyj [rozszerzenia sterownika procesora GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-windows.md) dla maszyn wirtualnych z procesorami GPU firmy NVIDIA i Użyj [rozszerzenia sterownika procesora GPU AMD](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) dla maszyn wirtualnych z procesorami GPU AMD.
* Rozważ użycie zasady grupy Active Directory, aby uprościć konfigurację zasad grupy na wielu maszynach wirtualnych. Aby uzyskać informacje na temat wdrażania zasady grupy w domenie Active Directory, zobacz [Praca z obiektami zasady grupy](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).