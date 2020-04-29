---
title: Skonfiguruj maszynę wirtualną hostowaną na Microsoft Azure dla portalu Azure Marketplace
description: Wyjaśnia, jak zmieniać rozmiar, aktualizować i uogólniać maszynę wirtualną hostowaną na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4cb247a3e64f8d44cc64010dde40963f4e9a1993
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146099"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurowanie maszyny wirtualnej hostowanej na platformie Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule wyjaśniono, jak zmieniać rozmiar, aktualizować i uogólniać maszynę wirtualną hostowaną na platformie Azure.  Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia z portalu Azure Marketplace.

## <a name="sizing-the-vhds"></a>Ustalanie wielkości dysków VHD

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
W przypadku wybrania jednej z wstępnie skonfigurowanych maszyn wirtualnych z systemem operacyjnym (i opcjonalnie dodatkowymi usługami) wybrano już standardowy rozmiar maszyny wirtualnej platformy Azure, zgodnie z opisem na [karcie jednostki SKU maszyny wirtualnej](./cpp-skus-tab.md).  Zalecanym podejściem jest uruchomienie rozwiązania ze wstępnie skonfigurowanym systemem operacyjnym.  Jeśli jednak instalujesz system operacyjny ręcznie, należy zmienić rozmiar podstawowego wirtualnego dysku twardego w obrazie maszyny wirtualnej:

- Dla systemu Windows dysk VHD systemu operacyjnego powinien być utworzony jako dysk o stałym formacie 127-128 GB. 
- W przypadku systemu Linux ten wirtualny dysk twardy powinien zostać utworzony jako dysk VHD o stałym rozmiarze 30-50 GB.

Jeśli rozmiar fizyczny jest mniejszy niż 127-128 GB, wirtualny dysk twardy powinien być rozrzedzony. Podstawowe obrazy systemu Windows i SQL Server podane już spełniają te wymagania, dlatego nie należy zmieniać formatu ani rozmiaru wirtualnego dysku twardego. 

Dyski danych mogą mieć rozmiar maksymalnie 1 TB. Podczas wybierania ich rozmiaru należy pamiętać, że klienci nie mogą zmieniać rozmiarów wirtualnych dysków twardych w ramach obrazu w czasie wdrażania. Wirtualne dyski twarde z danymi powinny zostać utworzone jako dyski VHD o stałym formacie. Powinny one również być rozrzedzone. Dyski danych mogą początkowo być puste lub zawierać dane.


## <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

Podstawowe obrazy maszyn wirtualnych systemu operacyjnego zawierają najnowsze aktualizacje do daty ich opublikowania. Przed opublikowaniem utworzonego wirtualnego dysku twardego systemu operacyjnego upewnij się, że zaktualizowano system operacyjny i wszystkie zainstalowane usługi ze wszystkimi najnowszymi poprawkami zabezpieczeń i konserwacji.

W przypadku systemu Windows Server 2016 Uruchom polecenie **Check for Updates** .  W przeciwnym razie w przypadku starszych wersji systemu Windows Zapoznaj [się z tematem jak uzyskać aktualizację za Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update automatycznie zainstaluje najnowsze krytyczne i ważne aktualizacje zabezpieczeń.

W przypadku dystrybucji systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub narzędzia graficznego.  Na przykład Ubuntu Linux udostępnia polecenie [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) oraz narzędzie [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) do aktualizowania systemu operacyjnego.


## <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe sprawdzenia zabezpieczeń

Należy zachować wysoki poziom zabezpieczeń obrazów rozwiązań w portalu Azure Marketplace.  W poniższym artykule przedstawiono listę kontrolną konfiguracji i procedur zabezpieczeń, które pomagają w tym celu: [zalecenia dotyczące zabezpieczeń obrazów w portalu Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Niektóre z tych zaleceń dotyczą obrazów opartych na systemie Linux, ale większość obrazów maszyn wirtualnych. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

Jeśli wymagana jest dodatkowa konfiguracja, Zalecanym podejściem jest użycie zaplanowanego zadania uruchamianego przy uruchamianiu w celu wprowadzenia ostatecznych zmian w maszynie wirtualnej po jej wdrożeniu.  Należy również wziąć pod uwagę następujące zalecenia:
- Jeśli jest to zadanie uruchamiane raz, zalecane jest, aby zadanie zostało usunięte po pomyślnym zakończeniu.
- Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski, które zawsze są spełnione. Dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym.

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby osiągnąć tę możliwość, należy *uogólniony*wirtualny dysk twardy systemu operacyjnego, operacji, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="windows"></a>Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą [narzędzia Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Jeśli później zaktualizujesz lub ponownie skonfigurujesz system operacyjny, musisz ponownie uruchomić program Sysprep. 

> [!WARNING]
>  Ponieważ aktualizacje mogą być uruchamiane automatycznie, po uruchomieniu programu Sysprep należy wyłączyć maszynę wirtualną do momentu jej wdrożenia.  To zamknięcie spowoduje uniknięcie kolejnych aktualizacji w przypadku wprowadzania zmian specyficznych dla wystąpienia w systemie operacyjnym VHD lub zainstalowanych usługach.

Aby uzyskać więcej informacji na temat uruchamiania programu Sysprep, zobacz [procedura uogólniania wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

Poniższy dwuetapowy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną. Te dwa kroki stanowią jedynie podstawowe informacje o procesie. Aby uzyskać więcej informacji na temat tych dwóch kroków i przyczyn, które należy wykonać, zobacz [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md). Na potrzeby tworzenia wirtualnego dysku twardego dla oferty portalu Azure Marketplace można zatrzymać, gdy dojdziesz do sekcji "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

#### <a name="remove-the-azure-linux-agent"></a>Usuwanie agenta systemu Linux platformy Azure
1.  Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
2.  W oknie SSH wpisz następujące polecenie: <br/>
    `sudo waagent -deprovision+user`
3.  Wpisz `y` , aby kontynuować. (Można dodać `-force` parametr do poprzedniego polecenia, unikając tego kroku potwierdzenia).
4.  Po zakończeniu wykonywania polecenia wpisz `exit` polecenie, aby zamknąć klienta SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Przechwyć obraz
1.  Przejdź do Azure Portal, wybierz grupę zasobów (RG), a następnie usuń przydział maszyny wirtualnej.
2.  Wirtualny dysk twardy został uogólniony teraz i można utworzyć nową maszynę wirtualną za pomocą tego wirtualnego dysku twardego.


## <a name="create-one-or-more-copies"></a>Utwórz co najmniej jedną kopię

Tworzenie kopii maszyny wirtualnej jest często przydatne w przypadku tworzenia kopii zapasowych, testowania, dostosowywania trybu failover lub równoważenia obciążenia, w celu oferowania różnych konfiguracji rozwiązania i tak dalej. Aby uzyskać informacje na temat duplikowania i pobierania podstawowego wirtualnego dysku twardego, aby utworzyć niezarządzany klon, zobacz:

- Maszyna wirtualna z systemem Linux: [pobieranie wirtualnego dysku twardego z systemem Linux z platformy Azure](../../../virtual-machines/linux/download-vhd.md)
- Maszyna wirtualna z systemem Windows: [pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Następne kroki

Gdy maszyna wirtualna zostanie wycofana, zostanie cofnięta alokacja i utworzono obraz maszyny wirtualnej. Możesz teraz [wdrożyć maszynę wirtualną na podstawie wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md).
