---
title: Konfigurowanie procesora GPU dla pulpitu wirtualnego systemu Windows — Azure
description: Jak włączyć procesor GPU w szybszym wyrenderowaniu i kodowaniu na pulpicie wirtualnym systemu Windows.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 33b8d3f62ef45c6078f10535c6376f611472f5a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441752"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurowanie przyspieszania procesora graficznego (GPU) dla usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w celu zwiększenia wydajności i skalowalności aplikacji. Przyspieszenie GPU jest szczególnie istotne dla aplikacji intensywnie korzystających z grafiki.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora GPU, dodać ją do puli hostów i skonfigurować do używania przyspieszenia procesora GPU na potrzeby renderowania i kodowania. W tym artykule przyjęto założenie, że masz już skonfigurowaną dzierżawę pulpitu wirtualnego systemu Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Wybieranie rozmiaru maszyny wirtualnej platformy Azure zoptymalizowanej pod kątem procesora GPU

Platforma Azure oferuje wiele [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](/azure/virtual-machines/windows/sizes-gpu). Wybór właściwy dla puli hostów zależy od wielu czynników, w tym konkretnych obciążeń aplikacji, odpowiedniej jakości środowiska użytkownika i kosztów. Ogólnie rzecz biorąc, większe i wydajniejsze procesory GPU oferują lepsze środowisko użytkownika w danej gęstości użytkownika.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Tworzenie puli hostów, Inicjowanie obsługi administracyjnej maszyny wirtualnej i Konfigurowanie grupy aplikacji

Utwórz nową pulę hostów przy użyciu maszyny wirtualnej o wybranym rozmiarze. Aby uzyskać instrukcje, zobacz [Samouczek: Tworzenie puli hostów przy użyciu Azure Portal](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie procesora GPU w następujących systemach operacyjnych:

* Windows 10 w wersji 1511 lub nowszej
* Windows Server 2016 lub nowszy

Należy również skonfigurować grupę aplikacji lub użyć domyślnej grupy aplikacji pulpitu (o nazwie "aplikacja klasyczna"), która jest tworzona automatycznie podczas tworzenia nowej puli hostów. Aby uzyskać instrukcje, zobacz [Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instaluj obsługiwane sterowniki grafiki na maszynie wirtualnej

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N w systemie Windows, należy zainstalować odpowiednie sterowniki grafiki. Postępuj zgodnie z instrukcjami w obszarze [obsługiwane systemy operacyjne i sterowniki](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) , aby zainstalować sterowniki od odpowiedniego dostawcy grafiki, ręcznie lub przy użyciu rozszerzenia maszyny wirtualnej platformy Azure.

Dla pulpitu wirtualnego systemu Windows są obsługiwane tylko sterowniki dystrybuowane przez platformę Azure. Ponadto w przypadku maszyn wirtualnych platformy Azure z procesorami GPU są obsługiwane tylko [Sterowniki sieci NVIDIA](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) dla systemu Windows.

Po zainstalowaniu sterownika wymagane jest ponowne uruchomienie maszyny wirtualnej. Wykonaj kroki weryfikacji opisane powyżej, aby potwierdzić, że sterowniki grafiki zostały pomyślnie zainstalowane.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurowanie renderowania aplikacji przyspieszonej przez procesor GPU

Domyślnie aplikacje i komputery stacjonarne działające w konfiguracjach wielosesyjnych są renderowane z użyciem procesora CPU i nie wykorzystują dostępnych procesorów GPU do renderowania. Skonfiguruj zasady grupy dla hosta sesji w celu włączenia renderowania przyspieszanego przez procesor GPU:

1. Połącz się z pulpitem maszyny wirtualnej przy użyciu konta z uprawnieniami administratora lokalnego.
2. Otwórz menu Start i wpisz "gpedit. msc", aby otworzyć Edytor zasady grupy.
3. Przejdź do węzła **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **środowisku sesji zdalnej**hosta sesji.
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

* W przypadku maszyn wirtualnych platformy Azure z procesorem GPU NVIDIA Użyj `nvidia-smi` narzędzia zgodnie z opisem w temacie [Weryfikowanie instalacji sterowników](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) , aby sprawdzić użycie procesora GPU podczas uruchamiania aplikacji.
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

* Rozważ użycie [rozszerzenia maszyny wirtualnej](/azure/virtual-machines/extensions/overview) w celu uproszczenia instalacji sterowników i aktualizacji na wielu maszynach wirtualnych. Użyj [rozszerzenia sterownika procesora GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) dla maszyn wirtualnych z procesorami GPU firmy NVIDIA i Użyj [rozszerzenia sterownika procesora GPU AMD](/azure/virtual-machines/extensions/hpccompute-amd-gpu-windows) dla maszyn wirtualnych z procesorami GPU AMD.
* Rozważ użycie zasady grupy Active Directory, aby uprościć konfigurację zasad grupy na wielu maszynach wirtualnych. Aby uzyskać informacje na temat wdrażania zasady grupy w domenie Active Directory, zobacz [Praca z obiektami zasady grupy](https://go.microsoft.com/fwlink/p/?LinkId=620889).
