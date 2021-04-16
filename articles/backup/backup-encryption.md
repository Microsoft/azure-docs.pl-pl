---
title: Szyfrowanie w usłudze Azure Backup
description: Dowiedz się, jak funkcje szyfrowania Azure Backup pomagają chronić dane kopii zapasowej i spełniać potrzeby firmy dotyczące zabezpieczeń.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: 28d165ccc8a966091a96fc433660899d8eef1595
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518477"
---
# <a name="encryption-in-azure-backup"></a>Szyfrowanie w usłudze Azure Backup

Wszystkie dane kopii zapasowej są automatycznie szyfrowane podczas przechowywania w chmurze przy użyciu szyfrowania usługi Azure Storage, co pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności. Te dane w spoczynku są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych, i są zgodne ze standardem FIPS 140-2. Oprócz szyfrowania danych w spoczynku wszystkie przesyłane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Poziomy szyfrowania w Azure Backup

Azure Backup obejmuje szyfrowanie na dwóch poziomach:

- **Szyfrowanie danych w magazynie usługi Recovery Services**
  - **Przy użyciu kluczy zarządzanych przez** platformę: domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć to szyfrowanie, nie musisz nic jawnie akcję od swojej strony. Dotyczy to wszystkich obciążeń, których kopię zapasową należy wrócić do magazynu usługi Recovery Services.
  - **Korzystanie z kluczy zarządzanych** przez klienta: podczas kopii zapasowej usługi Azure Virtual Machines możesz wybrać szyfrowanie danych przy użyciu kluczy szyfrowania należących do Ciebie i zarządzanych przez Ciebie. Azure Backup umożliwia szyfrowanie kopii zapasowych przy użyciu kluczy RSA przechowywanych Azure Key Vault chmurze. Klucz szyfrowania używany do szyfrowania kopii zapasowych może różnić się od klucza używanego dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych (DEK) opartego na standardach AES 256, który z kolei jest chroniony przy użyciu kluczy. Daje to pełną kontrolę nad danymi i kluczami. Aby zezwolić na szyfrowanie, należy udzielić magazynowi usługi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie potrzeby można wyłączyć klucz lub odwołać dostęp. Jednak przed podjęciem próby ochrony jakichkolwiek elementów w magazynie należy włączyć szyfrowanie przy użyciu kluczy. [Więcej informacji można znaleźć tutaj](encryption-at-rest-with-cmk.md).
  - **Szyfrowanie na poziomie** infrastruktury: oprócz szyfrowania danych w magazynie usługi Recovery Services przy użyciu kluczy zarządzanych przez klienta można również skonfigurować dodatkową warstwę szyfrowania w infrastrukturze magazynu. To szyfrowanie infrastruktury jest zarządzane przez platformę. Wraz z szyfrowaniem danych w spoczynku przy użyciu kluczy zarządzanych przez klienta umożliwia ona dwuwarstwowe szyfrowanie danych kopii zapasowej. Szyfrowanie infrastruktury można skonfigurować tylko wtedy, gdy najpierw zdecydujesz się używać własnych kluczy do szyfrowania danych w spoczynku. Szyfrowanie infrastruktury używa kluczy zarządzanych przez platformę do szyfrowania danych.
- **Szyfrowanie specyficzne dla obciążenia, dla których jest dawana kopii zapasowej**  
  - **Kopia zapasowa** maszyny wirtualnej platformy Azure: usługa Azure Backup kopie zapasowe maszyn wirtualnych [](../virtual-machines/disk-encryption.md#customer-managed-keys) z dyskami zaszyfrowanymi przy użyciu kluczy zarządzanych przez platformę, [](../virtual-machines/disk-encryption.md#platform-managed-keys)a także kluczy zarządzanych przez klienta należących do Ciebie i zarządzanych przez Ciebie. Ponadto możesz również utworzyć kopię zapasową maszyn wirtualnych platformy Azure, które mają dyski systemu operacyjnego lub danych zaszyfrowane przy użyciu [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). Program ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows i DM-Crypt dla maszyn wirtualnych z systemem Linux do szyfrowania gościa.

>[!NOTE]
>Szyfrowanie infrastruktury jest obecnie w ograniczonej wersji zapoznawczej i jest dostępne tylko w regionach Wschodnie stany USA, Zachodnie stany USA 2, Południowo-środkowe stany USA, US Gov Arizona i US GOV Wirginia. Jeśli chcesz użyć tej funkcji w dowolnym z tych regionów, wypełnij ten [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) i wyślij wiadomość e-mail na adres [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)
- [Azure Backup często zadawanych](/backup-azure-backup-faq.yml#encryption) pytań dotyczących szyfrowania