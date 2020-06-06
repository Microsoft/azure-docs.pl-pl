---
title: Konfigurowanie laboratorium przy użyciu procesorów GPU w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium z maszynami wirtualnymi GPU (jednostkami przetwarzania grafiki).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450773"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Konfigurowanie laboratorium z procesorem GPU (jednostka przetwarzania grafiki) rozmiar maszyny wirtualnej
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Wybór między wirtualizacją a procesorami GPU
- Upewnij się, że są zainstalowane odpowiednie sterowniki procesora GPU
- Konfigurowanie ustawień RDP w celu nawiązania połączenia z maszyną wirtualną procesora GPU

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>Wybór między rozmiarami wirtualizacji i procesora GPU
Na pierwszej stronie Kreatora tworzenia laboratorium wybiera się **rozmiar maszyn wirtualnych** potrzebnych dla danej klasy.  

![Wybierz rozmiar maszyny wirtualnej](../media/how-to-setup-gpu/lab-gpu-selection.png)

Tutaj można wybrać **wizualizację** **i procesory** GPU.  Ważne jest, aby wybrać odpowiedni typ procesora GPU oparty na oprogramowaniu, którego będą używać uczniowie.  

Zgodnie z opisem w poniższej tabeli **rozmiar procesora GPU** jest przeznaczony dla aplikacji intensywnie korzystających z obliczeń.  Na przykład, [głębokie uczenie w typie klasy przetwarzanie języka naturalnego](./class-type-deep-learning-natural-language-processing.md) pokazuje użycie **małego procesora GPU (obliczeń)** .  Procesor **GPU** jest odpowiedni dla tego typu klasy, ponieważ uczniowie wykorzystują platformy uczenia głębokiego i narzędzia dostarczone przez [obraz Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) do uczenia modeli uczenia głębokiego z dużymi zestawami danych.

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mały procesor GPU (obliczenia) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li></ul>  | [Standardowa_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z komputerów, takich jak sztuczna inteligencja i uczenie głębokie. |

Rozmiary **wizualizacji** są przeznaczone dla aplikacji intensywnie korzystających z grafiki.  Na przykład [Typ klasy inżynieryjnej SolidWorks](./class-type-solidworks.md) pokazuje użycie **małego procesora GPU (wizualizacji)** .  Procesor GPU **wizualizacji** jest odpowiedni dla tego typu klasy, ponieważ uczniowie współpracują ze środowiskiem projektowania (CAD) SolidWorks "3W" na potrzeby modelowania i wizualizacji obiektów stałych.

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mały procesor GPU (wizualizacja) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li>  | [Standardowa_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |
| Średni procesor GPU (wizualizacja) | <ul><li>12 rdzeni</li><li>112 GB PAMIĘCI RAM</li></ul>  | [Standardowa_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Upewnij się, że są zainstalowane odpowiednie sterowniki procesora GPU
Aby skorzystać z możliwości procesora GPU maszyn wirtualnych laboratorium, należy upewnić się, że są zainstalowane odpowiednie sterowniki procesora GPU.  W Kreatorze tworzenia laboratorium w przypadku wybrania rozmiaru maszyny wirtualnej procesora GPU istnieje możliwość **zainstalowania sterowników procesora GPU**.  

![Instalowanie sterowników procesora GPU](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Ta opcja jest domyślnie **włączona** i zapewnia, że *najnowsze* sterowniki są zainstalowane dla typu procesora GPU i wybranego obrazu:
- Po wybraniu rozmiaru **procesora GPU** maszyny wirtualne laboratorium są obsługiwane przez procesor GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  W takim przypadku najnowsze sterowniki [cuda](https://www.nvidia.com/object/io_69526.html) są instalowane w celu włączenia obliczeń o wysokiej wydajności.
- Po wybraniu rozmiaru graficznej **wizualizacji** maszyny wirtualne laboratorium są obsługiwane przez technologię [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i [sieci](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  W takim przypadku najnowsze sterowniki siatki są instalowane w celu włączenia aplikacji intensywnie korzystających z grafiki.

> [!IMPORTANT]
> Aby zapewnić najlepsze środowisko użytkownika przy użyciu **wizualizacji** GPU, należy się upewnić, że *oba* sterowniki są zainstalowane, *a* procesor GPU jest włączony przy użyciu połączeń RDP.  Zapoznaj się z instrukcjami w sekcji o nazwie [Włączanie procesora GPU przez połączenie RDP z maszynami wirtualnymi z systemem Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).

### <a name="manually-install-drivers"></a>Ręczne instalowanie sterowników
Mogą istnieć scenariusze, w których należy zainstalować inną wersję sterowników niż Najnowsza wersja.  W procedurach przedstawionych w tej sekcji pokazano, jak ręcznie zainstalować odpowiednie sterowniki w zależności od **tego, czy** jest używany procesor GPU lub do **wizualizacji** .

#### <a name="compute-gpu-drivers"></a>Sterowniki procesora GPU obliczeniowe
Wykonaj następujące kroki, aby ręcznie zainstalować sterowniki dla **rozmiaru procesora GPU** :
1. Podczas [tworzenia laboratorium](./how-to-manage-classroom-labs.md)należy **wyłączyć** ustawienie **Zainstaluj sterowniki procesora GPU** w Kreatorze tworzenia laboratorium.
1. Po utworzeniu laboratorium Połącz się z szablonową maszyną wirtualną w celu zainstalowania odpowiednich sterowników.
   ![Pobieranie sterowników NVIDIA](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. W przeglądarce Otwórz [stronę pobierania sterowników firmy NVIDIA](https://www.nvidia.com/Download/index.aspx).
   2. Ustaw **Typ produktu** na **Tesla**.
   3. Ustaw **serię produktów** na **serii K**.
   4. Ustaw **produkt** na **Tesla K80**.
   5. Ustaw **system operacyjny** zgodnie z typem obrazu podstawowego wybranego podczas tworzenia laboratorium.
   6. Ustaw zestaw **narzędzi cuda** na wymaganą wersję sterowników cuda.
   7. Kliknij przycisk **Wyszukaj** , aby znaleźć sterowniki.
   8. Kliknij pozycję **Pobierz** , aby pobrać Instalatora.
   9. Uruchom Instalatora, aby zainstalować sterowniki na maszynie wirtualnej z szablonem.  
2. Sprawdź poprawność zainstalowanych sterowników, wykonując kroki opisane w sekcji o nazwie [Weryfikuj zainstalowane sterowniki](how-to-setup-lab-gpu.md#validate-installed-drivers). 
3. Po zainstalowaniu sterowników i innych programów wymaganych dla danej klasy można kliknąć pozycję **Publikuj** , aby utworzyć maszyny wirtualne uczniów.

> [!NOTE]
> W przypadku korzystania z obrazu systemu Linux należy również zapoznać się z poniższymi krokami, aby zainstalować sterowniki po pobraniu instalatora: [Zainstaluj sterowniki cuda w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>Wizualizacja sterowników procesora GPU
Wykonaj następujące kroki, aby ręcznie zainstalować sterowniki dla **rozmiarów procesora GPU** :
1. Podczas [tworzenia laboratorium](./how-to-manage-classroom-labs.md)należy **wyłączyć** ustawienie **Zainstaluj sterowniki procesora GPU** w Kreatorze tworzenia laboratorium.
1. Po utworzeniu laboratorium Połącz się z szablonową maszyną wirtualną w celu zainstalowania odpowiednich sterowników.
1. Zainstaluj sterowniki siatki dostarczone przez firmę Microsoft na maszynie wirtualnej szablonu, wykonując następujące czynności:
   -  [Sterowniki sieci NVIDIA systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Sterowniki siatki NVIDIA systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Uruchom ponownie szablon maszyny wirtualnej.
1. Sprawdź poprawność zainstalowanych sterowników, wykonując kroki opisane w sekcji o nazwie [Weryfikuj zainstalowane sterowniki](how-to-setup-lab-gpu.md#validate-installed-drivers).
1. Skonfiguruj ustawienia protokołu RDP, aby włączyć procesor GPU, wykonując czynności opisane w sekcji o nazwie [Włącz procesor GPU przez połączenie RDP z maszynami wirtualnymi z systemem Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).
1. Po zainstalowaniu sterowników i innych programów wymaganych dla danej klasy można kliknąć pozycję **Publikuj** , aby utworzyć maszyny wirtualne uczniów.

### <a name="validate-installed-drivers"></a>Weryfikuj zainstalowane sterowniki
W procedurach przedstawionych w tej sekcji opisano, jak sprawdzić, czy sterowniki procesora GPU są prawidłowo zainstalowane.

#### <a name="windows-images"></a>Obrazy systemu Windows
1.  Wykonaj kroki opisane w artykule, w którym pokazano, jak [zweryfikować instalację sterownika w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Jeśli używasz procesora GPU **wizualizacji** , możesz również:
    - Dostęp do **Panelu sterowania NVIDIA** w celu wyświetlania i dostosowywania ustawień procesora GPU.  Aby uzyskać dostęp do tych ustawień, Otwórz **Panel sterowania > sprzęt** i wybierz pozycję **Panel sterowania firmy NVIDIA**.
      ![Otwórz Panel sterowania NVIDIA](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - Wyświetlanie wydajności procesora GPU przy użyciu **Menedżera zadań**.  Aby je wyświetlić, Otwórz **Menedżera zadań** i wybierz kartę **wydajność** . ![ Wydajność procesora GPU Menedżera zadań](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Ustawienia **Panelu sterowania NVIDIA** są dostępne tylko dla **wizualizacji** GPU.  Jeśli spróbujesz otworzyć **Panel sterowania NVIDIA** **dla procesora GPU** , zostanie wyświetlony następujący błąd: **Ustawienia wyświetlania NVIDIA nie są dostępne.  Obecnie nie używasz ekranu dołączonego do procesora GPU NVIDIA.**  Podobnie informacje o wydajności procesora GPU w **Menedżerze zadań** są dostępne tylko dla **wizualizacji** GPU.

#### <a name="linux-images"></a>Obrazy systemu Linux
Wykonaj kroki opisane w artykule, w którym opisano sposób [weryfikacji instalacji sterowników w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Włącz procesor GPU przez połączenie RDP z maszynami wirtualnymi z systemem Windows
W przypadku używania protokołu RDP (Remote Desktop Protocol) do nawiązywania połączenia z maszyną wirtualną z systemem Windows, która jest obsługiwana przez procesor GPU **wizualizacji** , należy wykonać dodatkową konfigurację, aby procesor GPU był używany do renderowania grafiki (w przeciwnym razie używany jest procesor CPU).

Poniższe kroki należy wykonać na szablonie maszyny wirtualnej.

1. Najpierw skonfiguruj ustawienia protokołu RDP na potrzeby używania procesora GPU:

   1. Wykonaj kroki opisane w tym artykule, aby zobaczyć, jak [skonfigurować renderowanie aplikacji przez procesor GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).

   2. Wykonaj kroki opisane w tym artykule, aby dowiesz się, jak [skonfigurować szyfrowanie ramek procesora GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Następnie sprawdź konfigurację: 

   1. Postępuj zgodnie z instrukcjami w tym artykule, które pokazują, jak [weryfikować przyspieszanie renderowania aplikacji przez procesor GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).

   2.  Postępuj zgodnie z instrukcjami w tym artykule, które pokazują, jak [sprawdzać przyspieszenie kodowania ramek procesora GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Na koniec masz zainstalowane sterowniki i skonfigurowano ustawienia RDP do korzystania z procesora GPU.  Po zainstalowaniu innego oprogramowania wymaganego dla danej klasy możesz kliknąć przycisk **Publikuj** , aby utworzyć maszyny wirtualne uczniów.  Gdy uczniowie nawiązują połączenie z maszynami wirtualnymi przy użyciu protokołu RDP, ich pulpit będzie renderowany przy użyciu procesora GPU maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Tworzenie laboratoriów na potrzeby zajęć i zarządzanie nimi](how-to-manage-classroom-labs.md)
- [SolidWorks typ klasy projektowania (CAD) dla komputerów](class-type-solidworks.md)
- [Typ klasy MATLAB](class-type-matlab.md)



