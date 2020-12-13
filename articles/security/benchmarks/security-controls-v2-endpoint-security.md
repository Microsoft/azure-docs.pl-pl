---
title: Azure Security test — wersja 2-Endpoint Security
description: Zabezpieczenia usługi Azure Security test w wersji 2 Endpoint Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 937d0b379c4f669e5b57e5053d5e3bffeb091e78
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368957"
---
# <a name="security-control-v2-endpoint-security"></a>Kontrola zabezpieczeń v2: zabezpieczenia punktu końcowego

Zabezpieczenia punktu końcowego obejmują kontrolki wykrywania i reagowania punktów końcowych. Obejmuje to korzystanie z wykrywania i reagowania punktów końcowych (EDR) oraz usługi chroniącej przed złośliwym kodem dla punktów końcowych w środowiskach platformy Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES — 1 | 8.1 | SI-2, SI-3, SC-3 |

Włącz możliwości wykrywania i reagowania punktów końcowych (EDR) dla serwerów i klientów oraz integruj je z procesami operacji SIEM i zabezpieczeń.

Funkcja zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender oferuje EDRą funkcję w ramach platformy zabezpieczeń punktu końcowego przedsiębiorstwa, która pozwala zapobiegać zaawansowanym zagrożeniom, wykrywać je i reagować na nie. 

- [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Usługa Microsoft Defender ATP dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Usługa Microsoft Defender ATP dla serwerów spoza systemu Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Użyj centralnie zarządzanych nowoczesnych programów chroniących przed złośliwym oprogramowaniem

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Użyj centralnie zarządzanego rozwiązania do ochrony przed złośliwym kodem punktu końcowego, które może być w trakcie rzeczywistym i okresowego skanowania

Azure Security Center może automatycznie identyfikować użycie wielu popularnych rozwiązań chroniących przed złośliwym oprogramowaniem dla maszyn wirtualnych i zgłaszać stan działania programu Endpoint Protection i podejmować zalecenia. 

Program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services to domyślne oprogramowanie chroniące przed złośliwym oprogramowaniem dla maszyn wirtualnych z systemem Windows. W przypadku maszyn wirtualnych z systemem Linux należy użyć rozwiązania chroniącego przed złośliwym oprogramowaniem innej firmy.  Ponadto można użyć wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont usługi Azure Storage. 

- [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](../fundamentals/antimalware.md)

- [Obsługiwane rozwiązania programu Endpoint Protection](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES — 3 | 8.2 | SI-2, SI-3 |

Upewnij się, że podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane szybko i spójnie. 

Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft automatycznie zainstaluje najnowsze aktualizacje sygnatur i aparatu. W przypadku systemu Linux należy użyć rozwiązania chroniącego przed złośliwym kodem innej firmy.

- [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center](../../security-center/security-center-endpoint-protection.md)