---
title: Konfigurowanie laboratorium przy użyciu procesorów GPU w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium z maszynami wirtualnymi procesora graficznego (GPU).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: abd182339719f19a521feed95f7cfbed6942b3e8
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91404786"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Konfigurowanie laboratorium z maszynami wirtualnymi GPU

W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Wybierz między *wizualizacją* a procesorami przetwarzania grafiki *obliczeniowej* (GPU).
- Upewnij się, że są zainstalowane odpowiednie sterowniki procesora GPU.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Wybór między rozmiarami wizualizacji i procesora GPU
Na pierwszej stronie Kreatora tworzenia laboratorium, w **której rozmiarze maszyny wirtualnej jest potrzebna?** lista rozwijana umożliwia wybranie rozmiaru maszyn wirtualnych potrzebnych dla danej klasy.  

![Zrzut ekranu przedstawiający okienko "nowe laboratorium" służące do wybierania rozmiaru maszyny wirtualnej](./media/how-to-setup-gpu/lab-gpu-selection.png)

W tym procesie można wybrać **wizualizację** **lub procesory** GPU.  Ważne jest, aby wybrać typ procesora GPU oparty na oprogramowaniu, którego będą używać uczniowie.  

Zgodnie z opisem w poniższej tabeli *rozmiar procesora GPU* jest przeznaczony dla aplikacji intensywnie korzystających z obliczeń.  Na przykład, [głębokie uczenie w typie klasy przetwarzanie języka naturalnego](./class-type-deep-learning-natural-language-processing.md) używa **małego procesora GPU (obliczeń)** .  Procesor GPU jest odpowiedni dla tego typu klasy, ponieważ uczniowie wykorzystują platformy uczenia głębokiego i narzędzia dostarczone przez [obraz Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) do uczenia modeli uczenia głębokiego z dużymi zestawami danych.

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mały procesor GPU (obliczenia) | -&nbsp;6 &nbsp; rdzeni<br>-&nbsp;56 &nbsp; GB &nbsp; pamięci RAM  | [Standardowa_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z mocy obliczeniowej, takich jak sztuczna inteligencja (AI) i uczenie głębokie. |

Rozmiary procesora GPU *wizualizacji* są przeznaczone dla aplikacji intensywnie korzystających z grafiki.  Na przykład [Typ klasy inżynieryjnej SolidWorks](./class-type-solidworks.md) pokazuje użycie **małego procesora GPU (wizualizacji)** .  Procesor GPU wizualizacji jest odpowiedni dla tego typu klasy, ponieważ uczniowie współpracują z środowiskiem projektowania (CAD) SOLIDWORKS 3W na potrzeby modelowania i wizualizacji obiektów stałych.

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mały procesor GPU (wizualizacja) | -&nbsp;6 &nbsp; rdzeni<br>-&nbsp;56 &nbsp; GB &nbsp; pamięci RAM  | [Standardowa_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania, który używa platform, takich jak OpenGL i DirectX. |
| Średni procesor GPU (wizualizacja) | -&nbsp;12 &nbsp; rdzeni<br>-&nbsp;112 &nbsp; GB &nbsp; pamięci RAM  | [Standardowa_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania, który używa platform, takich jak OpenGL i DirectX. |

> [!NOTE]
> Niektóre z tych rozmiarów maszyn wirtualnych mogą nie być widoczne na liście podczas tworzenia laboratorium zajęć. Lista jest wypełniana na podstawie bieżącej pojemności lokalizacji laboratorium. Jeśli twórca konta laboratorium [umożliwia twórcom laboratorium wybranie lokalizacji dla laboratorium](allow-lab-creator-pick-lab-location.md), możesz spróbować wybrać inną lokalizację dla laboratorium i sprawdzić, czy rozmiar maszyny wirtualnej jest dostępny. Aby uzyskać dostęp do maszyn wirtualnych, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/?products=virtual-machines).

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Upewnij się, że są zainstalowane odpowiednie sterowniki procesora GPU
Aby skorzystać z możliwości procesora GPU maszyn wirtualnych laboratorium, upewnij się, że są zainstalowane odpowiednie sterowniki procesora GPU.  Po wybraniu rozmiaru maszyny wirtualnej procesora GPU w Kreatorze tworzenia laboratorium można wybrać opcję **Zainstaluj sterowniki procesora GPU** .  

![Zrzut ekranu przedstawiający "nowe laboratorium" z opcją "Zainstaluj sterowniki procesora GPU"](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Jak pokazano na poprzedniej ilustracji, ta opcja jest domyślnie włączona, co gwarantuje, że *najnowsze* sterowniki są zainstalowane dla wybranego typu procesora GPU i obrazu.
- Po wybraniu rozmiaru *procesora GPU* maszyny wirtualne laboratorium są obsługiwane przez procesor GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  W takim przypadku instalowane są najnowsze sterowniki [obliczeniowe Unified Device Architecture (cuda)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) , które umożliwiają przetwarzanie o wysokiej wydajności.
- Po wybraniu rozmiaru graficznej *wizualizacji* maszyny wirtualne laboratorium są obsługiwane przez technologię [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i [sieci](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  W takim przypadku instalowane są najnowsze sterowniki siatki, które umożliwiają korzystanie z aplikacji intensywnie korzystających z grafiki.

### <a name="install-the-drivers-manually"></a>Ręczne instalowanie sterowników
Może być konieczne zainstalowanie wersji sterownika innej niż Najnowsza wersja.  W tej sekcji pokazano, jak ręcznie zainstalować odpowiednie sterowniki, w zależności od tego, czy *używasz procesora GPU czy procesora* GPU *visualization* .

#### <a name="install-the-compute-gpu-drivers"></a>Instalowanie sterowników procesora GPU

Aby ręcznie zainstalować sterowniki dla rozmiaru procesora GPU, wykonaj następujące czynności:

1. Podczas [tworzenia laboratorium](./how-to-manage-classroom-labs.md)w Kreatorze tworzenia laboratorium wyłącz ustawienie **Zainstaluj sterowniki procesora GPU** .

1. Po utworzeniu laboratorium Połącz się z szablonową maszyną wirtualną w celu zainstalowania odpowiednich sterowników.

   ![Zrzut ekranu przedstawiający stronę pobierania sterownika NVIDIA](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. W przeglądarce przejdź do [strony pliki do pobrania sterownika firmy NVIDIA](https://www.nvidia.com/Download/index.aspx).  
   b. Ustaw **Typ produktu** na **Tesla**.  
   c. Ustaw **serię produktów** na **serii K**.  
   d. Ustaw **system operacyjny** zgodnie z typem obrazu podstawowego wybranego podczas tworzenia laboratorium.  
   e. Ustaw zestaw **narzędzi cuda** na wymaganą wersję sterownika cuda.  
   f. Wybierz pozycję **Wyszukaj** , aby wyszukać sterowniki.  
   przykład Wybierz pozycję **Pobierz** , aby pobrać Instalatora.  
   h. Uruchom Instalatora, aby zainstalować sterowniki na maszynie wirtualnej z szablonem.  
1. Sprawdź, czy sterowniki są poprawnie zainstalowane, postępując zgodnie z instrukcjami w sekcji [Sprawdzanie zainstalowanych sterowników](how-to-setup-lab-gpu.md#validate-the-installed-drivers) . 
1. Po zainstalowaniu sterowników i innego oprogramowania, które są wymagane dla danej klasy, wybierz pozycję **Publikuj** , aby utworzyć maszyny wirtualne uczniów.

> [!NOTE]
> Jeśli używasz obrazu systemu Linux, po pobraniu instalatora Zainstaluj sterowniki, postępując zgodnie z instrukcjami w temacie [Install cuda drivers in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Instalowanie wizualizacji sterowników procesora GPU

Aby ręcznie zainstalować sterowniki dla rozmiaru procesora GPU, wykonaj następujące czynności:

1. Podczas [tworzenia laboratorium](./how-to-manage-classroom-labs.md)w Kreatorze tworzenia laboratorium wyłącz ustawienie **Zainstaluj sterowniki procesora GPU** .
1. Po utworzeniu laboratorium Połącz się z szablonową maszyną wirtualną w celu zainstalowania odpowiednich sterowników.
1. Zainstaluj sterowniki siatki dostarczone przez firmę Microsoft na maszynie wirtualnej szablonu, postępując zgodnie z instrukcjami dotyczącymi systemu operacyjnego:
   -  [Sterowniki sieci NVIDIA systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Sterowniki siatki NVIDIA systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Uruchom ponownie szablon maszyny wirtualnej.
1. Sprawdź, czy sterowniki są poprawnie zainstalowane, postępując zgodnie z instrukcjami w sekcji [Sprawdzanie zainstalowanych sterowników](how-to-setup-lab-gpu.md#validate-the-installed-drivers) .
1. Po zainstalowaniu sterowników i innego oprogramowania, które są wymagane dla danej klasy, wybierz pozycję **Publikuj** , aby utworzyć maszyny wirtualne uczniów.

### <a name="validate-the-installed-drivers"></a>Weryfikowanie zainstalowanych sterowników
W tej sekcji opisano, jak sprawdzić, czy sterowniki procesora GPU są prawidłowo zainstalowane.

#### <a name="windows-images"></a>Obrazy systemu Windows
1.  Postępuj zgodnie z instrukcjami w sekcji "Weryfikowanie instalacji sterowników" [w temacie Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych z serii N z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Jeśli używasz procesora GPU *wizualizacji* , możesz również:
    - Wyświetlanie i dostosowywanie ustawień procesora GPU w panelu sterowania NVIDIA. W tym celu w **Panelu sterowania systemu Windows**wybierz pozycję **sprzęt**, a następnie wybierz pozycję **Panel sterowania firmy NVIDIA**.

      ![Zrzut ekranu przedstawiający panel sterowania systemu Windows z linkiem panelu sterowania NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Wyświetlanie wydajności procesora GPU przy użyciu **Menedżera zadań**.  Aby to zrobić, wybierz kartę **wydajność** , a następnie wybierz opcję **procesor GPU** .

       ![Zrzut ekranu przedstawiający kartę wydajności procesora GPU Menedżera zadań](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Ustawienia panelu sterowania NVIDIA są dostępne tylko dla *wizualizacji* GPU.  Jeśli spróbujesz otworzyć Panel sterowania NVIDIA dla procesora GPU, zostanie wyświetlony następujący błąd: "Ustawienia wyświetlania NVIDIA nie są dostępne.  Obecnie nie używasz wyświetlania dołączonego do procesora GPU NVIDIA ".  Podobnie informacje o wydajności procesora GPU w Menedżerze zadań są udostępniane tylko dla wizualizacji GPU.

#### <a name="linux-images"></a>Obrazy systemu Linux
Postępuj zgodnie z instrukcjami w sekcji "Weryfikowanie instalacji sterowników" [w temacie Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych z serii N z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Tworzenie laboratoriów na potrzeby zajęć i zarządzanie nimi](how-to-manage-classroom-labs.md)
- [SOLIDWORKS typ klasy projektowania (CAD) dla komputerów](class-type-solidworks.md)
- [MATLAB (laboratorium macierzowe) typ klasy](class-type-matlab.md)



