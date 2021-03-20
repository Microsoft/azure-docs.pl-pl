---
title: Rozwiązywanie problemów z replikacją w przypadku odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Azure Site Recovery | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336209"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją związanych z maszynami wirtualnymi VMware i serwerami fizycznymi

W tym artykule opisano niektóre typowe problemy i konkretne błędy, które mogą wystąpić w przypadku replikowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure przy użyciu [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1. monitorowanie kondycji serwera przetwarzania

Site Recovery używa [serwera przetwarzania](vmware-physical-azure-config-process-server-overview.md#process-server) do odbierania i optymalizowania replikowanych danych oraz wysyłania ich do platformy Azure.

Zalecamy monitorowanie kondycji serwerów przetwarzania w portalu, aby upewnić się, że są one połączone i działają prawidłowo oraz że replikacja postępuje zgodnie z maszynami źródłowymi skojarzonymi z serwerem przetwarzania.

- [Dowiedz się więcej o](vmware-physical-azure-monitor-process-server.md) serwerach procesów monitorowania.
- [Przeglądanie najlepszych rozwiązań](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Rozwiązywanie problemów z](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) kondycją serwera przetwarzania.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2. Rozwiązywanie problemów z łącznością i replikacją

Początkowe i ciągłe błędy replikacji często są spowodowane przez problemy z łącznością między serwerem źródłowym a serwerem przetwarzania lub między serwerem przetwarzania i platformą Azure.

Aby rozwiązać te problemy, [Rozwiąż problem z łącznością i replikacją](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3. Rozwiązywanie problemów z maszynami źródłowymi, które nie są dostępne do replikacji

Gdy próbujesz wybrać maszynę źródłową, aby włączyć replikację przy użyciu Site Recovery, maszyna może być niedostępna z jednego z następujących powodów:

* **Dwie maszyny wirtualne z tym samym identyfikatorem UUID**: Jeśli dwie maszyny wirtualne w ramach programu vCenter mają ten sam identyfikator UUID, pierwsza maszyna wirtualna odnaleziona przez serwer konfiguracji jest pokazana w Azure Portal. Aby rozwiązać ten problem, upewnij się, że żadna z dwóch maszyn wirtualnych nie ma tego samego identyfikatora UUID. Ten scenariusz jest często widoczny w wystąpieniach, w których kopia zapasowa maszyny wirtualnej stanie się aktywna i jest zarejestrowana w naszych rekordach odnajdowania. Zapoznaj się Azure Site Recovery z oprogramowaniem [VMware na platformie Azure: Jak oczyścić zduplikowane lub nieodświeżone wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) , aby rozwiązać ten problem.
* **Nieprawidłowe poświadczenia użytkownika vCenter**: Upewnij się, że dodano poprawne poświadczenia programu vCenter podczas konfigurowania serwera konfiguracji za pomocą szablonu OVF lub ujednoliconej konfiguracji. Aby sprawdzić poświadczenia dodane podczas instalacji, zobacz [Modyfikowanie poświadczeń na potrzeby automatycznego odnajdywania](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter niewystarczające uprawnienia**: Jeśli uprawnienia dostępne w celu uzyskania dostępu do programu vCenter nie mają wymaganych uprawnień, mogą wystąpić błędy odnajdywania maszyn wirtualnych. Upewnij się, że uprawnienia opisane w artykule [Przygotowywanie konta do automatycznego odnajdywania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) są dodawane do konta użytkownika vCenter.
* **Azure Site Recovery serwery zarządzania**: Jeśli maszyna wirtualna jest używana jako serwer zarządzania w ramach co najmniej jednej z następujących ról — serwer konfiguracji/Scale-Out Process Server/Master Target Server, nie będziesz w stanie wybrać maszyny wirtualnej z portalu. Nie można zreplikować serwerów zarządzania.
* **Włączono już ochronę/przełączenie w tryb failover za pośrednictwem usług Azure Site Recovery**: Jeśli maszyna wirtualna jest już chroniona lub przełączana w tryb failover za pośrednictwem Site Recovery, maszyna wirtualna nie jest dostępna do wybrania ochrony w portalu. Upewnij się, że maszyna wirtualna, której szukasz w portalu, nie jest już chroniona przez innego użytkownika lub inną subskrypcję.
* serwer **vCenter nie jest połączony**: Sprawdź, czy program vCenter jest w stanie połączonym. Aby sprawdzić, przejdź do Recovery Services magazynu > Site Recovery infrastruktury > serwery konfiguracji > kliknij odpowiedni serwer konfiguracji > zostanie otwarty blok z prawej strony ze szczegółowymi informacjami o skojarzonych serwerach. Sprawdź, czy program vCenter jest połączony. Jeśli jest to stan "niepołączony", Rozwiąż problem, a następnie [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Następnie maszyna wirtualna zostanie wyświetlona na liście w portalu.
* **ESXi wyłączone**: Jeśli host ESXi, na którym znajduje się maszyna wirtualna, jest w stanie wyłączenia, maszyna wirtualna nie zostanie wyświetlona lub nie będzie można jej wybrać na Azure Portal. Włącz hosta ESXi, [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) w portalu. Następnie maszyna wirtualna zostanie wyświetlona na liście w portalu.
* **Oczekiwanie na ponowne uruchomienie**: Jeśli istnieje Oczekujące ponowne uruchomienie maszyny wirtualnej, nie będzie można wybrać maszyny na Azure Portal. Upewnij się, że wykonano czynności oczekujące na ponowne uruchomienie, [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Następnie maszyna wirtualna zostanie wyświetlona na liście w portalu.
* **Nie znaleziono adresu IP**: Jeśli maszyna wirtualna nie ma skojarzonego z nim prawidłowego adresu IP, nie będzie można wybrać maszyny na Azure Portal. Upewnij się, że przypiszesz prawidłowy adres IP do maszyny wirtualnej, [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Następnie maszyna wirtualna zostanie wyświetlona na liście w portalu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Rozwiązywanie problemów z chronionymi maszynami wirtualnymi wyszarzonymi w portalu

Maszyny wirtualne replikowane w obszarze Site Recovery nie są dostępne w Azure Portal, jeśli w systemie istnieją zduplikowane wpisy. Aby dowiedzieć się, jak usunąć nieodświeżone wpisy i rozwiązać problem, zapoznaj się z tematem [Azure Site Recovery VMware-to-Azure: Jak oczyścić zduplikowane lub stare wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Brak dostępnego punktu odzyskiwania spójnego na poziomie awarii dla maszyny wirtualnej w ciągu ostatnich "XXX" minut

Poniżej wymieniono niektóre z najczęstszych problemów

### <a name="initial-replication-issues-error-78169"></a>Problemy z replikacją początkową [błąd 78169]

Przed zagwarantowaniem, że nie występują problemy z łącznością, przepustowością lub synchronizacją czasu, upewnij się, że:

- Żadne oprogramowanie chroniące przed wirusami nie blokuje Azure Site Recovery. Dowiedz się [więcej](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) na temat wykluczania folderów wymaganych przez Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Komputery źródłowe z wysoką zmianą [błąd 78188]

Możliwe przyczyny:
- Współczynnik zmian danych (bajty zapisu/s) na wymienionych dyskach maszyny wirtualnej jest większy niż [Azure Site Recovery obsługiwane limity](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) dla typu konta magazynu docelowego replikacji.
- Występuje nagłe zwiększenie współczynnika zmian, ze względu na to, które duże ilości danych oczekuje na przekazanie.

W celu rozwiązania tego problemu:
- Upewnij się, że docelowy typ konta magazynu (w warstwie Standardowa lub Premium) jest obsługiwany zgodnie z wymaganiami dotyczącymi współczynnika zmian w źródle.
- Jeśli przeprowadzasz już replikację do dysku zarządzanego w warstwie Premium (typ asrseeddisk), upewnij się, że rozmiar dysku obsługuje zaobserwowany współczynnik zmian zgodnie z limitami Site Recovery. W razie potrzeby można zwiększyć rozmiar asrseeddisk. Wykonaj poniższe czynności:
    - Przejdź do bloku dyski zagrożonej replikowanej maszyny i skopiuj nazwę dysku repliki
    - Przejdź do tego dysku zarządzanego repliki
    - W bloku przeglądu może zostać wyświetlony transparent informujący o wygenerowaniu adresu URL sygnatury dostępu współdzielonego. Kliknij ten transparent i Anuluj eksport. Zignoruj ten krok, jeśli transparent nie jest widoczny.
    - Gdy tylko adres URL sygnatury dostępu współdzielonego zostanie odwołany, przejdź do bloku Konfiguracja dysku zarządzanego i Zwiększ rozmiar, tak aby Azure Site Recovery obsługiwał częstotliwość zaobserwowanych zmian na dysku źródłowym
- Jeśli zaobserwowane zmiany są tymczasowe, poczekaj kilka godzin, aż oczekujące przekazywanie danych zostanie wyszukane i utworzone w celu utworzenia punktów odzyskiwania.
- Jeśli dysk zawiera dane niekrytyczne, takie jak dzienniki tymczasowe, dane testowe itp., należy rozważyć przeniesienie tych danych w innym miejscu lub całkowite wykluczenie tego dysku z replikacji
- Jeśli problem będzie nadal występował, użyj [planisty wdrażania](site-recovery-deployment-planner.md#overview) Site Recovery, aby pomóc w zaplanowaniu replikacji.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Maszyny źródłowe bez pulsu [Error 78174]

Dzieje się tak, gdy Azure Site Recovery Agent mobilności na maszynie źródłowej nie komunikuje się z serwerem konfiguracji (CS).

Aby rozwiązać ten problem, wykonaj następujące kroki, aby zweryfikować połączenie sieciowe ze źródłowej maszyny wirtualnej do serwera konfiguracji:

1. Sprawdź, czy maszyna źródłowa jest uruchomiona.
2. Zaloguj się do maszyny źródłowej przy użyciu konta z uprawnieniami administratora.
3. Sprawdź, czy następujące usługi są uruchomione, a jeśli nie, uruchom ponownie usługi:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. Na maszynie źródłowej Przejrzyj dzienniki w lokalizacji, aby uzyskać szczegółowe informacje o błędzie:

    *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Serwer przetwarzania bez pulsu [błąd 806]
W przypadku braku pulsu z serwera przetwarzania (PS), sprawdź, czy:
1. Maszyna wirtualna PS jest uruchomiona
2. Sprawdź następujące dzienniki na stronie PS, aby uzyskać szczegółowe informacje o błędzie:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    lub
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Główny serwer docelowy bez pulsu [Error 78022]

Dzieje się tak, gdy Azure Site Recovery Agent mobilności na głównym serwerze docelowym nie komunikuje się z serwerem konfiguracji.

Aby rozwiązać ten problem, wykonaj następujące kroki w celu sprawdzenia stanu usługi:

1. Sprawdź, czy główna docelowa maszyna wirtualna jest uruchomiona.
2. Zaloguj się do głównej docelowej maszyny wirtualnej przy użyciu konta, które ma uprawnienia administratora.
    - Sprawdź, czy usługa svagents jest uruchomiona. Jeśli jest uruchomiona, należy ponownie uruchomić usługę.
    - Sprawdź dzienniki w lokalizacji, aby uzyskać szczegółowe informacje o błędzie:

        *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*
3. Aby zarejestrować główny cel z serwerem konfiguracji, przejdź do folderu **%ProgramData%\ASR\Agent**, a następnie uruchom następujące polecenie w wierszu polecenia:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Identyfikator błędu 78144 — Brak dostępnego na poziomie aplikacji punktu odzyskiwania dla maszyny wirtualnej w ciągu ostatnich "XXX" minut

Wprowadzono ulepszenia w wersjach Agent mobilności [9,23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher)  &  [9,27](site-recovery-whats-new.md#update-rollup-39) do obsługi zachowań błędów instalacji usługi VSS. Upewnij się, że korzystasz z najnowszych wersji, aby uzyskać najlepsze wskazówki dotyczące rozwiązywania problemów z usługą VSS.

Poniżej wymieniono niektóre z najczęstszych problemów

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Przyczyna 1: znany problem w programie SQL Server 2008/2008 R2
**Jak naprawić** : występuje znany problem z programem SQL Server 2008/2008 R2. Zapoznaj się z tym artykułem w bazie wiedzy [Azure Site Recovery agenta lub innej kopii zapasowej usługi VSS, która nie jest składnikiem, kończy się niepowodzeniem na serwerze 2008 SQL Server hostującym](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Przyczyna 2: zadania Azure Site Recovery kończą się niepowodzeniem na serwerach obsługujących dowolną wersję wystąpienia SQL Server z AUTO_CLOSE baz danych
**Jak naprawić** : [artykuł](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) z bazy wiedzy


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Przyczyna 3: znany problem w SQL Server 2016 i 2017
**Jak naprawić** : [artykuł](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) z bazy wiedzy

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Przyczyna 4: App-Consistency nie jest włączona na serwerach z systemem Linux
**Jak naprawić** : Azure Site Recovery dla systemu operacyjnego Linux obsługuje niestandardowe skrypty aplikacji na potrzeby spójności aplikacji. Skrypt niestandardowy z opcjami pre i post będzie używany przez agenta mobilności Azure Site Recovery w celu zapewnienia spójności aplikacji. [Poniżej](./site-recovery-faq.md#replication) przedstawiono kroki, które należy włączyć.

### <a name="more-causes-due-to-vss-related-issues"></a>Więcej przyczyn spowodowanych problemami związanymi z usługą VSS:

Aby przeprowadzić dalsze Rozwiązywanie problemów, sprawdź pliki na maszynie źródłowej, aby uzyskać dokładny kod błędu dla niepowodzenia:

*C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Jak znaleźć błędy w pliku?
Wyszukaj ciąg "vacpError", otwierając plik vacp. log w edytorze

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

W powyższym przykładzie **2147754994** jest kod błędu informujący o niepowodzeniu, jak pokazano poniżej

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Składnik zapisywania usługi VSS nie jest zainstalowany — błąd 2147221164

*Jak naprawić*: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft. Powoduje zainstalowanie dostawcy usługi VSS w celu wykonania migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. Jeśli nie zainstalowano usługi dostawcy VSS, tworzenie migawki spójności aplikacji kończy się niepowodzeniem z IDENTYFIKATORem błędu 0x80040154 "Klasa nie jest zarejestrowana". </br>
Zobacz [artykuł dotyczący rozwiązywania problemów z instalacją składnika zapisywania usługi VSS](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Składnik zapisywania usługi VSS jest wyłączony — błąd 2147943458

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft. Powoduje zainstalowanie dostawcy usługi VSS w celu wykonania migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. W przypadku wyłączenia usługi dostawcy VSS Tworzenie migawki spójności aplikacji kończy się niepowodzeniem z IDENTYFIKATORem błędu "określona usługa jest wyłączona i nie można jej uruchomić (0x80070422)". </br>

- Jeśli usługa VSS jest wyłączona,
    - Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Azure Site Recovery dostawcę usługi VSS
        - Usługa VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED dostawcy usługi VSS — błąd 2147754756

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft.
Sprawdź, czy Azure Site Recovery jest zainstalowana usługa dostawcy usługi VSS. </br>

- Ponów próbę instalacji dostawcy, używając następujących poleceń:
- Odinstaluj istniejącego dostawcę: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Zainstaluj ponownie: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd

Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Azure Site Recovery dostawcę usługi VSS
        - Usługa VDS

## <a name="error-id-95001---insufficient-permissions-found"></a>Identyfikator błędu 95001 — nie znaleziono wystarczających uprawnień

Ten błąd występuje podczas próby włączenia replikacji, a foldery aplikacji nie mają wystarczających uprawnień.

**Jak naprawić**: Aby rozwiązać ten problem, upewnij się, że użytkownik IUSR ma rolę właściciela dla wszystkich wymienionych poniżej folderów —

- *C\ProgramData\Microsoft witryny Azure Recovery\private*
- Katalog instalacji. Na przykład, jeśli katalog instalacyjny to dysk F, wprowadź odpowiednie uprawnienia do-
    - *F:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems*
- Folder *\pushinstallsvc* w katalogu instalacyjnym. Na przykład jeśli katalog instalacyjny to dysk F, podaj odpowiednie uprawnienia do-
    - *F:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc*
- Folder *\etc* w katalogu instalacyjnym. Na przykład jeśli katalog instalacyjny to dysk F, podaj odpowiednie uprawnienia do-
    - *F:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- Wszystkie elementy pod poniższą ścieżką —
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, Opublikuj swoje pytanie na [stronie pytania&](/answers/topics/azure-site-recovery.html)pytań i odpowiedzi dla Azure Site Recovery. Mamy aktywną społeczność i jeden z naszych inżynierów może Ci pomóc.
