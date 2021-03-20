---
title: Instalacja SAP HANA na maszynach wirtualnych platformy Azure | Microsoft Docs "
description: Przewodnik dotyczący instalacji SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670967"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalacja SAP HANA na maszynach wirtualnych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik pomaga wskazać odpowiednie zasoby w celu pomyślnego wdrożenia platformy HANA w usłudze Azure Virtual Machines. Ten przewodnik ma na celu wskazanie zasobów dokumentacji, które należy sprawdzić przed zainstalowaniem SAP HANA na maszynie wirtualnej platformy Azure. Dzięki temu można wykonać odpowiednie kroki, aby zakończyć z obsługiwaną konfiguracją SAP HANA na maszynach wirtualnych platformy Azure.  

> [!NOTE]
> W tym przewodniku opisano wdrożenia SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać informacje na temat sposobu wdrażania SAP HANA w dużych wystąpieniach platformy HANA, zobacz [jak zainstalować i skonfigurować SAP HANA (duże wystąpienia) na platformie Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto również założenie, że znasz:
* SAP HANA i SAP NetWeaver oraz sposób instalacji lokalnych.
* Jak zainstalować i obsługiwać wystąpienia aplikacji SAP HANA i SAP na platformie Azure.
* Koncepcje i procedury opisane w:
   * Planowanie wdrożenia oprogramowania SAP na platformie Azure, w tym usługi Azure Virtual Network Planning i Azure Storage. Zobacz temat [SAP NetWeaver na platformie Virtual Machines Azure — przewodnik planowania i implementacji](./planning-guide.md)
   * Zasady wdrażania i sposoby wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrożenie usługi Azure Virtual Machines dla oprogramowania SAP](./deployment-guide.md)
   * Pojęcia dotyczące wysokiej dostępności SAP HANA zgodnie z opisem w [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Krok po kroku przed wdrożeniem
W tej sekcji przedstawiono różne czynności, które należy wykonać przed rozpoczęciem instalacji SAP HANA na maszynie wirtualnej platformy Azure. Zamówienie jest wyliczane i należy je stosować jako Wyliczenie:

1. Nie wszystkie możliwe scenariusze wdrażania są obsługiwane na platformie Azure. W związku z tym należy sprawdzić dokument [obciążenie SAP na maszynach wirtualnych platformy Azure obsługiwane scenariusze](./sap-planning-supported-configurations.md) dla scenariusza, w którym myślisz o wdrożeniu SAP HANA. Jeśli scenariusza nie ma na liście, należy założyć, że nie został przetestowany i w związku z tym nie jest obsługiwany.
2. Przy założeniu, że masz niezbędny pomysł na wymagania dotyczące pamięci dla wdrożenia SAP HANA, musisz znaleźć dopasowaną maszynę wirtualną platformy Azure. Nie wszystkie maszyny wirtualne certyfikowane dla oprogramowania SAP NetWeaver, zgodnie z opisem w temacie [Pomoc techniczna sap #1928533](https://launchpad.support.sap.com/#/notes/1928533), są SAP HANA certyfikowane. Źródłem prawdy dla SAP HANA certyfikowane maszyny wirtualne platformy Azure jest witryna internetowa [SAP HANA katalogu sprzętu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Jednostki zaczynające się od **S** to jednostki [dużych wystąpień usługi Hana](./hana-overview-architecture.md) , a nie maszyny wirtualne platformy Azure.
3. Różne typy maszyn wirtualnych platformy Azure mają różne minimalne wersje systemu operacyjnego w systemie SUSE Linux lub Red Hat Linux. W witrynie sieci Web [SAP HANA Katalog sprzętu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)kliknij wpis na liście SAP HANA jednostek certyfikowanych, aby uzyskać szczegółowe dane tej jednostki. Oprócz obsługiwanego obciążenia platformy HANA wersje systemu operacyjnego obsługiwane przez te jednostki dla SAP HANA są wymienione na liście
4. W przypadku wersji systemu operacyjnego należy wziąć pod uwagę pewne minimalne wersje jądra. Te minimalne wersje zostały udokumentowane w tych informacjach o pomocy technicznej SAP:
    - [Uwaga dotycząca pomocy technicznej SAP #2814271 SAP HANA kopia zapasowa kończy się niepowodzeniem na platformie Azure z błędem Checksum](https://launchpad.support.sap.com/#/notes/2814271)
    - [Uwaga dotycząca pomocy technicznej SAP #2753418 potencjalne obniżenie wydajności z powodu powrotu czasomierza](https://launchpad.support.sap.com/#/notes/2753418)
    - [Uwaga dotycząca pomocy technicznej SAP #2791572 spadek wydajności z powodu braku obsługi VDSO dla funkcji Hyper-V na platformie Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. W zależności od wersji systemu operacyjnego, która jest obsługiwana dla wybranego typu maszyny wirtualnej, należy sprawdzić, czy żądana wersja SAP HANA jest obsługiwana w tej wersji systemu operacyjnego. Przeczytaj [uwagi dotyczące pomocy technicznej SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) dla macierzy obsługi SAP HANA wydań z różnymi wersjami systemu operacyjnego.
5. Ponieważ prawdopodobnie znaleziono prawidłową kombinację typu maszyny wirtualnej platformy Azure, wydanie systemu operacyjnego i wersja SAP HANA należy zaewidencjonować macierz dostępności produktu SAP. W macierzy dostępności SAP można dowiedzieć się, czy produkt SAP, który ma być uruchamiany dla bazy danych SAP HANA jest obsługiwany.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Zagadnienia dotyczące wdrożenia maszyny wirtualnej i systemu operacyjnego gościa
W tej fazie należy wykonać kroki wdrażania maszyn wirtualnych w celu zainstalowania platformy HANA i ostatecznie zoptymalizować wybrany system operacyjny po instalacji.

1. Wybierz obraz podstawowy z galerii platformy Azure. Jeśli chcesz utworzyć własny obraz systemu operacyjnego dla SAP HANA, musisz znać wszystkie pakiety, które są niezbędne do pomyślnej instalacji SAP HANA. W przeciwnym razie zalecamy korzystanie z obrazów SUSE i Red Hat dla oprogramowania SAP lub SAP HANA z galerii obrazów systemu Azure. Obrazy te zawierają pakiety niezbędne do pomyślnej instalacji platformy HANA. W oparciu o umowę dotyczącą pomocy technicznej z dostawcą systemu operacyjnego musisz wybrać obraz, na którym będziesz mieć własną licencję. Lub wybierz obraz systemu operacyjnego, który zawiera pomoc techniczną
2. W przypadku wybrania obrazu systemu operacyjnego gościa, który wymaga wprowadzenia własnej licencji, należy zarejestrować obraz systemu operacyjnego w ramach subskrypcji, aby można było pobrać i zastosować najnowsze poprawki. Ten krok powoduje wymaganie publicznego dostępu do Internetu. Dopóki nie skonfigurujesz prywatnego wystąpienia programu, na przykład serwera z liczbą SMT na platformie Azure.
3. Zdecyduj, jak konfiguracja sieci maszyny wirtualnej. Więcej informacji można znaleźć w dokumencie [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](./hana-vm-operations.md). Należy pamiętać, że nie ma limitów przepustowości sieci, które można przypisać do wirtualnych kart sieciowych na platformie Azure. W związku z tym jedynym celem kierowania ruchu przez różne vNICs opiera się na zagadnieniach związanych z bezpieczeństwem. Firma Microsoft ufa, aby znaleźć kompromis z obsługą systemu między złożonością routingu ruchu przez wiele vNICs i wymagania wymuszane przez aspekty zabezpieczeń.
3. Zastosowanie najnowszych poprawek do systemu operacyjnego po wdrożeniu i zarejestrowaniu maszyny wirtualnej. Zarejestrowana w ramach własnej subskrypcji. Lub w przypadku wybrania obrazu, który obejmuje obsługę systemu operacyjnego, maszyna wirtualna powinna mieć dostęp do poprawek. 
4. Zastosuj dostosowania niezbędne do SAP HANA. Te dostosowania są wymienione w następujących uwagach dotyczących obsługi SAP:

    - [Uwaga dotycząca pomocy technicznej SAP #2694118-Red Hat Enterprise Linux HA Add-On na platformie Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Uwaga dotycząca pomocy technicznej SAP #1984787 — SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Uwaga dotycząca pomocy technicznej SAP #2578899-SUSE Linux Enterprise Server 15: Uwaga dotycząca instalacji](https://launchpad.support.sap.com/#/notes/2578899)
    - [Uwaga dotycząca pomocy technicznej SAP #2002167-Red Hat Enterprise Linux 7. x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Uwaga dotycząca pomocy technicznej SAP #2292690-SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Uwaga dotycząca pomocy technicznej SAP #2772999-Red Hat Enterprise Linux 8. x: Instalacja i konfiguracja](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Uwaga dotycząca pomocy technicznej SAP #2777782-SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Uwaga dotycząca pomocy technicznej SAP #2455582-Linux: uruchamianie aplikacji SAP skompilowanych w serwisie 4. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Uwaga dotycząca pomocy technicznej SAP #2382421 — Optymalizacja konfiguracji sieci na poziomie platformy HANA i systemu operacyjnego](https://launchpad.support.sap.com/#/notes/2382421)

1. Wybierz typ magazynu platformy Azure dla SAP HANA. W tym kroku należy podjąć decyzję o układzie magazynu na potrzeby instalacji SAP HANA. Zamierzasz korzystać z dołączonych dysków platformy Azure lub natywnych udziałów systemu plików NFS platformy Azure. Typy magazynów platformy Azure, które lub obsługiwane i kombinacje różnych typów magazynu platformy Azure, które mogą być używane, są udokumentowane w [SAP HANA konfiguracjach magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md). Wykonaj konfiguracje udokumentowane jako punkt wyjścia. W przypadku systemów nieprodukcyjnych może istnieć możliwość skonfigurowania mniejszej przepływności lub liczby operacji we/wy na sekundę. W celach produkcyjnych może zajść potrzeba skonfigurowania nieco większej przepływności i liczby operacji we/wy na sekundę.
2. Upewnij się, że skonfigurowano [usługę Azure akcelerator zapisu](../../how-to-enable-write-accelerator.md) dla woluminów, które zawierają dzienniki transakcji DBMS, lub wykonaj dzienniki ponownie w przypadku używania maszyn wirtualnych z serii M lub Mv2-Series. Należy pamiętać o ograniczeniach dla akcelerator zapisu zgodnie z opisem.
2. Sprawdź, czy na wdrożonych maszynach wirtualnych jest włączona funkcja [przyspieszonej sieci platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) .

> [!NOTE]
> Nie wszystkie polecenia w różnych profilach dostrajania SAP lub opisane w uwagach mogą zostać pomyślnie uruchomione na platformie Azure. Polecenia, które mogłyby manipulować trybem zasilanym maszyn wirtualnych, zwykle zwracają z błędem, ponieważ nie można manipulować trybem energetycznym podstawowego sprzętu hosta platformy Azure.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Przygotowania krok po kroku dotyczące maszyn wirtualnych platformy Azure
Jedną z specyficznych dla platformy Azure jest instalacja rozszerzenia maszyny wirtualnej platformy Azure, które dostarcza dane monitorowania dla agenta hosta SAP. Szczegółowe informacje o instalacji tego rozszerzenia monitorowania są udokumentowane w:

-  W przypadku [oprogramowania SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) omówiono ulepszone monitorowanie SAP z maszynami wirtualnymi z systemem Linux na platformie Azure 
-  [Uwaga dotycząca oprogramowania SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) Omówienie informacji o SAPOSCOL w systemie Linux 
-  W przypadku [oprogramowania SAP uwaga 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) omówiono metryki monitorowania kluczy dla oprogramowania SAP na Microsoft Azure
-  [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalacja SAP HANA
Po wdrożeniu maszyn wirtualnych platformy Azure oraz zarejestrowanym i skonfigurowanym systemem operacyjnym można zainstalować SAP HANA zgodnie z instalacją SAP. Aby uzyskać dostęp do tej dokumentacji, Zacznij od tego, co jest przydatne w przypadku tego typu zasobów witryny SAP Web [Hana](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)

Aby uzyskać SAP HANA konfiguracje skalowalne w poziomie przy użyciu bezpośrednio dołączonych dysków z platformą Azure Premium Storage lub Ultra Disk, przeczytaj informacje w dokumencie [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Dodatkowe zasoby dla SAP HANA kopii zapasowej
Aby uzyskać informacje na temat tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure, zobacz:
* [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA Azure Backup na poziomie pliku](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](./hana-vm-operations.md)
- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](./hana-vm-operations-storage.md)
