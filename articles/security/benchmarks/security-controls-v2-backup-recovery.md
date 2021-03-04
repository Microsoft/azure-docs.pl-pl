---
title: Azure Security test — wersja 2 — kopia zapasowa i odzyskiwanie
description: Usługa Azure Security test — tworzenie kopii zapasowych i odzyskiwanie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39466ad621eff1a7d3490c936c90fbff6f63e0fc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051552"
---
# <a name="security-control-v2-backup-and-recovery"></a>Kontrola zabezpieczeń v2: kopia zapasowa i odzyskiwanie

Tworzenie kopii zapasowych i odzyskiwanie obejmuje kontrolki, aby zapewnić, że kopie zapasowe danych i konfiguracji w różnych warstwach usług są wykonywane, weryfikowane i chronione.

Aby wyświetlić odpowiednie wbudowane Azure Policy, zobacz [szczegóły dotyczące zgodności z przepisami opartymi na usłudze Azure Security test: Tworzenie kopii zapasowych i odzyskiwanie](../../governance/policy/samples/azure-security-benchmark.md#backup-and-recovery)

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Upewnij się, że tworzysz kopię zapasową systemów i danych, aby zachować ciągłość działania po nieoczekiwanym zdarzeniu. Powinno to być zdefiniowane przez dowolne cele dotyczące celu punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO).

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowych (takie jak maszyny wirtualne platformy Azure, SQL Server, bazy danych HANA lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

W przypadku wyższego poziomu ochrony można włączyć opcję magazynu geograficznie nadmiarowego, aby replikować dane kopii zapasowej do regionu pomocniczego i odzyskać przy użyciu funkcji przywracania między regionami.

- [Ciągłość działalności biznesowej i odzyskiwanie po awarii w skali przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Jak włączyć Azure Backup](../../backup/index.yml)

- [Jak włączyć przywracanie między regionami](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zasady i standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.

W przypadku lokalnych kopii zapasowych przy użyciu Azure Backup szyfrowanie jest dostarczane przy użyciu podanego hasła. W przypadku zwykłych kopii zapasowych usługi Azure dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure. Możesz zaszyfrować kopie zapasowe przy użyciu klucza zarządzanego przez klienta. W takim przypadku upewnij się, że ten klucz zarządzany przez klienta w magazynie kluczy należy również do zakresu kopii zapasowych.

Użyj kontroli dostępu opartej na rolach platformy Azure w Azure Backup, Azure Key Vault lub innych zasobów, aby chronić kopie zapasowe i klucze zarządzane przez klienta. Dodatkowo można włączyć zaawansowane funkcje zabezpieczeń, aby wymagać uwierzytelniania wieloskładnikowego, zanim będzie można zmienić lub usunąć kopie zapasowe.

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](../../backup/security-overview.md)

- [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta](../../backup/encryption-at-rest-with-cmk.md) 

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe przed atakami](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikowanie wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Okresowe wykonywanie przywracania danych kopii zapasowej. Upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmniejszanie ryzyka utraty kluczy

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Upewnij się, że masz miary, aby zapobiec utracie kluczy i je odzyskać. Włącz usuwanie nietrwałe i przeczyść ochronę w usłudze Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)