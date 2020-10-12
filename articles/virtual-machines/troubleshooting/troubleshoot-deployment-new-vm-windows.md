---
title: Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych systemu Windows na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z wdrażaniem Menedżer zasobów podczas tworzenia nowej maszyny wirtualnej z systemem Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: daberry
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b2f21cab4740013eb2de1d69f558c95461c493e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028400"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Rozwiązywanie problemów z wdrażaniem podczas tworzenia nowej maszyny wirtualnej z systemem Windows na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Inne problemy z wdrażaniem maszyn wirtualnych i pytania można znaleźć [w temacie Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych systemu Windows na platformie Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, Zbierz dzienniki aktywności w celu zidentyfikowania błędu związanego z problemem. Poniższe linki zawierają szczegółowe informacje dotyczące procesu do wykonania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**T:** Jeśli system operacyjny jest uogólniony, a został przekazany i/lub przechwycony przy użyciu ustawienia uogólnionego, nie będzie żadnych błędów. Podobnie, jeśli system operacyjny jest wyspecjalizowany dla systemu Windows i został przekazany i/lub przechwycony przy użyciu ustawienia specjalistycznego, nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli system operacyjny jest uogólniony i jest przekazywany jako wyspecjalizowany, zostanie wyświetlony błąd limitu czasu inicjowania obsługi administracyjnej maszyny wirtualnej, która została zablokowana na ekranie OOBE.

**N<sup>2</sup>:** Jeśli system operacyjny jest wyspecjalizowany dla systemu Windows i został przekazany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi administracyjnej maszyny wirtualnej zablokowanej na ekranie OOBE, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem.

**Rozwiązanie**

Aby rozwiązać oba te błędy, użyj polecenie [Add-AzVhd w celu przekazania oryginalnego wirtualnego dysku twardego](/powershell/module/az.compute/add-azvhd), dostępnego lokalnie, z tym samym ustawieniem jak w przypadku systemu operacyjnego (uogólniony/wyspecjalizowany). Aby przekazać jako uogólniony, pamiętaj, aby najpierw uruchomić program Sysprep.

**Błędy przechwytywania:**

**N<sup>3</sup>:** Jeśli system operacyjny jest uogólniony i jest przechwytywany jako wyspecjalizowany, zostanie wyświetlony błąd limitu czasu inicjowania obsługi, ponieważ oryginalna maszyna wirtualna nie będzie mogła być używana, ponieważ jest oznaczona jako uogólniona.

**N<sup>4</sup>:** Jeśli system operacyjny jest wyspecjalizowany dla systemu Windows i jest przechwytywany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem. Ponadto oryginalna maszyna wirtualna nie jest używana, ponieważ jest oznaczona jako wyspecjalizowana.

**Rozwiązanie**

Aby rozwiązać oba te błędy, Usuń bieżący obraz z portalu i [Przechwyć go ponownie z bieżącego dysku VHD](../windows/create-vm-specialized.md) z tym samym ustawieniem dla systemu operacyjnego (uogólniony/wyspecjalizowany).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: obraz niestandardowy/Galeria/witryna Marketplace; Niepowodzenie alokacji
Ten błąd występuje w sytuacji, gdy nowe żądanie maszyny wirtualnej jest przypięte do klastra, który nie obsługuje żądanego rozmiaru maszyny wirtualnej lub nie ma dostępnego wolnego miejsca, aby pomieścić żądanie.

**Przyczyna 1:** Klaster nie obsługuje żądanego rozmiaru maszyny wirtualnej.

**Rozwiązanie 1:**

* Spróbuj ponownie wykonać żądanie, używając mniejszego rozmiaru maszyny wirtualnej.
* Jeśli nie można zmienić rozmiaru żądanej maszyny wirtualnej:
  * Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
    Kliknij pozycję **grupy zasobów**  >  *zasoby grupy zasobów*  >  **Resources**  >  *Twój zestaw dostępności*  >  **Virtual Machines**  >  *zatrzymać maszynę wirtualną*  >  **Stop**.
  * Po zatrzymaniu wszystkich maszyn wirtualnych Utwórz nową maszynę wirtualną w żądanym rozmiarze.
  * Najpierw uruchom nową maszynę wirtualną, a następnie wybierz każdą z zatrzymanych maszyn wirtualnych, a następnie kliknij przycisk **Uruchom**.

**Przyczyna 2:** W klastrze nie ma bezpłatnych zasobów.

**Rozwiązanie 2:**

* Ponów żądanie w późniejszym czasie.
* Jeśli nowa maszyna wirtualna może być częścią innego zestawu dostępności
  * Utwórz nową maszynę wirtualną w innym zestawie dostępności (w tym samym regionie).
  * Dodaj nową MASZYNę wirtualną do tej samej sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny wirtualnej z systemem Windows lub zmiany rozmiaru istniejącej maszyny wirtualnej z systemem Windows na platformie Azure, zobacz [Rozwiązywanie problemów z wdrożeniem Menedżer zasobów z ponownym uruchamianiem lub zmianami rozmiaru istniejącej maszyny wirtualnej z systemem Windows na platformie Azure](restart-resize-error-troubleshooting.md).
