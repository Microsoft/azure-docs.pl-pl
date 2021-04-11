---
title: Odzyskiwanie po awarii maszyn wirtualnych VMware/serwerów fizycznych do lokacji dodatkowej za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii maszyn wirtualnych VMware lub serwerów fizycznych z systemami Windows i Linux do lokacji dodatkowej z Azure Site Recovery.
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: c2456fcfffd9645f94856998142f262298b5eef9
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581460"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware lub serwerów fizycznych do lokacji dodatkowej

Program InMage Scout w [Azure Site Recovery](site-recovery-overview.md) zapewnia replikację w czasie rzeczywistym między lokalnymi lokacjami programu VMware. Usługa InMage Scout jest dołączona do subskrypcji usługi Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Ogłoszenie końca wsparcia

Azure Site Recovery scenariusz replikacji między lokalnymi centrami danych programu VMware lub fizycznymi centrami danych zbliża się do końca wsparcia.

-   Od sierpnia 2018 scenariusza nie można skonfigurować w magazynie Recovery Services i nie można pobrać oprogramowania InMage Scout z magazynu. Istniejące wdrożenia będą obsługiwane. 
-   W grudniu 31 2020 scenariusz nie będzie obsługiwany.
- Istniejący partnerzy mogą dołączać nowych klientów do scenariusza do momentu zakończenia pomocy technicznej.

W okresie 2018 i 2019 zostaną wydane dwie aktualizacje: 

-   Update 7: Rozwiązywanie problemów z konfiguracją sieci i zgodnością oraz zapewnia obsługę protokołu TLS 1,2.
-   Update 8: dodaje obsługę systemów operacyjnych Linux RHEL/CentOS 7.3/7.4/7.5 i dla SUSE 12

Po aktualizacji 8 nie zostaną wydane żadne dalsze aktualizacje. Zostanie ograniczona obsługa poprawek dla systemów operacyjnych dodanych w Update 8 i poprawki błędów na podstawie najlepszego nakładu pracy.

Azure Site Recovery kontynuuje wprowadzanie innowacji przez udostępnienie klientom rozwiązań firmy VMware i funkcji Hyper-V łatwego i najlepszego w klasie rozwiązania DRaaS z platformą Azure jako lokacji odzyskiwania po awarii. Firma Microsoft zaleca, aby istniejący klienci korzystający z usług InMage/ASR Scout rozważali scenariusz dotyczący ciągłości działania firmy Azure Site Recovery. Scenariusz VMware do platformy Azure Azure Site Recovery to rozwiązanie DR klasy korporacyjnej dla aplikacji VMware, które oferuje cel punktu odzyskiwania i RTO minut, obsługę replikacji i odzyskiwania aplikacji obejmujących wiele maszyn wirtualnych, bezproblemowe dołączanie, kompleksowe monitorowanie i znaczną korzyść w zakresie TCO.

### <a name="scenario-migration"></a>Migracja scenariusza
Alternatywnie zalecamy skonfigurowanie odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware i maszyn fizycznych przez replikowanie ich na platformę Azure. W tym celu wykonaj następujące czynności:

