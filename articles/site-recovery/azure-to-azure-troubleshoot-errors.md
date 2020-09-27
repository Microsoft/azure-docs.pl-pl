---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure w Azure Site Recovery
description: Rozwiązywanie problemów podczas replikowania maszyn wirtualnych platformy Azure na potrzeby odzyskiwania po awarii.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: ab3597ddba3e41e88c8b2b575ed5857aca01e610
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397967"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Rozwiązywanie problemów z błędami z replikacją maszyny wirtualnej z platformy Azure do platformy Azure

W tym artykule opisano sposób rozwiązywania typowych błędów w Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [Macierz obsługi w celu replikowania maszyn wirtualnych platformy Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemy z przydziałami zasobów platformy Azure (kod błędu 150097)

Upewnij się, że subskrypcja jest włączona, aby utworzyć maszyny wirtualne platformy Azure w regionie docelowym, który ma być używany jako region odzyskiwania po awarii (DR). Twoja subskrypcja wymaga wystarczającego limitu przydziału do tworzenia maszyn wirtualnych o niezbędnych rozmiarach. Domyślnie Site Recovery wybiera docelowy rozmiar maszyny wirtualnej, który jest taki sam jak rozmiar źródłowej maszyny wirtualnej. Jeśli odpowiedni rozmiar nie jest dostępny, Site Recovery automatycznie wybiera najbliższy dostępny rozmiar.

Jeśli nie ma rozmiaru obsługującego konfigurację źródłowej maszyny wirtualnej, zostanie wyświetlony następujący komunikat:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- W Twoim IDENTYFIKATORze subskrypcji nie jest włączona funkcja tworzenia żadnych maszyn wirtualnych w lokalizacji regionu docelowego.
- Identyfikator subskrypcji nie jest włączony lub nie ma wystarczającego limitu przydziału, aby utworzyć określone rozmiary maszyn wirtualnych w lokalizacji regionu docelowego.
- Nie znaleziono odpowiedniego docelowego rozmiaru maszyny wirtualnej, aby dopasować liczbę kart sieciowych źródłowej maszyny wirtualnej (2) do identyfikatora subskrypcji w lokalizacji regionu docelowego.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Skontaktuj się z [pomocą techniczną dotyczącą rozliczeń platformy Azure](../azure-portal/supportability/resource-manager-core-quotas-request.md) , aby umożliwić subskrypcji Tworzenie maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej. Następnie ponów próbę wykonania operacji zakończonej niepowodzeniem.

Jeśli lokalizacja docelowa ma ograniczenie pojemności, wyłącz replikację do tej lokalizacji. Następnie należy włączyć replikację do innej lokalizacji, w której subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o wymaganych rozmiarach.

## <a name="trusted-root-certificates-error-code-151066"></a>Zaufane certyfikaty główne (kod błędu 151066)

Jeśli na maszynie wirtualnej nie ma wszystkich najnowszych zaufanych certyfikatów głównych, zadanie umożliwiające włączenie replikacji dla Site Recovery może zakończyć się niepowodzeniem. Uwierzytelnianie i autoryzacja wywołań usługi Site Recovery z maszyny wirtualnej kończy się niepowodzeniem bez certyfikatów.

Jeśli zadanie włączania replikacji nie powiedzie się, zostanie wyświetlony następujący komunikat:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Możliwa przyczyna

Zaufane certyfikaty główne wymagane do autoryzacji i uwierzytelniania nie są obecne na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

#### <a name="windows"></a>Windows

W przypadku maszyny wirtualnej z systemem operacyjnym Windows należy zainstalować najnowsze aktualizacje systemu Windows, tak aby wszystkie zaufane certyfikaty główne znajdowały się na maszynie wirtualnej. Wykonaj typowe procesy zarządzania aktualizacjami systemu Windows lub zarządzania aktualizacjami certyfikatów w organizacji, aby uzyskać najnowsze certyfikaty główne i zaktualizowaną listę odwołania certyfikatów na maszynach wirtualnych.

- Jeśli jesteś w niepołączonym środowisku, postępuj zgodnie ze standardowym procesem aktualizacji systemu Windows w organizacji, aby uzyskać certyfikaty.
- Jeśli wymagane certyfikaty nie są obecne na maszynie wirtualnej, wywołania usługi Site Recovery nie powiodą się ze względów bezpieczeństwa.

Aby sprawdzić, czy problem został rozwiązany, przejdź do `login.microsoftonline.com` przeglądarki na maszynie wirtualnej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaufanych katalogów głównych i niedozwolonych certyfikatów](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora wersji systemu operacyjnego Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i najnowszej listy odwołania certyfikatów na maszynie wirtualnej.

Ponieważ system SUSE Linux używa linków symbolicznych lub linków symbolicznych, aby zachować listę certyfikatów, wykonaj następujące kroki:

1. Zaloguj się jako użytkownik **główny** . Symbol skrótu ( `#` ) jest domyślnym wierszem polecenia.

1. Aby zmienić katalog, uruchom następujące polecenie:

   `cd /etc/ssl/certs`

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji firmy Symantec jest obecny:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Jeśli certyfikat głównego urzędu certyfikacji firmy Symantec nie został znaleziony, uruchom następujące polecenie, aby pobrać plik. Sprawdź pod kątem błędów i postępuj zgodnie z zalecanymi akcjami dotyczącymi błędów sieci.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji Baltimore jest obecny:

   `ls Baltimore_CyberTrust_Root.pem`

   - Jeśli nie znaleziono certyfikatu głównego urzędu certyfikacji Baltimore, Uruchom to polecenie, aby pobrać certyfikat:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Sprawdź, czy certyfikat DigiCert_Global_Root_CA jest obecny:

   `ls DigiCert_Global_Root_CA.pem`

    - Jeśli nie można odnaleźć DigiCert_Global_Root_CA, uruchom następujące polecenia, aby pobrać certyfikat:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Aby zaktualizować skróty podmiotu certyfikatu dla nowo pobranych certyfikatów, uruchom skrypt skrótu:

   `c_rehash`

1. Aby sprawdzić, czy dla certyfikatów zostały utworzone wartości skrótu podmiotu jako linków symbolicznych, uruchom następujące polecenia:

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

1. Utwórz kopię pliku _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ z nazwą pliku _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Utwórz kopię pliku _Baltimore_CyberTrust_Root. pem_ z nazwą pliku _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Utwórz kopię pliku _DigiCert_Global_Root_CA. pem_ z nazwą pliku _3513523f. 0_:

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

Aby replikacja Site Recovery działała, z poziomu maszyny wirtualnej wymagane jest połączenie wychodzące z określonymi adresami URL. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) do kontrolowania łączności wychodzącej, może to być przyczyną jednego z tych problemów. Mimo że nadal obsługujemy dostęp wychodzący za pośrednictwem adresów URL, korzystanie z listy dozwolonych zakresów adresów IP nie jest już obsługiwane.

