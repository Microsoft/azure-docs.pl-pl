---
title: Konfigurowanie odzyskiwania po awarii Active Directory/DNS przy użyciu Azure Site Recovery
description: W tym artykule opisano sposób implementacji rozwiązania do odzyskiwania po awarii dla Active Directory i systemu DNS z Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132317"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Konfigurowanie odzyskiwania po awarii dla Active Directory i systemu DNS

Aplikacje dla przedsiębiorstw, takie jak SharePoint, Dynamics AX i SAP, zależą od Active Directory i infrastruktury DNS w celu poprawnego działania. Po skonfigurowaniu odzyskiwania po awarii dla aplikacji często należy odzyskać Active Directory i system nazw domen (DNS) przed odzyskanie innych składników aplikacji, aby zapewnić poprawne działanie aplikacji.

Za pomocą [Site Recovery](site-recovery-overview.md) można utworzyć plan odzyskiwania po awarii dla Active Directory. W przypadku wystąpienia przerwy można zainicjować pracę w trybie failover. Usługę Active Directory można uruchomić w ciągu kilku minut. Jeśli wdrożono Active Directory dla wielu aplikacji w lokacji głównej, na przykład w przypadku programu SharePoint i oprogramowania SAP, może zajść potrzeba przełączenia całej lokacji do trybu failover. Najpierw można przejść do trybu failover Active Directory przy użyciu Site Recovery. Następnie można przenieść inne aplikacje do trybu failover przy użyciu planów odzyskiwania specyficznych dla aplikacji.

W tym artykule wyjaśniono, jak utworzyć rozwiązanie odzyskiwania po awarii dla Active Directory. Zawiera wymagania wstępne i instrukcje trybu failover. Przed rozpoczęciem należy zapoznać się z Active Directory i Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli przeprowadzasz replikację do platformy Azure, [Przygotuj zasoby platformy Azure](tutorial-prepare-azure.md), w tym subskrypcję, Virtual Network platformy Azure, konto magazynu i magazyn Recovery Services.
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](./vmware-physical-azure-support-matrix.md) wszystkich składników.

## <a name="replicate-the-domain-controller"></a>Replikowanie kontrolera domeny

