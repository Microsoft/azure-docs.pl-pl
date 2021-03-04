---
title: Azure Security test — wersja 2 — Ochrona danych
description: Azure Security Test — Data Protection w wersji 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037119"
---
# <a name="security-control-v2-data-protection"></a>Kontrola zabezpieczeń v2: Ochrona danych

Ochrona danych obejmuje kontrolę nad ochroną danych w czasie spoczynku, w tranzycie oraz za pomocą autoryzowanych mechanizmów dostępu. Obejmuje to odnajdywanie, klasyfikowanie, ochronę i monitorowanie poufnych zasobów danych przy użyciu funkcji kontroli dostępu, szyfrowania i rejestrowania na platformie Azure.

Aby wyświetlić odpowiednie Azure Policy, zobacz [szczegóły wbudowanej inicjatywy zgodność z przepisami usługi Azure Security Tests: Ochrona danych](../../governance/policy/samples/azure-security-benchmark.md#data-protection)

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC — 28 |

Odkryj, klasyfikuj i Oznacz poufne dane, aby umożliwić projektowanie odpowiednich kontrolek w celu zapewnienia, że poufne informacje są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne organizacji.

Użyj usługi Azure Information Protection (i skojarzonego z nią narzędzia do skanowania), aby zlokalizować informacje poufne w dokumentach pakietu Office na platformie Azure, lokalnie, w pakiecie Office 365 i w innych lokalizacjach.

Usługa Azure SQL Information Protection może pomóc w klasyfikacji i etykietowaniu informacji przechowywanych w bazach danych Azure SQL Database.

- [Tagowanie informacji poufnych przy użyciu usługi Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Jak wdrożyć usługę Azure SQL Data Discovery](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: Współużytkowane

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Ochrona poufnych danych przez ograniczenie dostępu przy użyciu kontroli dostępu opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w bazach danych SQL i innych). 

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być dostosowane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa powinna być również oparta na lokalizacji poufnych lub krytycznych danych i systemów.

W odniesieniu do platformy podstawowej zarządzanej przez firmę Microsoft, firma Microsoft traktuje całą zawartość kliencką jako poufną i zapewnia ochronę przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft zaimplementowała pewne domyślne mechanizmy kontroli i możliwości ochrony danych.

- [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorowanie nieautoryzowanego transferu danych poufnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitoruj nieautoryzowany transfer danych do lokalizacji poza widocznością i kontrolą przedsiębiorstwa. Zazwyczaj obejmuje to monitorowanie pod kątem nietypowych działań (dużych lub nietypowych transferów), które mogą wskazywać na nieautoryzowaną eksfiltrację danych. 

Usługa Azure Storage Advanced Threat Protection (ATP) i usługa Azure SQL ATP mogą generować alerty dotyczące nietypowego transferu informacji, które mogą wskazywać na nieautoryzowane transfery informacji poufnych. 

Usługa Azure Information Protection (AIP) oferuje funkcje monitorowania informacji, które zostały sklasyfikowane i oznaczone etykietami. 

Jeśli jest to wymagane do zapewnienia zgodności pod kątem ochrony przed utratą danych (DLP), można użyć rozwiązania DLP opartego na hoście, aby wymusić wykrywające i/lub prewencyjne mechanizmy kontrolne w celu zapobiegania eksfiltracji danych.

- [Azure Defender for SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [Usługa Azure Defender dla usługi Storage](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Odpowiedzialność**: Współużytkowane

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Aby dopełnić kontrolę dostępu, przesyłane dane powinny być chronione przed atakami typu "poza pasmem" (na przykład przechwytywaniem ruchu) przy użyciu szyfrowania, aby upewnić się, że osoby atakujące nie mogą łatwo odczytać lub zmodyfikować danych.

Chociaż jest to opcjonalne dla ruchu w sieciach prywatnych, ma to kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych. W przypadku ruchu HTTP upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej. W celu zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows), a nie z nieszyfrowanym protokołem. Przestarzałe protokoły SSL, TLS i SSH oraz słabe szyfry powinny być wyłączone.

Domyślnie platforma Azure zapewnia szyfrowanie danych przesyłanych między centrami danych platformy Azure.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem)

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../fundamentals/double-encryption.md#data-in-transit)

**Odpowiedzialność**: Współużytkowane

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Szyfrowanie poufnych danych nieużywanych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Aby uzupełnienie kontroli dostępu, dane przechowywane w spoczynku powinny być chronione przed atakami typu "poza pasmem" (takimi jak dostęp do magazynu bazowego) przy użyciu szyfrowania. Pozwala to zagwarantować, że osoby atakujące nie mogą łatwo odczytywać ani modyfikować danych. 

Platforma Azure zapewnia szyfrowanie danych przechowywanych domyślnie. W przypadku wysoce poufnych danych masz możliwość zaimplementowania dodatkowego szyfrowania dla wszystkich zasobów platformy Azure, jeśli jest to możliwe. Platforma Azure domyślnie zarządza kluczami szyfrowania, ale platforma Azure udostępnia opcje zarządzania własnymi kluczami (kluczami zarządzanymi przez klienta) dla niektórych usług platformy Azure.

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Jak skonfigurować klucze szyfrowania zarządzane przez klienta](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Model szyfrowania i tabela zarządzania kluczami](../fundamentals/encryption-models.md)

- [Szyfrowanie danych przy podwójnej obspoczynku na platformie Azure](../fundamentals/double-encryption.md#data-at-rest)

**Odpowiedzialność**: Współużytkowane

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)