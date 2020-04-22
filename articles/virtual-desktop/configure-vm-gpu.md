---
title: Konfigurowanie procesora graficznego dla pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak włączyć renderowanie i kodowanie z przyspieszeniem gpu na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767027"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurowanie akceleracji procesora graficznego (GPU) dla pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie z przyspieszeniem procesora graficznego, co zwiększa wydajność i skalowalność aplikacji. Akceleracja GPU jest szczególnie ważna w przypadku aplikacji intensywnie korzystających z grafiki.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora GPU, dodać ją do puli hostów i skonfigurować tak, aby używała akceleracji procesora GPU do renderowania i kodowania. W tym artykule założono, że masz już skonfigurowaną dzierżawę pulpitu wirtualnego systemu Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Wybieranie rozmiaru maszyny wirtualnej zoptymalizowanej pod kątem procesora graficznego platformy Azure

Platforma Azure oferuje szereg [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora graficznego.](/azure/virtual-machines/windows/sizes-gpu) Właściwy wybór dla puli hostów zależy od wielu czynników, w tym od obciążeń poszczególnych aplikacji, żądanej jakości środowiska użytkownika i kosztów. Ogólnie rzecz biorąc, większe i bardziej wydajne procesory graficzne oferują lepsze środowisko użytkownika w danej gęstości użytkownika.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Tworzenie puli hostów, aprowizowanie maszyny wirtualnej i konfigurowanie grupy aplikacji