- Należy skonfigurować Site Recovery replikację na co najmniej jednej maszynie wirtualnej, która hostuje kontroler domeny lub serwer DNS.
- Jeśli w danym środowisku znajduje się wiele kontrolerów domeny, należy również skonfigurować dodatkowy kontroler domeny w lokacji docelowej. Dodatkowy kontroler domeny może być na platformie Azure lub w pomocniczym lokalnym centrum danych.
- Jeśli masz tylko kilka aplikacji i jeden kontroler domeny, możesz chcieć jednocześnie przełączyć całą lokację do trybu failover. W tym przypadku zalecamy używanie Site Recovery do replikowania kontrolera domeny do lokacji docelowej, na platformie Azure lub w pomocniczym lokalnym centrum danych. Do [testowania pracy w trybie failover](#test-failover-considerations)można użyć tego samego zreplikowanego kontrolera domeny lub maszyny wirtualnej DNS.
- Jeśli masz wiele aplikacji i więcej niż jeden kontroler domeny w środowisku lub jeśli planujesz awaryjne przełączenie kilku aplikacji jednocześnie, oprócz replikowania maszyny wirtualnej kontrolera domeny z Site Recovery zalecamy skonfigurowanie dodatkowego kontrolera domeny w lokacji docelowej (na platformie Azure lub w pomocniczym lokalnym centrum danych). W przypadku [testowego przełączania do trybu failover](#test-failover-considerations)można użyć kontrolera domeny, który jest replikowany przez Site Recovery. W przypadku trybu failover można użyć dodatkowego kontrolera domeny w lokacji docelowej.

## <a name="enable-protection-with-site-recovery"></a>Włącz ochronę za pomocą Site Recovery

Za pomocą Site Recovery można chronić maszynę wirtualną, która hostuje kontroler domeny lub serwer DNS.

### <a name="protect-the-vm"></a>Ochrona maszyny wirtualnej

Kontroler domeny, który jest replikowany za pomocą Site Recovery jest używany do [testowania pracy w trybie failover](#test-failover-considerations). Upewnij się, że spełnia on następujące wymagania:

1. Kontroler domeny jest serwerem wykazu globalnego.
1. Kontroler domeny powinien być elastycznym właścicielem roli pojedynczego wzorca (FSMO) dla ról, które są odpowiednie podczas testu pracy w trybie failover. W przeciwnym razie te role będą musiały zostać [przejęte](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) po przejściu do trybu failover.

### <a name="configure-vm-network-settings"></a>Konfigurowanie ustawień sieci maszyny wirtualnej

W przypadku maszyny wirtualnej, która hostuje kontroler domeny lub serwer DNS, w Site Recovery Skonfiguruj ustawienia sieci w obszarze Ustawienia **obliczeń i sieci** replikowanej maszyny wirtualnej. Gwarantuje to, że maszyna wirtualna jest dołączona do odpowiedniej sieci po przejściu do trybu failover.

## <a name="protect-active-directory"></a>Ochrona Active Directory

### <a name="site-to-site-protection"></a>Ochrona lokacja-lokacja

Utwórz kontroler domeny w lokacji dodatkowej. Podczas podwyższania poziomu serwera do roli kontrolera domeny należy określić nazwę tej samej domeny, która jest używana w lokacji głównej. Za pomocą przystawki **Lokacje i usługi Active Directory** można skonfigurować ustawienia obiektu linku lokacji, do którego dodawane są lokacje. Konfigurując ustawienia w łączu lokacji, można kontrolować, kiedy replikacja odbywa się między dwiema lub większą liczbą lokacji i jak często występuje. Aby uzyskać więcej informacji, zobacz [planowanie replikacji między lokacjami](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Ochrona między lokacjami

Najpierw utwórz kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższania poziomu serwera do roli kontrolera domeny należy określić tę samą nazwę domeny, która jest używana w lokacji głównej.

Następnie ponownie skonfiguruj serwer DNS dla sieci wirtualnej, aby korzystał z serwera DNS na platformie Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Sieć platformy Azure":::

### <a name="azure-to-azure-protection"></a>Ochrona systemu Azure na platformę Azure

Najpierw utwórz kontroler domeny w sieci wirtualnej platformy Azure. Podczas podwyższania poziomu serwera do roli kontrolera domeny należy określić tę samą nazwę domeny, która jest używana w lokacji głównej.

Następnie ponownie skonfiguruj serwer DNS dla sieci wirtualnej, aby korzystał z serwera DNS na platformie Azure.

## <a name="test-failover-considerations"></a>Uwagi dotyczące testowania pracy w trybie failover

Aby uniknąć wpływu na obciążenia produkcyjne, test pracy w trybie failover odbywa się w sieci, która jest odizolowana od sieci produkcyjnej.

Większość aplikacji wymaga obecności kontrolera domeny lub serwera DNS. W związku z tym przed przełączeniem aplikacji w tryb failover należy utworzyć kontroler domeny w sieci izolowanej, aby można było go używać na potrzeby testowego przełączania. Najprostszym sposobem jest użycie Site Recovery do replikowania maszyny wirtualnej, która hostuje kontroler domeny lub serwer DNS. Następnie uruchom test trybu failover maszyny wirtualnej kontrolera domeny przed uruchomieniem testowej pracy w trybie failover planu odzyskiwania dla aplikacji.

1. Użyj Site Recovery, aby [replikować](vmware-azure-tutorial.md) maszynę wirtualną, która hostuje kontroler domeny lub serwer DNS.
1. Utwórz sieć izolowaną. Każda sieć wirtualna utworzona na platformie Azure jest domyślnie odizolowana od innych sieci. Zalecamy używanie tego samego zakresu adresów IP dla tej sieci, która jest używana w sieci produkcyjnej. Nie włączaj łączności między lokacjami w tej sieci.
1. Podaj adres IP DNS w sieci izolowanej. Użyj adresu IP, który ma zostać pobrany przez maszynę wirtualną DNS. Jeśli wykonujesz replikację do platformy Azure, podaj adres IP maszyny wirtualnej używanej w trybie failover. Aby wprowadzić adres IP, na zreplikowanej maszynie wirtualnej w ustawieniach **obliczenia i sieci** wybierz **docelowe ustawienia adresu IP** .

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure test Network":::

   > [!TIP]
   > Site Recovery próbuje utworzyć testowe maszyny wirtualne w podsieci o tej samej nazwie i przy użyciu tego samego adresu IP, który jest podany w ustawieniach **obliczeniowych i sieciowych** maszyny wirtualnej. Jeśli podsieć o tej samej nazwie nie jest dostępna w sieci wirtualnej platformy Azure podanej na potrzeby testowego przejścia w tryb failover, testowa maszyna wirtualna jest tworzona w pierwszej podsieci w kolejności alfabetycznej.
   >
   > Jeśli docelowy adres IP należy do wybranej podsieci, Site Recovery próbuje utworzyć maszynę wirtualną testowej pracy w trybie failover przy użyciu docelowego adresu IP. Jeśli docelowy adres IP nie należy do wybranej podsieci, maszyna wirtualna testowego trybu failover jest tworzona przy użyciu następnego dostępnego adresu IP w wybranej podsieci.

### <a name="test-failover-to-a-secondary-site"></a>Testowanie pracy w trybie failover w lokacji dodatkowej

1. Jeśli wykonujesz replikację do innej lokacji lokalnej i używasz protokołu DHCP, [Skonfiguruj DNS i DHCP na potrzeby testowego przełączania do trybu failover](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Wykonaj test pracy w trybie failover maszyny wirtualnej kontrolera domeny, która działa w sieci izolowanej. Użyj najnowszego dostępnego punktu odzyskiwania _spójnego na poziomie aplikacji_ maszyny wirtualnej kontrolera domeny w celu przeprowadzenia testowej pracy w trybie failover.
1. Uruchom test pracy w trybie failover dla planu odzyskiwania zawierającego maszyny wirtualne, na których działa aplikacja.
1. Po zakończeniu testowania _Oczyść test pracy w trybie failover_ na maszynie wirtualnej kontrolera domeny. Ten krok powoduje usunięcie kontrolera domeny, który został utworzony na potrzeby testowego przełączania do trybu failover.

### <a name="remove-references-to-other-domain-controllers"></a>Usuwanie odwołań do innych kontrolerów domeny

Po zainicjowaniu testowej pracy w trybie failover nie należy uwzględniać wszystkich kontrolerów domeny w sieci testowej. Aby usunąć odwołania do innych kontrolerów domeny istniejących w środowisku produkcyjnym, może być konieczne [przejęcie ról FSMO Active Directory](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) i [oczyszczenie metadanych](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) dla brakujących kontrolerów domeny.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemy spowodowane przez zabezpieczenia wirtualizacji

> [!IMPORTANT]
> Niektóre konfiguracje opisane w tej sekcji nie są standardowymi lub domyślnymi konfiguracjami kontrolerów domeny. Jeśli nie chcesz wprowadzać tych zmian na kontrolerze domeny produkcyjnej, możesz utworzyć kontroler domeny, który jest przeznaczony dla Site Recovery używany do testowania pracy w trybie failover. Wprowadź te zmiany tylko do tego kontrolera domeny.

Począwszy od systemu Windows Server 2012, [dodatkowe zabezpieczenia są wbudowane w Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Zabezpieczenia te ułatwiają ochronę zwirtualizowanych kontrolerów domeny przed wycofywaniem numerów sekwencji aktualizacji (USN), jeśli bazowa platforma hypervisor obsługuje **maszynę wirtualną-GenerationId**. Platforma Azure obsługuje **maszynę wirtualną GenerationId**. Z tego powodu kontrolery domeny z systemem Windows Server 2012 lub nowszym na maszynach wirtualnych platformy Azure mają takie dodatkowe zabezpieczenia.

Po zresetowaniu **maszyny wirtualnej GenerationId** jest również resetowana wartość **InvocationID** bazy danych AD DS. Ponadto Pula identyfikatorów względnych (RID) została odrzucona, a `SYSVOL` folder jest oznaczony jako nieautorytatywny. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Active Directory Domain Services wirtualizacji](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) i [bezpiecznego wirtualizacji rozproszony system plików replikacji (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Przełączenie w tryb failover do platformy Azure może spowodować zresetowanie **maszyny wirtualnej GenerationId** . Resetowanie **maszyny wirtualnej — GenerationId** wyzwala dodatkowe zabezpieczenia w przypadku uruchamiania maszyny wirtualnej kontrolera domeny na platformie Azure. Może to spowodować znaczne opóźnienie w możliwości zalogowania się do maszyny wirtualnej kontrolera domeny.

Ponieważ ten kontroler domeny jest używany tylko w testowaniu pracy w trybie failover, zabezpieczenia wirtualizacji nie są konieczne. Aby upewnić się, że wartość **GenerationId** maszyny wirtualnej kontrolera domeny nie ulega zmianie, można zmienić wartość następujących elementów `DWORD` na **4** na lokalnym kontrolerze domeny:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Objawy zabezpieczeń wirtualizacji

Jeśli zabezpieczenia wirtualizacji są wyzwalane po testowym przejściu w tryb failover, może pojawić się jeden lub więcej z następujących objawów:

- Wartość **GenerationId** zmieni się:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Zmiana identyfikatora generacji":::

- Wartość **InvocationID** zmieni się:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Zmiana identyfikatora wywołania":::

- `SYSVOL` folder i `NETLOGON` udziały nie są dostępne.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Udział folderu SYSVOL":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Folder SYSVOL NtFrs":::

- Bazy danych DFSR są usuwane.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Bazy danych DFSR są usuwane":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Rozwiązywanie problemów z kontrolerem domeny podczas testu pracy w trybie failover

> [!IMPORTANT]
> Niektóre konfiguracje opisane w tej sekcji nie są standardowymi lub domyślnymi konfiguracjami kontrolerów domeny. Jeśli nie chcesz wprowadzać tych zmian na kontrolerze domeny produkcyjnej, możesz utworzyć kontroler domeny, który jest przeznaczony do Site Recovery testowej pracy w trybie failover. Wprowadź zmiany tylko do tego dedykowanego kontrolera domeny.

1. W wierszu polecenia Uruchom następujące polecenie, aby sprawdzić, czy `SYSVOL` folder i `NETLOGON` folder są udostępnione:

    `NET SHARE`

1. W wierszu polecenia Uruchom następujące polecenie, aby upewnić się, że kontroler domeny działa prawidłowo:

    `dcdiag /v > dcdiag.txt`

1. W dzienniku danych wyjściowych poszukaj następującego tekstu. Tekst potwierdza, że kontroler domeny działa prawidłowo.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Jeśli powyższe warunki są spełnione, prawdopodobnie kontroler domeny działa prawidłowo. Jeśli nie, wykonaj następujące czynności:

1. Wykonaj Autorytatywne przywracanie kontrolera domeny. Należy pamiętać o następujących kwestiach:

    - Chociaż nie zalecamy replikacji przy użyciu [usługi replikacji plików (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379), w przypadku korzystania z replikacji usługi FRS wykonaj kroki przywracania autorytatywnego. Ten proces jest opisany w temacie [przy użyciu klucza rejestru BURFLAGS, aby ponownie zainicjować usługę replikacji plików](https://support.microsoft.com/kb/290762).

      Aby uzyskać więcej informacji na temat BurFlags, zobacz wpis w blogu [D2 i D4: co to jest?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - W przypadku korzystania z replikacji DFSR wykonaj kroki przywracania autorytatywnego. Ten proces jest opisany w [Wymuszaj synchronizację autorytatywną i nieautorytatywną dla folderu SYSVOL replikowanego przez usługę DFSR (na przykład "D4/D2" w przypadku usługi FRS)](https://support.microsoft.com/kb/2218556).

      Można również użyć funkcji programu PowerShell. Aby uzyskać więcej informacji, zobacz usługi [DFSR-SYSVOL autorytatywne/nieautorytatywne Przywróć funkcje programu PowerShell](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Pomiń wymaganie synchronizacji początkowej przez ustawienie następującego klucza rejestru na **0** na lokalnym kontrolerze domeny. Jeśli `DWORD` nie istnieje, można utworzyć ją w węźle **Parameters** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z identyfikatorem zdarzenia dns 4013: serwer DNS nie może załadować stref DNS zintegrowanych z usługą AD](https://support.microsoft.com/kb/2001093).

1. Wyłącz wymaganie, aby serwer wykazu globalnego był dostępny do weryfikacji logowania użytkownika. W tym celu w lokalnym kontrolerze domeny Ustaw następujący klucz rejestru na **1**. Jeśli `DWORD` nie istnieje, można utworzyć ją w węźle **LSA** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Aby uzyskać więcej informacji, zobacz [jak działa wykaz globalny](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>System DNS i kontroler domeny na różnych komputerach

Jeśli na tej samej maszynie wirtualnej jest uruchomiony kontroler domeny i system DNs, można pominąć tę procedurę.

Jeśli usługa DNS nie znajduje się na tej samej maszynie wirtualnej, co kontroler domeny, należy utworzyć maszynę wirtualną DNS dla testu pracy w trybie failover. Możesz użyć nowego serwera DNS i utworzyć wszystkie wymagane strefy. Na przykład jeśli Twoja domena Active Directory to `contoso.com` , możesz utworzyć strefę DNS o nazwie `contoso.com` . Wpisy, które odpowiadają Active Directory muszą zostać zaktualizowane w systemie DNS w następujący sposób:

1. Upewnij się, że te ustawienia są stosowane przed rozpoczęciem jakiejkolwiek innej maszyny wirtualnej w planie odzyskiwania:

   - Strefa musi mieć nazwę po nazwie głównej lasu.
   - Strefa musi być oparta na plikach.
   - Strefa musi być włączona dla bezpiecznych i niezabezpieczonych aktualizacji.
   - Program rozpoznawania maszyny wirtualnej, który hostuje kontroler domeny, powinien wskazywać adres IP maszyny wirtualnej DNS.

1. Uruchom następujące polecenie na maszynie wirtualnej, która hostuje kontroler domeny:

   `nltest /dsregdns`

1. Uruchom następujące polecenia, aby dodać strefę na serwerze DNS, zezwolić na niezabezpieczone aktualizacje i dodać wpis dla strefy do systemu DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń przedsiębiorstwa za pomocą Azure Site Recovery.
