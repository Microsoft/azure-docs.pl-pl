---
title: Odzyskiwanie po awarii urządzenia z Azure Stack Edge
description: Opisuje sposób odzyskania z poziomu urządzenia z niepowodzeniem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ef99ff874376e0d76e45e4b0b2c36a78d938130b
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844009"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Odzyskiwanie po awarii urządzenia GPU z niepowodzeniem Azure Stack Edge 

W tym artykule opisano sposób odzyskania z niedopuszczalnej awarii na urządzeniu z systemem Azure Stack Edge z procesorem GPU. Niedopuszczalne awarie na urządzeniu z procesorem GPU w systemie Azure Stack Edge wymaga zastąpienia urządzenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że masz:

- Nawiązano kontakt pomoc techniczna firmy Microsoft dotyczący awarii urządzenia i zaleca się zastępowanie urządzeń. 
- Należy wykonać kopię zapasową konfiguracji urządzenia zgodnie z opisem w artykule [przygotowanie do awarii urządzenia](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Konfiguruj urządzenie zastępcze

Gdy urządzenie napotka błąd niedopuszczalny, należy zamówić urządzenie zamienne. Kroki konfiguracji urządzenia zastępującego pozostają takie same. 

Pobierz informacje o konfiguracji urządzenia, których kopia zapasowa została utworzona na urządzeniu, które się nie powiodło. Te informacje zostaną użyte do skonfigurowania urządzenia zastępczego.  

Wykonaj następujące kroki, aby skonfigurować urządzenie zamienne:

1. Zbierz informacje wymagane na liście [kontrolnej wdrożenia](azure-stack-edge-gpu-deploy-checklist.md). Należy używać informacji zapisanych w poprzedniej konfiguracji urządzenia. 
1. Zamów nowe urządzenie z taką samą konfiguracją jak ta, która się nie powiodła.  Aby złożyć zamówienie, [Utwórz nowy zasób Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#) w Azure Portal.
1. [Rozpakowywanie](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), montowanie w [stojaku](azure-stack-edge-gpu-deploy-install.md#rack-the-device) i Podłączanie [kabli do urządzenia](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Nawiąż połączenie z lokalnym interfejsem użytkownika urządzenia](azure-stack-edge-gpu-deploy-connect.md).
1. Skonfiguruj sieć przy użyciu tych samych adresów IP, które były używane w starym urządzeniu. Minimalizuje to wpływ na wszystkie komputery klienckie używane w danym środowisku. Zobacz jak [skonfigurować ustawienia sieciowe](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Przypisz tę samą nazwę urządzenia i domenę DNS jak stare urządzenie. Dzięki temu klienci mogą korzystać z tej samej nazwy urządzenia, aby komunikować się z nowym urządzeniem. Zobacz jak [skonfigurować ustawienie urządzenia](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Skonfiguruj certyfikaty na nowym urządzeniu w taki sam sposób jak w przypadku starego urządzenia. Należy pamiętać, że nowe urządzenie ma nowy numer seryjny węzła. Jeśli na starym urządzeniu użyto własnych certyfikatów, należy uzyskać nowy certyfikat węzła. Zobacz jak [skonfigurować certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Pobierz klucz aktywacji z Azure Portal i aktywuj nowe urządzenie. Zapoznaj się z tematem jak [aktywować urządzenie](azure-stack-edge-gpu-deploy-activate.md).

Teraz można przystąpić do wdrażania obciążeń, które były uruchomione na starym urządzeniu.

## <a name="restore-edge-cloud-shares"></a>Przywróć udziały w chmurze usługi Edge

Wykonaj następujące kroki, aby przywrócić dane z udziałów w chmurze Edge na urządzeniu:

1. [Dodaj udziały](azure-stack-edge-j-series-manage-shares.md#add-a-share) o tych samych nazwach udziałów utworzonych wcześniej na urządzeniu zakończonym niepowodzeniem. Upewnij się, że podczas tworzenia udziałów **Wybierz pozycję kontener obiektów BLOB** , aby **użyć istniejącej** opcji, a następnie wybierz kontener, który był używany z poprzednim urządzeniem.
1. [Dodaj użytkowników](azure-stack-edge-j-series-manage-users.md#add-a-user) , którzy mieli dostęp do poprzedniego urządzenia.
1. [Dodaj konta magazynu](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) skojarzone z udziałami wcześniej na urządzeniu. Podczas tworzenia kont magazynu Edge wybierz z istniejącego kontenera i wskaż kontener, który został zmapowany na konto usługi Azure Storage zamapowane na poprzednim urządzeniu. Wszystkie dane z urządzenia, które zostały zapisana na koncie magazynu brzegowego na poprzednim urządzeniu, zostały przekazane do wybranego kontenera magazynu na mapowanym koncie usługi Azure Storage.
1. [Odśwież dane udziału](azure-stack-edge-j-series-manage-shares.md#refresh-shares) z platformy Azure. Spowoduje to wyciągnięcie wszystkich danych w chmurze z istniejącego kontenera do udziałów.

## <a name="restore-edge-local-shares"></a>Przywróć lokalne udziały krawędzi

Aby przygotować się do potencjalnego błędu urządzenia, można wdrożyć jedno z następujących rozwiązań do tworzenia kopii zapasowych w celu ochrony lokalnych danych udziałów z obciążeń Kubernetes lub IoT:

| Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
| Firmę                        | Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |

Po całkowitym skonfigurowaniu urządzenia zastępczego włącz je na potrzeby lokalnego magazynu. 

Wykonaj następujące kroki, aby odzyskać dane z udziałów lokalnych: 

1. [Skonfiguruj obliczenia na urządzeniu](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Dodaj udział lokalny](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) .
1. Uruchom procedurę odzyskiwania dostarczoną przez wybór rozwiązania do ochrony danych. Zobacz odwołania z powyższej tabeli.

## <a name="restore-vm-files-and-folders"></a>Przywracanie plików i folderów maszyny wirtualnej

Aby przygotować się do potencjalnego błędu urządzenia, można wdrożyć jedno z następujących rozwiązań do tworzenia kopii zapasowych w celu ochrony danych na maszynach wirtualnych:



| Rozwiązania do tworzenia kopii zapasowych        | Obsługiwane systemy operacyjne   | Tematy pomocy                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) dla Azure Backup | Windows        | [Informacje o agencie MARS](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integracja, rozwiązanie do tworzenia kopii zapasowych i odzyskiwania](https://www.cohesity.com/solution/cloud/azure) <br>Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.                          |
| Firmę                 | Windows, Linux | Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.                    |

Po całkowitym skonfigurowaniu urządzenia zastępczego można ponownie wdrożyć maszyny wirtualne z wcześniej używanym obrazem maszyny wirtualnej. 

Wykonaj następujące kroki, aby odzyskać dane na maszynach wirtualnych:
 
1. [Wdróż maszynę wirtualną na podstawie obrazu maszyny wirtualnej](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) na urządzeniu. 
1. Zainstaluj wybrane rozwiązanie do ochrony danych na maszynie wirtualnej.
1. Uruchom procedurę odzyskiwania dostarczoną przez wybór rozwiązania do ochrony danych. Zobacz odwołania z powyższej tabeli.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób [zwrócić urządzenie Azure Stack Edge](azure-stack-edge-return-device.md).