1.  Zapoznaj się z poniższym krótkim porównaniem. Aby można było przeprowadzić replikację maszyn lokalnych, należy sprawdzić, czy spełniają one [wymagania](./vmware-physical-azure-support-matrix.md#replicated-machines) związane z replikacją na platformę Azure. Jeśli replikujesz maszyny wirtualne VMware, zalecamy zapoznanie się z [wytycznymi planowania pojemności](./site-recovery-plan-capacity-vmware.md)i uruchomienie [Narzędzia planista wdrażania](./site-recovery-deployment-planner.md) do identyfikacji wymagań dotyczących pojemności i zweryfikowanie zgodności.
2.  Po uruchomieniu Planista wdrażania można skonfigurować replikację: o dla maszyn wirtualnych VMware, postępować zgodnie z tymi samouczkami w celu [przygotowania platformy Azure](./tutorial-prepare-azure.md), [przygotowania lokalnego środowiska VMware](./vmware-azure-tutorial-prepare-on-premises.md)i [skonfigurowania odzyskiwania po awarii](./vmware-azure-tutorial-prepare-on-premises.md).
o dla maszyn fizycznych, postępuj zgodnie z tym [samouczkiem](./physical-azure-disaster-recovery.md).
3.  Gdy maszyny są replikowane na platformę Azure, możesz uruchomić polecenie [przechodzenia do odzyskiwania po awarii](./site-recovery-test-failover-to-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

### <a name="quick-comparison"></a>Szybkie porównanie

**Funkcja** | **Replikacja do platformy Azure** |**Replikacja między centrami danych VMware**
--|--|--
**Składniki wymagane** |Usługa mobilności na replikowanych maszynach. Lokalny serwer konfiguracji, serwer przetwarzania, główny serwer docelowy. Tymczasowy serwer przetwarzania na platformie Azure na potrzeby powrotu po awarii.|Usługa mobilności, serwer przetwarzania, serwer konfiguracji i główny element docelowy
**Konfiguracja i aranżacja** |Magazyn Recovery Services w Azure Portal | Korzystanie z vContinuum 
**Replikowane** |Dysk (systemy Windows i Linux) |Volume-Windows<br> Disk-Linux
**Udostępniony klaster dysków** |Nieobsługiwane|Obsługiwane
**Limity zmian danych (średnia)** |10 MB/s danych na dysk<br> dane 25 MB/s na maszynę wirtualną<br> [Dowiedz się więcej](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s na dysk  <br> > 25 MB/s na maszynę wirtualną
**Monitorowanie** |Z Azure Portal|Z CX (serwer konfiguracji)
**Macierz obsługi** | [Kliknij tutaj, aby uzyskać szczegółowe informacje](./vmware-physical-azure-support-matrix.md)|[Pobieranie macierzy zgodnej z systemem ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Wymagania wstępne
W celu ukończenia tego samouczka:

- [Zapoznaj](vmware-physical-secondary-support-matrix.md) się z wymaganiami dotyczącymi obsługi wszystkich składników.
- Upewnij się, że maszyny, które chcesz replikować, są zgodne z [obsługą zreplikowanej maszyny](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Pobierz i zainstaluj aktualizacje składników

 Przejrzyj i zainstaluj najnowsze [aktualizacje](#updates). Aktualizacje należy zainstalować na serwerach w następującej kolejności:

1. Serwer RX (jeśli dotyczy)
2. Serwery konfiguracji
3. Serwery przetwarzania
4. Główne serwery docelowe
5. serwery vContinuum
6. Serwer źródłowy (serwery z systemem Windows i Linux)

Zainstaluj aktualizacje w następujący sposób:

> [!NOTE]
>Wersja aktualizacji plików wszystkich składników programu Scout nie może być taka sama w pliku Update. zip. Starsza wersja wskazuje, że składnik nie zmienia się od czasu poprzedniej aktualizacji tej aktualizacji.

Pobierz plik [Update](https://aka.ms/asr-scout-update7) . zip i pliki konfiguracji [uaktualnienia MySQL i php](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) . Plik Update. zip zawiera wszystkie podstawowe pliki binarne i pliki binarne uaktualniania zbiorczego następujących składników: 
- InMage_ScoutCloud_RX_8 InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015. tar. gz
- RX_8 RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3 32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3 64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3 32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3 64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. Wyodrębnij pliki. zip.
  2. **Serwer RX**: Skopiuj **RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz** do serwera RX i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.
  3. **Serwer konfiguracji i serwer przetwarzania**: Skopiuj **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** na serwer konfiguracji i serwer przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Główny serwer docelowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer. Kliknij go dwukrotnie, aby go uruchomić. Ten sam plik może być również używany do instalacji nowej. Ta sama ujednolicona aktualizacja agenta ma również zastosowanie do serwera źródłowego.
  Aktualizacja nie musi być stosowana w głównym miejscu docelowym przygotowanym z **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**  , ponieważ jest to nowy Instalator z aktualizacją ga ze wszystkimi najnowszymi zmianami.
  5. **serwer vContinuum**: Skopiuj **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer.  Upewnij się, że zamknąłeś kreatora vContinuum. Kliknij dwukrotnie plik, aby go uruchomić.
  6. **Główny serwer docelowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** do głównego serwera docelowego systemu Linux i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.
  7. **Serwer źródłowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer źródłowy. Kliknij dwukrotnie plik, aby go uruchomić. 
  8. **Serwer źródłowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj odpowiednią wersję ujednoliconego pliku agenta na serwer z systemem Linux i Wyodrębnij ją. W wyodrębnionym folderze Uruchom polecenie **/Install**.  Przykład: dla RHEL 6,7 64-bit Server Skopiuj **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** do serwera i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.
  9. Po uaktualnieniu serwera konfiguracji, serwera przetwarzania i serwera RX z wymienionymi wyżej instalatorami, biblioteki PHP i MySQL muszą zostać uaktualnione ręcznie z krokami opisanymi w sekcji 7,4 [przewodnika szybkich instalacji](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Włączanie replikacji

1. Skonfiguruj replikację między źródłową i docelową lokacją VMware.
2. Aby dowiedzieć się więcej na temat instalacji, ochrony i odzyskiwania, zobacz następujące dokumenty:

   * [Informacje o wersji](https://aka.ms/asr-scout-release-notes)
   * [Macierz zgodności](https://aka.ms/asr-scout-cm)
   * [Przewodnik użytkownika](https://aka.ms/asr-scout-user-guide)
   * [Podręcznik użytkownika RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Przewodnik szybkiej instalacji](https://aka.ms/asr-scout-quick-install-guide)
   * [Uaktualnianie bibliotek MYSQL i PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualizacje

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Zaktualizowano: 31 grudnia 2018 pobrać [Scout Update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 to pełny Instalator, którego można użyć do nowej instalacji, a także do uaktualnienia istniejących agentów/MT, które znajdują się w poprzednich aktualizacjach (od wersji Update 1 do Update 6). Zawiera wszystkie poprawki z aktualizacji Update 1 do Update 6 oraz nowe poprawki i ulepszenia opisane poniżej.
 
#### <a name="new-features"></a>Nowe funkcje
* Zgodność ze standardem PCI
* Obsługa protokołu TLS w wersji 1.2

#### <a name="bug-and-security-fixes"></a>Poprawki błędów i zabezpieczeń
* Rozwiązano problem: w przypadku klastra/maszyn autonomicznych z systemem Windows podczas odzyskiwania/przywracania po awarii jest niepoprawna konfiguracja adresów IP.
* Naprawiono: czasami dodanie operacji dyskowej kończy się niepowodzeniem dla klastra V2V.
* Naprawiono: Kreator vContinuum jest zablokowany podczas fazy odzyskiwania, jeśli główny element docelowy to Windows Server 2016
* Naprawiono: problemy z bezpieczeństwem programu MySQL zostały skorygowane przez uaktualnienie programu MySQL do wersji 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Ręczne uaktualnienie dla technologii PHP i MySQL w systemach CS, PS i RX
Platformę obsługi skryptów PHP należy uaktualnić do wersji 7.2.10 na serwerze konfiguracji, serwerze przetwarzania i serwerze RX.
System zarządzania bazami danych MySQL należy uaktualnić do wersji 5.7.23 na serwerze konfiguracji, serwerze przetwarzania i serwerze RX.
Wykonaj czynności ręczne podane w [przewodniku szybkich instalacji](https://aka.ms/asr-scout-quick-install-guide) , aby uaktualnić wersje PHP i MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Zaktualizowano: 12 października 2017

Pobierz [aktualizację Scout 6](https://aka.ms/asr-scout-update6).

Scout Update 6 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z aktualizacji Update 1 do Update 5 oraz nowe poprawki i ulepszenia opisane poniżej. 

#### <a name="new-platform-support"></a>Obsługa nowych platform
* Dodano obsługę dla źródła systemu Windows Server 2016
* Dodano obsługę następujących systemów operacyjnych Linux:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Dodano obsługę programu VMware Center 6,5

Zainstaluj aktualizacje w następujący sposób:

> [!NOTE]
>Wersja aktualizacji plików wszystkich składników programu Scout nie może być taka sama w pliku Update. zip. Starsza wersja wskazuje, że składnik nie zmienia się od czasu poprzedniej aktualizacji tej aktualizacji.

Pobierz plik [Update](https://aka.ms/asr-scout-update6) . zip. Plik zawiera następujące składniki: 
- RX_8 RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA Update4 bity dla RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
  1. Wyodrębnij pliki. zip.
  2. **Serwer RX**: Skopiuj **RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz** do serwera RX i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.
  3. **Serwer konfiguracji i serwer przetwarzania**: Skopiuj **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** na serwer konfiguracji i serwer przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Główny serwer docelowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na serwer. Kliknij go dwukrotnie, aby go uruchomić. Ta sama ujednolicona aktualizacja agenta ma również zastosowanie do serwera źródłowego. Jeśli źródło nie zostało zaktualizowane do aktualizacji Update 4, należy zaktualizować ujednoliconego agenta.
  Aktualizacja nie musi być stosowana w głównym miejscu docelowym przygotowanym z **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**  , ponieważ jest to nowy Instalator z aktualizacją ga ze wszystkimi najnowszymi zmianami.
  5. **serwer vContinuum**: Skopiuj **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** na serwer.  Upewnij się, że zamknąłeś kreatora vContinuum. Kliknij dwukrotnie plik, aby go uruchomić.
  Aktualizacja nie musi być stosowana w głównym miejscu docelowym przygotowanym z **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , ponieważ jest to nowy Instalator z aktualizacją ga ze wszystkimi najnowszymi zmianami.
  6. **Główny serwer docelowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** do głównego serwera docelowego i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.
  7. **Serwer źródłowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na serwer źródłowy. Kliknij dwukrotnie plik, aby go uruchomić. 
  Nie musisz instalować agenta Update 5 na serwerze źródłowym, jeśli został on już zaktualizowany do wersji Update 4 lub Agent źródłowy jest instalowany przy użyciu najnowszego Instalatora podstawowego **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Serwer źródłowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj odpowiednią wersję ujednoliconego pliku agenta na serwer z systemem Linux i Wyodrębnij ją. W wyodrębnionym folderze Uruchom polecenie **/Install**.  Przykład: dla serwera RHEL 6,7 64-bitowy, skopiuj **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** do serwera i wyodrębnij go. W wyodrębnionym folderze Uruchom polecenie **/Install**.


> [!NOTE]
> * Instalator podstawowego programu Unified Agent (UA) dla systemu Windows został odświeżony w celu obsługi systemu Windows Server 2016. Nowy **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** Instalatora jest spakowany z podstawowym pakietem Scout GA (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Ten sam Instalator będzie używany dla wszystkich obsługiwanych wersji systemu Windows. 
> * Podstawowy Instalator docelowego programu Windows vContinuum & został odświeżony w celu obsługi systemu Windows Server 2016. Nowy **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** Instalatora jest spakowany z podstawowym pakietem Scout GA (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Ten sam Instalator będzie używany do wdrażania systemu Windows 2016 Master Target i głównego elementu docelowego systemu Windows 2012R2.
> * System Windows Server 2016 na serwerze fizycznym nie jest obsługiwany przez usługę ASR Scout. Obsługuje tylko maszynę wirtualną VMware systemu Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia
- Niepowodzenie ochrony powrotu po awarii dla maszyny wirtualnej z systemem Linux z listą dysków do replikacji jest pusta na końcu konfiguracji.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z aktualizacji Update 1 do Update 4 oraz nowe poprawki opisane poniżej.
- Poprawki z Site Recovery Scout Update 4 do Update 5 są przeznaczone dla głównych elementów docelowych i vContinuum.
- Jeśli serwery źródłowe, główny serwer docelowy, konfiguracja, proces i odbieranie są już uruchomione w aktualizacji Update 4, zastosuj je tylko na głównym serwerze docelowym. 

#### <a name="new-platform-support"></a>Obsługa nowych platform
* SUSE Linux Enterprise Server 11 z dodatkiem Service Pack 4 (SP4)
* SLES 11 z dodatkiem SP4 64 bit  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release. tar. gz** jest spakowany z podstawowym pakietem Scout GA (**InMage_Scout_Standard_8.0,1 GA.zip**). Pobierz pakiet GA z portalu zgodnie z opisem w temacie Tworzenie magazynu.


#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia

* Poprawki zwiększające niezawodność obsługi klastrów systemu Windows:
    * Naprawiono — niektóre dyski klastrowe programu P2V MSCS stają się surowe po odzyskaniu.
    * Odzyskiwanie klastra z systemem MSCSd z prefiksem P2V kończy się niepowodzeniem z powodu niezgodności kolejności dysku.
    * Naprawiono — operacja klastra MSCS w celu dodania dysków kończy się niepowodzeniem z powodu błędu niezgodności rozmiaru dysku.
    * Naprawiono — sprawdzanie gotowości dla źródłowego klastra usługi MSCS z RDM LUN nie powiodło się.
    * Ochrona klastra z stałym węzłem nie powiodła się z powodu problemu z niezgodnością SCSI. 
    * Stała ponowna ochrona serwera klastra P2V systemu Windows kończy się niepowodzeniem, jeśli istnieją docelowe dyski klastrowe. 
    
* Naprawiono: podczas ochrony przed powrotu po awarii, jeśli wybrany główny serwer docelowy nie znajduje się na tym samym serwerze ESXi co chroniona maszyna źródłowa (w trakcie ochrony przed awarią), vContinuum pobiera niewłaściwy główny serwer docelowy podczas odzyskiwania po awarii, a operacja odzyskiwania kończy się niepowodzeniem.

> [!NOTE]
> * Poprawki klastra P2V mają zastosowanie tylko do fizycznych klastrów MSCS, które są nowo chronione za pomocą programu Site Recovery Scout Update 5. Aby zainstalować poprawki klastra dla chronionych klastrów P2V systemu MSCS ze starszymi aktualizacjami, wykonaj kroki uaktualniania wymienione w sekcji 12 [informacji o wersji Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * w przypadku ponownej ochrony ten sam zestaw dysków jest aktywny na każdym z węzłów klastra, gdy były początkowo chronione, a następnie ponownej ochrony klastra usługi MSCS można używać tylko na istniejących dyskach docelowych. Jeśli tak nie jest, wykonaj czynności ręczne w sekcji 12 programu [Site Recovery Scout informacje o wersji](https://aka.ms/asr-scout-release-notes), aby przenieść dyski po stronie docelowej do odpowiedniej ścieżki magazynu danych do ponownego użycia podczas ponownej ochrony. W przypadku ponownego włączenia ochrony klastra MSCS w trybie P2V bez wykonania kroków uaktualniania program tworzy nowy dysk na docelowym serwerze ESXi. Należy ręcznie usunąć stare dyski z magazynu danych.
> * Gdy serwer źródłowy SLES11 lub SLES11 (z dowolnymi dodatkami Service Pack) jest bezpiecznie uruchamiany ponownie, należy ręcznie oznaczyć pary replikacji dysków **głównych** do ponownej synchronizacji. W interfejsie CX nie ma powiadomienia. Jeśli nie oznaczesz dysku głównego do ponownej synchronizacji, możesz zauważyć problemy z integralnością danych.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z aktualizacji Update 1 do Update 3 i nowe poprawki opisane poniżej.

#### <a name="new-platform-support"></a>Obsługa nowych platform

* Dodano obsługę dla vCenter/vSphere 6,0, 6,1 i 6,2
* Dodano obsługę dla tych systemów operacyjnych Linux:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 i 7,2
  * CentOS 7,0, 7,1 i 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bit  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release. tar. gz** jest spakowany z podstawowym pakietem Scout ga o wartości **InMage_Scout_Standard_8.0,1 GA.zip**. Pobierz pakiet Scout GA z portalu zgodnie z opisem w temacie Tworzenie magazynu.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia

* Ulepszono obsługę zamykania dla następujących systemów operacyjnych Linux i klonów, aby zapobiec niepożądanym problemom z ponowną synchronizacją:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (olu) 6. x
* W przypadku systemu Linux wszystkie uprawnienia dostępu do folderu w katalogu instalacji ujednoliconego agenta są teraz ograniczone tylko do użytkownika lokalnego.
* W systemie Windows Naprawiono problem z limitem czasu, który wystąpił podczas wystawiania wspólnych, rozmieszczonych zakładek spójności, na silnie załadowane aplikacje rozproszone, takie jak SQL Server i klastry punktów udostępniania.
* Poprawka dotycząca dziennika w instalatorze podstawowym serwera konfiguracji.
* Link do pobierania do programu VMware vCLI 6,0 został dodany do podstawowego Instalatora bazowego systemu Windows.
* Dodano dodatkowe sprawdzenia i dzienniki w celu zmiany konfiguracji sieci podczas pracy w trybie failover i odzyskiwania po awarii.
* Poprawka dotycząca problemu, który spowodował, że informacje o przechowywaniu nie są raportowane na serwerze konfiguracji.  
* W przypadku klastrów fizycznych Poprawka dotycząca problemu powodującego zmianę rozmiaru woluminu nie powiedzie się w Kreatorze vContinuum podczas zmniejszania woluminu źródłowego.
* Poprawka dotycząca problemu z ochroną klastra, która się nie powiodła z powodu błędu: "nie można odnaleźć sygnatury dysku", gdy dysk klastrowy jest dyskiem PRDM.
* Poprawka dla serwera transportu cxps uległa awarii, spowodowany przez wyjątek poza zakresem.
* Kolumny Nazwa serwera i adres IP są teraz zmieniane na stronie **Instalacja wypychana** kreatora vContinuum.
* Udoskonalenia interfejsu API odbierania:
  * Dostępne są teraz pięć najnowszych dostępnych wspólnych punktów spójności (tylko Tagi gwarantowane).
  * Szczegóły pojemności i ilości wolnego miejsca są wyświetlane dla wszystkich chronionych urządzeń.
  * Stan sterownika Scout na serwerze źródłowym jest dostępny.

> [!NOTE]
> * Pakiet **InMage_Scout_Standard_8.0.1_GA.zip** Base:
>     * Zaktualizowany Instalator podstawowy serwera konfiguracji (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Instalator podstawowy docelowego systemu Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * W przypadku wszystkich nowych instalacji należy użyć nowego serwera konfiguracji i usługi Windows Master Target GA.
> * Aktualizację Update 4 można zastosować bezpośrednio w witrynie 8.0.1 GA.
> * Po zastosowaniu aktualizacji serwer konfiguracji i odbieranie nie mogą zostać wycofane.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Wszystkie aktualizacje Site Recovery są zbiorcze. Aktualizacja Update 3 zawiera wszystkie poprawki z aktualizacji Update 1 i Update 2. Aktualizację Update 3 można zastosować bezpośrednio w witrynie 8.0.1 GA. Po zastosowaniu aktualizacji serwer konfiguracji i odbieranie nie mogą zostać wycofane.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia
Aktualizacja Update 3 rozwiązuje następujące problemy:

* Serwer konfiguracji i odbieranie nie są rejestrowane w magazynie, gdy znajdują się za serwerem proxy.
* Liczba godzin, w których nie został osiągnięty cel punktu odzyskiwania (RPO), nie jest aktualizowana w raporcie kondycji.
* Serwer konfiguracji nie jest synchronizowany z RX, gdy szczegóły dotyczące sprzętu ESX lub sieci zawierają dowolne znaki UTF-8.
* Kontrolery domeny systemu Windows Server 2008 R2 nie są uruchamiane po odzyskaniu.
* Synchronizacja w trybie offline nie działa zgodnie z oczekiwaniami.
* Po przejściu do trybu failover maszyny wirtualnej usuwanie pary replikacji nie postępuje w konsoli serwera konfiguracji przez długi czas. Użytkownicy nie mogą ukończyć operacji powrotu po awarii lub wznowienia.
* Zoptymalizowano ogólne operacje migawek według zadania spójności, aby zmniejszyć liczbę połączeń aplikacji, takich jak klienci SQL Server.
* Ulepszono wydajność narzędzia spójności (VACP.exe). Użycie pamięci wymaganej do tworzenia migawek w systemie Windows zostało zmniejszone.
* Usługa instalacji wypychanej ulega awarii, gdy hasło jest większe niż 16 znaków.
* vContinuum nie sprawdza i nie monituje o nowe poświadczenia vCenter, gdy poświadczenia są modyfikowane.
* W systemie Linux, główny Menedżer pamięci podręcznej (cachemgr) nie pobiera plików z serwera przetwarzania. Powoduje to ograniczenie pary replikacji.
* Gdy kolejność dysków fizycznego klastra trybu failover (MSCS) nie jest taka sama we wszystkich węzłach, replikacja nie jest ustawiona dla niektórych woluminów klastra. Aby skorzystać z tej poprawki, należy ponownie włączyć ochronę klastra.  
* Funkcja SMTP nie działa zgodnie z oczekiwaniami, po uaktualnieniu odbierania z programu Scout 7,1 do Scout 8.0.1.
* Więcej statystyk zostało dodanych w dzienniku dla operacji wycofywania, aby śledzić czas potrzebny na jej ukończenie.
* Dodano obsługę dla systemów operacyjnych Linux na serwerze źródłowym:
  * Red Hat Enterprise Linux (RHEL) 6 z aktualizacją 7
  * CentOS 6, aktualizacja 7
* Serwer konfiguracji i konsole RX teraz wyświetlają powiadomienia dla pary, które przechodzą w tryb bitmapy.
* Dodano następujące poprawki zabezpieczeń w usłudze RX:
    * Obejście autoryzacji poprzez manipulowanie parametrami: ograniczony dostęp do nieodpowiednich użytkowników.
    * Fałszerstwo żądania między lokacjami: koncepcja strony-tokeny została zaimplementowana i jest generowana losowo dla każdej strony. Oznacza to, że istnieje tylko jedno wystąpienie logowania dla tego samego użytkownika, a odświeżanie strony nie działa. Zamiast tego przekierowuje do pulpitu nawigacyjnego.
    * Przekazywanie złośliwego pliku: pliki są ograniczone do określonych rozszerzeń: z, AIFF, ASF, AVI, BMP, CSV, doc, docx, FLA, FLV, GIF, GZ, GZIP, JPEG, jpg, log, MID, MOV, MP3, MP4, MPC, MPEG, MPG, ODS, ODT, PDF, PNG, PPT, PPTX, PXD, QT, RAM, RAR, RM, RMI, RMVB, RTF,, SDC, SWF, sitd, SXC, smoły, SXW, TIF, TIFF, txt, VSD, WAV, WMA, WMV, xls , xlsx, XML i zip.
    * Trwałe skrypty między lokacjami: dodano walidacje danych wejściowych.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Poprawki w aktualizacji Update 2 obejmują:

* **Serwer konfiguracji**: wystąpiły problemy z nieoczekiwanym działaniem z 31-dniową bezpłatną funkcją zliczania, gdy serwer konfiguracji został zarejestrowany w magazynie Azure Site Recovery.
* **Ujednolicony Agent**: Poprawka dotycząca problemu w aktualizacji Update 1, która spowodowała, że aktualizacja nie jest instalowana na głównym serwerze docelowym, podczas uaktualniania z wersji 8,0 do 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Aktualizacja Update 1 zawiera następujące poprawki błędów i nowe funkcje:

* 31 dni bezpłatnej ochrony na wystąpienie serwera. Dzięki temu można testować funkcje lub konfigurować sprawdzanie koncepcji.
* Wszystkie operacje na serwerze, w tym przełączanie do trybu failover i powrotu po awarii, są bezpłatne przez pierwsze 31 dni. Czas jest uruchamiany, gdy serwer jest najpierw chroniony przy użyciu Site Recovery Scout. Od 32nd dnia każdy chroniony serwer jest naliczany według standardowej stawki wystąpienia dla Site Recovery ochrony do lokacji należącej do klienta.
* W każdej chwili liczba obecnie naliczanych serwerów chronionych jest dostępna na **pulpicie nawigacyjnym** w magazynie.
* Dodano obsługę do vSphere Command-Line Interface (vCLI) 5,5 Update 2.
* Dodano obsługę dla tych systemów operacyjnych Linux na serwerze źródłowym:
    * RHEL 6, aktualizacja 6
    * RHEL 5, aktualizacja 11
    * CentOS 6, aktualizacja 6
    * CentOS 5, aktualizacja 11
* Poprawki błędów w celu rozwiązania następujących problemów:
  * Rejestracja magazynu kończy się niepowodzeniem na serwerze konfiguracji lub serwerze RX.
  * Woluminy klastra nie są wyświetlane w oczekiwany sposób, gdy klastrowane maszyny wirtualne są ponownie chronione podczas ich wznawiania.
  * Powrót po awarii kończy się niepowodzeniem, gdy główny serwer docelowy jest hostowany na innym serwerze ESXi z lokalnej produkcji maszyn wirtualnych.
  * Uprawnienia do pliku konfiguracji są zmieniane po uaktualnieniu do 8.0.1. Ta zmiana ma wpływ na ochronę i operacje.
  * Próg ponownej synchronizacji nie jest wymuszany zgodnie z oczekiwaniami, powodując zachowanie niespójnej replikacji.
  * Ustawienia punktu odzyskiwania nie są prawidłowo wyświetlane w konsoli serwera konfiguracji. Nieskompresowana wartość danych nieprawidłowo przedstawia wartość skompresowaną.
  * Operacja usuwania nie zostanie usunięta w Kreatorze vContinuum w oczekiwany sposób, a replikacja nie zostanie usunięta z konsoli serwera konfiguracji.
  * W Kreatorze vContinuum dysk jest automatycznie zaznaczany po kliknięciu **szczegółów** w widoku dysku podczas ochrony maszyn wirtualnych MSCS.
  * W przypadku scenariusza Physical-to-Virtual (P2V) wymagane usługi firmy HP (takie jak CIMnotify i CqMgHost) nie są przenoszone do ręcznego w ramach odzyskiwania maszyny wirtualnej. Ten problem powoduje dodatkowy czas rozruchu.
  * Ochrona maszyny wirtualnej z systemem Linux kończy się niepowodzeniem, gdy na głównym serwerze docelowym znajduje się więcej niż 26 dysków.

