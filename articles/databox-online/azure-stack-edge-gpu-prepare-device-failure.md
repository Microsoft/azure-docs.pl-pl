---
title: Przygotowywanie do niepowodzenia urządzenia Azure Stack EDGE Pro
description: Opisuje, jak uzyskać zamiennik dla urządzenia z niepowodzeniem programu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: f7c7caf6c01c2e7ea9aaf141a2597ef338735d3d
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173596"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Przygotowanie do niepowodzenia urządzenia GPU w Azure Stack Edge

Ten artykuł ułatwia przygotowanie się do awarii urządzenia przez szczegółowe instrukcje dotyczące zapisywania i tworzenia kopii zapasowej konfiguracji i danych urządzenia na urządzeniu z systemem Azure Stack Edge. 

Artykuł nie obejmuje kroków związanych z wykonywaniem kopii zapasowych kontenerów Kubernetes i IoT wdrożonych na urządzeniu z systemem Azure Stack Edge. 

## <a name="understand-device-failures"></a>Omówienie błędów urządzeń

Urządzenie GPU w programie Azure Stack Edge może obsłużyć dwa typy awarii sprzętu.

- Dopuszczalne błędy, które wymagają zastąpienia składnika sprzętowego. Te błędy pozwolą na działanie urządzenia w stanie obniżonej wydajności. Przykłady tych niepowodzeń obejmują pojedynczą niepowodzeniem jednostkę zasilacza (PSU) lub jeden uszkodzony dysk na urządzeniu. W każdym z tych przypadków urządzenie może nadal działać. Zaleca się, aby skontaktować się z pomoc techniczna firmy Microsoftą najwcześniej w celu zastąpienia składników zakończonych niepowodzeniem.

- Niedopuszczalne błędy, które wymagały zastąpienia całego urządzenia. Przykładem tego błędu może być niepowodzenie wystąpienia dwóch dysków na urządzeniu. W tych przypadkach skontaktujesz się z firmą pomoc techniczna firmy Microsoft i po ustaleniu, że zastępowanie urządzenia jest potrzebne, mogą one ułatwić zastępowanie Azure Stack urządzenia brzegowego.

Aby przygotować się do niedopuszczalnych awarii, należy wykonać kopię zapasową następujących danych na urządzeniu:

- Informacje na temat konfiguracji urządzenia.
- Dane, które znajdują się w lokalnych udziałach i udziałach w chmurze.
- Pliki i foldery skojarzone z maszynami wirtualnymi działającymi na urządzeniu.


## <a name="back-up-device-configuration"></a>Tworzenie kopii zapasowej konfiguracji urządzenia

Podczas początkowej konfiguracji urządzenia ważne jest, aby zachować kopię informacji o konfiguracji urządzenia, jak opisano na liście [kontrolnej wdrożenia](azure-stack-edge-gpu-deploy-checklist.md). Podczas odzyskiwania informacje o konfiguracji będą używane do zastosowania na nowym urządzeniu zastępczym. 

## <a name="protect-device-data"></a>Ochrona danych na urządzeniu

Dane urządzenia mogą mieć jeden z następujących typów:

- Dane w udziałach w chmurze Edge
- Dane w udziałach lokalnych
- Pliki i foldery na maszynach wirtualnych

W poniższych sekcjach omówiono kroki i zalecenia dotyczące ochrony każdego z tych typów danych na urządzeniu.

## <a name="protect-data-in-edge-cloud-shares"></a>Ochrona danych w udziałach w chmurze Edge

Na platformie Azure można utworzyć udziały w chmurze, które mają dane warstwy z urządzenia. W zależności od dostępnej przepustowości sieci Skonfiguruj szablony przepustowości na urządzeniu, aby zminimalizować utratę danych w przypadku niedopuszczalnego błędu.

> [!IMPORTANT] 
> Jeśli urządzenie ma niedopuszczalny błąd, dane lokalne, które nie są warstwą z urządzenia do platformy Azure, mogą zostać utracone. 

## <a name="protect-data-in-edge-local-shares"></a>Ochrona danych w udziałach lokalnych Edge

W przypadku wdrażania Kubernetes lub IoT Edge Skonfiguruj do lokalnego zapisywania danych aplikacji na urządzeniu i nie Synchronizuj z usługą Azure Storage. Dane są przechowywane w udziale na urządzeniu. Może się okazać, że ważne jest, aby utworzyć kopię zapasową danych w tych udziałach.

Poniższe rozwiązania do ochrony danych innych firm mogą udostępniać rozwiązanie do tworzenia kopii zapasowych danych w lokalnych udziałach SMB lub NFS. 

| Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
| CommVault                      | https://www.commvault.com/azure <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.          |
| Firmę                        | http://veritas.com/azure <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |


## <a name="protect-files-and-folders-on-vms"></a>Ochrona plików i folderów na maszynach wirtualnych

Azure Stack Edge współpracuje z Azure Backup i innymi rozwiązaniami do ochrony danych innych firm, aby udostępnić rozwiązanie do tworzenia kopii zapasowych w celu ochrony danych zawartych w maszynach wirtualnych wdrożonych na urządzeniu. Poniższa tabela zawiera odwołania do dostępnych rozwiązań, spośród których można dokonać wyboru.


| Rozwiązania do tworzenia kopii zapasowych        | Obsługiwane systemy operacyjne   | Dokumentacja                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) dla Azure Backup | Windows        | [Informacje o agencie MARS](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integracja, rozwiązanie do tworzenia kopii zapasowych i odzyskiwania](https://www.cohesity.com/solution/cloud/azure) <br>Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br>Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.                          |
| Firmę                 | Windows, Linux | http://veritas.com/azure <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.                    |



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak przeprowadzić [odzyskiwanie z niepowodzenia urządzenia GPU z systemem Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md).
