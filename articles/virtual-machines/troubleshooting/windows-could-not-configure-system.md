---
title: Rozwiązywanie problemów polegających na tym, że system Windows nie mógł zakończyć konfigurowania systemu
titlesuffix: Azure Virtual Machines
description: Ten artykuł zawiera kroki rozwiązywania problemów, w których proces Sysprep uniemożliwia rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629593"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Rozwiązywanie problemów polegających na tym, że system Windows nie mógł zakończyć konfigurowania systemu

Ten artykuł zawiera kroki rozwiązywania problemów, w których proces Sysprep uniemożliwia rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zostanie wyświetlony komunikat o błędzie instalacji systemu Windows podczas uruchamiania usług Instalatora systemu Windows. W przypadku błędu zostanie wyświetlony komunikat:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Rysunek 1. wyświetla błąd instalacji systemu Windows z komunikatem: "system Windows nie może zakończyć konfigurowania systemu. Aby spróbować wznowić konfigurację, uruchom ponownie komputer. Instalator uruchamia usługi "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że system operacyjny (OS) nie może ukończyć [procesu Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Ten błąd wystąpi podczas próby początkowego uruchomienia uogólnionej maszyny wirtualnej. Jeśli wystąpi ten problem, Utwórz ponownie uogólniony obraz, ponieważ obraz jest w stanie niegotowym do wdrożenia i nie można go odzyskać.

## <a name="solution"></a>Rozwiązanie

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

Aby rozwiązać ten problem, postępuj zgodnie ze [wskazówkami platformy Azure dotyczącymi przygotowania/przechwytywania obrazu](../windows/upload-generalized-managed.md) i przygotowania nowego uogólnionego obrazu.