#### <a name="possible-causes"></a>Możliwe przyczyny

- Nie można nawiązać połączenia z Site Recovery punktami końcowymi z powodu błędu rozpoznawania systemu nazw domen (DNS).
- Ten problem jest bardziej typowy podczas przechodzenia w tryb failover maszyny wirtualnej, ale serwer DNS nie jest dostępny z regionu odzyskiwania po awarii (DR).

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

W przypadku korzystania z niestandardowej usługi DNS upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii.

Aby sprawdzić, czy maszyna wirtualna używa niestandardowego ustawienia DNS:

1. Otwórz **maszyn wirtualnych** i wybierz maszynę wirtualną.
1. Przejdź do **ustawień** maszyny wirtualne i wybierz pozycję **Sieć**.
1. W obszarze **Sieć wirtualna/podsieć**wybierz link, aby otworzyć stronę zasobów sieci wirtualnej.
1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **serwery DNS**.

Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli serwer DNS nie jest dostępny, udostępnij go przez przechodzenie przez serwer DNS w tryb failover lub Tworzenie linii lokacji między siecią DR i systemem DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com — błąd.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja Site Recovery nie powiodła się (151196)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia w celu Microsoft 365nia punktów końcowych uwierzytelniania i tożsamości.

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Azure Site Recovery wymagany dostęp do Microsoft 365 zakresów adresów IP na potrzeby uwierzytelniania.
Jeśli używasz zasad grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń) platformy Azure/serwera proxy zapory do kontrolowania łączności sieciowej wychodzącej na maszynie wirtualnej, upewnij się, że Azure Active Directory używasz [(AAD) tag usługi](../virtual-network/security-overview.md#service-tags) opartej na sieciowej grupy zabezpieczeń reguły, aby umożliwić dostęp do usługi AAD. Nie obsługujemy już reguł sieciowej grupy zabezpieczeń opartych na adresach IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja Site Recovery nie powiodła się (151197)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia, aby Azure Site Recovery punkty końcowe usługi.

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli używasz zasad grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń) platformy Azure/serwera proxy zapory do kontrolowania łączności sieciowej wychodzącej na maszynie wirtualnej, upewnij się, że używasz tagów usługi. Nie obsługujemy już listy dozwolonych adresów IP za pomocą usługi sieciowych grup zabezpieczeń dla Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problem 4: replikacja kończy się niepowodzeniem, gdy ruch sieciowy używa lokalnego serwera proxy (151072)

