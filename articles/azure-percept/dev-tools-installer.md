---
title: Omówienie Instalatora pakietu narzędzi deweloperskich platformy Azure Percept
description: Dowiedz się więcej o korzystaniu z Instalatora pakietu narzędzi deweloperskich w celu przyspieszenia zaawansowanego programowania za pomocą usługi Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608599"
---
# <a name="dev-tools-pack-installer-overview"></a>Omówienie Instalatora pakietu narzędzi deweloperskich

Instalator pakietu narzędzi deweloperskich to jedno-stopne rozwiązanie, które instaluje i konfiguruje wszystkie narzędzia wymagane do opracowania zaawansowanego rozwiązania inteligentnej krawędzi.

## <a name="mandatory-tools"></a>Narzędzia obowiązkowe

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 lub nowszy](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Zestaw SDK Azure Machine Learning 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Narzędzia opcjonalne

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (zestaw narzędzi do tworzenia rozwiązań dla akceleratorów NVIDIA)
* [Intel OpenVino toolkit 2020,2](https://docs.openvinotoolkit.org/) (zestaw narzędzi do tworzenia rozwiązań dla akceleratorów firmy Intel)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) lub 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Moduł łańcucha 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Znane problemy

- Opcjonalna instalacja Caffe może zakończyć się niepowodzeniem, jeśli platforma Docker nie działa prawidłowo. Jeśli chcesz zainstalować Caffe, upewnij się, że platforma Docker jest zainstalowana i uruchomiona przed podjęciem próby instalacji Caffe za pomocą Instalatora pakietu narzędzi deweloperskich.

- Opcjonalna instalacja CUDA kończy się niepowodzeniem w systemach niezgodnych. Przed podjęciem próby zainstalowania [zestawu narzędzi cuda toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) za pomocą Instalatora pakietu narzędzi deweloperskich Sprawdź zgodność systemu.

## <a name="docker-minimum-requirements"></a>Minimalne wymagania platformy Docker

### <a name="windows"></a>Windows

- Windows 10 64-bit: Pro, Enterprise lub Education (kompilacja 16299 lub nowsza).

- Funkcja Hyper-V i kontenery funkcji systemu Windows muszą być włączone. Do pomyślnego uruchomienia funkcji Hyper-V w systemie Windows 10 wymagane są następujące wymagania wstępne dotyczące sprzętu:

    - 64-bitowy procesor z [translacją adresów drugiego poziomu (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - 4 GB pamięci RAM systemu
    - Obsługa wirtualizacji sprzętu na poziomie systemu BIOS musi być włączona w ustawieniach systemu BIOS. Aby uzyskać więcej informacji, zobacz wirtualizacja.

> [!NOTE]
> Platforma Docker obsługuje pulpit platformy Docker w systemie Windows na podstawie cyklu pomocy technicznej firmy Microsoft dla systemu operacyjnego Windows 10. Aby uzyskać więcej informacji, zobacz [Arkusz faktów cyklu życia systemu Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

Dowiedz się więcej o [instalowaniu pulpitu platformy Docker w systemie Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- Komputery Mac muszą mieć model 2010 lub nowszy z następującymi atrybutami:
    - Procesor Intel
    - Obsługa sprzętowa przez firmę Intel do wirtualizacji jednostki zarządzania pamięci (pamięcią), w tym rozszerzone tabele stron (EPT) i Tryb nieograniczony. Aby sprawdzić, czy maszyna ma tę pomoc techniczną, możesz uruchomić następujące polecenie w terminalu: ```sysctl kern.hv_support``` . Jeśli komputer Mac obsługuje strukturę funkcji hypervisor, polecenie drukuje ```kern.hv_support: 1``` .

- macOS w wersji 10,14 lub nowszej (Mojave, Catalina lub Big Sur). Zalecamy uaktualnienie do najnowszej wersji programu macOS. Jeśli wystąpią jakieś problemy po uaktualnieniu macOS do wersji 10,15, musisz zainstalować najnowszą wersję programu Docker Desktop, aby była zgodna z tą wersją programu macOS.

- Co najmniej 4 GB pamięci RAM.

- NIE instaluj VirtualBox przed wersją 4.3.30--nie jest to zgodne z programem Docker Desktop.

- Instalator nie jest obsługiwany przez firmę Apple M1.

Dowiedz się więcej o [instalowaniu pulpitu platformy Docker na komputerze Mac](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Uruchom instalatora

Pobierz instalatora pakietu narzędzi deweloperskich dla [systemu Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)lub [Mac](https://go.microsoft.com/fwlink/?linkid=2132296). Uruchom Instalatora zgodnie z Twoją platformą, zgodnie z poniższym opisem.

### <a name="windows"></a>Windows

1. Kliknij pozycję **dev-Tools-Pack-Installer** , aby otworzyć Kreatora instalacji.

### <a name="mac"></a>Mac

1. Po pobraniu Przenieś plik **dev-Tools-Pack-Installer.app** do folderu **Applications** .

1. Kliknij pozycję **dev-Tools-Pack-Installer.app** , aby otworzyć Kreatora instalacji.

1. Jeśli zostanie wyświetlone okno dialogowe zabezpieczeń "niezidentyfikowane deweloperem":

    1. Przejdź do pozycji **Preferencje systemu**  ->  **zabezpieczenia & prywatność**  ->  **Ogólne** , a następnie kliknij przycisk **Otwórz mimo wszystko** obok **dev-Tools-Pack-Installer.app**.
    1. Kliknij ikonę elektronu.
    1. W oknie dialogowym Zabezpieczenia kliknij pozycję **Otwórz** .

### <a name="linux"></a>Linux

1. Po wyświetleniu monitu przez przeglądarkę kliknij przycisk **Zapisz** , aby ukończyć pobieranie instalatora.

1. Dodaj uprawnienia wykonywania do pliku **appimage** :

    1. Otwórz terminal systemu Linux.

    1. Wprowadź następujące elementy w terminalu, aby przejść do folderu **pliki do pobrania** :

        ```bash
        cd ~/Downloads/
        ```

    1. Utwórz plik wykonywalny AppImage:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Uruchom Instalatora:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Dodaj uprawnienia wykonywania do pliku **appimage** :

    1. Kliknij prawym przyciskiem myszy plik. appimage i wybierz polecenie **Właściwości**.
    1. Otwórz kartę **uprawnienia** .
    1. Zaznacz pole wyboru obok pozycji **Zezwalaj na wykonywanie pliku jako programu**.
    1. Zamknij **Właściwości** i Otwórz plik **. appimage** .

## <a name="run-the-installer"></a>Uruchamianie instalatora

1. Na stronie **Instalowanie Instalatora pakietu narzędzi deweloperskich** kliknij pozycję **Wyświetl licencję** , aby wyświetlić umowy licencyjne poszczególnych pakietów oprogramowania zawartych w instalatorze. Jeśli akceptujesz warunki umowy licencyjnej, zaznacz pole wyboru i kliknij przycisk **dalej**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Ekran Umowa licencyjna w instalatorze.":::

1. Kliknij zasady **zachowania poufności informacji** , aby zapoznać się z zasadami zachowania poufności informacji firmy Microsoft. Jeśli akceptujesz warunki zasad zachowania poufności informacji i chcesz wysyłać dane diagnostyczne do firmy Microsoft, wybierz pozycję **tak** i kliknij przycisk **dalej**. W przeciwnym razie wybierz pozycję **nie** i kliknij przycisk **dalej**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Ekran umowa dotycząca zasad zachowania poufności informacji w instalatorze.":::

1. Na stronie **Konfiguruj składniki** Wybierz opcjonalne narzędzia, które chcesz zainstalować (domyślnie zostaną zainstalowane obowiązkowe narzędzia).

    1. Jeśli pracujesz z usługą Azure Percept audio SoM, która jest częścią platformy Azure Percept DK, pamiętaj, aby zainstalować zestaw narzędzi firmy Intel OpenVino i Miniconda3.

    1. Kliknij przycisk **Instaluj** , aby kontynuować instalację.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Ekran Instalatora z informacjami o dostępnych pakietach oprogramowania.":::

1. Po pomyślnej instalacji wszystkich wybranych składników Kreator przechodzi do strony **kończącej pracę Kreatora instalacji** . Kliknij przycisk **Zakończ** , aby zamknąć Instalatora.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Ekran uzupełniania Instalatora.":::

## <a name="docker-status-check"></a>Sprawdzenie stanu platformy Docker

Jeśli Instalator powiadamia, aby sprawdzić, czy program Docker Desktop jest w dobrym stanie, zobacz następujące kroki:

### <a name="windows"></a>Windows

1. Rozwiń ukryte ikony na pasku zadań.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Pasek zadań.":::

1. Upewnij się, że ikona platformy Docker zawiera program **Docker Desktop uruchomiony**.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Stan platformy Docker.":::

1. Jeśli powyższa ikona nie zostanie wyświetlona na pasku zadań, uruchom program Docker Desktop z menu Start.

1. Jeśli platforma Docker poprosi o ponowne uruchomienie, warto zamknąć Instalatora i uruchomić ponownie po ponownym uruchomieniu, a platforma Docker jest w stanie uruchomienia. Wszystkie pomyślnie zainstalowane aplikacje innych firm powinny być wykrywane i nie będą instalowane automatycznie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [repozytorium zaawansowanego programowania w usłudze Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) , aby rozpocząć pracę z zaawansowanym programowaniem na platformie Azure Percept.
