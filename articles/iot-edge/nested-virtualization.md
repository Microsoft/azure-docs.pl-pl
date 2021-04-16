---
title: Zagnieżdżona wirtualizacja dla Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Dowiedz się, jak nawigować po zagnieżdżonych wirtualizacjach w Azure IoT Edge dla systemu Linux w systemie Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496592"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Zagnieżdżona wirtualizacja dla Azure IoT Edge dla systemu Linux w systemie Windows
Istnieją dwie formy wirtualizacji zagnieżdżone zgodne z Azure IoT Edge dla systemu Linux w systemie Windows. Użytkownicy mogą zdecydować się na wdrożenie za pośrednictwem lokalnej maszyny wirtualnej lub maszyny wirtualnej platformy Azure. Ten artykuł zawiera informacje dla użytkowników dotyczące tego, która opcja jest najlepsza dla ich scenariusza, i zapewnia wgląd w wymagania dotyczące konfiguracji.

> [!NOTE]
>
> Upewnij się, aby włączyć [jedną opcję sieci dla](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) wirtualizacji zagnieżdżonych. Jeśli tego nie zrobisz, spowoduje to błędy instalacji przepływu EFLOW. 

## <a name="deployment-on-local-vm"></a>Wdrażanie na lokalnej maszynie wirtualnej
Jest to podejście bazowe dla każdej maszyny wirtualnej z systemem Windows, która hostuje maszyny Azure IoT Edge dla systemu Linux w systemie Windows. W tym przypadku należy włączyć zagnieżdżoną wirtualizację przed rozpoczęciem wdrażania. Przeczytaj [temat Run Hyper-V in a Virtual Machine with Nested Virtualization](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) (Uruchamianie funkcji Hyper-V na maszynie wirtualnej z zagnieżdżona wirtualizacją), aby uzyskać więcej informacji na temat sposobu konfigurowania tego scenariusza.

Jeśli używasz systemu Windows Server, upewnij się, że [instalujesz rolę funkcji Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)

## <a name="deployment-on-azure-vms"></a>Wdrażanie na maszynach wirtualnych platformy Azure
Jeśli zdecydujesz się na wdrożenie na maszyny wirtualne platformy Azure, pamiętaj, że nie ma żadnego przełącznika zewnętrznego. Azure IoT Edge dla systemu Linux w systemie Windows również nie jest zgodna na maszynie wirtualnej platformy Azure z uruchomionym serwerem SKU, chyba że zostanie wykonany określony skrypt, który uruchamia przełącznik domyślny. Aby uzyskać więcej informacji na temat sposobu przełączania domyślnego, zobacz sekcję [Windows Server poniżej.](#windows-server) 

> [!NOTE]
>
> Wszystkie maszyny wirtualne platformy Azure, które mają hostować przepływ EFLOW, muszą być maszyną wirtualną [obsługą wirtualizacji zagnieżdżonych](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Ograniczone użycie przełącznika zewnętrznego
W każdym scenariuszu, w którym maszyna wirtualna nie może uzyskać adresu IP za pośrednictwem przełącznika zewnętrznego, funkcja wdrażania automatycznie używa wewnętrznego przełącznika domyślnego dla wdrożenia. Oznacza to, że zarządzanie maszyną wirtualną z programem Azure IoT Edge dla systemu Linux może być wykonywane tylko na samym urządzeniu docelowym (tj. nawiązanie połączenia z maszyną wirtualną z systemem Azure IoT Edge dla systemu Linux za pośrednictwem rozszerzenia SSH programu PowerShell WAC jest możliwe tylko w przypadku hosta lokalnego).

## <a name="windows-server"></a>Windows Server
W przypadku użytkowników systemu Windows Server należy pamiętać, Azure IoT Edge dla systemu Linux w systemie Windows nie obsługuje automatycznie przełącznika domyślnego.

* Konsekwencje dla lokalnej maszyny wirtualnej: Upewnij się, że maszyna wirtualna EFLOW może uzyskać adres IP za pośrednictwem przełącznika zewnętrznego.

* Konsekwencje dla maszyny wirtualnej platformy Azure: ponieważ na maszynach wirtualnych platformy Azure nie ma przełącznika zewnętrznego, nie można wdrożyć przepływu EFLOW przed skonfigurowaniem przełącznika wewnętrznego na serwerze.

Domyślnie nie ma przełącznika domyślnego dla jednostki SKU serwera (niezależnie od tego, czy jednostki SKU serwera są uruchamiane na maszynie wirtualnej platformy Azure, czy nie). W przypadku wdrażania na maszynie wirtualnej platformy Azure, na której nie można użyć przełącznika zewnętrznego, należy ręcznie skonfigurować i skonfigurować przełącznik domyślny przed Azure IoT Edge dla systemu Linux w systemie Windows. Nasze funkcje wdrażania obejmują tę funkcję, ponieważ wymaga ona konfiguracji adresu IP dla przełącznika wewnętrznego, konfiguracji nat oraz instalowania i konfigurowania serwera DHCP. Nasze funkcje wdrażania w publicznej wersji zapoznawczej stwierdzają, że te ustawienia nie zakłócają konfiguracji sieci podczas wdrożeń produkcyjnych.

* Odpowiednie informacje na temat ręcznego skonfigurowania przełącznika domyślnego można znaleźć tutaj: [How to enable nested virtualization in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (Jak włączyć zagnieżdżone wirtualizację w usłudze Azure Virtual Machines
* Dokumentacja dotycząca sposobu konfiguracji serwera DHCP dla tego scenariusza znajduje się tutaj: [Wdrażanie protokołu DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) przy użyciu Windows PowerShell
