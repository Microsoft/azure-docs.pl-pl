---
title: Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a1ea83dc62c2752e34584b89de2cdb6dbde3dfa0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443922"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Usługa Azure Disk Encryption dla maszyn wirtualnych z systemem Linux 

Usługa Azure Disk Encryption pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Używa funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure, a ponadto jest zintegrowane z [Azure Key Vault](../../key-vault/index.yml) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków i wpisami tajnymi.

Azure Disk Encryption jest odporny na strefy w taki sam sposób jak Virtual Machines. Aby uzyskać szczegółowe informacje, zobacz [usługi platformy Azure, które obsługują strefy dostępności](../../availability-zones/az-region.md).

Jeśli używasz [Azure Security Center](../../security-center/index.yml), zostanie wyświetlony alert, jeśli masz maszyny wirtualne, które nie są zaszyfrowane. Alerty są wyświetlane jako o wysokiej ważności, a zalecenie polega na zaszyfrowaniu tych maszyn wirtualnych.

![Alert szyfrowania dysku Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Jeśli w celu zaszyfrowania maszyny wirtualnej użyto wcześniej Azure Disk Encryption z usługą Azure AD, musisz użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
> - Niektóre zalecenia mogą zwiększyć użycie zasobów, sieci lub obliczeń, co skutkuje dodatkowymi kosztami licencji lub subskrypcji. Aby tworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.

Podstawowe informacje o Azure Disk Encryption dla systemu Linux można uzyskać w ciągu kilku minut od [utworzenia i zaszyfrowania maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure — szybki start](disk-encryption-cli-quickstart.md) lub [Tworzenie i szyfrowanie maszyny wirtualnej z systemem linux przy użyciu Azure PowerShell przewodnika Szybki Start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Obsługiwane maszyny wirtualne i systemy operacyjne

### <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Maszyny wirtualne z systemem Linux są dostępne w [różnych rozmiarach](../sizes.md). Azure Disk Encryption jest obsługiwana na maszynach wirtualnych 1. i 2. generacji. Azure Disk Encryption jest również dostępna dla maszyn wirtualnych z magazynem w warstwie Premium.

Zobacz [rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego](../azure-vms-no-temp-disk.md).

Azure Disk Encryption nie jest również dostępna na [podstawowych maszynach wirtualnych serii A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)lub na maszynach wirtualnych, które nie spełniają minimalnych wymagań dotyczących pamięci:

| Maszyna wirtualna | Minimalne wymagania dotyczące pamięci |
|--|--|
| Maszyny wirtualne z systemem Linux, w których są szyfrowane tylko woluminy danych| 2 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) jest 4 GB lub mniej | 8 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) o wartości większej niż 4 GB | Użycie głównego systemu plików * 2. Na przykład użycie 16 GB z głównego systemu plików wymaga co najmniej 32 GB pamięci RAM |

Po zakończeniu procesu szyfrowania dysku systemu operacyjnego na maszynach wirtualnych z systemem Linux można skonfigurować maszynę wirtualną do uruchamiania z mniejszą ilością pamięci.

