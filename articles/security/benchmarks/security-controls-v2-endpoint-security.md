---
title: Azure Security test — wersja 2-Endpoint Security
description: Zabezpieczenia usługi Azure Security test w wersji 2 Endpoint Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 48b22ba913370b27cd01319a14a2a627d7589ce4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051518"
---
# <a name="security-control-v2-endpoint-security"></a>Kontrola zabezpieczeń v2: zabezpieczenia punktu końcowego

Zabezpieczenia punktu końcowego obejmują kontrolki wykrywania i reagowania punktów końcowych. Obejmuje to korzystanie z wykrywania i reagowania punktów końcowych (EDR) oraz usługi chroniącej przed złośliwym kodem dla punktów końcowych w środowiskach platformy Azure.

Aby wyświetlić odpowiednie wbudowane Azure Policy, zobacz [szczegóły dotyczące zgodności z przepisami opartymi na testach zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](../../governance/policy/samples/azure-security-benchmark.md#endpoint-security)

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES — 1 | 8.1 | SI-2, SI-3, SC-3 |

Włącz możliwości wykrywania i reagowania punktów końcowych (EDR) dla serwerów i klientów oraz integruj je z procesami operacji SIEM i zabezpieczeń.

Usługa Microsoft Defender for Endpoint zawiera funkcję EDR w ramach platformy zabezpieczeń punktu końcowego przedsiębiorstwa, która pozwala zapobiegać zaawansowanym zagrożeniom, wykrywać je i reagować na nie.

- [Omówienie usługi Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender for Endpoint dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender for Endpoint dla serwerów z systemem innym niż Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

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

Program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services to domyślne oprogramowanie chroniące przed złośliwym oprogramowaniem dla maszyn wirtualnych z systemem Windows. W przypadku maszyn wirtualnych z systemem Linux należy użyć rozwiązania chroniącego przed złośliwym oprogramowaniem innej firmy. Ponadto można użyć wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont usługi Azure Storage. 

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

Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE & Apps", aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft automatycznie zainstaluje najnowsze aktualizacje sygnatur i aparatu. W przypadku systemu Linux upewnij się, że podpisy zostały zaktualizowane w rozwiązaniu chroniącym przed złośliwym kodem innej firmy.

- [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center](../../security-center/security-center-endpoint-protection.md)