---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure za pomocą Azure Site Recovery
description: Rozwiązywanie problemów z replikacją w Azure Site Recovery ramach odzyskiwania po awarii maszyny wirtualnej platformy Azure
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130406"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Rozwiązywanie problemów z replikacją w ramach odzyskiwania po awarii maszyny wirtualnej Azure

W tym artykule opisano typowe problemy w Azure Site Recovery w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Wyjaśniono również, jak rozwiązywać typowe problemy. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [Macierz obsługi w celu replikowania maszyn wirtualnych platformy Azure](./azure-to-azure-support-matrix.md).

Azure Site Recovery spójnie replikuje dane z regionu źródłowego do regionu odzyskiwania po awarii. Tworzy również punkt odzyskiwania spójny na poziomie awarii co 5 minut. Jeśli Site Recovery nie może utworzyć punktów odzyskiwania przez 60 minut, powiadamia o tych informacjach:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

W poniższych sekcjach opisano przyczyny i rozwiązania.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Wysoki współczynnik zmian danych na źródłowej maszynie wirtualnej

Azure Site Recovery tworzy zdarzenie, jeśli współczynnik zmian danych na źródłowej maszynie wirtualnej jest większy niż obsługiwane limity. Aby sprawdzić, czy problem jest spowodowany dużym zmianą, przejdź do **pozycji zreplikowane elementy**  >  zdarzenia**maszyny wirtualnej**  >  **— ostatnie 72 godzin**.
Powinna zostać wyświetlona **częstotliwość zmian danych zdarzeń poza obsługiwanymi limitami**:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Strona Azure Site Recovery, która pokazuje zbyt wysoki współczynnik zmian danych.":::

W przypadku wybrania zdarzenia powinny zostać wyświetlone dokładne informacje o dysku:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Strona Azure Site Recovery, która pokazuje zbyt wysoki współczynnik zmian danych.":::

### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji wejścia/wyjścia aplikacji (we/wy). Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.

Istnieją dwa ograniczenia, które należy wziąć pod uwagę: zmiany danych na dysk i zmiany danych na maszynę wirtualną. Spójrzmy na dysk P20 Premium w poniższej tabeli, aby zapoznać się z przykładem. Dla jednej maszyny wirtualnej Site Recovery może obsłużyć 5 MB/s zmian na dysk z maksymalnie pięcioma dyskami. Site Recovery ma limit 54 MB/s całkowitej liczby zmian na maszynę wirtualną.

**Cel magazynu replikacji** | **Średni rozmiar operacji we/wy dla dysku źródłowego** |**Średni współczynnik zmian danych na dysku źródłowym** | **Łączny współczynnik zmian danych dziennie dla dysku z danymi źródłowymi**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

### <a name="solution"></a>Rozwiązanie

Azure Site Recovery ma limity szybkości zmian danych, w zależności od typu dysku. Aby sprawdzić, czy ten problem jest cykliczny, czy tymczasowy, Znajdź szybkość zmian danych maszyny wirtualnej, której dotyczy problem. Przejdź do źródłowej maszyny wirtualnej, Znajdź metryki w obszarze **monitorowanie**i Dodaj metryki, jak pokazano na poniższym zrzucie ekranu:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Strona Azure Site Recovery, która pokazuje zbyt wysoki współczynnik zmian danych.":::

1. Wybierz pozycję **Dodaj metrykę**i Dodaj **Bajty zapisu dysku systemu operacyjnego/s** i **Bajty zapisu na dysku danych/s**.
1. Monitoruj skok, jak pokazano na zrzucie ekranu.
1. Wyświetlanie całkowitej liczby operacji zapisu wykonywanych między dyskami systemu operacyjnego a wszystkimi połączonymi dyskami danych. Te metryki mogą nie podawać informacji na poziomie dysku, ale wskazują łączny wzorzec zmian danych.

