---
title: Azure Security test — wersja 2 — kopia zapasowa i odzyskiwanie
description: Usługa Azure Security test — tworzenie kopii zapasowych i odzyskiwanie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ed477c6cf069dbb402e5bda9c1f48f7bc208f18
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698805"
---
# <a name="security-control-v2-backup-and-recovery"></a>Kontrola zabezpieczeń v2: kopia zapasowa i odzyskiwanie

Tworzenie kopii zapasowych i odzyskiwanie obejmuje kontrolki, aby zapewnić, że kopie zapasowe danych i konfiguracji w różnych warstwach usług są wykonywane, weryfikowane i chronione.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Upewnij się, że tworzysz kopię zapasową systemów i danych, aby zachować ciągłość działania po nieoczekiwanym zdarzeniu. Powinno to być zdefiniowane przez dowolne cele dotyczące celu punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO).

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowych (np. maszyny wirtualne platformy Azure, SQL Server, bazy danych HANA lub udziały plików), a także żądaną częstotliwość i okres przechowywania.  

Aby zapewnić wyższy poziom ochrony, można włączyć opcję magazynu geograficznie nadmiarowego w celu replikowania danych kopii zapasowej do regionu pomocniczego i odzyskania przy użyciu funkcji przywracania między regionami.

- [Ciągłość działalności biznesowej i odzyskiwanie po awarii w skali przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Jak włączyć Azure Backup](../../backup/index.yml)

- [Jak włączyć przywracanie między regionami](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zasady i standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.   

W przypadku lokalnych kopii zapasowych przy użyciu Azure Backup szyfrowanie jest dostarczane przy użyciu podanego hasła. W przypadku zwykłych kopii zapasowych usługi Azure dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure. Możesz zaszyfrować kopie zapasowe przy użyciu klucza zarządzanego przez klienta. W takim przypadku upewnij się, że ten klucz zarządzany przez klienta w magazynie kluczy należy również do zakresu kopii zapasowych. 

Użyj kontroli dostępu opartej na rolach platformy Azure w Azure Backup, Azure Key Vault lub innych zasobów, aby chronić kopie zapasowe i klucze zarządzane przez klienta. Dodatkowo można włączyć zaawansowane funkcje zabezpieczeń, aby wymagać uwierzytelniania wieloskładnikowego, zanim będzie można zmienić lub usunąć kopie zapasowe.

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](../../backup/security-overview.md)

- [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta](../../backup/encryption-at-rest-with-cmk.md) 

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe przed atakami](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Okresowe wykonywanie przywracania danych kopii zapasowej. Upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: zmniejszanie ryzyka utraty kluczy

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Upewnij się, że masz środki, aby zapobiec utracie kluczy i je odzyskać. Włącz trwałe usuwanie i przeczyszczanie ochrony w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  

- [Jak włączyć funkcję usuwania nietrwałego i przeczyszczania w programie Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)