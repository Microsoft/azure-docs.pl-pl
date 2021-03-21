---
title: Przygotowywanie do niepowodzenia urządzenia Azure Stack EDGE Pro
description: Opisuje, jak uzyskać zamiennik dla urządzenia z niepowodzeniem programu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442982"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Przygotowanie do niepowodzenia urządzenia GPU w Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ten artykuł ułatwia przygotowanie się do awarii urządzenia przez szczegółowe instrukcje dotyczące zapisywania i tworzenia kopii zapasowej konfiguracji i danych urządzenia na urządzeniu z systemem Azure Stack Edge. 

Artykuł nie obejmuje kroków związanych z wykonywaniem kopii zapasowych kontenerów Kubernetes i IoT wdrożonych na urządzeniu z systemem Azure Stack Edge. 

## <a name="understand-device-failures"></a>Omówienie błędów urządzeń

Urządzenie GPU w programie Azure Stack Edge może obsłużyć dwa typy awarii sprzętu.

- Dopuszczalne błędy, które wymagają zastąpienia składnika sprzętowego. Te błędy pozwolą na działanie urządzenia w stanie obniżonej wydajności. Przykłady tych niepowodzeń obejmują pojedynczą niepowodzeniem jednostkę zasilacza (PSU) lub jeden uszkodzony dysk na urządzeniu. W każdym z tych przypadków urządzenie może nadal działać. Skontaktuj się pomoc techniczna firmy Microsoft najszybciej, jak to możliwe, aby zastąpić składniki uszkodzone.

- Niedopuszczalne błędy, które wymagają zastąpienia całego urządzenia — na przykład jeśli na urządzeniu nie powiodło się dwa dyski. W takich przypadkach skontaktuj się pomoc techniczna firmy Microsoft od razu. Po ustaleniu, że zachodzi potrzeba zastępowania urządzenia, pomoc techniczna pomoże zastąpić urządzenie Azure Stack Edge.

Aby przygotować się do niedopuszczalnych awarii, należy wykonać kopię zapasową następujących elementów na urządzeniu:

- Informacje o konfiguracji urządzenia
- Dane w udziałach lokalnych Edge i udziały w chmurze Edge
- Pliki i foldery skojarzone z maszynami wirtualnymi działającymi na urządzeniu


## <a name="back-up-device-configuration"></a>Tworzenie kopii zapasowej konfiguracji urządzenia

Podczas początkowej konfiguracji urządzenia ważne jest, aby zachować kopię informacji o konfiguracji urządzenia, jak opisano na liście [kontrolnej wdrożenia](azure-stack-edge-gpu-deploy-checklist.md). Podczas odzyskiwania informacje o konfiguracji będą używane do zastosowania na nowym urządzeniu zastępczym. 

## <a name="protect-device-data"></a>Ochrona danych na urządzeniu

Dane urządzenia mogą mieć jeden z następujących typów:

- Dane w udziałach w chmurze Edge
- Dane w udziałach lokalnych
- Pliki i foldery na maszynach wirtualnych

W poniższych sekcjach omówiono kroki i zalecenia dotyczące ochrony każdego z tych typów danych na urządzeniu.

## <a name="protect-data-in-edge-cloud-shares"></a>Ochrona danych w udziałach w chmurze Edge

Na platformie Azure można utworzyć udziały w chmurze, które mają dane warstwy z urządzenia. W zależności od dostępnej przepustowości sieci Skonfiguruj szablony przepustowości na urządzeniu, aby zminimalizować utratę danych w przypadku wystąpienia awarii niedopuszczalnej.

> [!IMPORTANT]
> Jeśli urządzenie ma niedopuszczalny błąd, dane lokalne, które nie są warstwą z urządzenia do platformy Azure, mogą zostać utracone. 

## <a name="protect-data-in-edge-local-shares"></a>Ochrona danych w udziałach lokalnych Edge

Jeśli wdrażasz Kubernetes lub IoT Edge, skonfiguruj, aby zapisać dane aplikacji na urządzeniu lokalnie i nie synchronizować się z usługą Azure Storage. Dane są przechowywane w udziale na urządzeniu. Może się okazać, że ważne jest, aby utworzyć kopię zapasową danych w tych udziałach.

Poniższe rozwiązania do ochrony danych innych firm mogą udostępniać rozwiązanie do tworzenia kopii zapasowych danych w lokalnych udziałach SMB lub NFS. 

| Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.          |
| Firmę                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |


## <a name="protect-files-and-folders-on-vms"></a>Ochrona plików i folderów na maszynach wirtualnych

Azure Stack Edge współpracuje z Azure Backup i innymi rozwiązaniami do ochrony danych innych firm, aby udostępnić rozwiązanie do tworzenia kopii zapasowych w celu ochrony danych zawartych w maszynach wirtualnych wdrożonych na urządzeniu. Poniższa tabela zawiera odwołania do dostępnych rozwiązań, spośród których można dokonać wyboru.


| Rozwiązania do tworzenia kopii zapasowych        | Obsługiwany system operacyjny   | Dokumentacja                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) dla Azure Backup | Windows        | [Informacje o agencie MARS](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Integracja Microsoft Azure, Krótki opis rozwiązania Backup & Recovery](https://www.cohesity.com/solution/cloud/azure) <br>Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.                          |
| Firmę                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak przeprowadzić [odzyskiwanie z niepowodzenia urządzenia GPU z systemem Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md).