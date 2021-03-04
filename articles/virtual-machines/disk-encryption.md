---
title: Szyfrowanie po stronie serwera dla usługi Azure Managed disks
description: Usługa Azure Storage chroni dane, szyfrując je w stanie spoczynku przed utrwalaniem ich w klastrach magazynu. Za pomocą kluczy zarządzanych przez klienta można zarządzać szyfrowaniem przy użyciu własnych kluczy lub można polegać na kluczach zarządzanych przez firmę Microsoft w celu szyfrowania dysków zarządzanych.
author: roygara
ms.date: 03/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a1fbd536943023d3e6724b9c1638f7a0bd97d847
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036949"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Szyfrowanie po stronie serwera Azure Disk Storage

Szyfrowanie po stronie serwera (SSE) chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji. Funkcja SSE automatycznie szyfruje dane przechowywane na dyskach zarządzanych przez platformę Azure (dyski systemu operacyjnego i danych) domyślnie, gdy są utrwalane w chmurze. 

Dane w usłudze Azure Managed disks są szyfrowane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z dyskami zarządzanymi platformy Azure, zobacz [interfejs API kryptografii: Kolejna generacja](/windows/desktop/seccng/cng-portal)

Szyfrowanie po stronie serwera nie ma wpływu na wydajność dysków zarządzanych i nie ma dodatkowych kosztów. 

> [!NOTE]
> Dyski tymczasowe nie są dyskami zarządzanymi i nie są szyfrowane przez funkcję SSE, o ile nie zostanie włączone szyfrowanie na hoście.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez platformę do szyfrowania dysku zarządzanego lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy użyciu własnych kluczy, możesz określić *klucz zarządzany przez klienta* , który będzie używany do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

W poniższych sekcjach opisano każdą z opcji zarządzania kluczami w bardziej szczegółowy sposób.

### <a name="platform-managed-keys"></a>Klucze zarządzane przez platformę

Domyślnie dyski zarządzane korzystają z kluczy szyfrowania zarządzanych przez platformę. Wszystkie dyski zarządzane, migawki, obrazy i dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę.

### <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla danego dysku, nie można jej wyłączyć.
    Jeśli zachodzi potrzeba obejścia tego problemu, należy skopiować wszystkie dane przy użyciu [modułu Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) lub [interfejsu wiersza polecenia platformy Azure](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)do zupełnie innego dysku zarządzanego, który nie korzysta z kluczy zarządzanych przez klienta.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Obsługiwane regiony

Klucze zarządzane przez klienta są dostępne we wszystkich regionach, w których są dostępne dyski zarządzane.

Automatyczne rotacja kluczy jest w wersji zapoznawczej i dostępne tylko w następujących regionach:

