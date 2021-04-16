---
title: Szyfrowanie dysków zarządzanych platformy Azure po stronie serwera
description: Usługa Azure Storage chroni dane, szyfrując je w spoczynku przed utrwaleniem ich w klastrach magazynu. Za pomocą kluczy zarządzanych przez klienta można zarządzać szyfrowaniem przy użyciu własnych kluczy lub można polegać na kluczach zarządzanych przez firmę Microsoft w celu szyfrowania dysków zarządzanych.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531521"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Szyfrowanie po stronie serwera Azure Disk Storage

Większość dysków zarządzanych platformy Azure jest szyfrowana za pomocą szyfrowania usługi Azure Storage, która korzysta z szyfrowania po stronie serwera (SSE) w celu ochrony danych i spełnienia wymagań organizacji dotyczących zabezpieczeń i zgodności. Szyfrowanie usługi Azure Storage automatycznie szyfruje dane przechowywane na dyskach zarządzanych platformy Azure (dyskach systemu operacyjnego i danych) magazynowanych domyślnie podczas utrwalania ich w chmurze. Dyski z włączonym szyfrowaniem na hoście nie są jednak szyfrowane za pośrednictwem usługi Azure Storage. W przypadku dysków z włączonym szyfrowaniem na hoście serwer hostowania maszyny wirtualnej zapewnia szyfrowanie danych i że zaszyfrowane dane przepływają do usługi Azure Storage.

