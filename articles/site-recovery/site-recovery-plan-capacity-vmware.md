---
title: Planowanie wydajności odzyskiwania po awarii programu VMware przy użyciu Azure Site Recovery
description: Ten artykuł ułatwia planowanie pojemności i skalowania podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368030"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planowanie pojemności i skalowanie na potrzeby odzyskiwania po awarii oprogramowania VMware na platformę Azure

Ten artykuł służy do planowania pojemności i skalowania w przypadku replikowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak mogę rozpocząć planowanie pojemności?

Aby dowiedzieć się więcej o wymaganiach dotyczących infrastruktury Azure Site Recovery, Zbierz informacje o środowisku replikacji, uruchamiając [planista wdrażania usługi Azure Site Recovery](./site-recovery-deployment-planner.md) na potrzeby replikacji oprogramowania VMware. Aby uzyskać więcej informacji, zobacz [Informacje o Site Recovery planista wdrażania dla oprogramowania VMware na platformie Azure](site-recovery-deployment-planner.md). 

Site Recovery Planista wdrażania zawiera raport, który zawiera pełne informacje o zgodnych i niezgodnych maszynach wirtualnych, dyskach na maszynę wirtualną oraz o postępach danych na dysku. Narzędzie podsumowuje także wymagania dotyczące przepustowości sieci w celu spełnienia docelowego celu punktu odzyskiwania i infrastruktury platformy Azure, która jest wymagana do pomyślnej replikacji i testowego przejścia w tryb failover.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

Składnik | Szczegóły
--- | ---
**Replikacja** | **Maksymalny dzienny współczynnik zmian**: komputer chroniony może korzystać tylko z jednego serwera przetwarzania. Pojedynczy serwer przetwarzania może obsłużyć dziennie do 2 TB zmian. W związku z tym 2 TB jest maksymalną dzienną szybkością zmiany danych obsługiwaną przez maszynę chronioną.<br /><br /> **Maksymalna przepływność**: replikowana maszyna może należeć do jednego konta magazynu na platformie Azure. Standardowe konto usługi Azure Storage może obsłużyć maksymalnie 20 000 żądań na sekundę. Zalecamy ograniczenie liczby operacji wejścia/wyjścia na sekundę (IOPS) na maszynę źródłową do 20 000. Na przykład jeśli masz maszynę źródłową, która ma pięć dysków, a każdy dysk generuje 120 operacji we/wy (8 KB rozmiaru) na maszynie źródłowej, maszyna źródłowa jest w limicie liczby operacji we/wy dysku na dysk (500). (Liczba wymaganych kont magazynu jest równa łącznej liczbie operacji we/wy maszyny źródłowej podzielonej przez 20 000).
**Serwer konfiguracji** | Serwer konfiguracji musi być w stanie obsługiwać dzienną szybkość zmiany wydajności dla wszystkich obciążeń uruchomionych na chronionych maszynach. Komputer konfiguracji musi mieć wystarczającą przepustowość, aby ciągle replikować dane do usługi Azure Storage.<br /><br /> Najlepszym rozwiązaniem jest umieszczenie serwera konfiguracji w tej samej sieci i segmencie LAN co maszyny, które mają być chronione. Serwer konfiguracji można umieścić w innej sieci, ale maszyny, które mają być chronione, powinny mieć widoczność sieci w warstwie 3.<br /><br /> Zalecenia dotyczące rozmiaru serwera konfiguracji zostały podsumowane w tabeli w następującej sekcji.
**Serwer przetwarzania** | Pierwszy serwer przetwarzania jest instalowany domyślnie na serwerze konfiguracji. Można wdrożyć dodatkowe serwery przetwarzania w celu skalowania środowiska. <br /><br /> Serwer przetwarzania otrzymuje dane replikacji z chronionych maszyn. Serwer przetwarzania optymalizuje dane przy użyciu pamięci podręcznej, kompresji i szyfrowania. Następnie serwer przetwarzania wysyła dane do platformy Azure. Komputer serwera przetwarzania musi mieć wystarczającą ilość zasobów do wykonania tych zadań.<br /><br /> Serwer przetwarzania korzysta z pamięci podręcznej opartej na dyskach. Użyj oddzielnego dysku pamięci podręcznej o 600 GB lub więcej, aby obsłużyć zmiany danych, które są przechowywane, jeśli wystąpi wąskie gardło lub awaria sieci.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Zalecenia dotyczące rozmiaru serwera konfiguracji i serwera przetwarzania skompilowanego

