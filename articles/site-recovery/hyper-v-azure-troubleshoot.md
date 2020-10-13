---
title: Rozwiązywanie problemów z odzyskiwaniem po awarii funkcji Hyper-V za pomocą Azure Site Recovery
description: Opisuje sposób rozwiązywania problemów z odzyskiwaniem po awarii za pomocą funkcji Hyper-V do replikacji platformy Azure przy użyciu Azure Site Recovery
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: 9e8a1246a2e48c3c569b82845c713ba25ee2467a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426126"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Rozwiązywanie problemów z replikacją i przełączaniem w tryb failover z funkcji Hyper-V do platformy Azure

W tym artykule opisano typowe problemy, które można napotkać podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Włącz problemy z ochroną

Jeśli podczas włączania ochrony maszyn wirtualnych funkcji Hyper-V występują problemy, zapoznaj się z następującymi zaleceniami:

1. Sprawdź, czy hosty i maszyny wirtualne funkcji Hyper-V spełniają wszystkie [wymagania i wymagania wstępne](hyper-v-azure-support-matrix.md).
2. Jeśli serwery funkcji Hyper-V znajdują się w chmurach System Center Virtual Machine Manager (VMM), sprawdź, czy przygotowano [serwer programu VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Sprawdź, czy usługa zarządzania maszynami wirtualnymi funkcji Hyper-V jest uruchomiona na hostach funkcji Hyper-V.
4. Sprawdź, czy występują problemy, które pojawiają się w Hyper-V-VMMS\Admin Zaloguj się do maszyny wirtualnej. Ten dziennik znajduje się w dziennikach **aplikacji i usług**  >  **Microsoft**  >  **Windows**.
5. Na maszynie wirtualnej gościa Sprawdź, czy usługa WMI jest włączona i dostępna.
   - [Poznaj](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) podstawowe testy WMI.
   - [Rozwiązywanie problemów](https://aka.ms/WMiTshooting) WMI.
   - [Rozwiązywanie](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) problemów z skryptami i usługami WMI.
6. Upewnij się, że na maszynie wirtualnej gościa jest uruchomiona Najnowsza wersja usług integracji.
    - [Sprawdź](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , czy masz najnowszą wersję.
    - [Zachowaj](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Usługi integracji są aktualne.
    
## <a name="replication-issues"></a>Problemy dotyczące replikacji

Rozwiązywanie problemów ze wstępną i trwającą replikacją:

1. Upewnij się, że korzystasz z [najnowszej wersji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) usług Site Recovery Services.
2. Sprawdź, czy replikacja została wstrzymana:
   - Sprawdź stan kondycji maszyny wirtualnej w konsoli Menedżera funkcji Hyper-V.
   - Jeśli jest to krytyczne, kliknij prawym przyciskiem myszy maszynę wirtualną, > **Replication**  >  **kondycja replikacji widoku**replikacji.
   - Jeśli replikacja jest wstrzymana, kliknij pozycję **Wznów replikację**.
3. Sprawdź, czy są uruchomione wymagane usługi. Jeśli nie, uruchom je ponownie.
    - W przypadku replikacji funkcji Hyper-V bez programu VMM Sprawdź, czy te usługi są uruchomione na hoście funkcji Hyper-V:
        - Usługa zarządzania maszynami wirtualnymi
        - Usługa agenta usług Microsoft Azure Recovery Services
        - Usługa Microsoft Azure Site Recovery
        - Usługa hosta dostawcy WMI
    - Jeśli przeprowadzasz replikację przy użyciu programu VMM w środowisku, sprawdź, czy są uruchomione następujące usługi:
        - Na hoście funkcji Hyper-V Sprawdź, czy jest uruchomiona usługa zarządzania maszynami wirtualnymi, agent Microsoft Azure Recovery Services i usługa hosta dostawcy WMI.
        - Upewnij się, że usługa System Center Virtual Machine Manager jest uruchomiona na serwerze programu VMM.
4. Sprawdź łączność między serwerem funkcji Hyper-V a platformą Azure. Aby sprawdzić łączność, Otwórz Menedżera zadań na hoście funkcji Hyper-V. Na karcie **wydajność** kliknij pozycję **Otwórz Monitor zasobów**. Na karcie **sieć** > **procesu z aktywnością sieci**Sprawdź, czy cbengine.exe aktywnie wysyła duże ilości danych (MB).
5. Sprawdź, czy hosty funkcji Hyper-V mogą łączyć się z adresem URL obiektów BLOB usługi Azure Storage. Aby sprawdzić, czy hosty mogą się łączyć, zaznacz i sprawdź **cbengine.exe**. Wyświetl **połączenia TCP** , aby zweryfikować łączność z hosta do obiektu BLOB usługi Azure Storage.
6. Sprawdź problemy z wydajnością, zgodnie z poniższym opisem.
    
### <a name="performance-issues"></a>Problemy z wydajnością

Ograniczenia przepustowości sieci mogą mieć wpływ na replikację. Rozwiązywanie problemów w następujący sposób:

1. [Sprawdź](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) , czy w danym środowisku istnieją ograniczenia przepustowości lub ograniczania przepływności.
2. Uruchom narzędzie [planista wdrażania Profiler](hyper-v-deployment-planner-run.md).
3. Po uruchomieniu profilera postępuj zgodnie z zaleceniami dotyczącymi [przepustowości](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) .
4. Sprawdź [ograniczenia zmian danych](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Jeśli widzisz duże zmiany danych na maszynie wirtualnej, wykonaj następujące czynności:
   - Sprawdź, czy maszyna wirtualna jest oznaczona do ponownej synchronizacji.
   - Wykonaj [następujące kroki](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) , aby zbadać Źródło zmian.
   - Zmiany mogą wystąpić, gdy pliki dziennika HRL przekraczają 50% ilości dostępnego miejsca na dysku. Jeśli jest to problem, Zapewnij więcej miejsca do magazynowania dla wszystkich maszyn wirtualnych, na których występuje problem.
   - Sprawdź, czy replikacja nie jest wstrzymana. W takim przypadku kontynuuje zapisywanie zmian w pliku HRL, który może współtworzyć w większym rozmiarze.
 

## <a name="critical-replication-state-issues"></a>Problemy dotyczące stanu replikacji krytycznej

1. Aby sprawdzić kondycję replikacji, Połącz się z lokalną konsolą Menedżera funkcji Hyper-V, wybierz maszynę wirtualną i Sprawdź kondycję.

    ![Kondycja replikacji](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknij pozycję **Wyświetl kondycję replikacji** , aby wyświetlić szczegóły:

    - Jeśli replikacja jest wstrzymana, kliknij prawym przyciskiem myszy maszynę **wirtualną > replikacja**  >  **Wznów replikację**.
    - Jeśli maszyna wirtualna na hoście funkcji Hyper-V skonfigurowana w Site Recovery jest migrowana do innego hosta funkcji Hyper-V w tym samym klastrze lub do komputera autonomicznego, nie ma to wpływu na replikację maszyny wirtualnej. Po prostu Sprawdź, czy nowy host funkcji Hyper-V spełnia wszystkie wymagania wstępne i czy jest skonfigurowany w Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemy dotyczące migawek spójnych na poziomie aplikacji

Migawka spójna na poziomie aplikacji to migawka danych aplikacji znajdujących się w danym momencie w ramach maszyny wirtualnej. Usługa kopiowania woluminów w tle (VSS) gwarantuje, że aplikacje na maszynie wirtualnej są w stanie spójnym podczas tworzenia migawki.  W tej sekcji przedstawiono niektóre typowe problemy, które można napotkać.

### <a name="vss-failing-inside-the-vm"></a>Usługa VSS kończy się niepowodzeniem wewnątrz maszyny wirtualnej

1. Sprawdź, czy zainstalowano i uruchomiono najnowszą wersję usług integracji.  Sprawdź, czy aktualizacja jest dostępna, uruchamiając następujące polecenie w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V: **Get-VM | SELECT Name, State, IntegrationServicesState**.
2. Sprawdź, czy usługi VSS działają i są w dobrej kondycji:
   - Aby sprawdzić usługi, zaloguj się do maszyny wirtualnej gościa. Następnie otwórz wiersz polecenia administratora i uruchom następujące polecenia, aby sprawdzić, czy wszystkie składniki zapisywania usługi VSS są w dobrej kondycji.
       - **Vssadmin List Writers**
       - **Vssadmin List Shadows**
       - **Vssadmin List Providers**
   - Sprawdź dane wyjściowe. Jeśli moduły zapisujące są w stanie niepowodzenia, wykonaj następujące czynności:
       - Sprawdź dziennik zdarzeń aplikacji na maszynie wirtualnej pod kątem błędów operacji usługi VSS.
   - Spróbuj ponownie uruchomić te usługi skojarzone z autorem zakończonego niepowodzeniem:
     - Kopiowanie woluminów w tle
       - Azure Site Recovery dostawcę usługi VSS
   - Po wykonaniu tej czynności poczekaj kilka godzin, aby sprawdzić, czy migawki spójne z aplikacjami zostały pomyślnie wygenerowane.
   - Jako ostatni etap spróbuj ponownie uruchomić maszynę wirtualną. Może to spowodować rozwiązanie usług, które nie odpowiadają.
3. Sprawdź, czy na maszynie wirtualnej nie ma dysków dynamicznych. Nie jest to obsługiwane w przypadku migawek spójnych na poziomie aplikacji. Możesz zaewidencjonować przystawkę Zarządzanie dyskami (diskmgmt. msc).

    ![Dysk dynamiczny](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Sprawdź, czy dysk iSCSI nie jest podłączony do maszyny wirtualnej. Ta funkcja nie jest obsługiwana.
5. Sprawdź, czy usługa tworzenia kopii zapasowych jest włączona. Sprawdź, czy jest włączona w usługach integracji **ustawień funkcji Hyper-V**  >  **Integration Services**.
6. Upewnij się, że nie ma żadnych konfliktów z aplikacjami korzystającymi z migawek VSS. Jeśli wiele aplikacji próbuje wykonać migawki VSS w tym samym czasie, mogą wystąpić konflikty. Jeśli na przykład aplikacja kopii zapasowej pobiera migawki usługi VSS, gdy Site Recovery jest zaplanowana przez zasady replikacji w celu utworzenia migawki.   
7. Sprawdź, czy maszyna wirtualna ma dużą szybkość zmian:
    - Można zmierzyć dzienny współczynnik zmian danych dla maszyn wirtualnych gościa przy użyciu liczników wydajności na hoście funkcji Hyper-V. Aby zmierzyć współczynnik zmian danych, Włącz następujący licznik. Agreguj próbkę tej wartości na dyskach maszyn wirtualnych przez 5-15 minut, aby uzyskać zmiany w maszynie wirtualnej.
        - Kategoria: "wirtualne urządzenie magazynujące funkcji Hyper-V"
        - Licznik: "Bajty zapisu/s"</br>
        - Ta częstotliwość zmian danych zostanie zwiększona lub pozostanie na wysokim poziomie, w zależności od tego, jak zajęta jest maszyna wirtualna lub jej aplikacje.
        - Średni współczynnik zmian danych na dysku źródłowym to 2 MB/s dla magazynu w warstwie Standardowa dla Site Recovery. [Dowiedz się więcej](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Dodatkowo można [sprawdzić cele skalowalności magazynu](../storage/common/scalability-targets-standard-account.md).
8. Upewnij się, że w przypadku korzystania z serwera z systemem Linux włączono spójność aplikacji. [Dowiedz się więcej](./site-recovery-faq.md#replication)
9. Uruchom [planista wdrażania](hyper-v-deployment-planner-run.md).
10. Zapoznaj się z zaleceniami dotyczącymi [sieci](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) i [magazynu](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Usługa VSS kończy się niepowodzeniem wewnątrz hosta funkcji Hyper-V

1. Sprawdź dzienniki zdarzeń pod kątem błędów i zaleceń usługi VSS:
    - Na serwerze hosta funkcji Hyper-v Otwórz dziennik zdarzeń administratora funkcji Hyper-v w **Podgląd zdarzeń**  >  **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **Hyper-v**  >  **administrator**.
    - Sprawdź, czy istnieją jakieś zdarzenia wskazujące na błędy migawek spójnych na poziomie aplikacji.
    - Typowym błędem jest: "funkcja Hyper-V nie może wygenerować zestawu migawek VSS dla maszyny wirtualnej" XYZ ": moduł zapisujący napotkał błąd nieprzejściowy. Ponowne uruchomienie usługi VSS może rozwiązać problemy, jeśli usługa nie odpowiada. "

2. Aby wygenerować migawki usługi VSS dla maszyny wirtualnej, sprawdź, czy usługi integracji funkcji Hyper-V są zainstalowane na maszynie wirtualnej i czy usługa integracji kopii zapasowej (VSS) jest włączona.
    - Upewnij się, że usługi/demony VSS usług Integration Services są uruchomione na gościu i są w **stanie** prawidłowym.
    - Można to sprawdzić w sesji programu PowerShell z podwyższonym poziomem uprawnień na hoście funkcji Hyper-V za pomocą polecenia **Get-VMIntegrationService-VMName \<VMName> -name VSS** można także uzyskać te informacje, logując się do maszyny wirtualnej gościa. [Dowiedz się więcej](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Upewnij się, że usługi integracji kopii zapasowej/VSS na maszynie wirtualnej są uruchomione i w dobrej kondycji. W przeciwnym razie uruchom ponownie te usługi i usługę żądającą kopiowania woluminów w tle funkcji Hyper-V na serwerze hosta funkcji Hyper-V.

### <a name="common-errors"></a>Typowe błędy

**Kod błędu** | **Wiadomość** | **Szczegóły**
--- | --- | ---
**0x800700EA** | "Funkcja Hyper-V nie może wygenerować zestawu migawek VSS dla maszyny wirtualnej: dostępne są więcej danych. (0x800700EA). Generowanie zestawu migawek VSS może zakończyć się niepowodzeniem, jeśli operacja tworzenia kopii zapasowej jest w toku.<br/><br/> Operacja replikacji maszyny wirtualnej nie powiodła się: dostępne są więcej danych ". | Sprawdź, czy na maszynie wirtualnej jest włączony dysk dynamiczny. Ta funkcja nie jest obsługiwana.
**0x80070032** | "Obiekt żądający kopiowania woluminów w tle funkcji Hyper-V nie może nawiązać połączenia z maszyną wirtualną <./VMname>, ponieważ wersja nie jest zgodna z wersją oczekiwaną przez funkcję Hyper-V | Sprawdź, czy są zainstalowane najnowsze aktualizacje systemu Windows.<br/><br/> [Uaktualnij](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) do najnowszej wersji usług Integration Services.



## <a name="collect-replication-logs"></a>Zbieranie dzienników replikacji

Wszystkie zdarzenia replikacji funkcji Hyper-V są rejestrowane w dzienniku Hyper-V-VMMS\Admin, który znajduje się w dziennikach **aplikacji i usług**  >  **Microsoft**  >  **Windows**. Ponadto można włączyć dziennik analityczny dla usługi zarządzania maszynami wirtualnymi funkcji Hyper-V w następujący sposób:

1. Udostępnij dzienniki analityczne i debugowania w Podgląd zdarzeń. Aby udostępnić dzienniki, w Podgląd zdarzeń kliknij pozycję **Wyświetl**  >  **Pokaż dzienniki analityczne i debugowania.** Dziennik analityczny jest wyświetlany w obszarze **Hyper-V — usługa zarządzania**kluczami.
2. W okienku **Akcje** kliknij pozycję **Włącz dziennik**. 

    ![Włącz dziennik](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po włączeniu tego elementu pojawia się on w **monitorze wydajności**jako **Sesja śledzenia zdarzeń** w obszarze **zestawy modułów zbierających dane**. 
4. Aby wyświetlić zebrane informacje, Zatrzymaj sesję śledzenia, wyłączając dziennik. Następnie Zapisz dziennik i otwórz go ponownie w Podgląd zdarzeń lub użyj innych narzędzi, aby przekonwertować go zgodnie z potrzebami.


### <a name="event-log-locations"></a>Lokalizacje dzienników zdarzeń

**Dziennik zdarzeń** | **Szczegóły** |
--- | ---
**Dzienniki aplikacji i usług/Microsoft/VirtualMachineManager/serwer/administrator** (serwer VMM) | Dzienniki służące do rozwiązywania problemów z programem VMM.
**Dzienniki aplikacji i usług/MicrosoftAzureRecoveryServices/replikacja** (host funkcji Hyper-V) | Dzienniki umożliwiające rozwiązywanie problemów z agentem Microsoft Azure Recovery Services. 
**Dzienniki aplikacji i usług/Microsoft/Azure Site Recovery/dostawca/operacyjny** (host funkcji Hyper-V)| Dzienniki służące do rozwiązywania problemów z usługą Microsoft Azure Site Recovery.
**Dzienniki aplikacji i usług/Microsoft/Windows/Hyper-V — usługa zarządzania** usługami (host funkcji Hyper-v) | Dzienniki umożliwiające rozwiązywanie problemów z zarządzaniem MASZYNami wirtualnymi funkcji Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Zbieranie dzienników w celu zaawansowania rozwiązywania problemów

Narzędzia te mogą pomóc w zaawansowaniu rozwiązywania problemów:

-   W przypadku programu VMM wykonaj Site Recovery zbieranie dzienników za pomocą [Narzędzia do obsługi platformy diagnostyki (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   W przypadku funkcji Hyper-V bez programu VMM [Pobierz to narzędzie](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)i uruchom je na hoście funkcji Hyper-V, aby zebrać dzienniki.
