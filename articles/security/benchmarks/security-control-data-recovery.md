---
title: Kontrola zabezpieczeń platformy Azure — odzyskiwanie danych
description: Odzyskiwanie danych kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698822"
---
# <a name="security-control-data-recovery"></a>Kontrola zabezpieczeń: odzyskiwanie danych

Upewnij się, że regularnie tworzone są kopie zapasowe wszystkich danych, konfiguracji i wpisów tajnych systemu.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,1 | 10.1 | Klient |

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowej (maszyny wirtualne platformy Azure, SQL Server lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

- [Jak włączyć Azure Backup](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,2 | 10,2 | Klient |

Włącz Azure Backup i docelowe maszyny wirtualne, a także żądaną częstotliwość i okres przechowywania. Utwórz kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.

- [Jak włączyć Azure Backup](../../backup/index.yml)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,3 | 10,3 | Klient |

Pamiętaj, aby okresowo wykonywać przywracanie danych w Azure Backup. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze magazynu kluczy na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 9,4 | 10,4 | Klient |

W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie danych w spoczynku odbywa się przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE). Za pomocą kontroli dostępu opartej na rolach platformy Azure można chronić kopie zapasowe i klucze zarządzane przez klienta.  

Włącz ochronę Soft-Delete i przeczyszczania w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, należy włączyć funkcję usuwania nietrwałego w celu zapisywania i odzyskiwania danych po usunięciu obiektów blob lub migawek obiektów BLOB. 

- [Opis kontroli RBAC platformy Azure](../../role-based-access-control/overview.md)

- [Jak włączyć ochronę Soft-Delete i przeczyszczania w programie Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Soft delete for Azure Storage blobs](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń:  [odpowiedź na zdarzenie](security-control-incident-response.md)