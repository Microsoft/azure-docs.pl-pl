---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery
description: Rozwiązywanie problemów z błędami podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 9f95677211a89c1bb2a18076741dec08e76c5d70
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390376"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Rozwiązywanie problemów z błędami replikacji maszyny wirtualnej platformy Azure do platformy Azure

W tym artykule opisano, jak rozwiązywać typowe błędy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure (VM) z jednego regionu do drugiego. Aby uzyskać więcej informacji na temat obsługiwanych konfiguracji, zobacz [macierz obsługi replikacji maszyn wirtualnych platformy Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemy z przydziałem zasobów platformy Azure (kod błędu 150097)

Upewnij się, że subskrypcja jest włączona do tworzenia maszyn wirtualnych platformy Azure w regionie docelowym, który ma być używany jako region odzyskiwania po awarii (DR). Subskrypcja wymaga wystarczającego przydziału do tworzenia maszyn wirtualnych o niezbędnych rozmiarach. Domyślnie usługa Site Recovery wybiera docelowy rozmiar maszyny Wirtualnej, który jest taki sam jak rozmiar źródłowej maszyny Wirtualnej. Jeśli pasujący rozmiar nie jest dostępny, usługa Site Recovery automatycznie wybiera najbliższy dostępny rozmiar.

Jeśli nie ma rozmiaru obsługującego konfigurację źródłowej maszyny Wirtualnej, wyświetlany jest następujący komunikat:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- Identyfikator subskrypcji nie jest włączona do tworzenia żadnych maszyn wirtualnych w lokalizacji regionu docelowego.
- Identyfikator subskrypcji nie jest włączony lub nie ma wystarczającego przydziału, aby utworzyć określone rozmiary maszyn wirtualnych w lokalizacji regionu docelowego.
- Nie znaleziono odpowiedniego docelowego rozmiaru maszyny Wirtualnej, aby dopasować się do licznika karty interfejsu sieciowego maszyny Wirtualnej źródła (2) dla identyfikatora subskrypcji w lokalizacji regionu docelowego.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Skontaktuj się z [pomocą techniczną rozliczeń platformy Azure,](/azure/azure-portal/supportability/resource-manager-core-quotas-request) aby umożliwić subskrypcję w celu tworzenia maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej. Następnie ponów próbę wykonania nieudanej operacji.

Jeśli lokalizacja docelowa ma ograniczenie pojemności, wyłącz replikację do tej lokalizacji. Następnie włącz replikację do innej lokalizacji, w której subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o wymaganych rozmiarach.

## <a name="trusted-root-certificates-error-code-151066"></a>Zaufane certyfikaty główne (kod błędu 151066)

Jeśli nie wszystkie najnowsze zaufane certyfikaty główne są obecne na maszynie wirtualnej, zadanie włączenia replikacji dla usługi Site Recovery może zakończyć się niepowodzeniem. Uwierzytelnianie i autoryzacja wywołań usługi odzyskiwania lokacji z maszyny Wirtualnej zakończy się niepowodzeniem bez tych certyfikatów.

Jeśli zadanie włączenia replikacji nie powiedzie się, zostanie wyświetlony następujący komunikat:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Możliwa przyczyna

Zaufane certyfikaty główne wymagane do autoryzacji i uwierzytelniania nie są obecne na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

#### <a name="windows"></a>Windows

W przypadku maszyny Wirtualnej z systemem operacyjnym Windows zainstaluj najnowsze aktualizacje systemu Windows, aby wszystkie zaufane certyfikaty główne były obecne na maszynie wirtualnej. Postępuj zgodnie z typowym procesem zarządzania aktualizacjami aktualizacji systemu Windows lub zarządzania aktualizacjami certyfikatów w organizacji, aby uzyskać najnowsze certyfikaty główne i zaktualizowaną listę odwołania certyfikatów na maszynach wirtualnych.

- Jeśli jesteś w środowisku rozłączony, postępuj zgodnie ze standardowym procesem aktualizacji systemu Windows w organizacji, aby uzyskać certyfikaty.
- Jeśli wymagane certyfikaty nie są obecne na maszynie Wirtualnej, wywołania usługi site recovery nie powiodą się ze względów bezpieczeństwa.

Aby sprawdzić, czy problem został `login.microsoftonline.com` rozwiązany, przejdź do przeglądarki na maszynie Wirtualnej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaufanych katalogów głównych i niedozwolonych certyfikatów](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora wersji systemu operacyjnego Linux, aby uzyskać najnowsze zaufane certyfikaty główne i najnowszą listę odwołania certyfikatów na maszynie wirtualnej.

Ponieważ SUSE Linux używa dowiązań symbolicznych lub dowiązań symbolicznych do obsługi listy certyfikatów, wykonaj następujące kroki:

1. Zaloguj się jako użytkownik **root.** Symbol skrótu`#`( ) jest domyślnym wierszem polecenia.

1. Aby zmienić katalog, uruchom następujące polecenie:

   `cd /etc/ssl/certs`

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji firmy Symantec jest obecny:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Jeśli certyfikat głównego urzędu certyfikacji firmy Symantec nie zostanie znaleziony, uruchom następujące polecenie, aby pobrać plik. Sprawdź, czy nie ma błędów i postępuj zgodnie z zalecanymi akcjami w przypadku awarii sieci.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji Baltimore jest obecny:

   `ls Baltimore_CyberTrust_Root.pem`

   - Jeśli certyfikat głównego urzędu certyfikacji Baltimore nie został znaleziony, uruchom to polecenie, aby pobrać certyfikat:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Sprawdź, czy certyfikat DigiCert_Global_Root_CA jest obecny:

   `ls DigiCert_Global_Root_CA.pem`

    - Jeśli DigiCert_Global_Root_CA nie zostanie znaleziony, uruchom następujące polecenia, aby pobrać certyfikat:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Aby zaktualizować skróty tematu certyfikatu dla nowo pobranych certyfikatów, uruchom skrypt rehash:

   `c_rehash`

1. Aby sprawdzić, czy skróty tematu jako dowiązanie symboliczne zostały utworzone dla certyfikatów, uruchom następujące polecenia:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Utwórz kopię pliku _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ z nazwami plików _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Utwórz kopię pliku _Baltimore_CyberTrust_Root.pem_ z nazwami plików _653b494a.0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Utwórz kopię pliku _DigiCert_Global_Root_CA.pem_ z nazwami plików _3513523f.0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Sprawdź, czy pliki są obecne:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Wychodzące adresy URL lub zakresy adresów IP (kod błędu 151037 lub 151072)

Aby replikacja usługi Site Recovery działała, wymagana jest łączność wychodząca z określonymi adresami URL z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej, może napotkać jeden z tych problemów. Podczas gdy nadal obsługujemy dostęp wychodzący za pośrednictwem adresów URL, przy użyciu listy dozwolonych zakresów adresów IP nie jest już obsługiwana.

### <a name="issue-1-failed-to-register-azure-vm-with-site-recovery-151195"></a>Problem 1: nie można zarejestrować maszyny Wirtualnej platformy Azure w usłudze Site Recovery (151195)

#### <a name="possible-causes"></a>Możliwe przyczyny

- Nie można ustanowić połączenia z punktami końcowymi usługi Site Recovery z powodu błędu rozpoznawania systemu nazw domen (DNS).
- Ten problem występuje częściej podczas ponownego odzyskiwania po awarii przez maszynę wirtualną, ale serwer DNS nie jest osiągalny z regionu odzyskiwania po awarii (DR).

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli używasz niestandardowego systemu DNS, upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii.

Aby sprawdzić, czy maszyna wirtualna używa niestandardowego ustawienia DNS:

1. Otwórz **maszyny wirtualne** i wybierz maszynę wirtualną.
1. Przejdź do **ustawień** maszyn wirtualnych i wybierz pozycję **Sieć**.
1. W **obszarze Sieć wirtualna/podsieć**wybierz łącze, aby otworzyć stronę zasobów sieci wirtualnej.
1. Przejdź do **pozycji Ustawienia** i wybierz pozycję **Serwery DNS**.

Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli serwer DNS nie jest dostępny, udostępnij go przez serwer DNS w wyniku awarii lub utworzenie linii lokacji między siecią odzyskiwania po awarii a systemem DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-błąd.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja odzyskiwania lokacji nie powiodła się (151196)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można ustanowić połączenia z punktami końcowymi uwierzytelniania usługi Office 365 i tożsamości IP4.

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Usługa Azure Site Recovery wymagała dostępu do zakresów adresów IP usługi Office 365 do uwierzytelniania.
Jeśli używasz reguł/serwera proxy usługi Azure Network Security Group (NSG) do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że używasz [reguły nsg opartej na usłudze Azure Active Directory (AAD)](/azure/virtual-network/security-overview#service-tags) do zezwalania na dostęp do usługi AAD. Nie obsługujemy już reguł sieciowej sieciowej o numerze IP opartych na adresach IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja odzyskiwania lokacji nie powiodła się (151197)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli używasz reguł/serwera proxy usługi Azure Network Security Group (NSG) do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że używasz tagów usługi. Nie obsługujemy już przy użyciu listy dozwolonych adresów IP za pośrednictwem witryn NSG dla usługi Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problem 4: Replikacja kończy się niepowodzeniem, gdy ruch sieciowy korzysta z lokalnego serwera proxy (151072)

#### <a name="possible-cause"></a>Możliwa przyczyna

Niestandardowe ustawienia serwera proxy są nieprawidłowe, a agent usługi mobilności nie wykrył automatycznie ustawień serwera proxy w programie Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

1. Agent usługi mobilności wykrywa ustawienia serwera proxy z `/etc/environment` IE w systemie Windows i linux.
1. Jeśli wolisz ustawić serwer proxy tylko dla usługi mobilności, możesz podać szczegóły serwera proxy w _ProxyInfo.conf_ znajdujące się pod adresem:

   - **Linux**:`/usr/local/InMage/config/`
   - **Okna**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _Plik ProxyInfo.conf_ powinien mieć ustawienia serwera proxy w następującym formacie _INI._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent usługi mobilności obsługuje tylko **nieuwierzyte serwery proxy.**

### <a name="more-information"></a>Więcej informacji

Aby określić [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)postępuj zgodnie ze wskazówkami dotyczącymi [sieci na platformie Azure do replikacji platformy Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Nie znaleziono dysku w maszynie wirtualnej (kod błędu 150039)

Należy zainicjować nowy dysk dołączony do maszyny Wirtualnej. Jeśli dysk nie zostanie znaleziony, zostanie wyświetlony następujący komunikat:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- Nowy dysk danych został dołączony do maszyny Wirtualnej, ale nie został zainicjowany.
- Dysk danych wewnątrz maszyny Wirtualnej nie poprawnie zgłasza wartość numeru jednostki logicznej (LUN), przy której dysk został dołączony do maszyny Wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że dyski danych są inicjowane, a następnie ponów próbę wykonania operacji.

- **Windows**: [Dołączanie i inicjowanie nowego dysku](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Inicjowanie nowego dysku danych w Systemie Linux](/azure/virtual-machines/linux/add-disk).

Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Wiele dysków dostępnych do ochrony (kod błędu 153039)

### <a name="possible-causes"></a>Możliwe przyczyny

- Jeden lub więcej dysków zostały niedawno dodane do maszyny wirtualnej po ochronie.
- Jeden lub więcej dysków zostało zainicjowanych po ochronie maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby ponownie wyprogramować stan replikacji maszyny Wirtualnej, można wybrać opcję ochrony dysków lub odrzucenia ostrzeżenia.

#### <a name="to-protect-the-disks"></a>Aby chronić dyski

1. Przejdź do pozycji **Replikowane elementy** > _nazwy_ > maszyny**Wirtualnej Dyski**.
1. Wybierz dysk niechroniony, a następnie wybierz pozycję **Włącz replikację:**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Włącz replikację na dyskach maszyn wirtualnych.":::

#### <a name="to-dismiss-the-warning"></a>Aby odrzucić ostrzeżenie

1. Przejdź do_nazwy maszyny wirtualnej_ **z replikowanymi elementami** > .
1. Zaznacz ostrzeżenie w sekcji **Przegląd,** a następnie wybierz przycisk **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Odrzuć ostrzeżenie o nowym dysku.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Maszyna wirtualna usunięta z magazynu została ukończona z informacjami (kod błędu 150225)

Gdy usługa Site Recovery chroni maszynę wirtualną, tworzy łącza na źródłowej maszynie wirtualnej. Po usunięciu ochrony lub wyłączeniu replikacji usługa Site Recovery usuwa te łącza jako część zadania oczyszczania. Jeśli maszyna wirtualna ma blokadę zasobów, zadanie oczyszczania zostanie zakończone z informacjami. Informacje mówią, że maszyna wirtualna została usunięta z magazynu usług odzyskiwania, ale niektóre nieaktualne łącza nie można wyczyścić na komputerze źródłowym.

Można zignorować to ostrzeżenie, jeśli nigdy nie zamierzasz ponownie chronić tej maszyny wirtualnej. Ale jeśli musisz chronić tę maszynę wirtualną później, wykonaj kroki opisane w tej sekcji, aby wyczyścić łącza.

> [!WARNING]
> Jeśli nie wykonasz oczyszczania:
>
> - Po włączeniu replikacji za pomocą magazynu usług odzyskiwania, maszyna wirtualna nie zostanie wyświetlona.
> - Jeśli spróbujesz chronić maszynę wirtualną przy użyciu**odzyskiwania po awarii****ustawień** >  **maszyny wirtualnej,** > operacja zakończy się niepowodzeniem z **komunikatem Replikacja nie może być włączona z powodu istniejących starych łączy zasobów na maszynie wirtualnej**.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów `MoveDemo` na nazwie maszyny Wirtualnej musi zostać usunięta:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny Wirtualnej.":::

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj jako parametry **identyfikator subskrypcji,** **grupę zasobów maszyn**wirtualnych i nazwę maszyny **wirtualnej.**
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replikacja nie jest włączona na maszynie Wirtualnej ze starych zasobów (kod błędu 150226)

### <a name="possible-causes"></a>Możliwe przyczyny

Maszyna wirtualna ma przestarzałą konfigurację z poprzedniej ochrony odzyskiwania witryny.

Nieaktualna konfiguracja może wystąpić na maszynie Wirtualnej platformy Azure, jeśli włączono replikację maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie:

- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobów.
- Magazyn odzyskiwania witryny został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn odzyskiwania lokacji bez jawnego wyłączania replikacji na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów `MoveDemo` na nazwie maszyny Wirtualnej musi zostać usunięta:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny Wirtualnej.":::

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj jako parametry **identyfikator subskrypcji,** **grupę zasobów maszyn**wirtualnych i nazwę maszyny **wirtualnej.**
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Nie można wybrać maszyny Wirtualnej lub grupy zasobów w zadaniu włączania replikacji

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problem 1: Grupa zasobów i źródło maszyny Wirtualnej znajdują się w różnych lokalizacjach

Usługa Site Recovery wymaga obecnie, aby grupa zasobów regionu źródłowego i maszyny wirtualne znajdować się w tej samej lokalizacji. Jeśli tak nie jest, nie będzie można znaleźć maszyny wirtualnej lub grupy zasobów podczas próby zastosowania ochrony.

Aby obejść ten problem, można włączyć replikację z maszyny Wirtualnej zamiast magazynu usług odzyskiwania. Przejdź do odzyskiwania po awarii**właściwości** >  **maszyny** > **wirtualnej** źródła i włącz replikację.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problem 2: Grupa zasobów nie jest częścią wybranej subskrypcji

Nie można znaleźć grupy zasobów w momencie ochrony, jeśli grupa zasobów nie jest częścią wybranej subskrypcji. Upewnij się, że grupa zasobów należy do używanej subskrypcji.

### <a name="issue-3-stale-configuration"></a>Problem 3: Nieaktualna konfiguracja

Maszyna wirtualna, którą chcesz włączyć dla replikacji, może nie być widoczna, jeśli na maszynie Wirtualnej platformy Azure istnieje przestarzała konfiguracja usługi Site Recovery. Ten warunek może wystąpić, jeśli włączono replikację maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie:

- Magazyn odzyskiwania witryny został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn odzyskiwania lokacji bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobów.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Przed użyciem `AzureRM.Resources` skryptu wymienionego w tej sekcji należy zaktualizować moduł. Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę, jeśli istnieje, z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów `MoveDemo` na nazwie maszyny Wirtualnej musi zostać usunięta:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny Wirtualnej.":::

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj jako parametry **identyfikator subskrypcji,** **grupę zasobów maszyn**wirtualnych i nazwę maszyny **wirtualnej.**
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-select-a-vm-for-protection"></a>Nie można wybrać maszyny Wirtualnej do ochrony

### <a name="possible-cause"></a>Możliwa przyczyna

Maszyna wirtualna ma zainstalowane rozszerzenie w stanie nierozpoznawczym lub nieodpowiadanym

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Przejdź do rozszerzenia**ustawień** >  **maszyn** > **wirtualnych** i sprawdź, czy nie ma żadnych rozszerzeń w stanie awarii. Odinstaluj wszystkie nieudane rozszerzenie, a następnie spróbuj ponownie chronić maszynę wirtualną.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>Stan inicjowania obsługi administracyjnej maszyny Wirtualnej jest nieprawidłowy (kod błędu 150019)

Aby włączyć replikację na maszynie Wirtualnej, jej stan inicjowania obsługi administracyjnej musi **zakończyć się pomyślnie**. Wykonaj następujące kroki, aby sprawdzić stan inicjowania obsługi administracyjnej:

1. W witrynie Azure portal wybierz **Eksploratora zasobów** ze **wszystkich usług**.
1. Rozwiń listę **Subskrypcje** i wybierz subskrypcję.
1. Rozwiń listę **ResourceGroups** i wybierz grupę zasobów maszyny Wirtualnej.
1. Rozwiń listę **Zasoby** i wybierz maszynę wirtualną.
1. Sprawdź pole **inicjowania obsługi administracyjnejState** w widoku wystąpienia po prawej stronie.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

- Jeśli **inicjowanie obsługi administracyjnejState** jest **nie powiodło się,** skontaktuj się z pomocą techniczną ze szczegółami, aby rozwiązać problem.
- Jeśli **aprowizacjiState** jest **aktualizacja,** inne rozszerzenie może być wdrażany. Sprawdź, czy istnieją bieżące operacje na maszynie Wirtualnej, poczekaj na ich zakończenie, a następnie ponów próbę wykonania zadania odzyskiwania lokacji, które nie powiodło się, aby włączyć replikację.

## <a name="unable-to-select-target-vm"></a>Nie można wybrać docelowej maszyny Wirtualnej

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problem 1: Maszyna wirtualna jest dołączona do sieci, która jest już zamapowana do sieci docelowej

Podczas konfiguracji odzyskiwania po awarii, jeśli źródłowa maszyna wirtualna jest częścią sieci wirtualnej, a inna maszyna wirtualna z tej samej sieci wirtualnej jest już mapowana za pomocą sieci w docelowej grupie zasobów, pole listy rozwijanej wyboru sieci jest domyślnie niedostępne (jest wyszarzone).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lista wyboru sieci jest niedostępna.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problem 2: Poprzednio chroniłeś maszynę wirtualną, a następnie wyłączono replikację

Wyłączenie replikacji maszyny Wirtualnej nie powoduje usunięcia mapowania sieci. Mapowanie musi zostać usunięte z magazynu usług odzyskiwania, w którym maszyna wirtualna była chroniona. Wybierz **magazyn usług odzyskiwania** i przejdź do **zarządzania** > **infrastrukturą** > odzyskiwania witryn**dla maszyn wirtualnych** > platformy Azure**Mapowanie sieci**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Usuwanie mapowania sieci.":::

Sieć docelowa skonfigurowana podczas konfiguracji odzyskiwania po awarii może zostać zmieniona po wstępnej konfiguracji i po zabezpieczeniu maszyny wirtualnej. Aby **zmodyfikować mapowanie sieci,** wybierz nazwę sieci:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modyfikowanie mapowania sieci.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ lub VSS (kod błędu 151025)

Po wystąpieniu błędu USŁUGI COM+ lub Usługi kopiowania woluminów (VSS) wyświetlany jest następujący komunikat:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- Usługa aplikacji systemowych COM+ jest wyłączona.
- Usługa kopiowania woluminów w tle jest wyłączona.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Ustaw usługę aplikacji systemowych i tomów com+ na tryb automatycznego lub ręcznego uruchamiania.

1. Otwórz konsolę Usługi w systemie Windows.
1. Upewnij się, że aplikacja systemowa COM+ i usługa kopiowania woluminów w tle nie są ustawione na **Wyłączone** jako **ich typ uruchamiania**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Sprawdź typ uruchamiania usługi COM plus System Application i Volume Shadow Copy Service.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nieobsługiwał rozmiar dysku zarządzanego (kod błędu 150172)

W przypadku wystąpienia tego błędu wyświetlany jest następujący komunikat:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Możliwa przyczyna

Dysk jest mniejszy niż obsługiwany rozmiar 1024 MB.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że rozmiar dysku mieści się w obsługiwanym zakresie rozmiaru, a następnie ponów próbę wykonania operacji.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Ochrona nie jest włączona, gdy GRUB używa nazwy urządzenia (kod błędu 151126)

### <a name="possible-causes"></a>Możliwe przyczyny

Pliki konfiguracyjne Linux Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_lub _/etc/default/grub_) mogą określać rzeczywiste `root` `resume` nazwy urządzeń zamiast uniwersalnych unikatowych wartości identyfikatorów (UUID) dla i parametrów. Odzyskiwanie witryny wymaga identyfikatorów UUID, ponieważ nazwy urządzeń mogą ulec zmianie. Po ponownym uruchomieniu maszyny Wirtualnej może nie wymyślić tej samej nazwy w pracy awaryjnej, co powoduje problemy.

Poniższe przykłady to wiersze z plików GRUB, w których zamiast wymaganych identyfikatorów UUID są wyświetlane nazwy urządzeń:

- Plik _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Plik: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Zastąp każdą nazwę urządzenia odpowiednim identyfikatorem UUID:

1. Znajdź identyfikator UUID urządzenia, wykonując `blkid <device name>`polecenie . Przykład:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Zastąp nazwę urządzenia identyfikatorem UUID w formatach `root=UUID=<UUID>` i `resume=UUID=<UUID>`. Na przykład po wymianie wiersz z _/boot/grub/menu.lst_ będzie wyglądał następująco:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Ponów próbę ochrony.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Ochrona nie powiodła się, ponieważ urządzenie GRUB nie istnieje (kod błędu 151124)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracyjne GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, lub `rd.lvm.lv` `rd_LVM_LV` _/etc/default/grub_) mogą zawierać parametry lub . Parametry te identyfikują urządzenia Menedżera woluminów logicznych (LVM), które mają zostać wykryte podczas rozruchu. Jeśli te urządzenia LVM nie istnieją, chroniony system sam nie uruchomi się i utknie w procesie rozruchu. Ten sam problem będzie również widoczny w pracy awaryjnej maszyny Wirtualnej. Oto kilka przykładów:

- Plik: _/boot/grub2/grub.cfg_ na RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Plik: _/etc/default/grub_ na RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Plik: _/boot/grub/menu.lst_ na RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

W każdym przykładzie GRUB musi wykryć dwa `root` `swap` urządzenia LVM z nazwami i z grupy woluminów `rootvg`.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli urządzenie LVM nie istnieje, utwórz je lub usuń odpowiednie parametry z plików konfiguracyjnych GRUB. Następnie spróbuj ponownie, aby włączyć ochronę.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizacja usługi mobilności zakończona ostrzeżeniami (kod błędu 151083)

Usługa Site Recovery Mobility ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchamiania systemu. Za każdym razem, gdy aktualizacja usługi mobilności zawiera zmiany sterownika filtru, komputer jest aktualizowany, ale nadal jest widoczne ostrzeżenie, że niektóre poprawki wymagają ponownego uruchomienia. Ostrzeżenie pojawia się, ponieważ poprawki sterownika filtru mogą zostać zastosowane tylko po załadowaniu nowego sterownika filtru, co ma miejsce tylko podczas ponownego uruchamiania.

> [!NOTE]
> To tylko ostrzeżenie. Istniejąca replikacja będzie nadal działać nawet po aktualizacji nowego agenta. Możesz ponownie uruchomić komputer, gdy chcesz korzystać z zalet nowego sterownika filtru, ale stary sterownik filtru działa, jeśli nie zostanie uruchomiony ponownie.
>
> Oprócz sterownika filtru korzyści wynikające z innych ulepszeń i poprawek w aktualizacji usługi mobilności są obowiązywać bez konieczności ponownego uruchamiania.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Ochrona nie jest włączona, jeśli istnieje dysk zarządzany repliki

Ten błąd występuje, gdy dysk zarządzany repliki już istnieje, bez oczekiwanych tagów, w docelowej grupie zasobów.

### <a name="possible-cause"></a>Możliwa przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna była wcześniej chroniona, a gdy replikacja została wyłączona, dysk repliki nie został usunięty.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Usuń dysk repliki zidentyfikowany w komunikacie o błędzie i ponów próbę wykonania nieudanego zadania ochrony.

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](azure-to-azure-how-to-enable-replication.md)
