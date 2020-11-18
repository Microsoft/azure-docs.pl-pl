---
title: Azure Security test — wersja 2 — Ochrona danych
description: Azure Security Test — Data Protection w wersji 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8e12a74ee689ba7b013b1c4a6881deac78680be0
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701052"
---
# <a name="security-control-v2-data-protection"></a>Kontrola zabezpieczeń v2: Ochrona danych

Ochrona danych obejmuje kontrolę nad ochroną danych w czasie spoczynku, w tranzycie oraz za pomocą autoryzowanych mechanizmów dostępu. Obejmuje to odnajdywanie, klasyfikowanie, ochronę i monitorowanie poufnych zasobów danych przy użyciu funkcji kontroli dostępu, szyfrowania i rejestrowania na platformie Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC — 28 |

Odkryj, klasyfikuj i Oznacz poufne dane, aby umożliwić projektowanie odpowiednich kontrolek w celu zapewnienia, że poufne informacje są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne organizacji. 

Użyj Azure Information Protection (i skojarzonego z nim narzędzia do skanowania), aby uzyskać informacje poufne w dokumentach pakietu Office na platformie Azure, lokalnie, w pakiecie Office 365 i w innych lokalizacjach. 

Możesz użyć usługi Azure SQL Information Protection, aby pomóc w klasyfikacji i etykietowania informacji przechowywanych w bazach danych Azure SQL.

- [Otaguj informacje poufne przy użyciu Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Jak wdrożyć usługę Azure SQL Data Discovery](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Ochrona poufnych danych przez ograniczenie dostępu przy użyciu kontroli dostępu opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w bazach danych SQL i innych). 

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa należy również uzyskać informacje o lokalizacji poufnych lub krytycznych danych i systemów.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft zaimplementował pewne domyślne funkcje i możliwości ochrony danych.

- [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Zrozumienie ochrony danych klientów na platformie Azure](../fundamentals/protection-customer-data.md)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitoruj nieautoryzowane transfery danych poufnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitoruj nieautoryzowany transfer danych do lokalizacji poza widocznością i kontrolą przedsiębiorstwa. Zwykle obejmuje to monitorowanie nietypowych działań (dużych lub nietypowych transferów), które mogą wskazywać nieautoryzowane eksfiltracji danych. 

Usługa Azure Storage Advanced Threat Protection (ATP) i usługa Azure SQL ATP mogą otrzymywać alerty dotyczące nietypowego transferu informacji, które mogą wskazywać nieautoryzowane transfery informacji poufnych. 

Usługa Azure Information Protection (w systemie) oferuje funkcje monitorowania dla informacji, które zostały sklasyfikowane i oznaczone etykietami. 

Jeśli jest to wymagane do zapewnienia zgodności ochrony przed utratą danych (DLP), można użyć rozwiązania DLP opartego na hoście, aby wymusić środków wykrywających i/lub kontrolę prewencyjną, aby zapobiec eksfiltracji danych.

- [Włączanie usługi Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Włączanie usługi Azure Storage ATP](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfruj poufne informacje podczas przesyłania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Aby dopełnić kontrolę dostępu, przesyłane dane powinny być chronione przed atakami typu "poza pasmem" (np. przechwytywaniem ruchu) przy użyciu szyfrowania, aby zapewnić, że osoby atakujące nie mogą łatwo odczytać ani zmodyfikować danych. 

Chociaż jest to opcjonalne dla ruchu w sieciach prywatnych, ma to kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych. W przypadku ruchu HTTP upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej. W celu zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows), a nie z nieszyfrowanym protokołem. Przestarzałe protokoły SSL, TLS i SSH oraz słabe szyfry powinny być wyłączone.  

Domyślnie platforma Azure zapewnia szyfrowanie danych przesyłanych między centrami danych platformy Azure. 

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem)

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../fundamentals/double-encryption.md#data-in-transit)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Szyfruj poufne dane w spoczynku

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Aby uzupełnienie kontroli dostępu, dane przechowywane w spoczynku powinny być chronione przed atakami typu "poza pasmem" (takimi jak dostęp do magazynu bazowego) przy użyciu szyfrowania. Pozwala to zagwarantować, że osoby atakujące nie mogą łatwo odczytywać ani modyfikować danych. 

Platforma Azure zapewnia szyfrowanie danych przechowywanych domyślnie. W przypadku wysoce poufnych danych masz możliwość zaimplementowania dodatkowego szyfrowania dla wszystkich zasobów platformy Azure, jeśli jest to możliwe. Platforma Azure domyślnie zarządza kluczami szyfrowania, ale platforma Azure udostępnia opcje zarządzania własnymi kluczami (kluczami zarządzanymi przez klienta) dla niektórych usług platformy Azure.

- [Informacje o szyfrowaniu na platformie Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Jak skonfigurować klucze szyfrowania zarządzane przez klienta](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Model szyfrowania i tabela zarządzania kluczami](../fundamentals/encryption-models.md)

- [Szyfrowanie danych przy podwójnej obspoczynku na platformie Azure](../fundamentals/double-encryption.md#data-at-rest)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)