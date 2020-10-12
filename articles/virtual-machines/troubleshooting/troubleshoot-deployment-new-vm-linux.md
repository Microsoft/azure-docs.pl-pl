---
title: Rozwiązywanie problemów z wdrożeniem maszyny wirtualnej systemu Linux | Microsoft Docs
description: Rozwiązywanie problemów z wdrażaniem Menedżer zasobów podczas tworzenia nowej maszyny wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: daberry
ms.openlocfilehash: f48963a4d18e80cb67bfbbdc532d34f89b8b5d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028451"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Rozwiązywanie problemów z wdrażaniem Menedżer zasobów przy tworzeniu nowej maszyny wirtualnej z systemem Linux na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Inne problemy i pytania dotyczące wdrażania maszyn wirtualnych można znaleźć w artykule [Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Linux na platformie Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, Zbierz dzienniki aktywności w celu zidentyfikowania błędu związanego z problemem. Poniższe linki zawierają szczegółowe informacje dotyczące procesu do wykonania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**T:** Jeśli system operacyjny jest uogólniony, a został przekazany i/lub przechwycony przy użyciu ustawienia uogólnionego, nie będzie żadnych błędów. Podobnie, jeśli system operacyjny jest w systemie Linux wyspecjalizowany i/lub przechwytywany przy użyciu ustawienia wyspecjalizowanego, nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli system operacyjny jest uogólniony w systemie Linux i został przekazany jako wyspecjalizowany, zostanie wyświetlony błąd limitu czasu aprowizacji, ponieważ maszyna wirtualna jest zablokowana na etapie aprowizacji.

**N<sup>2</sup>:** Jeśli system operacyjny jest wyspecjalizowany dla systemu Linux i został przekazany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem.

**Rozwiązanie:**

Aby rozwiązać oba te błędy, należy przekazać oryginalny wirtualny dysk twardy, który jest dostępny lokalnie, przy użyciu tego samego ustawienia jak w przypadku systemu operacyjnego (uogólniony/wyspecjalizowany). Aby przekazać jako uogólniony, pamiętaj, aby najpierw uruchomić Inicjowanie obsługi administracyjnej.

**Błędy przechwytywania:**

**N<sup>3</sup>:** Jeśli system operacyjny jest uogólniony w systemie Linux i jest przechwytywany jako wyspecjalizowany, zostanie wyświetlony błąd limitu czasu inicjowania obsługi, ponieważ oryginalna maszyna wirtualna nie będzie mogła być używana, ponieważ jest oznaczona jako uogólniona.

**N<sup>4</sup>:** Jeśli system operacyjny jest w systemie Linux wyspecjalizowany i jest przechwytywany jako uogólniony, zostanie wyświetlony błąd inicjowania obsługi, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwą komputera, nazwą użytkownika i hasłem. Ponadto oryginalna maszyna wirtualna nie jest używana, ponieważ jest oznaczona jako wyspecjalizowana.

**Rozwiązanie:**

Aby rozwiązać oba te błędy, Usuń bieżący obraz z portalu i [Przechwyć go ponownie z bieżącego dysku VHD](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) z tym samym ustawieniem dla systemu operacyjnego (uogólniony/wyspecjalizowany).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: obraz niestandardowy/Galeria/witryna Marketplace; Niepowodzenie alokacji
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
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny wirtualnej z systemem Linux lub zmiany rozmiaru istniejącej maszyny wirtualnej z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z wdrożeniem Menedżer zasobów z ponownym uruchamianiem lub zmianami rozmiaru istniejącej maszyny wirtualnej z systemem Linux na platformie Azure](./troubleshoot-deploy-vm-linux.md?toc=/azure/virtual-machines/linux/toc.json).
