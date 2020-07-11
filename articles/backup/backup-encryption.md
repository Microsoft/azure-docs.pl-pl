---
title: Szyfrowanie w Azure Backup
description: Dowiedz się więcej na temat sposobu, w jaki funkcje szyfrowania w Azure Backup ułatwiają ochronę danych kopii zapasowych i zaspokajanie potrzeb firmy.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: aafb9868dfb6a63ec9b6a3ae654b88b202a1a145
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171826"
---
# <a name="encryption-in-azure-backup"></a>Szyfrowanie w Azure Backup

Wszystkie dane kopii zapasowej są automatycznie szyfrowane, gdy są przechowywane w chmurze przy użyciu szyfrowania usługi Azure Storage, co pomaga sprostać zobowiązaniom dotyczącym zabezpieczeń i zgodności. Te dane przechowywane są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.

Oprócz szyfrowania przechowywane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Zapoznaj się z [Azure Backup często zadawanych](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) pytań, aby odpowiedzieć na pytania dotyczące szyfrowania.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Nie musisz podejmować żadnych działań jawnych, aby włączyć to szyfrowanie i ma zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Podczas tworzenia kopii zapasowej Virtual Machines platformy Azure można teraz szyfrować dane przy użyciu kluczy należących do Ciebie i zarządzanych przez użytkownika. Azure Backup umożliwia korzystanie z kluczy RSA przechowywanych w Azure Key Vault na potrzeby szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256, który jest z kolei chroniony przy użyciu kluczy. Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, wymagane jest przyznanie magazynowi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie konieczności można wyłączyć klucz lub odwołać dostęp. Należy jednak włączyć szyfrowanie przy użyciu kluczy przed podjęciem próby włączenia ochrony wszystkich elementów do magazynu.

Przeczytaj więcej na temat sposobu szyfrowania danych kopii zapasowej przy [użyciu kluczy zarządzanych](encryption-at-rest-with-cmk.md)przez klienta.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Kopia zapasowa maszyn wirtualnych z dyskami zarządzanymi szyfrowanymi przy użyciu kluczy zarządzanych przez klienta

Azure Backup umożliwia również tworzenie kopii zapasowych maszyn wirtualnych platformy Azure, które używają klucza do [szyfrowania usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Klucz używany do szyfrowania dysków jest przechowywany w Azure Key Vault i zarządzane przez Ciebie. Szyfrowanie usługi Storage (SSE) przy użyciu kluczy zarządzanych przez klienta różni się od Azure Disk Encryption, ponieważ w programie ADE są używane funkcje BitLocker (dla systemu Windows) i DM-Crypt (w systemie Linux) w celu przeprowadzenia szyfrowania w trybie gościnnym, funkcja SSE szyfruje dane w usłudze Storage, umożliwiając korzystanie z dowolnego systemu operacyjnego lub obrazów dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

## <a name="infrastructure-level-encryption-for-backup-data"></a>Szyfrowanie na poziomie infrastruktury dla danych kopii zapasowej

Oprócz szyfrowania danych w magazynie Recovery Services przy użyciu kluczy zarządzanych przez klienta można również wybrać dodatkową warstwę szyfrowania skonfigurowaną w infrastrukturze magazynu. To szyfrowanie infrastruktury jest zarządzane przez platformę i wraz z szyfrowaniem w stanie spoczynku przy użyciu kluczy zarządzanych przez klienta, dzięki czemu możliwe jest szyfrowanie dwóch warstw danych kopii zapasowej. Należy zauważyć, że szyfrowanie infrastruktury można skonfigurować tylko wtedy, gdy użytkownik najpierw zdecyduje się użyć własnych kluczy do szyfrowania w stanie spoczynku. Szyfrowanie infrastruktury używa kluczy zarządzanych przez platformę do szyfrowania danych.

>[!NOTE]
>Szyfrowanie infrastruktury jest obecnie w ograniczonej wersji zapoznawczej i jest dostępne w regionach Wschodnie stany USA, Stany USA West2, Południowo-środkowe stany USA, US Gov Arizona i stany USA GOV Wirginia. Jeśli chcesz użyć funkcji w dowolnym z tych regionów, Wypełnij [ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) i Wyślij wiadomość e-mail na adres [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Tworzenie kopii zapasowych maszyn wirtualnych zaszyfrowanych przy użyciu systemu ADE

Za pomocą Azure Backup można także tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które mają zaszyfrowany system operacyjny lub dyski danych przy użyciu Azure Disk Encryption. ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows i programu DM-Crypt for Linux, aby można było przeprowadzić szyfrowanie w gościu. Aby uzyskać szczegółowe informacje, zobacz [wykonywanie kopii zapasowych i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowej i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](backup-azure-vms-encryption.md)
