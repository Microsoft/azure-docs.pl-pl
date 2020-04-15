---
title: Indeks przykładów strategii
description: Indeks przykładów dotyczących zgodności i przykładów standardowych dotyczących wdrażania środowisk, zasad i podstaw struktury Cloud Adoptions Framework za pomocą usługi Azure Blueprints.
ms.date: 02/26/2020
ms.topic: sample
ms.openlocfilehash: 745b16f8aff8fe003ec65449cdf3dc0d629ba6ff
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383525"
---
# <a name="azure-blueprints-samples"></a>Przykłady usługi Azure Blueprints

Poniższa tabela zawiera linki do przykładów dla usługi Azure Blueprints. Każdy przykład jest jakości produkcyjnej i gotowy do wdrożenia dzisiaj, aby pomóc w spełnieniu różnych wymagań w zakresie zgodności.

## <a name="standards-based-blueprint-samples"></a>Przykłady strategii oparte na standardach

|  |  |
|---------|---------|
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Zapewnia zabezpieczenia zgodności zgodnie z wytycznymi Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Udostępnia zestaw zasad pomagających w zachowaniu zgodności z zaleceniami CIS Microsoft Azure Foundations Benchmark. |
| [DoD Impact Level 4](./dod-impact-level-4/index.md) | Zawiera zestaw zasad, które ułatwiają zapewnienie zgodności ze standardem DoD Impact Level 4. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Zawiera zestaw zasad, które ułatwiają zapewnienie zgodności ze standardem FedRAMP Moderate. |
| [FedRAMP High](./fedramp-h/index.md) | Zawiera zestaw zasad, które ułatwiają zapewnienie zgodności ze standardem FedRAMP High. |
| [HIPAA HITRUST](./HIPAA-HITRUST/index.md) | Zawiera zestaw zasad, które ułatwiają zapewnienie zgodności ze standardem HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Zapewnia zabezpieczenia zgodności z wytycznymi IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Zapewnia zabezpieczenia zgodności z wytycznymi ISO 27001. |
| [Usługi udostępnione ISO 27001](./iso27001-shared/index.md) | Zawiera zestaw wzorców zgodnej infrastruktury i ochronę w postaci zasad, które ułatwiają zaświadczanie za pomocą standardu ISO 27001. |
| [Obciążenie środowiska App Service Environment/bazy danych SQL ISO 27001](./iso27001-ase-sql-workload/index.md) | Zapewnia dodatkową infrastrukturę dla przykładu strategii [usług udostępnionych ISO 27001](./iso27001-shared/index.md). |
| [Media](./media/index.md) | Zawiera zestaw zasad, które ułatwiają zapewnienie zgodności ze standardem Media MPAA. |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Zapewnia zabezpieczenia zgodności z wytycznymi NIST SP 800-53 R4. |
| [PCI-DSS 3.2.1](./pci-dss-3.2.1/index.md) | Udostępnia zestaw zasad, które ułatwiają zachowanie zgodności ze standardem PCI-DSS 3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Pomaga w zachowaniu zgodności ze standardem SWIFT CSP-CSCF v2020. |
| [Ład UK OFFICIAL i UK NHS](./ukofficial/index.md) | Zawiera zestaw wzorców zgodnej infrastruktury i ochronę w postaci zasad, które ułatwiają zaświadczanie na potrzeby standardów UK OFFICIAL i UK NHS. |
| [CAF Foundation](./caf-foundation/index.md) | Zawiera zestaw kontroli ułatwiających zarządzanie zasobami w chmurze zgodnie z przewodnikiem [Microsoft Cloud Adoption Framework dla platformy Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Strefa docelowa migracji w przewodniku CAF](./caf-migrate-landing-zone/index.md) | Zawiera zestaw kontroli ułatwiających konfigurowanie migracji Twojego pierwszego obciążenia i zarządzanie zasobami w chmurze zgodnie z przewodnikiem [Microsoft Cloud Adoption Framework (CAF) dla platformy Azure](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Strategia dotycząca przykładów

:::image type="content" source="../media/blueprint-samples-strategy.png" alt-text="Strategia dotycząca przykładowych strategii" border="false":::

Strategie CAF Foundation i strefy docelowej migracji w przewodniku CAF zakładają, że klient przygotowuje istniejącą czystą pojedynczą subskrypcję do migracji lokalnych zasobów i obciążeń na platformę Azure.
(Region A i B na rysunku).  

Istnieje możliwość przejścia po przykładowych strategiach i wyszukania wzorców dostosowań, które stosuje klient. Istnieje również możliwość proaktywnego rozwiązywania problemów ze strategiami, które są charakterystyczne dla branży, takiej jak usługi finansowe i handel elektroniczny (górny koniec regionu B). Podobnie wyobrażamy sobie tworzenie strategii dla złożonych zagadnień architektonicznych, takich jak wiele subskrypcji, wysoka dostępność, zasoby obejmujące wiele regionów i klienci implementujący kontrole dla istniejących subskrypcji i zasobów (regiony C i D).

Istnieją przykładowe strategie stanowiące rozwiązanie dla scenariusza klienta, w którym wymagania dotyczące zgodności i złożoność architektury są wysokie (region E na rysunku). Region F na rysunku jest regionem, który zostanie obsłużony przez klientów i partnerów wykorzystujących przykładowe strategie i dostosowujących je do swoich niepowtarzalnych potrzeb.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).
