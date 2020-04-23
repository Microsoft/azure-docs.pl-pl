---
title: Tworzenie strefyowej maszyny Wirtualnej systemu Windows za pomocą portalu Azure
description: Tworzenie maszyny Wirtualnej systemu Windows w strefie dostępności za pomocą portalu Azure
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 418d02b1f271efc1112b67e5dd12b6ba67a3fcd9
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086185"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Tworzenie maszyny wirtualnej systemu Windows w strefie dostępności za pomocą portalu Azure

W tym artykule kroki za pomocą witryny Azure portal, aby utworzyć maszynę wirtualną w strefie dostępności platformy Azure. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 

3. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Wybierz lokalizację, taką jak Wschodnia stany USA 2, która obsługuje strefy dostępności. Po zakończeniu kliknij przycisk **OK**.

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Wybierz rozmiar maszyny wirtualnej. Wybierz zalecany rozmiar lub filtr na podstawie operacji. Potwierdź, że rozmiar jest dostępny w strefie, której chcesz użyć.

    ![Wybieranie rozmiaru maszyny Wirtualnej](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. W obszarze **Ustawienia** > **Wysoka dostępność**wybierz jedną z numerowanych stref z listy rozwijanej Strefa **dostępności,** zachowaj pozostałe wartości domyślne i kliknij przycisk **OK**.

    ![Wybieranie strefy dostępności](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stronie podsumowania kliknij przycisk **Utwórz,** aby rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna zostanie przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdź strefę dla dysku zarządzanego i adresu IP

Gdy maszyna wirtualna jest wdrażana w strefie dostępności, dysk zarządzany dla maszyny Wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie publiczny adres IP jest również tworzony w tej strefie.

Ustawienia strefy dla tych zasobów można potwierdzić w portalu.  

1. Kliknij **pozycję Grupy zasobów,** a następnie nazwę grupy zasobów maszyny Wirtualnej, na przykład *myResourceGroup*.

2. Kliknij nazwę zasobu Dysk. Strona **Przegląd** zawiera szczegółowe informacje o strefie lokalizacji i dostępności zasobu.

    ![Strefa dostępności dysku zarządzanego](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kliknij nazwę zasobu Publiczny adres IP. Strona **Przegląd** zawiera szczegółowe informacje o strefie lokalizacji i dostępności zasobu.

    ![Strefa dostępności dla adresu IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [dostępności](availability.md) maszyn wirtualnych platformy Azure.