Wzrost liczby zmian danych może pochodzić z okazjonalnej serii danych. Jeśli częstotliwość zmian danych jest większa niż 10 MB/s (w przypadku wersji Premium) lub 2 MB/s (w przypadku wersji Standard) i zostanie wystawiona replikacja, zostanie wystąpić. Jeśli zmiany są spójne znacznie poza obsługiwanym limitem, należy wziąć pod uwagę jedną z następujących opcji:

- Wyklucz dysk powodujący wysoki współczynnik zmian danych: najpierw wyłącz replikację. Następnie możesz wykluczyć dysk przy użyciu [programu PowerShell](azure-to-azure-exclude-disks.md).
- Zmień warstwę dysku magazynu odzyskiwania po awarii: Ta opcja jest możliwa tylko wtedy, gdy ilość danych na dysku jest mniejsza niż 20 MB/s. Na przykład maszyna wirtualna z dyskiem P10 ma zmienione dane o rozmiarze większym niż 8 MB/s, ale mniej niż 10 MB/s. Jeśli klient może korzystać z dysku P30 dla magazynu docelowego podczas ochrony, problem może zostać rozwiązany. To rozwiązanie jest możliwe tylko dla maszyn, które używają Premium-Managed dysków. Wykonaj następujące kroki:

  1. Przejdź do obszaru **dyski** replikowanej maszyny i skopiuj nazwę dysku repliki.
  1. Przejdź do tej repliki dysku zarządzanego.
  1. Zobaczysz transparent w **omówieniu** informujący, że został wygenerowany adres URL sygnatury dostępu współdzielonego. Wybierz ten transparent i Anuluj eksport. Zignoruj ten krok, jeśli transparent nie jest widoczny.
  1. Gdy tylko adres URL sygnatury dostępu współdzielonego zostanie odwołany, przejdź do pozycji **Konfiguracja** dysku zarządzanego. Zwiększ rozmiar, tak aby Site Recovery obsługiwał częstotliwość zaobserwowanych zmian na dysku źródłowym.

## <a name="network-connectivity-problems"></a>Problemy z łącznością sieciową

### <a name="network-latency-to-a-cache-storage-account"></a>Opóźnienie sieci na konto magazynu pamięci podręcznej

Site Recovery wysyła zreplikowane dane na konto magazynu pamięci podręcznej. Opóźnienie sieci może wystąpić, jeśli przekazywanie danych z maszyny wirtualnej na konto magazynu pamięci podręcznej jest wolniejsze niż 4 MB w ciągu 3 sekund.

Aby sprawdzić, czy wystąpił problem związany z opóźnieniem, użyj [AzCopy](../storage/common/storage-use-azcopy-v10.md). Za pomocą tego narzędzia wiersza polecenia można przekazać dane z maszyny wirtualnej na konto magazynu pamięci podręcznej. Jeśli opóźnienie jest wysokie, sprawdź, czy używasz sieciowego urządzenia wirtualnego (urządzenie WUS) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych. Urządzenie może zostać ograniczone, jeśli cały ruch związany z replikacją przechodzi przez urządzenie WUS.