Dane na dyskach zarządzanych platformy Azure są szyfrowane w sposób przezroczysty przy użyciu 256-bitowego szyfrowania [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych i są zgodne ze standardem FIPS 140-2. Aby uzyskać więcej informacji na temat modułów kryptograficznych bazowych dla dysków zarządzanych platformy Azure, zobacz [Interfejs API kryptografii: następna generacja](/windows/desktop/seccng/cng-portal)

Szyfrowanie usługi Azure Storage nie ma wpływu na wydajność dysków zarządzanych i nie ma dodatkowych kosztów. Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [Szyfrowanie usługi Azure Storage.](/azure/storage/common/storage-service-encryption)

> [!NOTE]
> Dyski tymczasowe nie są dyskami zarządzanymi i nie są szyfrowane za pomocą funkcji SSE, chyba że włączysz szyfrowanie na hoście.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Do szyfrowania dysku zarządzanego można polegać na kluczach zarządzanych przez platformę lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się zarządzać szyfrowaniem przy  użyciu własnych kluczy, możesz określić klucz zarządzany przez klienta, który będzie używany do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

W poniższych sekcjach bardziej szczegółowo opisano każdą z opcji zarządzania kluczami.

### <a name="platform-managed-keys"></a>Klucze zarządzane przez platformę

Domyślnie dyski zarządzane używają kluczy szyfrowania zarządzanych przez platformę. Wszystkie dyski zarządzane, migawki, obrazy i dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez platformę.

### <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla dysku, nie można jej wyłączyć.
    Jeśli musisz to zrobić, musisz skopiować wszystkie dane przy użyciu modułu [Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) lub interfejsu wiersza polecenia platformy [Azure](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)na zupełnie inny dysk zarządzany, który nie używa kluczy zarządzanych przez klienta.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Obsługiwane regiony

Klucze zarządzane przez klienta są dostępne we wszystkich regionach, w których są dostępne dyski zarządzane.

Automatyczna rotacja kluczy jest dostępna w wersji zapoznawczej tylko w następujących regionach:

- East US
- Wschodnie stany USA 2
- South Central US
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- West Europe
- Francja Środkowa

> [!IMPORTANT]
> Klucze zarządzane przez klienta polegają na tożsamościach zarządzanych dla zasobów platformy Azure— funkcji usługi Azure Active Directory (Azure AD). Podczas konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do twoich zasobów. Jeśli następnie przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z dyskami zarządzanymi nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą już nie działać. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji między katalogami usługi Azure AD.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

Aby włączyć klucze zarządzane przez klienta dla dysków zarządzanych, zobacz nasze artykuły dotyczące włączania ich za pomocą modułu [Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-customer-managed-keys-cli.md) lub interfejsu [Azure Portal](disks-enable-customer-managed-keys-portal.md). Aby dowiedzieć się, jak włączyć klucze zarządzane przez klienta przy użyciu automatycznej rotacji kluczy, zobacz Konfigurowanie zestawu Azure Key Vault i [zestawu DiskEncryptionSet z](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)automatyczną rotacją kluczy (wersja zapoznawcza).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Szyfrowanie na hoście — end-to-end encryption dla danych maszyny wirtualnej

Po włączeniu szyfrowania na hoście to szyfrowanie rozpoczyna się na samym hoście maszyny wirtualnej, serwerze platformy Azure, do którym maszyna wirtualna jest przydzielona. Dane dla dysku tymczasowego i pamięci podręcznych dysków systemu operacyjnego/danych są przechowywane na tym hoście maszyny wirtualnej. Po włączeniu szyfrowania na hoście wszystkie te dane są szyfrowane w spoczynku i przepływy szyfrowane do usługi Storage, gdzie są utrwalane. Zasadniczo szyfrowanie na hoście szyfruje dane od początku do końca. Szyfrowanie na hoście nie używa procesora CPU maszyny wirtualnej i nie wpływa na wydajność maszyny wirtualnej. 

Dyski tymczasowe i efemeralne dyski systemu operacyjnego są szyfrowane w spoczynku za pomocą kluczy zarządzanych przez platformę po włączeniu szyfrowania na poziomie end-to-end. Pamięci podręczne dysków systemu operacyjnego i danych są szyfrowane przy użyciu kluczy zarządzanych przez klienta lub zarządzanych przez platformę, w zależności od wybranego typu szyfrowania dysku. Jeśli na przykład dysk jest zaszyfrowany za pomocą kluczy zarządzanych przez klienta, pamięć podręczna dysku jest szyfrowana przy użyciu kluczy zarządzanych przez klienta, a jeśli dysk jest zaszyfrowany za pomocą kluczy zarządzanych przez platformę, pamięć podręczna dysku jest szyfrowana przy użyciu kluczy zarządzanych przez platformę.

### <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Rozmiary maszyn wirtualnych można również znaleźć programowo. Aby dowiedzieć się, jak pobrać je programowo, zapoznaj się z sekcją znajdowanie obsługiwanych rozmiarów maszyn wirtualnych w Azure PowerShell [modułu lub](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) interfejsu wiersza polecenia [platformy Azure.](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)

Aby włączyć szyfrowanie na poziomie end-to-end przy użyciu szyfrowania na hoście, zobacz nasze artykuły dotyczące sposobu włączania go za pomocą modułu [Azure PowerShell,](windows/disks-enable-host-based-encryption-powershell.md)interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-host-based-encryption-cli.md)lub interfejsu [Azure Portal](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Podwójne szyfrowanie w spoczynku

Klienci o wysokim poziomie zabezpieczeń, którzy są zaniepokojeni ryzykiem związanym z konkretnym algorytmem szyfrowania, implementacją lub złamanym kluczem, mogą teraz zdecydować się na dodatkową warstwę szyfrowania przy użyciu innego algorytmu/trybu szyfrowania w warstwie infrastruktury przy użyciu kluczy szyfrowania zarządzanych przez platformę. Tę nową warstwę można zastosować do utrwalonych dysków systemu operacyjnego i danych, migawek i obrazów, z których wszystkie będą szyfrowane podczas przechowywania przy użyciu podwójnego szyfrowania.

### <a name="supported-regions"></a>Obsługiwane regiony

Podwójne szyfrowanie jest dostępne we wszystkich regionach, w których są dostępne dyski zarządzane.

Aby włączyć podwójne szyfrowanie danych magazynowych dla dysków zarządzanych, zobacz nasze artykuły, w których opisano sposób włączania go za pomocą modułu [Azure PowerShell,](windows/disks-enable-double-encryption-at-rest-powershell.md)interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-double-encryption-at-rest-cli.md) [lub interfejsu Azure Portal.](disks-enable-double-encryption-at-rest-portal.md)

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Szyfrowanie po stronie serwera a szyfrowanie dysków na platformie Azure

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) korzysta z funkcji [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux lub funkcji [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) systemu Windows do szyfrowania dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta na maszynie wirtualnej gościa.  Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta ulepsza usługę ADE, umożliwiając korzystanie z dowolnego typu systemu operacyjnego i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.
> [!IMPORTANT]
> Klucze zarządzane przez klienta polegają na tożsamościach zarządzanych dla zasobów platformy Azure— funkcji usługi Azure Active Directory (Azure AD). Podczas konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do twoich zasobów. Jeśli następnie przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z dyskami zarządzanymi nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą już nie działać. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji między katalogami usługi Azure AD.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="next-steps"></a>Następne kroki

- Włącz szyfrowanie end-to-end przy użyciu szyfrowania na hoście za pomocą modułu [Azure PowerShell,](windows/disks-enable-host-based-encryption-powershell.md)interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-host-based-encryption-cli.md) [lub interfejsu Azure Portal](disks-enable-host-based-encryption-portal.md).
- Włącz podwójne szyfrowanie w spoczynku dla dysków zarządzanych przy [użyciu modułu Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-double-encryption-at-rest-cli.md) lub [interfejsu Azure Portal](disks-enable-double-encryption-at-rest-portal.md).
- Włącz klucze zarządzane przez klienta dla dysków zarządzanych za pomocą [modułu Azure PowerShell ,](windows/disks-enable-customer-managed-keys-powershell.md)interfejsu wiersza polecenia platformy [Azure](linux/disks-enable-customer-managed-keys-cli.md) lub [interfejsu Azure Portal](disks-enable-customer-managed-keys-portal.md).
- [Zapoznaj się z Azure Resource Manager tworzenia zaszyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
