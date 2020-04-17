---
title: Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fa7e085f723d4f4c411f52e045c9437d5cb293b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459784"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux 

Usługa Azure Disk Encryption pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Używa funkcji [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure (VM) i jest zintegrowany z [usługą Azure Key Vault,](../../key-vault/index.yml) aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi oraz zarządzanie nimi. 

Jeśli używasz [usługi Azure Security Center,](../../security-center/index.yml)zostaniesz powiadomiony, jeśli masz maszyny wirtualne, które nie są szyfrowane. Alerty są wyświetlane jako wysoka ważność, a zalecenie jest szyfrowanie tych maszyn wirtualnych.

![Alert szyfrowania dysku usługi Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, musisz nadal używać tej opcji do szyfrowania maszyny wirtualnej. Szczegółowe informacje można znaleźć [w witrynie Azure Disk Encryption with Azure AD (previous release).](disk-encryption-overview-aad.md) 
> - Niektóre zalecenia mogą zwiększać użycie danych, sieci lub zasobów obliczeniowych, co spowoduje dodatkowe koszty licencji lub subskrypcji. Aby utworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.
> - Obecnie maszyny wirtualne generacji 2 nie obsługują szyfrowania dysków platformy Azure. Zobacz [pomoc techniczną dla maszyn wirtualnych generacji 2 na platformie Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) aby uzyskać szczegółowe informacje.

Podstawy szyfrowania dysków platformy Azure dla systemu Linux można poznać w ciągu zaledwie kilku minut za pomocą [narzędzia Utwórz i zaszyfruj maszynę wirtualną z systemem Linux za pomocą przewodnika Szybki start platformy Azure cli](disk-encryption-cli-quickstart.md) lub narzędzia [Utwórz i zaszyfruj maszynę wirtualną z systemem Linux za pomocą przewodnika Szybki start usługi Azure Powershell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Obsługiwane maszyny wirtualne i systemy operacyjne

### <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Maszyny wirtualne z systemem Linux są dostępne w [różnych rozmiarach.](sizes.md) Szyfrowanie dysków platformy Azure nie jest dostępne na [maszynach wirtualnych podstawowych i szeregowych serii A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ani na maszynach wirtualnych, które nie spełniają tych wymagań dotyczących pamięci minimalnej:

| Maszyna wirtualna | Minimalne zapotrzebowanie na pamięć |
|--|--|
| Maszyny wirtualne z systemem Linux podczas szyfrowania tylko woluminów danych| 2 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania zarówno woluminów danych, jak i systemów operacyjnych oraz gdzie użycie głównego systemu plików (/) wynosi 4 GB lub mniej | 8 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania zarówno woluminów danych, jak i systemów operacyjnych oraz gdzie użycie głównego systemu plików (/) jest większe niż 4 GB | Użycie głównego systemu plików * 2. Na przykład 16 GB użycia głównego systemu plików wymaga co najmniej 32 GB pamięci RAM |

Po zakończeniu procesu szyfrowania dysku systemu operacyjnego na maszynach wirtualnych systemu Linux maszyn wirtualnych można skonfigurować do pracy z mniejszą ilością pamięci. 

Szyfrowanie dysków platformy Azure jest również dostępne dla maszyn wirtualnych z magazynu w stanie Premium.

Szyfrowanie dysków platformy Azure nie jest dostępne na [maszynach wirtualnych generacji 2)](generation-2.md#generation-1-vs-generation-2-capabilities)i [maszynach wirtualnych z serii Lsv2](../lsv2-series.md)). Aby uzyskać więcej wyjątków, zobacz [Szyfrowanie dysków platformy Azure: Nieobsługiwowane scenariusze](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Szyfrowanie dysków platformy Azure jest obsługiwane w podzbiorze [dystrybucji systemu Linux zatwierdzonych przez platformę Azure,](endorsed-distros.md)która sama w sobie jest podzbiorem wszystkich możliwych dystrybucji serwera Linux.

![Diagram Venna dystrybucji serwerów systemu Linux, które obsługują szyfrowanie dysków platformy Azure](./media/disk-encryption/ade-supported-distros.png)

Dystrybucje serwera systemu Linux, które nie są zatwierdzone przez platformę Azure, nie obsługują szyfrowania dysków platformy Azure; z tych, które są zatwierdzone, tylko następujące dystrybucje i wersje obsługują szyfrowanie dysków Azure:

| Dystrybucja Linuksa | Wersja | Typ woluminu obsługiwany dla szyfrowania|
| --- | --- |--- |
| Ubuntu | 18.04| System operacyjny i dysk danych |
| Ubuntu | 16.04| System operacyjny i dysk danych |
| Ubuntu | 14.04.5</br>[z dostrojoną jądrem platformy Azure zaktualizowaną do 4.15 lub nowszej](disk-encryption-troubleshooting.md) | System operacyjny i dysk danych |
| RHEL | 7.7 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 7.6 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 7,5 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 7.4 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 7.3 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 7.2 | System operacyjny i dysk danych (patrz uwaga poniżej) |
| RHEL | 6.8 | Dysk danych (patrz uwaga poniżej) |
| RHEL | 6.7 | Dysk danych (patrz uwaga poniżej) |
| CentOS | 7.7 | System operacyjny i dysk danych |
| CentOS | 7.6 | System operacyjny i dysk danych |
| CentOS | 7,5 | System operacyjny i dysk danych |
| CentOS | 7.4 | System operacyjny i dysk danych |
| CentOS | 7.3 | System operacyjny i dysk danych |
| CentOS | 7.2n | System operacyjny i dysk danych |
| CentOS | 6.8 | Dysk z danymi |
| openSUSE | 42.3 | Dysk z danymi |
| SLES | 12-SP4 | Dysk z danymi |
| SLES | 12-SP3 | Dysk z danymi |

> [!NOTE]
> Nowa implementacja szyfrowania dysków azure jest obsługiwana dla systemu operacyjnego RHEL i dysku danych dla obrazów RHEL7 Pay-As-You-Go.  
>
> ADE jest również obsługiwane dla RHEL Bring-Your-Own-Subscription Gold Images, ale tylko **po** zarejestrowaniu subskrypcji. Aby uzyskać więcej informacji, zobacz [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Dodatkowe wymagania dotyczące maszyn wirtualnych

Szyfrowanie dysków platformy Azure wymaga, aby moduły dm-crypt i vfat były obecne w systemie. Usunięcie lub wyłączenie vfat z domyślnego obrazu uniemożliwi systemowi odczytanie woluminu klucza i uzyskanie klucza potrzebnego do odblokowania dysków przy kolejnych ponownych rozruchach. Kroki zaostrzenia systemu, które usuwają moduł vfat z systemu, nie są zgodne z szyfrowaniem dysku Azure. 

Przed włączeniem szyfrowania dyski danych, które mają być zaszyfrowane, muszą być poprawnie wymienione w /etc/fstab. Użyj trwałej nazwy urządzenia bloku dla tego wpisu, ponieważ nazwy urządzeń w formacie "/dev/sdX" nie mogą być skojarzone z tym samym dyskiem podczas ponownego uruchamiania, szczególnie po zastosowaniu szyfrowania. Aby uzyskać więcej informacji na temat tego zachowania, zobacz: [Rozwiązywanie problemów ze zmianami nazw urządzeń maszyn wirtualnych z systemem Linux](troubleshoot-device-names-problems.md)

Upewnij się, że ustawienia /etc/fstab są prawidłowo skonfigurowane do montażu. Aby skonfigurować te ustawienia, uruchom polecenie mount -a lub uruchom ponownie maszynę wirtualną i wyzwolć ponowne zamontowanie w ten sposób. Po zakończeniu sprawdź dane wyjściowe polecenia LSBLK, aby sprawdzić, czy napęd jest nadal zamontowany. 
- Jeśli plik /etc/fstab nie zainstaluje dysku poprawnie przed włączeniem szyfrowania, szyfrowanie dysków platformy Azure nie będzie w stanie poprawnie go zainstalować.
- Proces szyfrowania dysków azure przeniesie informacje o instalacji z /etc/fstab i do własnego pliku konfiguracyjnego w ramach procesu szyfrowania. Nie przejrzyj się, aby zobaczyć wpis brakuje /etc/fstab po zakończeniu szyfrowania dysku danych.
- Przed rozpoczęciem szyfrowania należy zatrzymać wszystkie usługi i procesy, które mogą być zapisywane na zainstalowanych dyskach danych i wyłączyć je, aby nie uruchamiały się automatycznie po ponownym uruchomieniu komputera. Mogą one przechowywać pliki otwarte na tych partycjach, uniemożliwiając procedurę szyfrowania, aby ponownie je zamontować, powodując niepowodzenie szyfrowania. 
- Po ponownym uruchomieniu komputera zajmie trochę czasu, aby proces szyfrowania dysków platformy Azure zainstalować nowo zaszyfrowane dyski. Nie będą one natychmiast dostępne po ponownym uruchomieniu komputera. Proces wymaga czasu, aby uruchomić, odblokować, a następnie zamontować zaszyfrowane dyski, zanim będą dostępne dla innych procesów, aby uzyskać dostęp. Ten proces może potrwać dłużej niż minutę po ponownym uruchomieniu komputera w zależności od charakterystyki systemu.

Przykład poleceń, które mogą być używane do instalowania dysków z danymi i tworzenia niezbędnych wpisów /etc/fstab można znaleźć w [skrypcie interfejsu wiersza polecenia szyfrowania dysków azure](https://github.com/ejarvi/ade-cli-getting-started) (linie 244-248) i [skryptu powershell wymagania wstępne szyfrowania dysku Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Aby włączyć funkcję szyfrowania dysków platformy Azure, maszyny wirtualne z systemem Linux muszą spełniać następujące wymagania konfiguracji sieciowego punktu końcowego:
  - Aby uzyskać token do łączenia się z magazynem kluczy, maszyna wirtualna z \[systemem Linux\]musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Active Directory, login.microsoftonline.com .
  - Aby zapisać klucze szyfrowania w magazynie kluczy, maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu platformy Azure, który obsługuje repozytorium rozszerzenia platformy Azure i konto magazynu platformy Azure, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozpoznać poprzedni identyfikator URI i skonfigurować określoną regułę, aby umożliwić łączność wychodzącą z adresami IP. Aby uzyskać więcej informacji, zobacz [Usługa Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące przechowywania kluczy szyfrowania  

Szyfrowanie dysków platformy Azure wymaga usługi Azure Key Vault do kontrolowania kluczy szyfrowania dysku i zarządzania nimi oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie platformy Azure i subskrypcji.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
W poniższej tabeli zdefiniowano niektóre typowe terminy używane w dokumentacji szyfrowania dysków platformy Azure:

| Terminologia | Definicja |
| --- | --- |
| W usłudze Azure Key Vault | Key Vault to kryptograficzna usługa zarządzania kluczami oparta na sprawdzonych przez federalne moduły zabezpieczeń fips (Federal Information Processing Standards). Te standardy pomagają chronić klucze kryptograficzne i poufne wpisy tajne. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oraz tworzenie i [konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md). |
| Interfejs wiersza polecenia platformy Azure | [Narzędzie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowane pod kątem zarządzania zasobami platformy Azure i administrowania nimi z wiersza polecenia.|
| DM-Krypta |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) to oparty na systemie Linux, przezroczysty podsystem szyfrowania dysków, który służy do włączania szyfrowania dysków na maszynach wirtualnych z systemem Linux. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Można podać klucz chroniony sprzętowym modułem zabezpieczeń (HSM) lub klucz chroniony programowo. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oraz tworzenie i [konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md). |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [polecenia cmdlet programu Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Następne kroki

- [Szybki start — tworzenie i szyfrowanie maszyny Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Szybki start — tworzenie i szyfrowanie maszyny Wirtualnej z systemem Linux za pomocą programu Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux](disk-encryption-linux.md)
- [Wymagane szyfrowanie dysków platformy Azure skrypt interfejsu wiersza polecenia](https://github.com/ejarvi/ade-cli-getting-started)
- [Wymagania wstępne szyfrowania dysków platformy Azure w programie PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](disk-encryption-key-vault.md)


