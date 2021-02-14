---
title: Renderowanie aplikacji
description: Możliwe jest korzystanie z aplikacji do renderowania z Azure Batch. Jednak obrazy maszyn wirtualnych portalu Azure Marketplace są dostępne ze wstępnie zainstalowanymi aplikacjami.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: e296ab09498b6bb7ee21e3d88c9c416c66368d69
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362237"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Wstępnie zainstalowane aplikacje do renderowania wsadowego obrazów maszyn wirtualnych

Możliwe jest korzystanie z aplikacji do renderowania z Azure Batch. Jednak obrazy maszyn wirtualnych portalu Azure Marketplace są dostępne ze wstępnie zainstalowanymi aplikacjami.

W razie potrzeby Licencjonowanie za korzystanie z opcji płatność za użycie jest dostępne dla wstępnie zainstalowanych aplikacji do renderowania. Po utworzeniu puli usługi Batch można określić wymagane aplikacje, a koszt maszyny wirtualnej i aplikacji będzie naliczany na minutę. Ceny aplikacji są wymienione na [stronie cennika Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Niektóre aplikacje obsługują tylko system Windows, ale większość z nich jest obsługiwana zarówno w systemie Windows, jak i Linux.

## <a name="applications-on-latest-centos-7-rendering-image"></a>Aplikacje na najnowszym obrazie renderowania CentOS 7

Poniższa lista ma zastosowanie do obrazu renderowania CentOS w wersji 1.1.7.

* Autodesk Maya operacji we/wy 2020 Update 4,6
* Autodesk Arnold for Maya 2020 (Arnold Version 6.2.0.0) MtoA-4.2.0-2020
* Grupa chaos V-Ray dla Maya 2020 (wersja 5.00.21)
* Blender (2,80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>Aplikacje na najnowszym obrazie renderowania systemu Windows Server

Poniższa lista ma zastosowanie do obrazu renderowania systemu Windows Server w wersji 1.5.0.

* Autodesk Maya operacji we/wy 2020 Update 4,4
* Autodesk 3ds Max we/wy 2021 Update 3
* Autodesk Arnold for Maya 2020 (Arnold Version 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold for 3ds Max 2021 (Arnold wersja 6.1.0.1) MAXtoA-4.2.2.20-2021
* Grupa chaos V-Ray dla Maya 2020 (wersja 5.00.21)
* Grupa chaos V-Ray dla 3ds Max 2021 (wersja 5.00.05)
* Blender (2.79)
* Blender (2,80)
* AZ 10

> [!IMPORTANT]
> Aby uruchomić polecenie V-Ray z Maya poza [szablonami rozszerzeń Azure Batch](https://github.com/Azure/batch-extension-templates), zacznij `vrayses.exe` przed uruchomieniem renderowania. Aby uruchomić vrayses.exe poza szablonami, można użyć poniższego polecenia `%MAYA_2020%\vray\bin\vrayses.exe"` .
>
> Aby zapoznać się z przykładem, zobacz Uruchamianie zadania [szablonu Maya i V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) w serwisie GitHub.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Aplikacje we wcześniejszych obrazach renderowania systemu Windows Server

Poniższa lista ma zastosowanie do systemu Windows Server 2016, w wersji 1.3.8 images.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya operacji we/wy 2018 Update 6 (wersja 18.4.0.7622)
* Autodesk Maya we/wy 2019
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk 3ds Max we/wy 2019 Update 1 (wersja 21.2.0.2219)
* Autodesk 3ds Max we/wy 2020 Update 2
* Autodesk Arnold for Maya 2017 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2018 r
* Autodesk Arnold for Maya 2019 (Arnold Version 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018 (Arnold wersja 5.3.0.2) (wersja 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold wersja 5.3.0.2) (wersja 1.2.926)
* Autodesk Arnold for 3ds Max 2020 (Arnold wersja 5.3.0.2) (wersja 1.2.926)
* Grupa chaos V-Ray dla Maya 2017 (wersja 4.12.01)
* Grupa chaos V-Ray dla Maya 2018 (wersja 4.12.01)
* Grupa chaos V-Ray dla Maya 2019 (wersja 4.04.03)
* Grupa chaos V-Ray dla 3ds Max 2018 (wersja 4.20.01)
* Grupa chaos V-Ray dla 3ds Max 2019 (wersja 4.20.01)
* Grupa chaos V-Ray dla 3ds Max 2020 (wersja 4.20.01)
* Blender (2.79)
* Blender (2,80)
* AZ 10

Poniższa lista ma zastosowanie do systemu Windows Server 2016, w wersji 1.3.7 images.

* Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459)
* Autodesk Maya operacji we/wy 2018 Update 4 (wersja 18.4.0.7622)
* Autodesk 3ds Max we/wy 2019 Update 1 (wersja 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018 r
* Autodesk Arnold for 3ds Max 2018 (Arnold wersja 5.0.2.4) (wersja 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold wersja 5.0.2.4) (wersja 1.2.926)
* Grupa chaos V-Ray dla Maya 2018 (wersja 3.52.03)
* Grupa chaos V-Ray dla 3ds Max 2018 (wersja 3.60.02)
* Grupa chaos V-Ray dla Maya 2019 (wersja 3.52.03)
* Grupa chaos V-Ray dla 3ds Max 2019 (wersja 4.10.01)
* Blender (2.79)

> [!NOTE]
> Grupa chaos V-Ray dla 3ds Max 2019 (wersja 4.10.01) wprowadza istotne zmiany w V-Ray. Aby użyć poprzedniej wersji (wersja 3.60.02), użyj węzłów renderowania w systemie Windows Server 2016.

## <a name="applications-on-previous-centos-rendering-images"></a>Aplikacje na poprzednich obrazach renderowania CentOS

Poniższa lista ma zastosowanie do CentOS 7,6 w wersji 1.1.6 obrazów renderowania.

* Autodesk Maya I/O 2017 Update 5 (wersja 201708032230)
* Autodesk Maya operacji we/wy 2018 Update 2 (Wytnij 201711281015)
* Autodesk Maya operacji we/wy 2019 Update 1
* Autodesk Arnold for Maya 2017 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2018 r
* Autodesk Arnold for Maya 2019 (Arnold Version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (wersja 3.60.04)
* Chaos Group V-Ray for Maya 2018 (wersja 3.60.04)
* Blender (2.68)
* Blender (2,8)

## <a name="next-steps"></a>Następne kroki

Aby można było użyć renderowania obrazów maszyn wirtualnych, muszą one być określone w konfiguracji puli podczas tworzenia puli; Zapoznaj się z [możliwościami renderowania puli usługi Batch](./batch-rendering-functionality.md).
