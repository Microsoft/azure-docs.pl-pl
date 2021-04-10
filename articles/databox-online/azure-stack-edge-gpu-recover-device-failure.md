---
title: Odzyskiwanie po awarii urządzenia z Azure Stack Edge
description: Opisuje sposób odzyskania z poziomu urządzenia z niepowodzeniem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636644"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Odzyskiwanie po awarii urządzenia GPU z niepowodzeniem Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

W tym artykule opisano sposób odzyskania z niedopuszczalnej awarii na urządzeniu z systemem Azure Stack Edge z procesorem GPU. Niedopuszczalne awarie na urządzeniu z procesorem GPU w systemie Azure Stack Edge wymaga zastąpienia urządzenia.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że masz:

- Nawiązano kontakt pomoc techniczna firmy Microsoft dotyczący awarii urządzenia i zaleca się zastępowanie urządzeń. 
- Należy wykonać kopię zapasową konfiguracji urządzenia zgodnie z opisem w artykule [przygotowanie do awarii urządzenia](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Konfiguruj urządzenie zastępcze

Gdy urządzenie napotka błąd niedopuszczalny, należy zamówić urządzenie zamienne. Kroki konfiguracji urządzenia zastępującego pozostają takie same. 

Pobierz informacje o konfiguracji urządzenia, których kopia zapasowa została utworzona na urządzeniu, które się nie powiodło. Te informacje zostaną użyte do skonfigurowania urządzenia zastępczego.  

Wykonaj następujące kroki, aby skonfigurować urządzenie zamienne:

1. Zbierz informacje wymagane na liście [kontrolnej wdrożenia](azure-stack-edge-gpu-deploy-checklist.md). Można użyć informacji zapisanych w poprzedniej konfiguracji urządzenia. 
1. Zamów nowe urządzenie z taką samą konfiguracją jak ta, która się nie powiodła.  Aby złożyć zamówienie, [Utwórz nowy zasób Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#) w Azure Portal.
1. [Rozpakowywanie](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), montowanie w [stojaku](azure-stack-edge-gpu-deploy-install.md#rack-the-device) i Podłączanie [kabli do urządzenia](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Nawiąż połączenie z lokalnym interfejsem użytkownika urządzenia](azure-stack-edge-gpu-deploy-connect.md).
1. Skonfiguruj sieć przy użyciu tych samych adresów IP, które były używane w starym urządzeniu. Użycie tych samych adresów IP minimalizuje wpływ na wszystkie komputery klienckie używane w danym środowisku. Zobacz jak [skonfigurować ustawienia sieciowe](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Przypisz tę samą nazwę urządzenia i domenę DNS jak stare urządzenie. Dzięki temu klienci mogą korzystać z tej samej nazwy urządzenia, aby komunikować się z nowym urządzeniem. Zobacz jak [skonfigurować ustawienie urządzenia](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Skonfiguruj certyfikaty na nowym urządzeniu w taki sam sposób jak w przypadku starego urządzenia. Należy pamiętać, że nowe urządzenie ma nowy numer seryjny węzła. Jeśli na starym urządzeniu użyto własnych certyfikatów, należy uzyskać nowy certyfikat węzła. Zobacz jak [skonfigurować certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Pobierz klucz aktywacji z Azure Portal i aktywuj nowe urządzenie. Zapoznaj się z tematem jak [aktywować urządzenie](azure-stack-edge-gpu-deploy-activate.md).

Teraz można przystąpić do wdrażania obciążeń, które były uruchomione na starym urządzeniu.

## <a name="restore-edge-cloud-shares"></a>Przywróć udziały w chmurze usługi Edge

Wykonaj następujące kroki, aby przywrócić dane z udziałów w chmurze Edge na urządzeniu:

1. [Dodaj udziały](azure-stack-edge-gpu-manage-shares.md#add-a-share) o tych samych nazwach udziałów utworzonych wcześniej na urządzeniu zakończonym niepowodzeniem. Upewnij się, że podczas tworzenia udziałów **Wybierz pozycję kontener obiektów BLOB** , aby **użyć istniejącej** opcji, a następnie wybierz kontener, który był używany z poprzednim urządzeniem.
1. [Dodaj użytkowników](azure-stack-edge-gpu-manage-users.md#add-a-user) , którzy mieli dostęp do poprzedniego urządzenia.
1. [Dodaj konta magazynu](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account) skojarzone z udziałami wcześniej na urządzeniu. Podczas tworzenia kont magazynu Edge wybierz z istniejącego kontenera i wskaż kontener, który został zmapowany na konto usługi Azure Storage zamapowane na poprzednim urządzeniu. Wszystkie dane z urządzenia, które zostały zapisana na koncie magazynu brzegowego na poprzednim urządzeniu, zostały przekazane do wybranego kontenera magazynu na mapowanym koncie usługi Azure Storage.
1. [Odśwież dane udziału](azure-stack-edge-gpu-manage-shares.md#refresh-shares) z platformy Azure. Spowoduje to pobranie wszystkich danych w chmurze z istniejącego kontenera do udziałów.

## <a name="restore-edge-local-shares"></a>Przywróć lokalne udziały krawędzi

Aby przygotować się do potencjalnego błędu urządzenia, można wdrożyć jedno z następujących rozwiązań do tworzenia kopii zapasowych w celu ochrony lokalnych danych udziałów z obciążeń Kubernetes lub IoT:

| Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault. |
| Firmę                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |

Po całkowitym skonfigurowaniu urządzenia zastępczego włącz je na potrzeby lokalnego magazynu. 

Wykonaj następujące kroki, aby odzyskać dane z udziałów lokalnych:

1. [Skonfiguruj obliczenia na urządzeniu](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Dodaj udział lokalny](azure-stack-edge-gpu-manage-shares.md#add-a-local-share) .
1. Uruchom procedurę odzyskiwania dostarczoną przez wybór rozwiązania do ochrony danych. Zobacz odwołania z powyższej tabeli.

## <a name="restore-vm-files-and-folders"></a>Przywracanie plików i folderów maszyny wirtualnej

Aby przygotować się do potencjalnego błędu urządzenia, można wdrożyć jedno z następujących rozwiązań do tworzenia kopii zapasowych w celu ochrony danych na maszynach wirtualnych:



| Rozwiązania do tworzenia kopii zapasowych        | Obsługiwany system operacyjny   | Odwołanie                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) dla Azure Backup | Windows        | [Informacje o agencie MARS](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Integracja Microsoft Azure, Krótki opis rozwiązania Backup & Recovery](https://www.cohesity.com/solution/cloud/azure) <br>Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.
| Firmę                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |

Po całkowitym skonfigurowaniu urządzenia zastępczego można ponownie wdrożyć maszyny wirtualne z wcześniej używanym obrazem maszyny wirtualnej. 

Wykonaj następujące kroki, aby odzyskać dane na maszynach wirtualnych:
 
1. [Wdróż maszynę wirtualną na podstawie obrazu maszyny wirtualnej](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) na urządzeniu. 
1. Zainstaluj wybrane rozwiązanie do ochrony danych na maszynie wirtualnej.
1. Uruchom procedurę odzyskiwania dostarczoną przez wybór rozwiązania do ochrony danych. Zobacz odwołania z powyższej tabeli.

## <a name="restore-a-kubernetes-deployment"></a>Przywracanie wdrożenia Kubernetes

Jeśli wykonano wdrożenie Kubernetes za pośrednictwem usługi Azure ARC, można przywrócić wdrożenie po awarii urządzenia niedopuszczalnego. Należy ponownie wdrożyć aplikację klienta/kontenery z repozytorium, w `git` którym jest przechowywana definicja aplikacji. [Informacje o wdrażaniu Kubernetes za pomocą usługi Azure Arc](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób [zwrócić urządzenie Azure Stack Edge](azure-stack-edge-return-device.md).
