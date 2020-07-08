---
title: Szyfrowanie w Azure Backup
description: Dowiedz się więcej na temat sposobu, w jaki funkcje szyfrowania w Azure Backup ułatwiają ochronę danych kopii zapasowych i zaspokajanie potrzeb firmy.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 0a3f4db4d248d2534cfebd617be0f3ccc9647f15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807735"
---
# <a name="encryption-in-azure-backup"></a>Szyfrowanie w Azure Backup

Wszystkie dane kopii zapasowej są automatycznie szyfrowane, gdy są przechowywane w chmurze przy użyciu szyfrowania usługi Azure Storage, co pomaga sprostać zobowiązaniom dotyczącym zabezpieczeń i zgodności. Te dane przechowywane są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.

Oprócz szyfrowania przechowywane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Zapoznaj się z [Azure Backup często zadawanych](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) pytań, aby odpowiedzieć na pytania dotyczące szyfrowania.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Nie musisz podejmować żadnych działań jawnych, aby włączyć to szyfrowanie i ma zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Podczas tworzenia kopii zapasowej Virtual Machines platformy Azure można teraz szyfrować dane przy użyciu kluczy należących do Ciebie i zarządzanych przez użytkownika. Azure Backup umożliwia korzystanie z kluczy RSA przechowywanych w Azure Key Vault na potrzeby szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256, który jest z kolei chroniony przy użyciu kluczy. Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, wymagane jest przyznanie magazynowi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie konieczności można wyłączyć klucz lub odwołać dostęp. Należy jednak włączyć szyfrowanie przy użyciu kluczy przed podjęciem próby włączenia ochrony wszystkich elementów do magazynu.

>[!NOTE]
>Ta funkcja jest obecnie w ograniczonej dostępności. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) i Wyślij wiadomość e-mail na adres, AskAzureBackupTeam@microsoft.com Jeśli chcesz zaszyfrować dane kopii zapasowej przy użyciu kluczy zarządzanych przez klienta. Należy zauważyć, że możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Kopia zapasowa maszyn wirtualnych z dyskami zarządzanymi szyfrowanymi przy użyciu kluczy zarządzanych przez klienta

Azure Backup umożliwia również tworzenie kopii zapasowych maszyn wirtualnych platformy Azure, które używają klucza do [szyfrowania usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Klucz używany do szyfrowania dysków jest przechowywany w Azure Key Vault i zarządzane przez Ciebie. Szyfrowanie usługi Storage (SSE) przy użyciu kluczy zarządzanych przez klienta różni się od Azure Disk Encryption, ponieważ w programie ADE są używane funkcje BitLocker (dla systemu Windows) i DM-Crypt (w systemie Linux) w celu przeprowadzenia szyfrowania w trybie gościnnym, funkcja SSE szyfruje dane w usłudze Storage, umożliwiając korzystanie z dowolnego systemu operacyjnego lub obrazów dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Tworzenie kopii zapasowych maszyn wirtualnych zaszyfrowanych przy użyciu systemu ADE

Za pomocą Azure Backup można także tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które mają zaszyfrowany system operacyjny lub dyski danych przy użyciu Azure Disk Encryption. ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows i programu DM-Crypt for Linux, aby można było przeprowadzić szyfrowanie w gościu. Aby uzyskać szczegółowe informacje, zobacz [wykonywanie kopii zapasowych i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowej i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](backup-azure-vms-encryption.md)
