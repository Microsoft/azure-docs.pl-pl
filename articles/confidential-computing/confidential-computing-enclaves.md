---
title: Poufne wyliczanie maszyn wirtualnych na platformie Azure
description: Dowiedz się więcej na temat sprzętu Intel SGX, aby umożliwić poufne obciążenia obliczeniowe.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551372"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure poufne maszyny wirtualne — Omówienie


Platforma Azure to pierwszy dostawca usług w chmurze, który oferuje poufne dane obliczeniowe w środowisku zwirtualizowanym. Opracowano maszyny wirtualne, które działają jako warstwa abstrakcji między sprzętem a aplikacją. Obciążenia można uruchamiać w odpowiedniej skali i z opcjami nadmiarowości i dostępności.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Virtual Machines z włączonym technologią Intel SGX

W przypadku maszyn wirtualnych z danymi poufnymi na platformie Azure część sprzętu procesora CPU jest zarezerwowana dla części kodu i danych w aplikacji. Ta część z ograniczeniami to enklawy. 

![Model maszyny wirtualnej](media/overview/hardware-backed-enclave.png)

Usługa Azure poufnej infrastruktury obliczeniowej składa się z specjalistycznej jednostki SKU maszyn wirtualnych. Te maszyny wirtualne działają na procesorach Intel z rozszerzeniem Guard (SGX). [Procesor Intel SGX](https://intel.com/sgx) to składnik, który umożliwia zwiększonej ochrony, którą ponosi poufne dane. 

Obecnie platforma Azure oferuje [DCsv2ą](../virtual-machines/dcv2-series.md) platformę opartą na technologii Intel SGX na potrzeby tworzenia sprzętowych enklawy. Można tworzyć bezpieczne aplikacje oparte na enklawy do uruchamiania w serii DCsv2 maszyn wirtualnych w celu ochrony danych aplikacji i używanego kodu. 

[Dowiedz się więcej](virtual-machine-solutions.md) o wdrażaniu maszyn wirtualnych do przetwarzania poufnego platformy Azure przy użyciu zaufanych enclaves opartych na sprzęcie.

## <a name="enclaves"></a>Enclaves

Enclaves są zabezpieczonymi częściami procesora sprzętowego i pamięci. Nie ma możliwości wyświetlania danych ani kodu w enklawy, nawet z debugerem. Jeśli niezaufany kod próbuje zmodyfikować zawartość w pamięci enklawy, środowisko zostanie wyłączone, a operacje zostaną odrzucone.

Zasadniczo należy traktować enklawy jako zabezpieczone pole. W polu należy umieścić zaszyfrowany kod i dane. Od zewnątrz pola nie są wyświetlane żadne elementy. Enklawy klucz do odszyfrowania danych, dane są następnie przetwarzane i szyfrowane ponownie przed wysłaniem z enklawy.

Każdy enklawy ma ustawiony rozmiar pamięci podręcznej zaszyfrowanej strony (EPC), która określa ilość pamięci, jaką może zamieścić każdy enklawy. Większe DCsv2 maszyny wirtualne mają więcej pamięci sygnatur EPC. Przeczytaj stronę ze [specyfikacjami DCsv2](../virtual-machines/dcv2-series.md) , aby uzyskać maksymalny rozmiar sygnatury EPC dla rozmiaru maszyny wirtualnej.



### <a name="developing-applications-to-run-inside-enclaves"></a>Opracowywanie aplikacji do uruchamiania wewnątrz enclaves
Podczas tworzenia aplikacji można używać [narzędzi programowych](application-development.md) do osłony fragmentów kodu i danych w enklawy. Narzędzia te zapewniają, że kod i dane nie mogą być wyświetlane ani modyfikowane przez żadną osobę spoza zaufanego środowiska. 

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [najlepszymi](virtual-machine-solutions.md) rozwiązaniami dotyczącymi wdrażania rozwiązań na platformie Azure.
- [Wdróż maszynę wirtualną DCsv2-Series](quick-create-portal.md)
- [Opracowywanie aplikacji obsługującej enklawy](application-development.md) za pomocą zestawu SDK programu OE