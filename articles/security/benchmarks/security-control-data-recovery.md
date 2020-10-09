---
title: Kontrola zabezpieczeń platformy Azure — odzyskiwanie danych
description: Odzyskiwanie danych kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408587"
---
# <a name="security-control-data-recovery"></a>Kontrola zabezpieczeń: odzyskiwanie danych

Upewnij się, że regularnie tworzone są kopie zapasowe wszystkich danych, konfiguracji i wpisów tajnych systemu.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,1 | 10.1 | Klient |

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowej (maszyny wirtualne platformy Azure, SQL Server lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

- [Jak włączyć Azure Backup](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,2 | 10,2 | Klient |

Włącz Azure Backup i docelowe maszyny wirtualne, a także żądaną częstotliwość i okres przechowywania. Utwórz kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.

- [Jak włączyć Azure Backup](https://docs.microsoft.com/azure/backup/)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,3 | 10,3 | Klient |

Pamiętaj, aby okresowo wykonywać przywracanie danych w Azure Backup. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,4 | 10,4 | Klient |

W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie danych w spoczynku odbywa się przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE). Za pomocą kontroli dostępu opartej na rolach można chronić kopie zapasowe i klucze zarządzane przez klienta.  

Włącz ochronę Soft-Delete i przeczyszczania w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, należy włączyć funkcję usuwania nietrwałego w celu zapisywania i odzyskiwania danych po usunięciu obiektów blob lub migawek obiektów BLOB. 

- [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Jak włączyć ochronę Soft-Delete i przeczyszczania w programie Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Soft delete for Azure Storage blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń:  [odpowiedź na zdarzenie](security-control-incident-response.md)