Serwer konfiguracji, który korzysta z skompilowanego serwera przetwarzania do ochrony obciążenia, może obsłużyć do 200 maszyn wirtualnych w oparciu o następujące konfiguracje:

Procesor CPU | Memory (Pamięć) | Rozmiar dysku pamięci podręcznej | Szybkość zmian danych | Chronione maszyny
--- | --- | --- | --- | ---
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | Służy do replikowania mniej niż 100 maszyn.
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | Służy do replikowania 100 do 150 maszyn.
16 procesorów wirtualnych vCPU (2 gniazda * 8 rdzeni \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB do 2 TB | Służy do replikowania 151 do 200 maszyn.
Wdróż inny serwer konfiguracji przy użyciu [szablonu OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Wdróż nowy serwer konfiguracji, jeśli wykonujesz replikację więcej niż 200 maszyn.
Wdróż inny [serwer przetwarzania](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Wdróż nowy serwer przetwarzania skalowalnego w poziomie, jeśli ogólna liczba codziennych zmian danych jest większa niż 2 TB.

W tych konfiguracjach:

* Każda maszyna źródłowa ma trzy dyski o pojemności 100 GB każdego z nich.
* W przypadku pomiarów dyskowych pamięci podręcznej użyto macierzy magazynowych o ośmiu dyskach sygnatury dostępu współdzielonego 10 K/min.

## <a name="size-recommendations-for-the-process-server"></a>Zalecenia dotyczące rozmiaru serwera przetwarzania

Serwer przetwarzania to składnik, który obsługuje replikację danych w Azure Site Recovery. Jeśli dzienny współczynnik zmian jest większy niż 2 TB, należy dodać serwery przetwarzania skalowalnego w poziomie w celu obsługi obciążenia replikacji. Aby skalować w poziomie, możesz:

* Zwiększ liczbę serwerów konfiguracji przez wdrożenie przy użyciu [szablonu OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Można na przykład chronić do 400 maszyn przy użyciu dwóch serwerów konfiguracji.
* Dodawanie [serwerów przetwarzania skalowalnego](vmware-azure-set-up-process-server-scale.md#download-installation-file)w poziomie. Używaj skalowalnych w poziomie serwerów procesów do obsługi ruchu związanego z replikacją zamiast (lub oprócz) serwera konfiguracji.

W poniższej tabeli opisano ten scenariusz:

* Skonfiguruj serwer przetwarzania skalowalny w poziomie.
* Chronione maszyny wirtualne można skonfigurować tak, aby korzystały z serwera przetwarzania skalowalnego w poziomie.
* Każda chroniona maszyna źródłowa ma trzy dyski o pojemności 100 GB każdego z nich.

Dodatkowy serwer przetwarzania | Rozmiar dysku pamięci podręcznej | Szybkość zmian danych | Chronione maszyny
--- | --- | --- | ---
4 procesorów wirtualnych vCPU (2 gniazda * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Służy do replikowania 85 lub mniej maszyn.
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci | 600 GB | 251 GB do 1 TB | Służy do replikowania 86 do 150 maszyn.
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz) 24 GB pamięci | 1 TB | >1 TB do 2 TB | Służy do replikowania 151 do 225 maszyn.

Sposób skalowania serwerów zależy od preferencji dla modelu skalowania w górę lub w poziomie. Aby skalować w górę, wdróż kilka serwerów konfiguracji wysokiej klasy i serwerów przetwarzania. Aby skalować w poziomie, wdróż więcej serwerów z mniejszą ilością zasobów. Na przykład jeśli chcesz chronić maszyny 200 z ogólną częstotliwością dziennej zmiany danych wynoszącą 1,5 TB, możesz wykonać jedną z następujących czynności:

* Skonfiguruj pojedynczy serwer przetwarzania (16 vCPU, 24 GB pamięci RAM).
* Skonfiguruj dwa serwery przetwarzania (2 x 8 vCPU, 2 * 12 GB pamięci RAM).

## <a name="control-network-bandwidth"></a>Sterowanie przepustowością sieci

Po użyciu [Planista wdrażania Site Recovery](site-recovery-deployment-planner.md) , aby obliczyć przepustowość potrzebną do replikacji (replikacja początkowa, a następnie Delta), istnieje kilka opcji kontrolowania przepustowości używanej na potrzeby replikacji:

* **Ograniczanie przepustowości**: ruch VMware replikowany do platformy Azure odbywa się za pomocą określonego serwera przetwarzania. Przepustowość można ograniczyć na maszynach, na których działają jako serwery przetwarzania.
* **Wpływ na przepustowość**: można mieć wpływ na przepustowość używaną do replikacji przy użyciu kilku kluczy rejestru:
  * Wartość rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** określa liczbę wątków używanych na potrzeby transferu danych (replikacja początkowa lub różnicowa) dysku. Wyższa wartość zwiększa przepustowość sieci używaną podczas replikacji.
  * Wartość rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** określa liczbę wątków, które są używane na potrzeby transferu danych podczas powrotu po awarii.

### <a name="throttle-bandwidth"></a>Ograniczanie przepustowości

1. Otwórz przystawkę MMC Azure Backup na komputerze używanym jako serwer przetwarzania. Domyślnie skrót do kopii zapasowej jest dostępny na pulpicie lub w następującym folderze: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. W przystawce wybierz pozycję **Zmień właściwości**.

    ![Zrzut ekranu opcji przystawka MMC Azure Backup, aby zmienić właściwości](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na karcie **ograniczanie** wybierz pozycję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**. Ustaw limity dla godzin pracy i czasu wolnego. Prawidłowe zakresy są z zakresu od 512 KB/s do 1 023 MB/s.

    ![Zrzut ekranu przedstawiający okno dialogowe właściwości Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

Możesz też użyć polecenia cmdlet [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)), aby ustawić ograniczanie przepływności. Oto przykład:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** wskazuje, że ograniczanie przepływności nie jest wymagane.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Zmiana przepustowości sieci dla maszyny wirtualnej

1. W rejestrze maszyny wirtualnej przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Aby zmienić ruch związany z przepustowością na dysku replikacji, należy zmodyfikować wartość **UploadThreadsPerVM**. Utwórz klucz, jeśli nie istnieje.
   * Aby zmienić przepustowość dla ruchu powrotu po awarii z platformy Azure, zmodyfikuj wartość **DownloadThreadsPerVM**.
2. Wartość domyślna dla każdego klucza to **4**. W sieci „o nadmiarowych zasobach” należy zmienić wartości domyślne tych kluczy rejestru. Maksymalna wartość, której można użyć, to **32**. Monitoruj ruch, aby zoptymalizować tę wartość.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Konfigurowanie infrastruktury Site Recovery do ochrony ponad 500 maszyn wirtualnych

Przed skonfigurowaniem infrastruktury Site Recovery należy uzyskać dostęp do środowiska w celu zmierzenia następujących czynników: zgodnych maszyn wirtualnych, dziennego współczynnika zmian danych, wymaganej przepustowości sieci dla celu punktu odzyskiwania, liczby wymaganych składników Site Recovery i czasu potrzebnego do ukończenia replikacji początkowej. Wykonaj następujące kroki, aby zebrać wymagane informacje:

1. Aby zmierzyć te parametry, należy uruchomić Site Recovery Planista wdrażania w danym środowisku. Aby uzyskać przydatne wskazówki, zobacz [Informacje o Site Recovery planista wdrażania dla oprogramowania VMware na platformie Azure](site-recovery-deployment-planner.md).
2. Wdróż serwer konfiguracji, który spełnia wymagania [dotyczące rozmiaru serwera konfiguracji](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Jeśli obciążenie produkcyjne przekracza 650 maszyn wirtualnych, wdróż inny serwer konfiguracji.
3. W oparciu o mierzoną liczbę codziennych zmian danych Wdróż [serwery przetwarzania skalowalne](vmware-azure-set-up-process-server-scale.md#download-installation-file) w poziomie za pomocą pomocy [dotyczącej wytycznych dotyczących rozmiaru](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Jeśli spodziewasz się, że szybkość zmian danych maszyny wirtualnej dysku przekroczy 2 MB/s, upewnij się, że używasz dysków zarządzanych w warstwie Premium. Site Recovery Planista wdrażania uruchamiany przez określony przedział czasu. Szczytowy współczynnik zmian danych w innych przypadkach może nie być przechwytywany w raporcie.
5. [Ustaw przepustowość sieci](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) na podstawie celu punktu odzyskiwania, który ma zostać osiągnięty.
6. Po skonfigurowaniu infrastruktury Włącz odzyskiwanie awaryjne dla obciążenia. Aby dowiedzieć się, jak to zrobić, zobacz [Konfigurowanie środowiska źródłowego na potrzeby replikacji oprogramowania VMware do platformy Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Wdrażanie dodatkowych serwerów przetwarzania

W przypadku skalowania wdrożenia poza 200 maszynami źródłowymi lub w przypadku całkowitego dziennego współczynnika zmian wynoszącego więcej niż 2 TB, należy dodać serwery przetwarzania do obsługi natężenia ruchu. Ulepszono produkt w wersji 9,24, aby zapewnić [alerty serwera przetwarzania](vmware-physical-azure-monitor-process-server.md#process-server-alerts) podczas konfigurowania serwera przetwarzania skalowalnego w poziomie. [Skonfiguruj serwer przetwarzania](vmware-azure-set-up-process-server-scale.md) w celu ochrony nowych maszyn źródłowych lub [Zrównoważ obciążenie](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrowanie maszyn w celu korzystania z nowego serwera przetwarzania

1. Wybierz pozycję **Ustawienia**  >  **Site Recovery serwery**. Wybierz serwer konfiguracji, a następnie rozwiń węzeł **serwery przetwarzania**.

    ![Zrzut ekranu przedstawiający okno dialogowe serwer przetwarzania](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kliknij prawym przyciskiem myszy aktualnie używany serwer przetwarzania, a następnie wybierz polecenie **Przełącz**.

    ![Zrzut ekranu przedstawiający okno dialogowe serwer konfiguracji](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Na stronie **Wybierz docelowy serwer przetwarzania** wybierz nowy serwer przetwarzania, którego chcesz użyć. Następnie wybierz maszyny wirtualne, które będą obsługiwane przez serwer. Aby uzyskać informacje o serwerze, wybierz ikonę informacji. Aby ułatwić podejmowanie decyzji dotyczących obciążenia, zostanie wyświetlony średni obszar, który jest wymagany do replikowania każdej wybranej maszyny wirtualnej na nowy serwer przetwarzania. Zaznacz znacznik wyboru, aby rozpocząć replikację do nowego serwera przetwarzania.

## <a name="deploy-additional-master-target-servers"></a>Wdrażanie dodatkowych głównych serwerów docelowych

W następujących scenariuszach jest wymagany więcej niż jeden główny serwer docelowy:

*   Chcesz chronić maszynę wirtualną z systemem Linux.
*   Główny serwer docelowy dostępny na serwerze konfiguracji nie ma dostępu do magazynu danych maszyny wirtualnej.
*   Łączna liczba dysków na głównym serwerze docelowym (liczba dysków lokalnych na serwerze i liczba dysków, które mają być objęte ochroną) jest większa niż 60 dysków.

Aby dowiedzieć się, jak dodać główny serwer docelowy dla maszyny wirtualnej opartej na systemie Linux, zobacz [Instalowanie głównego serwera docelowego systemu Linux na potrzeby powrotu po awarii](vmware-azure-install-linux-master-target.md).

Aby dodać główny serwer docelowy dla maszyny wirtualnej z systemem Windows:

1. Przejdź do **magazynu Recovery Services**  >  **Site Recovery**  >  **serwery konfiguracji** infrastruktury.
2. Wybierz wymagany serwer konfiguracji, a następnie wybierz opcję **główny serwer docelowy**.

    ![Zrzut ekranu przedstawiający przycisk Dodaj główny serwer docelowy](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Pobierz plik ujednoliconej konfiguracji, a następnie uruchom plik na maszynie wirtualnej, aby skonfigurować główny serwer docelowy.
4. Wybierz pozycję **Zainstaluj główny serwer docelowy**  >  **dalej**.

    ![Zrzut ekranu, który pokazuje Wybieranie opcji instalacji głównego serwera docelowego](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Wybierz domyślną lokalizację instalacji, a następnie wybierz pozycję **Zainstaluj**.

     ![Zrzut ekranu pokazujący domyślną lokalizację instalacji](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Aby zarejestrować główny element docelowy na serwerze konfiguracji, wybierz pozycję **Kontynuuj do konfiguracji**.

    ![Zrzut ekranu pokazujący przycisk przechodzenie do konfiguracji](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Wprowadź adres IP serwera konfiguracji, a następnie wprowadź hasło. Aby dowiedzieć się, jak wygenerować hasło, zobacz [generowanie hasła serwera konfiguracji](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Zrzut ekranu pokazujący, gdzie wprowadzić adres IP i hasło dla serwera konfiguracji](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Wybierz pozycję **Zarejestruj**. Po zakończeniu rejestracji wybierz pozycję **Zakończ**.

Po pomyślnym zakończeniu rejestracji serwer jest wymieniony w Azure Portal w **magazynie Recovery Services**  >  **Site Recovery**  >  **serwery konfiguracji** infrastruktury na głównych serwerach docelowych serwera konfiguracji.

 > [!NOTE]
 > Pobierz najnowszą wersję [ujednoliconego pliku instalacji głównego serwera docelowego dla systemu Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Następne kroki

Pobierz i uruchom [Site Recovery planista wdrażania](https://aka.ms/asr-deployment-planner).