- East US
- Wschodnie stany USA 2
- South Central US
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- Europa Zachodnia
- Francja Środkowa

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Aby włączyć klucze zarządzane przez klienta dla dysków zarządzanych, zapoznaj się z naszymi artykułami dotyczącymi sposobu włączania jej przy użyciu [modułu Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), [interfejsu wiersza polecenia platformy Azure](linux/disks-enable-customer-managed-keys-cli.md) lub [Azure Portal](disks-enable-customer-managed-keys-portal.md). Aby dowiedzieć się, jak włączyć klucze zarządzane przez klienta z automatycznym rotacją kluczy, zobacz [konfigurowanie Azure Key Vault i DiskEncryptionSet przy użyciu automatycznego rotacji kluczy (wersja zapoznawcza)](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Szyfrowanie na poziomie hosta-end-to-end dla danych maszyny wirtualnej

Po włączeniu szyfrowania na hoście to szyfrowanie zostanie uruchomione na hoście maszyny wirtualnej, do którego zostanie przypisana maszyna wirtualna. Dane na dysku tymczasowym i pamięci podręcznej dysku danych są przechowywane na tym hoście maszyny wirtualnej. Po włączeniu szyfrowania na hoście wszystkie te dane są szyfrowane w stanie spoczynku i przechodzą zaszyfrowane do usługi magazynu, gdzie są utrwalane. Zasadniczo szyfrowanie na hoście szyfruje dane od końca do końca. Szyfrowanie na hoście nie korzysta z procesora CPU maszyny wirtualnej i nie ma wpływu na wydajność maszyny wirtualnej. 

Dyski tymczasowe i krótkoterminowe dyski systemu operacyjnego są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez platformę po włączeniu kompleksowego szyfrowania. Pamięci podręczne dysków systemu operacyjnego i danych są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez klienta lub na platformę, w zależności od wybranego typu szyfrowania dysku. Na przykład, jeśli dysk jest szyfrowany przy użyciu kluczy zarządzanych przez klienta, pamięć podręczna dysku jest szyfrowana przy użyciu kluczy zarządzanych przez klienta, a jeśli dysk jest szyfrowany przy użyciu kluczy zarządzanych przez platformę, pamięć podręczna dysku jest szyfrowana za pomocą kluczy zarządzanych przez platformę.

### <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Aby włączyć kompleksowe szyfrowanie przy użyciu szyfrowania na hoście, zobacz nasze artykuły, w których opisano sposób włączania go za pomocą [modułu Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), [interfejsu wiersza polecenia platformy Azure](linux/disks-enable-host-based-encryption-cli.md)lub [Azure Portal](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Podwójne szyfrowanie w spoczynku

Klienci z wysokim poziomem zabezpieczeń, którzy są zainteresowani ryzykiem związanym z określonym algorytmem szyfrowania, implementacją lub złamanym kluczem, mogą teraz wybrać dodatkową warstwę szyfrowania przy użyciu innego algorytmu/trybu szyfrowania w warstwie infrastruktury przy użyciu kluczy szyfrowania zarządzanych przez platformę. Ta nowa warstwa może zostać zastosowana do utrwalonych dysków systemu operacyjnego i danych, migawek i obrazów, a wszystkie będą szyfrowane przy użyciu podwójnego szyfrowania.

### <a name="supported-regions"></a>Obsługiwane regiony

Podwójne szyfrowanie jest dostępne we wszystkich regionach, w których są dostępne dyski zarządzane.

Aby włączyć podwójne szyfrowanie dla dysków zarządzanych, zapoznaj się z artykułami dotyczącymi sposobu włączania go za pomocą [modułu Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), [interfejsu wiersza polecenia platformy Azure](linux/disks-enable-double-encryption-at-rest-cli.md) lub [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Szyfrowanie po stronie serwera a usługa Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) wykorzystuje funkcję [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux lub funkcję [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) w systemie Windows do szyfrowania dysków zarządzanych z kluczami ZARZĄDZANYMI przez klienta w ramach maszyny wirtualnej gościa.  Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta usprawnia w systemie ADE, umożliwiając korzystanie z dowolnych typów systemów operacyjnych i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Storage.
> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Następne kroki

- Włącz kompleksowe szyfrowanie przy użyciu szyfrowania na hoście z [modułem Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), [interfejsem wiersza polecenia platformy Azure](linux/disks-enable-host-based-encryption-cli.md)lub [Azure Portal](disks-enable-host-based-encryption-portal.md).
- Włącz podwójne szyfrowanie dla dysków zarządzanych przy użyciu [modułu Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), [interfejsu wiersza polecenia platformy Azure](linux/disks-enable-double-encryption-at-rest-cli.md) lub [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).
- Włącz klucze zarządzane przez klienta dla dysków zarządzanych przy użyciu [modułu Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), [interfejsu wiersza polecenia platformy Azure](linux/disks-enable-customer-managed-keys-cli.md) lub [Azure Portal](disks-enable-customer-managed-keys-portal.md).
- [Poznaj szablony Azure Resource Manager tworzenia szyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)