Utwórz nową pulę hosta przy użyciu maszyny Wirtualnej o wybranym rozmiarze. Aby uzyskać instrukcje, zobacz [Samouczek: Tworzenie puli hostów w portalu Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Pulpit wirtualny systemu Windows obsługuje renderowanie i kodowanie z przyspieszeniem gpu w następujących systemach operacyjnych:

* Windows 10 w wersji 1511 lub nowszej
* Windows Server 2016 lub nowszy

Należy również skonfigurować grupę aplikacji lub użyć domyślnej grupy aplikacji klasycznych (o nazwie "Desktop Application Group"), która jest automatycznie tworzona podczas tworzenia nowej puli hostów. Aby uzyskać instrukcje, zobacz [Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalowanie obsługiwanych sterowników graficznych na maszynie wirtualnej

Aby korzystać z możliwości procesora GPU maszyn wirtualnych z serii N platformy Azure na pulpicie wirtualnym systemu Windows, należy zainstalować odpowiednie sterowniki graficzne. Postępuj zgodnie z instrukcjami w [obsługiwanych systemach operacyjnych i sterownikach,](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) aby zainstalować sterowniki od odpowiedniego dostawcy grafiki ręcznie lub przy użyciu rozszerzenia maszyny Wirtualnej platformy Azure.

Tylko sterowniki dystrybuowane przez platformę Azure są obsługiwane dla pulpitu wirtualnego systemu Windows. Ponadto w przypadku maszyn wirtualnych platformy Azure z procesorami graficznymi NVIDIA tylko [sterowniki NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) są obsługiwane dla pulpitu wirtualnego systemu Windows.

Po zainstalowaniu sterownika wymagane jest ponowne uruchomienie maszyny Wirtualnej. Użyj kroków weryfikacji podanych w powyższych instrukcjach, aby potwierdzić, że sterowniki graficzne zostały pomyślnie zainstalowane.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurowanie renderowania aplikacji z akcelerowanym procesorem GRAFICZNYm

Domyślnie aplikacje i komputery stacjonarne działające w konfiguracjach wielosesyjnych są renderowane za pomocą procesora CPU i nie wykorzystują dostępnych procesorów GPU do renderowania. Skonfiguruj zasady grupy dla hosta sesji, aby włączyć renderowanie przyspieszane przez procesor GPU:

1. Połącz się z pulpitem maszyny Wirtualnej przy użyciu konta z uprawnieniami administratora lokalnego.
2. Otwórz menu Start i wpisz "gpedit.msc", aby otworzyć Edytor zasad grupy.
3. Przejdź do drzewa do **konfiguracji** > komputera**Szablony administracyjne** > **Składniki Windows Składniki** > **Pulpitu zdalnego Usługi** > **pulpitu Zdalnego Hosta sesji Remote** > Session**Environment**.
4. Wybierz zasady **Użyj sprzętowej domyślnej karty graficznej dla wszystkich sesji usług pulpitu zdalnego** i ustaw tę zasadę **na Włączono,** aby włączyć renderowanie gpu w sesji zdalnej.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurowanie kodowania ramek z akcelerowaniem gpu

Pulpit zdalny koduje całą grafikę renderowaną przez aplikacje i pulpity (renderowane za pomocą procesora GPU lub procesora CPU) w celu przesyłania ich do klientów pulpitu zdalnego. Domyślnie pulpit zdalny nie wykorzystuje dostępnych procesorów GPU dla tego kodowania. Skonfiguruj zasady grupy dla hosta sesji, aby włączyć kodowanie ramek z akcelerowaniem procesora GPU. Kontynuując powyższe kroki:

1. Wybierz opcję **Priorytet trybu grafiki H.264/AVC 444 dla połączeń pulpitu zdalnego** i ustaw tę zasadę na **Włączone,** aby wymusić wymuszenie kodeka H.264/AVC 444 w sesji zdalnej.
2. Wybierz opcję **Konfiguruj kodowanie sprzętowe H.264/AVC dla połączeń pulpitu zdalnego** i ustaw tę zasadę **na Włączono,** aby włączyć kodowanie sprzętowe dla formatu AVC/H.264 w sesji zdalnej.

    >[!NOTE]
    >W systemie Windows Server 2016 ustaw opcję **Preferuj kodowanie sprzętowe AVC,** aby **zawsze próbować**.

3. Teraz, gdy zasady grupy zostały edytowane, wymuś aktualizację zasad grupy. Otwórz wiersz polecenia i wpisz:

    ```batch
    gpupdate.exe /force
    ```

4. Wyloguj się z sesji pulpitu zdalnego.

## <a name="verify-gpu-accelerated-app-rendering"></a>Weryfikowanie renderowania aplikacji z przyspieszeniem gpu

Aby sprawdzić, czy aplikacje używają procesora GPU do renderowania, spróbuj wykonać dowolną z następujących czynności:

* W przypadku maszyn wirtualnych platformy Azure z `nvidia-smi` procesorem graficznym NVIDIA użyj narzędzia opisanego w [obszarze Weryfikuj instalację sterownika,](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) aby sprawdzić, czy wykorzystanie procesora GPU podczas uruchamiania aplikacji.
* W obsługiwanych wersjach systemu operacyjnego można użyć Menedżera zadań, aby sprawdzić wykorzystanie procesora GPU. Wybierz procesor graficzny na karcie "Wydajność", aby sprawdzić, czy aplikacje wykorzystują procesor graficzny.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Sprawdź kodowanie ramek z akcelerowaniem gpu

Aby sprawdzić, czy pulpit zdalny używa kodowania przyspieszanych przez procesor GPU:

1. Połącz się z pulpitem maszyny Wirtualnej za pomocą klienta pulpitu wirtualnego systemu Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego **węzła: Aplikacje i usługi Loguje** > **program Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational**
3. Aby ustalić, czy używane jest kodowanie przyspieszane przez procesor GPU, poszukaj identyfikatora zdarzenia 170. Jeśli widzisz "Koder sprzętowy AVC włączony: 1", kodowanie GPU jest używane.
4. Aby ustalić, czy używany jest tryb AVC 444, poszukaj zdarzenia o identyfikatorze 162. Jeśli widzisz "AVC Dostępne: 1 Początkowy profil: 2048", a następnie AVC 444 jest używany.

## <a name="next-steps"></a>Następne kroki

Te instrukcje powinny być uruchomione z przyspieszeniem GPU na jednym hoście sesji (jednej maszynie wirtualnej). Niektóre dodatkowe zagadnienia dotyczące włączania akceleracji GPU w większej puli hostów:

* Należy rozważyć użycie [rozszerzenia maszyny Wirtualnej,](/azure/virtual-machines/extensions/overview) aby uprościć instalację sterownika i aktualizacje na wielu maszynach wirtualnych. Użyj [rozszerzenia sterownika gpu NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) dla maszyn wirtualnych z procesorami graficznymi NVIDIA i użyj rozszerzenia sterownika PROCESORA GRAFICZNEGO AMD (wkrótce) dla maszyn wirtualnych z procesorami graficznymi AMD.
* Rozważ użycie zasad grupy usługi Active Directory w celu uproszczenia konfiguracji zasad grupy na wielu maszynach wirtualnych. Aby uzyskać informacje dotyczące wdrażania zasad grupy w domenie usługi Active Directory, zobacz [Praca z obiektami zasad grupy](https://go.microsoft.com/fwlink/p/?LinkId=620889).