Aby uzyskać więcej wyjątków, zobacz [Azure Disk Encryption: scenariusze nieobsługiwane](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Azure Disk Encryption jest obsługiwane w podzestawie [dystrybucji systemu Linux z zatwierdzona przez platformę Azure](endorsed-distros.md), które jest samym podzbiorem wszystkich możliwych dystrybucji serwerów z systemem Linux.

![Diagram Venna dystrybucji serwerów z systemem Linux, który obsługuje Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Dystrybucje serwera z systemem Linux, które nie są zatwierdzone przez platformę Azure, nie obsługują Azure Disk Encryption; z tych, które są zatwierdzone, obsługiwane są tylko następujące dystrybucje i wersje Azure Disk Encryption:


| Publisher | Oferta | SKU | Nazwa URN | Typ woluminu obsługiwany na potrzeby szyfrowania |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18,04 – LTS | Kanoniczny: UbuntuServer: 18.04-LTS: Najnowsza | System operacyjny i dysk z danymi |
| Canonical | Ubuntu 18.04 | 18,04 — CODZIENNIE — LTS | Kanoniczny: UbuntuServer: 18.04-DAILy-LTS: Najnowsza | System operacyjny i dysk z danymi |
| Canonical | Ubuntu 16.04 | 16,04 — CODZIENNIE — LTS | Kanoniczny: UbuntuServer: 16.04-DAILy-LTS: Najnowsze | System operacyjny i dysk z danymi |
| Canonical | Ubuntu 14.04.5</br>[Dzięki dostrojeniu jądra platformy Azure do wersji 4,15 lub nowszej](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Kanoniczny: UbuntuServer: 14.04.5-LTS: Najnowsza | System operacyjny i dysk z danymi |
| Canonical | Ubuntu 14.04.5</br>[Dzięki dostrojeniu jądra platformy Azure do wersji 4,15 lub nowszej](disk-encryption-troubleshooting.md) | 14.04.5 — CODZIENNIE — LTS | Kanoniczny: UbuntuServer: 14.04.5-DAILy-LTS: Najnowsza | System operacyjny i dysk z danymi |
| RedHat | RHEL 8 — LVM | 8 — LVM | RedHat: RHEL: 8-LVM: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 8,2 | 8.2 | RedHat: RHEL: 8.2: Najnowsze | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 8.1 | 8.1 | RedHat: RHEL: 8.1: Najnowsze | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7 — LVM | 7 — LVM | RedHat: RHEL: 7-LVM: 7.9.2020111202 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,9 | 7_9 | RedHat: RHEL: 7_9: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,8 | 7,8 | RedHat: RHEL: 7.8: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,7 | 7.7 | RedHat: RHEL: 7.7: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7.5 | 7,5 | RedHat: RHEL: 7.5: Najnowsze | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,4 | 7,4 | RedHat: RHEL: 7.4: Najpóźniejsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: Najnowsza | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: Najnowsze | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RedHat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6,8: Najnowsze | Dysk danych (patrz Uwaga poniżej) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6.7. Najnowsza | Dysk danych (patrz Uwaga poniżej) |
| OpenLogic | CentOS 8 — LVM | 8 — LVM | OpenLogic: CentOS-LVM: 8-LVM: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 8,2 | 8_2 | OpenLogic: CentOS: 8_2: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 8,1 | 8_1 | OpenLogic: CentOS: 8_1: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7 — LVM | 7 — LVM | OpenLogic: CentOS-LVM: 7-LVM: 7.9.2021020400 | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,9 | 7_9 | OpenLogic: CentOS: 7_9: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,8 | 7_8 | OpenLogic: CentOS: 7_8: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,7 | 7.7 | OpenLogic: CentOS: 7.7: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic: CentOS: 7.5: Najnowsze | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7.4 | 7,4 | OpenLogic: CentOS: 7.4: Najpóźniejsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,3 | 7.3 | OpenLogic: CentOS: 7.3: Najnowsza | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7.2 n | 7.2 n | OpenLogic: CentOS: 7.2 n: Najnowsze | System operacyjny i dysk z danymi |
| OpenLogic | CentOS 7,1 | 7.1 | OpenLogic: CentOS: 7.1: Najnowsze | Tylko dysk z danymi |
| OpenLogic | CentOS 7,0 | 7.0 | OpenLogic: CentOS: 7.0: Najnowsze | Tylko dysk z danymi |
| OpenLogic | CentOS 6,8 | 6.8 | OpenLogic: CentOS: 6,8: Najnowsze | Tylko dysk z danymi |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE-przestępne: 42.3: Najnowsze | Tylko dysk z danymi |
| SUSE | SLES 12 — SP4 | 12 — SP4 | SUSE: SLES: 12-SP4: Najnowsza | Tylko dysk z danymi |
| SUSE | SLES HPC 12-SP3 | 12 — SP3 | SUSE: SLES-HPC: 12-SP3: Najnowsze | Tylko dysk z danymi |

> [!NOTE]
> Nowa implementacja Azure Disk Encryption jest obsługiwana w przypadku systemu operacyjnego RHEL i dysku danych dla obrazów z opcją płatność zgodnie z rzeczywistym użyciem.  
>
> ADE jest również obsługiwane w przypadku RHELowych obrazów z subskrypcją, ale dopiero **po** zarejestrowaniu subskrypcji. Aby uzyskać więcej informacji, zobacz [Red Hat Enterprise Linux przenoszenie własnych subskrypcji Gold na platformie Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Dodatkowe wymagania dotyczące maszyn wirtualnych

Azure Disk Encryption wymaga obecności modułów dm-crypt i vfat w systemie. Usunięcie lub wyłączenie vfat z obrazu domyślnego uniemożliwi systemowi odczytywanie woluminu klucza i Uzyskiwanie klucza wymaganego do odblokowania dysków podczas kolejnych ponownych uruchomień. Czynności związane z ograniczaniem systemu, które usuwają moduł vfat z systemu lub wymuszają Rozszerzanie systemu operacyjnego mountpoints/folderów na dyskach danych, nie są zgodne z Azure Disk Encryption. 

Przed włączeniem szyfrowania, dyski danych, które mają być szyfrowane, muszą być poprawnie wymienione w katalogu/etc/fstab. Użyj opcji "nofail" podczas tworzenia wpisów i wybierz trwałej nazwy urządzenia blokowego (ponieważ nazwy urządzeń w formacie "/dev/sdX" mogą nie być skojarzone z tym samym dyskiem w przypadku ponownych uruchomień, szczególnie po szyfrowaniu. Aby uzyskać więcej szczegółów dotyczących tego zachowania, zobacz: [Rozwiązywanie problemów z zmianami nazw urządzeń maszyn wirtualnych systemu Linux](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)).

Upewnij się, że ustawienia/etc/fstab są prawidłowo skonfigurowane do zainstalowania. Aby skonfigurować te ustawienia, uruchom polecenie instalacji-a lub Uruchom ponownie maszynę wirtualną i Wyzwól w ten sposób ponowne zainstalowanie. Po zakończeniu Sprawdź dane wyjściowe polecenia lsblk, aby sprawdzić, czy dysk jest nadal zainstalowany. 

- Jeśli plik/etc/fstab nie instaluje dysku prawidłowo przed włączeniem szyfrowania, Azure Disk Encryption nie będzie w stanie zainstalować go prawidłowo.
- Proces Azure Disk Encryption spowoduje przeniesienie informacji o instalacji z/etc/fstab i do własnego pliku konfiguracji w ramach procesu szyfrowania. Nie można sprawdzić, czy nie ma wpisu w/etc/fstab po zakończeniu szyfrowania dysku danych.
- Przed rozpoczęciem szyfrowania należy zatrzymać wszystkie usługi i procesy, które mogą być zapisywane na zainstalowanych dyskach danych i je wyłączyć, aby nie uruchamiały się automatycznie po ponownym uruchomieniu komputera. Może to spowodować, że pliki będą otwierane na tych partycjach, co uniemożliwia procedurę szyfrowania w celu ich ponownego zainstalowania, powodując niepowodzenie szyfrowania. 
- Po ponownym uruchomieniu proces Azure Disk Encryption będzie miał czas na zainstalowanie nowo zaszyfrowanych dysków. Po ponownym uruchomieniu nie będą one natychmiast dostępne. Proces wymaga czasu uruchomienia, odblokowania, a następnie zainstalowania szyfrowanych dysków przed udostępnieniem ich innym procesom. Ten proces może potrwać kilka minut po ponownym uruchomieniu w zależności od charakterystyki systemu.

Poniżej przedstawiono przykładowe polecenia służące do instalowania dysków danych i tworzenia niezbędnych wpisów/etc/fstab:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Aby włączyć funkcję Azure Disk Encryption, maszyny wirtualne z systemem Linux muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory, \[ login.microsoftonline.com \] .
  - Aby można było napisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania  

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia

Poniższa tabela zawiera definicje typowych terminów używanych w dokumentacji usługi Azure Disk Encryption:

| Terminologia | Definicja |
| --- | --- |
| Azure Key Vault | Key Vault to kryptograficzna usługa zarządzania kluczami oparta na sprawdzonych modułach zabezpieczeń (FIPS) Te standardy pomagają chronić klucze kryptograficzne i poufne wpisy tajne. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany pod kątem zarządzania zasobami platformy Azure i administrowania nimi z wiersza poleceń.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) to oparty na systemie Linux, przezroczysty podsystem szyfrowania dysków używany do włączania szyfrowania dysków na maszynach wirtualnych z systemem Linux. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Można podać klucz chroniony przez sprzętowy moduł zabezpieczeń (HSM) lub klucz chroniony przez oprogramowanie. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [polecenia cmdlet Azure PowerShell](/powershell/azure/). |


## <a name="next-steps"></a>Następne kroki

- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure ](disk-encryption-cli-quickstart.md)
- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu Azure PowerShell](disk-encryption-powershell-quickstart.md) 
- [Scenariusze usługi Azure Disk Encryption w przypadku maszyn wirtualnych z systemem Linux](disk-encryption-linux.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](disk-encryption-key-vault.md)