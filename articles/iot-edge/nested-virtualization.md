---
title: Wirtualizacja zagnieżdżona dla Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Dowiedz się więcej na temat nawigowania po zagnieżdżonej wirtualizacji w Azure IoT Edge dla systemu Linux w systemie Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609657"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Wirtualizacja zagnieżdżona dla Azure IoT Edge dla systemu Linux w systemie Windows
Istnieją dwie formy zagnieżdżonej wirtualizacji zgodnej z Azure IoT Edge dla systemu Linux w systemie Windows. Użytkownicy mogą zdecydować się na wdrożenie przez lokalną maszynę wirtualną lub maszynę wirtualną platformy Azure. Ten artykuł zapewni użytkownikom przejrzystość, która opcja jest Najlepsza dla scenariusza i zapewnia wgląd w wymagania dotyczące konfiguracji.

> [!NOTE]
>
> Upewnij się, że włączono jedną [opcję netowrking](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) dla wirtualizacji zagnieżdżonej. Wykonanie tej czynności spowoduje błędy instalacji EFLOW. 

## <a name="deployment-on-local-vm"></a>Wdrożenie na lokalnej maszynie wirtualnej
Jest to podejście bazowe dla dowolnej maszyny wirtualnej z systemem Windows, która hostuje Azure IoT Edge dla systemu Linux w systemie Windows. W takim przypadku należy włączyć wirtualizację zagnieżdżoną przed rozpoczęciem wdrażania. Aby uzyskać więcej informacji na temat sposobu konfigurowania tego scenariusza, przeczytaj artykuł [Uruchamianie funkcji Hyper-V na maszynie wirtualnej z funkcją wirtualizacji zagnieżdżonej](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) .

W przypadku korzystania z systemu Windows Server należy się upewnić, że [zainstalowano rolę funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Wdrażanie na maszynach wirtualnych platformy Azure
W przypadku wybrania opcji wdrożenia na maszynach wirtualnych platformy Azure należy pamiętać, że nie istnieje przełącznik zewnętrzny według projektu. Azure IoT Edge dla systemu Linux w systemie Windows nie jest również zgodny na maszynie wirtualnej platformy Azure z uruchomioną jednostką SKU serwera, chyba że zostanie wykonany konkretny skrypt, który wywołuje przełącznik domyślny. Aby uzyskać więcej informacji na temat sposobu wyłączania przełącznika domyślnego, zapoznaj się z [sekcją systemu Windows Server](#windows-server) poniżej. 

> [!NOTE]
>
> Wszystkie maszyny wirtualne platformy Azure, które powinny hostować EFLOW, muszą być maszyną wirtualną obsługującą [wirtualizację zagnieżdżoną](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Ograniczone użycie przełącznika zewnętrznego
W każdym scenariuszu, w którym maszyna wirtualna nie może uzyskać adresu IP za pośrednictwem przełącznika zewnętrznego, funkcja wdrażania automatycznie używa wewnętrznego przełącznika domyślnego do wdrożenia. Oznacza to, że zarządzanie Azure IoT Edge dla maszyny wirtualnej z systemem Linux może być wykonywane tylko na samym urządzeniu docelowym (tj. Nawiązywanie połączenia z Azure IoT Edge dla maszyny wirtualnej z systemem Linux za pomocą rozszerzenia SSH programu WAC PowerShell jest możliwe tylko dla hosta lokalnego).

## <a name="windows-server"></a>Windows Server
W przypadku użytkowników systemu Windows Server należy pamiętać, że Azure IoT Edge dla systemu Linux w systemie Windows nie obsługują automatycznie przełącznika domyślnego.

* Konsekwencje dla lokalnej maszyny wirtualnej: Upewnij się, że maszyna wirtualna EFLOW może uzyskać adres IP za pomocą przełącznika zewnętrznego.

* Konsekwencje dla maszyny wirtualnej platformy Azure: ponieważ nie ma przełącznika zewnętrznego na maszynach wirtualnych platformy Azure, nie można wdrożyć EFLOW przed skonfigurowaniem wewnętrznego przełącznika na serwerze.

Domyślnie nie ma domyślnego przełącznika dla jednostek SKU serwera (niezależnie od tego, czy jednostka SKU serwera działa na maszynie wirtualnej platformy Azure, czy nie). Podczas wdrażania na maszynie wirtualnej platformy Azure, w której nie można użyć przełącznika zewnętrznego, domyślny przełącznik należy skonfigurować i skonfigurować ręcznie przed Azure IoT Edge dla systemu Linux w systemie Windows. Nasze funkcje wdrażania omawiają ten sposób, ponieważ wymaga konfiguracji protokołu IP dla przełącznika wewnętrznego, konfiguracji NAT i instalowania i konfigurowania serwera DHCP. Nasze funkcje wdrażania w publicznej wersji zapoznawczej nie FIDDLE się z tymi ustawieniami, aby nie zakłócać konfiguracji sieci w przypadku wdrożeń wydajnych.

* Odpowiednie informacje na temat ręcznego konfigurowania przełącznika domyślnego można znaleźć tutaj: [jak włączyć wirtualizację zagnieżdżoną na platformie Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Dokumentację dotyczącą sposobu konfigurowania serwera DHCP dla tego scenariusza można znaleźć tutaj: [wdrażanie protokołu DHCP przy użyciu programu Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
