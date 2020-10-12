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
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90078812"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Rozwiązywanie problemów polegających na tym, że system Windows nie mógł zakończyć konfigurowania systemu

Ten artykuł zawiera kroki rozwiązywania problemów, w których proces Sysprep uniemożliwia rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zostanie wyświetlony komunikat o błędzie instalacji systemu Windows podczas uruchamiania usług Instalatora systemu Windows. W przypadku błędu zostanie wyświetlony komunikat:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Rysunek 1. wyświetla błąd instalacji systemu Windows z komunikatem: "system Windows nie może zakończyć konfigurowania systemu. Aby spróbować wznowić konfigurację, uruchom ponownie komputer. Instalator uruchamia usługi "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że system operacyjny (OS) nie może ukończyć [procesu Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview). Ten błąd wystąpi podczas próby początkowego uruchomienia uogólnionej maszyny wirtualnej. Jeśli wystąpi ten problem, Utwórz ponownie uogólniony obraz, ponieważ obraz jest w stanie niegotowym do wdrożenia i nie można go odzyskać.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, postępuj zgodnie ze [wskazówkami platformy Azure dotyczącymi przygotowania/przechwytywania obrazu](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) i przygotowania nowego uogólnionego obrazu.