Zalecamy utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie przechodził do urządzenie WUS. Aby uzyskać więcej informacji, zobacz [Konfiguracja wirtualnego urządzenia sieciowego](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Łączność sieciowa

Aby replikacja Site Recovery działała, potrzebuje ona maszyny wirtualnej w celu zapewnienia łączności wychodzącej z określonymi adresami URL lub zakresami adresów IP. Być może maszyna wirtualna jest za zaporą lub używają reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej. Jeśli tak, mogą wystąpić problemy. Aby upewnić się, że wszystkie adresy URL są połączone, zobacz [połączenia wychodzące dla adresów URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Identyfikator błędu 153006 — Brak dostępnego na poziomie aplikacji punktu odzyskiwania dla maszyny wirtualnej w ciągu ostatnich "X" minut

Poniżej przedstawiono niektóre z najczęściej spotykanych problemów.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Znany problem w programie SQL Server 2008/2008 R2

**Jak naprawić:** Istnieje znany problem z programem SQL Server 2008/2008 R2. Zapoznaj się z artykułem [Azure Site Recovery agenta lub innej kopii zapasowej usługi VSS, która nie jest składnikiem, nie powiedzie się na serwerze hostującym SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Zadania Azure Site Recovery kończą się niepowodzeniem na serwerach obsługujących dowolną wersję wystąpienia SQL Server z AUTO_CLOSE baz danych

**Jak naprawić:** Zapoznaj się z artykułem [kopie zapasowe usługi VSS, które nie są składnikiem, takie jak zadania Azure Site Recovery, nie działają na serwerach hostującym wystąpienia SQL Server z AUTO_CLOSE baz danych](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Znany problem w programie SQL Server w wersjach 2016 i 2017

**Jak naprawić**: Zapoznaj się z artykułem [zbiorczym aktualizacji 16 dla SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Używasz konfiguracji usługi Azure Bezpośrednie miejsca do magazynowania

**Jak naprawić**: Azure Site Recovery nie może utworzyć punktu odzyskiwania spójnego na poziomie aplikacji dla konfiguracji bezpośrednie miejsca do magazynowania. [Skonfiguruj zasady replikacji](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Nie włączono spójności aplikacji na serwerach z systemem Linux

**Jak naprawić** : Azure Site Recovery dla systemu operacyjnego Linux obsługuje niestandardowe skrypty aplikacji na potrzeby spójności aplikacji. Skrypt niestandardowy z opcjami pre i post będzie używany przez agenta mobilności Azure Site Recovery w celu zapewnienia spójności aplikacji. [Poniżej](./site-recovery-faq.md#replication) przedstawiono kroki, które należy włączyć.

### <a name="more-causes-because-of-vss-related-issues"></a>Więcej przyczyn spowodowanych problemami związanymi z usługą VSS:

Aby przeprowadzić dalsze Rozwiązywanie problemów, sprawdź pliki na maszynie źródłowej, aby uzyskać dokładny kod błędu dla niepowodzenia:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Aby zlokalizować błędy, Otwórz plik _vacp. log_ w edytorze tekstu Wyszukaj ciąg **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

W poprzednim przykładzie **2147754994** to kod błędu, który informuje o niepowodzeniu po tym zdaniu.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Składnik zapisywania usługi VSS nie jest zainstalowany — błąd 2147221164

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa Usługa kopiowania woluminów W tle (VSS). Site Recovery instaluje dostawcę usługi VSS w celu wykonania migawek spójności aplikacji. Azure Site Recovery instaluje tego dostawcę usługi VSS jako usługę. Jeśli Dostawca usługi VSS nie jest zainstalowany, tworzenie migawki spójności aplikacji zakończy się niepowodzeniem. Pokazuje, że **Klasa identyfikatora błędu 0x80040154 nie jest zarejestrowana**. Zapoznaj się z artykułem dotyczącym [rozwiązywania problemów z instalacją składnika zapisywania usługi VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Składnik zapisywania usługi VSS jest wyłączony — błąd 2147943458

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi VSS. Site Recovery instaluje dostawcę usługi VSS w celu wykonania migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. Jeśli nie masz włączonej usługi dostawcy usługi VSS, tworzenie migawki spójności aplikacji zakończy się niepowodzeniem. Zostanie wyświetlony błąd: **określona usługa jest wyłączona i nie można jej uruchomić (0x80070422)**.

Jeśli usługa VSS jest wyłączona:

- Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.
- Uruchom ponownie następujące usługi:
  - Usługa VSS.
  - Azure Site Recovery dostawcę usługi VSS.
  - Usługa VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED dostawcy usługi VSS — błąd 2147754756

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi VSS. Sprawdź, czy Azure Site Recovery jest zainstalowana usługa dostawcy usługi VSS.

Użyj następujących poleceń, aby ponownie zainstalować dostawcę usługi VSS:

1. Odinstaluj istniejącego dostawcę:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Zainstaluj ponownie dostawcę VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.

Uruchom ponownie następujące usługi:

- Usługa VSS.
- Azure Site Recovery dostawcę usługi VSS.
- Usługa VDS.
