---
title: Uaktualnienie systemu operacyjnego dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Wykonaj uaktualnienie systemu operacyjnego SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdc6dd49fe98085edf3c6fb16606b9f540b5a3a0
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608694"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
W tym dokumencie opisano szczegółowe informacje dotyczące uaktualnień systemu operacyjnego w dużych wystąpieniach platformy HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialne za klienta, pomoc techniczna firmy Microsoft może poprowadzić Cię do kluczowych obszarów, które należy obejrzeć podczas uaktualniania. Przed zaplanowaniem uaktualnienia należy skonsultować się z dostawcą systemu operacyjnego.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *zabroniony*, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

Podczas inicjowania obsługi administracyjnej jednostki w usłudze HLI zespół operacyjny firmy Microsoft instaluje ten system.
W danym czasie wymagane jest zachowanie systemu operacyjnego (przykład: Poprawka, dostrajanie, uaktualnianie itp.) w jednostce.

Przed wprowadzeniem istotnych zmian w systemie operacyjnym (na przykład uaktualnieniem programu SP1 do wersji SP2) skontaktuj się z firmą Microsoft Operations Manager, otwierając bilet pomocy technicznej w celu konsultacji.

Dołącz do biletu:

* Identyfikator subskrypcji pakietu.
* Nazwa serwera.
* Poziom poprawki, który ma zostać zastosowany.
* Data, w której planowana jest ta zmiana. 

Zalecamy otworzenie tego biletu z co najmniej jednego tygodnia przed pożądanym uaktualnieniem, co umożliwi zespołowi oprationemu zapoznanie się z odpowiednią wersją oprogramowania układowego.

Aby uzyskać macierz pomocy technicznej dla różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [uwagi dotyczące oprogramowania SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znanych problemów podczas uaktualniania:
- W jednostkach SKU klasy SKU typu II, oprogramowanie oprogramowania Software Foundation (SFS) zostanie usunięte po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego należy ponownie zainstalować zgodną SFS.
- Sterowniki kart Ethernet (ENIC i FNIC) zostały przywrócone do starszej wersji. Po uaktualnieniu należy ponownie zainstalować zgodną wersję sterowników.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA zalecana konfiguracja: duże wystąpienie (typ I)

Konfiguracja systemu operacyjnego może przekroczyć zalecane ustawienia w miarę upływu czasu w związku z poprawkami, uaktualnieniami systemu i zmianami wprowadzonymi przez klientów. Ponadto firma Microsoft identyfikuje aktualizacje, które są konieczne dla istniejących systemów, aby upewnić się, że są one optymalnie skonfigurowane pod kątem najlepszej wydajności i odporności. Poniższe instrukcje przedstawiają zalecenia dotyczące wydajności sieci, stabilności systemu i optymalnej wydajności platformy HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Zgodne wersje sterowników eNIC/fNIC
  W celu zapewnienia prawidłowej wydajności sieci i stabilności systemu zaleca się zagwarantowanie, że odpowiednia wersja systemu operacyjnego eNIC i sterowniki fNIC są zainstalowane zgodnie z poniższą tabelą zgodności. Serwery są dostarczane do klientów ze zgodnymi wersjami. W niektórych przypadkach podczas stosowania poprawek jądra systemu operacyjnego sterowniki mogą zostać przywrócone do domyślnych wersji sterowników. Upewnij się, że w odpowiedniej wersji sterownika działają operacje publikowania poprawek dla systemu operacyjnego/jądra.
       
      
  |  Dostawca systemu operacyjnego    |  Wersja pakietu systemu operacyjnego     |  Wersja oprogramowania układowego  |  Sterownik eNIC |  Sterownik fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Szło        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   Szło        |  SLES 12 Z DODATKIEM SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   Szło        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   Szło        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   Szło        |  SLES 12 Z DODATKIEM SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   Szło        |  SLES 12 Z DODATKIEM SP5            |   3.2.3 i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Polecenia uaktualniania sterowników i czyszczenia starych pakietów rpm

#### <a name="command-to-check-existing-installed-drivers"></a>Polecenie do sprawdzania istniejących zainstalowanych sterowników
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Usuń istniejące eNIC/fNIC RPM
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Instalowanie zalecanych pakietów sterowników eNIC/fNIC
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Polecenia umożliwiające potwierdzenie instalacji
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Kroki instalacji sterowników eNIC/fNIC podczas uaktualniania systemu operacyjnego

* Uaktualnij wersję systemu operacyjnego
* Usuń pakiety starych RPM
* Instaluj zgodne sterowniki eNIC/fNIC zgodnie z zainstalowaną wersją systemu operacyjnego
* Uruchom ponownie system
* Po ponownym uruchomieniu Sprawdź wersję eNIC/fNIC


### <a name="suse-hlis-grub-update-failure"></a>Niepowodzenie aktualizacji SuSE HLIs GRUB
Usługa SAP w dużych wystąpieniach platformy Azure Hana (typ I) może być w stanie rozruchowym po uaktualnieniu. Poniższa procedura rozwiązuje ten problem.
#### <a name="execution-steps"></a>Kroki wykonywania


*   Wykonaj `multipath -ll` polecenie.
*   Pobierz identyfikator LUN o rozmiarze około 50G lub użyj polecenia: `fdisk -l | grep mapper`
*   Aktualizuj `/etc/default/grub_installdevice` plik z wierszem `/dev/mapper/<LUN ID>` . Przykład:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>Identyfikator LUN różni się od serwera do serwera.


### <a name="disable-edac"></a>Wyłącz EDAC 
   Moduł wykrywanie błędów i Korekcja (EDAC) pomaga w wykrywaniu i poprawianiu błędów pamięci. Jednak podstawowy sprzęt dla Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia (typ I) już wykonuje tę samą funkcję. Ta sama funkcja włączona na poziomach sprzętu i systemu operacyjnego może powodować konflikty i może prowadzić do okazjonalnych, nieplanowanych zamknięć serwera. W związku z tym zalecane jest wyłączenie modułu z systemu operacyjnego.

#### <a name="execution-steps"></a>Kroki wykonywania

* Sprawdź, czy moduł EDAC jest włączony. Jeśli dane wyjściowe są zwracane w poniższym poleceniu, oznacza to, że moduł jest włączony. 
```
lsmod | grep -i edac 
```
* Wyłącz moduły, dołączając następujące wiersze do pliku `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Aby zmiany zostały wprowadzone, wymagany jest ponowny rozruch. Wykonaj `lsmod` polecenie i sprawdź, czy moduł nie znajduje się w danych wyjściowych.

### <a name="kernel-parameters"></a>Parametry jądra
   Upewnij się, że są stosowane poprawne ustawienia dla `transparent_hugepage` ,, `numa_balancing` `processor.max_cstate` `ignore_ce` i `intel_idle.max_cstate` .

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = nigdy
* numa_balancing = Wyłącz
* MCE = ignore_ce

#### <a name="execution-steps"></a>Kroki wykonywania

* Dodaj te parametry do `GRB_CMDLINE_LINUX` wiersza w pliku `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Utwórz nowy plik grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Uruchom ponownie system.


## <a name="next-steps"></a>Następne kroki
- Odwołaj się do [kopii zapasowych i przywracania](hana-overview-high-availability-disaster-recovery.md) dla klasy SKU typu kopia zapasowa systemu operacyjnego.
- Zapoznaj się [z kopią zapasową systemu operacyjnego dla jednostek SKU typu II poprawki 3 sygnatury](os-backup-type-ii-skus.md) dla klasy SKU typu II.
