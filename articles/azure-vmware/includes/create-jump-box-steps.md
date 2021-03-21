---
title: Utwórz pole skoku rozwiązania Azure VMware
description: Procedura tworzenia pola skoku rozwiązania Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462263"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. W grupie zasobów wybierz pozycję **+ Dodaj** , a następnie wyszukaj i wybierz pozycję **Microsoft Windows 10**, a następnie wybierz pozycję **Utwórz**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Dodaj nową maszynę wirtualną z systemem Windows 10 dla pola skoku." border="true":::

1. Wprowadź wymagane informacje w polach, a następnie wybierz pozycję **Przegląd + Utwórz**. 

   Aby uzyskać więcej informacji na temat pól, zobacz poniższą tabelę.

   | Pole | Wartość |
   | --- | --- |
   | **Subskrypcja** | Wartość jest wstępnie wypełniona dla subskrypcji należącej do grupy zasobów. |
   | **Grupa zasobów** | Wartość jest wstępnie wypełniona dla bieżącej grupy zasobów, która została utworzona w poprzednim samouczku.  |
   | **Nazwa maszyny wirtualnej** | Wprowadź unikatową nazwę maszyny wirtualnej. |
   | **Region** | Wybierz lokalizację geograficzną maszyny wirtualnej. |
   | **Opcje dostępności** | Pozostaw wybraną wartość domyślną. |
   | **Obraz** | Wybierz obraz maszyny wirtualnej. |
   | **Rozmiar** | Pozostaw wartość domyślną rozmiar. |
   | **Authentication type** (Typ uwierzytelniania)  | Wybierz pozycję **hasło**. |
   | **Nazwa użytkownika** | Wprowadź nazwę użytkownika, aby zalogować się do maszyny wirtualnej. |
   | **Password** (Hasło) | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Potwierdź hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Publiczne porty ruchu przychodzącego** | Wybierz pozycję **Brak**. W przypadku wybrania opcji Brak można użyć [dostępu JIT](../../security-center/security-center-just-in-time.md#jit-configure) , aby kontrolować dostęp do maszyny wirtualnej tylko wtedy, gdy chcesz uzyskać do niej dostęp. Alternatywnie możesz użyć [usługi Azure bastionu](../../bastion/tutorial-create-host-portal.md) , jeśli chcesz bezpiecznie uzyskać dostęp do serwera z wykorzystaniem pola szybkiego z Internetu bez uwidaczniania żadnego portu sieciowego.  |


1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia maszyny wirtualnej.