#### <a name="possible-cause"></a>Możliwa przyczyna

Niestandardowe ustawienia serwera proxy są nieprawidłowe, a Agent usługi mobilności nie wykrył automatycznie ustawień serwera proxy w programie Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Rozwiązywanie problemu

1. Agent usługi mobilności wykrywa ustawienia serwera proxy z programu IE w systemach Windows i `/etc/environment` Linux.
1. Jeśli wolisz ustawić tylko serwer proxy dla usługi mobilności, możesz podać szczegóły serwera proxy w _ProxyInfo. conf_ znajdujący się w:

   - System **Linux**:`/usr/local/InMage/config/`
   - **System Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ powinna mieć ustawienia serwera proxy w następującym formacie _ini_ .

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent usługi mobilności obsługuje tylko **nieuwierzytelnione serwery proxy**.

### <a name="more-information"></a>Więcej informacji

Aby określić [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), postępuj zgodnie ze wskazówkami w [temacie dotyczącym sieci na platformie Azure do replikacji platformy Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Nie znaleziono dysku w maszynie wirtualnej (kod błędu 150039)

Należy zainicjować nowy dysk dołączony do maszyny wirtualnej. Jeśli dysk nie zostanie znaleziony, zostanie wyświetlony następujący komunikat:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- Nowy dysk danych został podłączony do maszyny wirtualnej, ale nie został zainicjowany.
- Dysk danych wewnątrz maszyny wirtualnej nie zgłasza poprawnie wartości numer jednostki logicznej (LUN), przy użyciu której dysk został dołączony do maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że dyski danych są zainicjowane, a następnie spróbuj ponownie wykonać operację.

- **System Windows**: [Dołącz i zainicjuj nowy dysk](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [zainicjuj nowy dysk danych w systemie Linux](../virtual-machines/linux/add-disk.md).

Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Wiele dysków dostępnych do ochrony (kod błędu 153039)

### <a name="possible-causes"></a>Możliwe przyczyny

- Co najmniej jeden dysk został niedawno dodany do maszyny wirtualnej po włączeniu ochrony.
- Co najmniej jeden dysk został zainicjowany po włączeniu ochrony maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby ponownie zmienić stan replikacji maszyny wirtualnej, możesz wybrać opcję ochrony dysków lub odrzucić ostrzeżenie.

#### <a name="to-protect-the-disks"></a>Aby chronić dyski

1. Przejdź do **pozycji zreplikowane elementy**  >  _Nazwa maszyny wirtualnej_  >  **dyski**.
1. Wybierz dysk niechroniony, a następnie wybierz pozycję **Włącz replikację**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Włącz replikację na dyskach maszyny wirtualnej.":::

#### <a name="to-dismiss-the-warning"></a>Aby odrzucić ostrzeżenie

1. Przejdź do **pozycji zreplikowane elementy**  >  _Nazwa maszyny wirtualnej_.
1. Wybierz ostrzeżenie w sekcji **Przegląd** , a następnie wybierz przycisk **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Odrzuć ostrzeżenie nowego dysku.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Pomyślnie usunięto maszynę wirtualną z magazynu z informacjami (kod błędu 150225)

Gdy Site Recovery chroni maszynę wirtualną, tworzy linki do źródłowej maszyny wirtualnej. Po usunięciu ochrony lub wyłączeniu replikacji program Site Recovery usuwa te linki w ramach zadania oczyszczania. Jeśli maszyna wirtualna ma blokadę zasobu, zadanie oczyszczania zostanie ukończone z informacjami. Informacje o tym, że maszyna wirtualna została usunięta z magazynu Recovery Services, ale nie można wyczyścić niektórych nieodświeżonych linków na maszynie źródłowej.

To ostrzeżenie można zignorować, jeśli nie chcesz ponownie chronić tej maszyny wirtualnej. Jeśli jednak chcesz później chronić tę maszynę wirtualną, wykonaj kroki opisane w tej sekcji, aby wyczyścić linki.

> [!WARNING]
> Jeśli nie wykonasz oczyszczania:
>
> - Po włączeniu replikacji za pomocą magazynu Recovery Services, maszyna wirtualna nie zostanie wyświetlona.
> - Jeśli spróbujesz chronić maszynę wirtualną przy użyciu **ustawień maszyny wirtualnej**  >  **Settings**  >  **odzyskiwanie awaryjne**, operacja zakończy się niepowodzeniem, **ponieważ nie będzie można włączyć replikacji komunikatów z powodu istniejących starych linków zasobów na maszynie wirtualnej**.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu o nazwie maszyna wirtualna `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny wirtualnej.":::

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj **Identyfikator subskrypcji**, **grupę zasobów maszyny wirtualnej**i **nazwę maszyny wirtualnej** jako parametry.
1. Jeśli zostanie wyświetlony monit o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replikacja nie jest włączona na maszynie wirtualnej ze starymi zasobami (kod błędu 150226)

### <a name="possible-causes"></a>Możliwe przyczyny

Maszyna wirtualna ma nieaktualną konfigurację z poprzedniej Site Recovery ochrony.

Nieodświeżona konfiguracja może wystąpić na maszynie wirtualnej platformy Azure, jeśli włączono replikację dla maszyny wirtualnej platformy Azure za pomocą Site Recovery, a następnie:

- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobu.
- Magazyn Site Recovery został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn Site Recovery bez jawnego wyłączania replikacji na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu o nazwie maszyna wirtualna `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny wirtualnej.":::

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj **Identyfikator subskrypcji**, **grupę zasobów maszyny wirtualnej**i **nazwę maszyny wirtualnej** jako parametry.
1. Jeśli zostanie wyświetlony monit o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Nie można wybrać maszyny wirtualnej lub grupy zasobów w zadanie włączania replikacji

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problem 1: Grupa zasobów i źródłowa maszyna wirtualna znajdują się w różnych lokalizacjach

Site Recovery obecnie wymaga, aby grupa zasobów regionu źródłowego i maszyny wirtualne znajdować się w tej samej lokalizacji. Jeśli nie, nie będzie można znaleźć maszyny wirtualnej ani grupy zasobów podczas próby zastosowania ochrony.

Aby obejść ten sposób, można włączyć replikację z poziomu maszyny wirtualnej zamiast magazynu Recovery Services. Przejdź do pozycji właściwości **źródłowej maszyny wirtualnej**  >  **Properties**  >  **odzyskiwanie po awarii** i Włącz replikację.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problem 2: Grupa zasobów nie jest częścią wybranej subskrypcji

Jeśli grupa zasobów nie jest częścią wybranej subskrypcji, może być możliwe znalezienie grupy zasobów w czasie ochrony. Upewnij się, że grupa zasobów należy do używanej subskrypcji.

### <a name="issue-3-stale-configuration"></a>Problem 3: nieodświeżona konfiguracja

Na maszynie wirtualnej platformy Azure może nie być widoczna maszyna wirtualna, która ma zostać włączona na potrzeby Site Recovery replikacji. Ten stan może wystąpić, jeśli włączono replikację maszyny wirtualnej platformy Azure przy użyciu Site Recovery, a następnie:

- Magazyn Site Recovery został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn Site Recovery bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobu.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Upewnij się, że moduł został zaktualizowany `AzureRM.Resources` przed użyciem skryptu wymienionego w tej sekcji. Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę, jeśli istnieje, z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu o nazwie maszyna wirtualna `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Usuń blokadę z maszyny wirtualnej.":::

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, _Cleanup-stale-asr-config-Azure-VM.ps1_. Podaj **Identyfikator subskrypcji**, **grupę zasobów maszyny wirtualnej**i **nazwę maszyny wirtualnej** jako parametry.
1. Jeśli zostanie wyświetlony monit o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-select-a-vm-for-protection"></a>Nie można wybrać maszyny wirtualnej do ochrony

### <a name="possible-cause"></a>Możliwa przyczyna

Maszyna wirtualna ma rozszerzenie zainstalowane w stanie awarii lub braku odpowiedzi

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Przejdź do pozycji ustawienia **maszyn wirtualnych**  >  **Settings**  >  **Extensions** i sprawdź, czy występują rozszerzenia w stanie niepowodzenia. Odinstaluj wszystkie nieudane rozszerzenia, a następnie spróbuj ponownie włączyć ochronę maszyny wirtualnej.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>Stan aprowizacji maszyny wirtualnej jest nieprawidłowy (kod błędu 150019)

Aby włączyć replikację na maszynie wirtualnej, jej stan aprowizacji musi się **powieść**. Wykonaj następujące kroki, aby sprawdzić stan aprowizacji:

1. W Azure Portal wybierz **Eksplorator zasobów** z **wszystkich usług**.
1. Rozwiń listę **subskrypcje** i wybierz swoją subskrypcję.
1. Rozwiń listę **ResourceGroups** i wybierz grupę zasobów maszyny wirtualnej.
1. Rozwiń listę **zasoby** i wybierz maszynę wirtualną.
1. Zaznacz pole **provisioningState** w widoku wystąpienia po prawej stronie.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

- Jeśli **provisioningState** się **nie powiodło**, skontaktuj się z pomocą techniczną, aby uzyskać szczegółowe informacje.
- W przypadku **aktualizowania** **provisioningState** może być wdrażane inne rozszerzenie. Sprawdź, czy na maszynie wirtualnej istnieją bieżące operacje, poczekaj na ich zakończenie, a następnie ponów zadanie Site Recovery nieudane, aby włączyć replikację.

## <a name="unable-to-select-target-vm"></a>Nie można wybrać docelowej maszyny wirtualnej

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problem 1. maszyna wirtualna jest dołączona do sieci, która jest już zamapowana na sieć docelową

Podczas konfigurowania odzyskiwania po awarii, jeśli źródłowa maszyna wirtualna jest częścią sieci wirtualnej, a inna maszyna wirtualna z tej samej sieci wirtualnej jest już zamapowana przy użyciu sieci w docelowej grupie zasobów, pole listy rozwijanej Wybór sieci jest niedostępne (domyślnie wygaszone).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lista wyboru sieci jest niedostępna.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problem 2: wcześniej była chroniona maszyna wirtualna, a następnie wyłączona replikacja

Wyłączenie replikacji maszyny wirtualnej nie powoduje usunięcia mapowania sieci. Należy usunąć mapowanie z magazynu Recovery Services, w którym maszyna wirtualna była chroniona. Wybierz **Magazyn Recovery Services** i przejdź do pozycji **Zarządzaj**  >  **infrastrukturą Site Recovery**  >  **dla mapowania sieci usługi Azure Virtual Machines**  >  **Network Mapping**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Usuń mapowanie sieci.":::

Sieć docelowa, która została skonfigurowana podczas konfigurowania odzyskiwania po awarii, może zostać zmieniona po początkowej konfiguracji i po włączeniu ochrony maszyny wirtualnej. Aby **zmodyfikować mapowanie sieci** , wybierz nazwę sieci:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modyfikowanie mapowania sieci.":::


## <a name="com-or-vss-error-code-151025"></a>Model COM+ lub VSS (kod błędu 151025)

Po wystąpieniu błędu modelu COM+ lub Usługa kopiowania woluminów w tle (VSS) zostanie wyświetlony następujący komunikat:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Możliwe przyczyny

- Usługa aplikacji systemu COM+ jest wyłączona.
- Usługa kopiowania woluminów w tle jest wyłączona.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Ustaw dla aplikacji systemowej COM+ Usługa kopiowania woluminów w tle tryb automatyczny lub ręczny.

1. Otwórz konsolę usługi w systemie Windows.
1. Upewnij się, że aplikacja systemowa modelu COM+ i Usługa kopiowania woluminów w tle nie są **wyłączone** jako ich **Typ uruchamiania**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Sprawdź typ uruchomienia modelu COM oraz aplikację systemową i Usługa kopiowania woluminów w tle.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nieobsługiwany rozmiar dysku zarządzanego (kod błędu 150172)

W przypadku wystąpienia tego błędu zostanie wyświetlony następujący komunikat:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Możliwa przyczyna

Dysk jest mniejszy niż obsługiwany rozmiar wynoszący 1024 MB.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że rozmiar dysku mieści się w obsługiwanym zakresie rozmiaru, a następnie spróbuj ponownie wykonać operację.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Ochrona nie jest włączona, gdy GRUB używa nazwy urządzenia (kod błędu 151126)

### <a name="possible-causes"></a>Możliwe przyczyny

Pliki konfiguracyjne systemu Linux (GRUB) z systemami (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_lub _/etc/default/grub_) mogą określać rzeczywiste nazwy urządzeń zamiast wartości unikatowych identyfikatorów uniwersalnych (UUID) dla `root` `resume` parametrów i. Site Recovery wymaga identyfikatora UUID, ponieważ nazwy urządzeń mogą się zmieniać. Po ponownym uruchomieniu maszyny wirtualnej w trybie failover mogą nie mieć takiej samej nazwy, co spowodowało problemy.

Poniższe przykłady są wierszami z plików GRUB, w których pojawiają się nazwy urządzeń zamiast wymaganych identyfikatorów UUID:

- _/Boot/grub2/grub.cfg_pliku:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Plik: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Zastąp nazwy poszczególnych urządzeń odpowiednimi identyfikatorami UUID:

1. Znajdź identyfikator UUID urządzenia, wykonując polecenie `blkid <device name>` . Na przykład:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Zastąp nazwę urządzenia identyfikatorem UUID w formatach `root=UUID=<UUID>` i `resume=UUID=<UUID>` . Na przykład po zamianie wiersz z _/boot/grub/menu.lst_ będzie wyglądać następująco:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Ponów próbę ochrony.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Ochrona nie powiodła się, ponieważ urządzenie GRUB nie istnieje (kod błędu 151124)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracji GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_lub _/etc/default/grub_) mogą zawierać parametry `rd.lvm.lv` lub `rd_LVM_LV` . Parametry te identyfikują urządzenia z menedżerem woluminów logicznych (LVM), które mają zostać odnalezione w czasie rozruchu. Jeśli te urządzenia LVM nie istnieją, chroniony system nie zostanie uruchomiony i zostanie zablokowany w procesie rozruchu. Ten sam problem będzie również widoczny dla maszyny wirtualnej trybu failover. Oto kilka przykładów:

- Plik: _/Boot/grub2/grub.cfg_ na RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Plik: _/etc/default/grub_ na RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Plik: _/boot/grub/menu.lst_ na RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

W każdym przykładzie GRUB musi wykryć dwa urządzenia LVM z nazwami `root` i `swap` grupą woluminów `rootvg` .

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli urządzenie LVM nie istnieje, utwórz je lub usuń odpowiednie parametry z plików konfiguracji GRUB. Następnie spróbuj ponownie włączyć ochronę.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizacja usługi mobilności została zakończona z ostrzeżeniami (kod błędu 151083)

Usługa mobilności Site Recovery ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchamiania systemu. Za każdym razem, gdy aktualizacja usługi mobilności obejmuje zmiany sterownika filtru, komputer zostanie zaktualizowany, ale nadal zobaczysz ostrzeżenie, że niektóre poprawki wymagają ponownego uruchomienia. Ostrzeżenie jest wyświetlane, ponieważ poprawki sterownika filtru mogą obowiązywać dopiero po załadowaniu nowego sterownika filtru, który jest wykonywany tylko podczas ponownego uruchamiania.

> [!NOTE]
> Jest to tylko ostrzeżenie. Istniejąca replikacja będzie nadal działała nawet po nowej aktualizacji agenta. Możesz wybrać opcję ponownego uruchomienia w każdym przypadku, gdy chcesz skorzystać z nowego sterownika filtru, ale stary sterownik filtru nadal działa, jeśli nie zostanie uruchomiony ponownie.
>
> Oprócz sterownika filtru, korzyści wynikające z innych ulepszeń i poprawek w ramach aktualizacji usługi mobilności mają zastosowanie bez konieczności ponownego uruchamiania.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Ochrona nie jest włączona, jeśli istnieje dysk zarządzany repliki

Ten błąd występuje, gdy dysk zarządzany repliki już istnieje bez oczekiwanych tagów w docelowej grupie zasobów.

### <a name="possible-cause"></a>Możliwa przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna była wcześniej chroniona i gdy replikacja została wyłączona, dysk repliki nie został usunięty.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Usuń dysk repliki określony w komunikacie o błędzie i spróbuj ponownie wykonać zadanie ochrony zakończone niepowodzeniem.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Włączenie ochrony nie powiodło się, ponieważ Instalator nie może znaleźć dysku głównego (kod błędu 151137)

Ten błąd występuje w przypadku maszyn z systemem Linux, w których dysk systemu operacyjnego jest szyfrowany przy użyciu Azure Disk Encryption (ADE). Jest to prawidłowy problem dotyczący tylko agenta w wersji 9,35.

### <a name="possible-causes"></a>Możliwe przyczyny

Instalator nie może znaleźć dysku głównego, który hostuje główny system plików.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać ten problem.

1. Znajdź bity agentów w katalogu _/var/lib/waagent_ na maszynach RHEL i CentOS przy użyciu poniższego polecenia: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Oczekiwane dane wyjściowe:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Utwórz nowy katalog i zmień katalog na ten nowy katalog.
3. Wyodrębnij plik agenta znaleziony w pierwszym kroku w tym miejscu przy użyciu poniższego polecenia:

    `tar -xf <Tar Ball File>`

4. Otwórz plik _prereq_check_installer.jsna_ i Usuń następujące wiersze. Zapisz plik po tym pliku.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Wywołaj Instalatora przy użyciu polecenia: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Jeśli Instalator powiedzie się, spróbuj ponownie wykonać zadanie włączania replikacji.

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](azure-to-azure-how-to-enable-replication.md)
