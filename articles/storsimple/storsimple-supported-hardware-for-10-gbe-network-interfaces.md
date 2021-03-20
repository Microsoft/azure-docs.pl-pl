---
title: Sprzęt dla interfejsów StorSimple 10 GbE | Microsoft Docs
description: W tym artykule opisano obsługiwane urządzenia nadawczo-Factor (SFP), kable i przełączniki dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954090"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Sprzęt obsługiwany w przypadku interfejsów sieciowych 10 GbE na urządzeniu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera informacje o dodatkowym sprzęcie, który współpracuje z urządzeniem Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista urządzeń przetestowanych przez firmę Microsoft
Firma Microsoft przetestowała następujące małe urządzenia nadawczo-Factor (SFP), kable i przełączniki, aby zapewnić optymalne działanie z urządzeniami. (Poniższe tabele zostaną zaktualizowane w miarę testowania nowego sprzętu).

### <a name="sfp-transceivers"></a>SFP + urządzenia nadawcze
| Marka | Model |
| --- | --- |
| Cisco |SFP-10G — SR |

### <a name="cables"></a>Kable
| S. Nie. | Marka | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Przełączniki
| S. Nie. | Marka | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista urządzeń przetestowanych w polu
Ta sekcja zawiera listę urządzeń, które zostały pomyślnie wdrożone w polu przez klientów StorSimple. Nie zostały one przetestowane przez firmę Microsoft, ale mogą pracować z urządzeniem StorSimple.

| Parametr | Wartość |
| --- | --- |
| Przełącz Marka |Juniper |
| Zmień model |ex4550-32F |
| Przełącz wersję systemu operacyjnego |JunOS 12.3 R 9.4 |
| Model blokowy |Porty Onboard (PIC 0) |
| Marka urządzenia nadawczego |Juniper |
| Model urządzenia nadawczego |Numer części 740-021308 <br></br> Numer części 740-030658 |
| Wersja oprogramowania układowego urządzenia nadawczego |Rev 01, wersja 0,0 (zgłoszona) |
| Model kabla |Zworka dwustronna LC/LC 50/125μ, OM3, LSZH |
| Model StorSimple |8600 |
| Wersja oprogramowania StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista urządzeń przetestowanych przez dostawcę OEM (Mellanox)
Mellanox przetestował następujące małe urządzenia nadawczo-Factor (SFP), kable i przełączniki, aby zapewnić optymalne działanie przy użyciu interfejsów sieci Mellanox, takich jak interfejsy sieciowe 10 GbE na urządzeniu StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kable i moduły obsługiwane przez Mellanox
W poniższej tabeli wymieniono kable i moduły obsługiwane przez program Mellanox. Nie zostały one przetestowane przez firmę Microsoft, ale mogą pracować z urządzeniem StorSimple.

| S. Nie. | Szybkość | Model | Opis | Marka |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |pasywny kabel miedzy SFP + 10 GB/s 1M |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2 MLN |pasywny kabel miedzy SFP + 10 GB/s 2 mln |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |pasywny kabel miedzy SFP + 10 GB/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5 M |pasywny kabel miedzy SFP + 10 GB/s 5 m |Arista |
| 5. |10 GbE |Cisco SFP — H10GBCU1M |Kabel Cisco SFP + |Cisco |
| 6. |10 GbE |Cisco SFP — H10GBCU3M |Kabel Cisco SFP + |Cisco |
| 7. |10 GbE |Cisco SFP — H10GBCU5M |Kabel Cisco SFP + |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + do SFP + 1M Direct dołączenie kabla miedzianego |FIRM |
| 9. |10 GbE |455883 — B21 HP BLc |10Gb SR SFP + opt |FIRM |
| 10. |10 GbE |455886 — B21 HP BLc |10Gb LR SFP + opt |FIRM |
| 11. |10 GbE |487649 — B21 HP BLc |Kabel SFP + 0,5 m 10GbE miedź |FIRM |
| 12. |10 GbE |487652 — B21 HP BLc |Kabel SFP + 1M 10GbE miedź |FIRM |
| trzynast. |10 GbE |487655 — B21 HP BLc |Kabel SFP + 3m 10 miedzy |FIRM |
| 14,5. |10 GbE |487658 — B21 HP BLc |Kabel SFP + 7 m 10 miedzy |FIRM |
| 15000. |10 GbE |537963 — B21 HP BLc |Kabel SFP + 5 m 10 miedzy |FIRM |
| 16. |10 GbE |AP784A HP |3m C-seria pasywna miedź SFP + |FIRM |
| 7. |10 GbE |AP785A HP |5 m C-seria pasywna miedź SFP + |FIRM |
| postanowienia. |10 GbE |AP818A HP |1M B-seria aktywne miedza SFP + |FIRM |
| 19. |10 GbE |AP819A HP |3m B — aktywne łącze miedziane SFP + |FIRM |
| 20C. |10 GbE |J9150A HP |X132 10G SFP + LC SR-odbiornik |FIRM |
| 43. |10 GbE |J9151A HP |X132 10G SFP + LC LR urządzenia nadawczego |FIRM |
| 22. |10 GbE |J9283B HP |Kabel DAC X242 10G SFP + SFP + 3m |FIRM |
| 233. |10 GbE |J9285B HP |Kabel DAC X242 10G SFP + SFP + 7 m |FIRM |
| codzienne. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 m |FIRM |
| 6,25. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1.2 m kabel DAC |FIRM |
| 25. |10 GbE |JD097B HP |Kabel X240 10G SFP + SFP + 3m tata |FIRM |
| 27. |10 GbE |MAM1Q00A — QSA Mellanox |QSFP do SFP + adapter |Technologie Mellanox |
| 28. |10 GbE |MC2309124-006 Mt |Pasywny kabel miedziany 1x SFP + do QSFP 10Gb/s 24AWG 7 m |Technologie Mellanox |
| dnia. |10 GbE |MC2309124 — 007 Mt |Pasywny kabel miedziany 1x SFP + do QSFP 10Gb/s 24AWG 7 m |Technologie Mellanox |
| 0,30. |10 GbE |MC2309130 — 003 Mt |Pasywny kabel miedziany 1x SFP + do QSFP 10Gb/s 30awg 3m |Technologie Mellanox |
| niż. |10 GbE |MC2309130 — 00A Mt |Pasywny kabel miedziany 1x SFP + do QSFP 10Gb/s 30awg 0,5 m |Technologie Mellanox |
| 32. |10 GbE |MC3309124 — 005 Mt |Pasywny kabel miedziany 1x SFP + 10Gb/s 24AWG 5 m |Technologie Mellanox |
| 33. |10 GbE |MC3309124 — 007 Mt |Pasywny kabel miedziany 1x SFP + 10Gb/s 24AWG 7 m |Technologie Mellanox |
| 34. |10 GbE |MC3309130 — 003 Mt |Pasywny kabel miedziany 1x SFP + 10Gb/s 30awg 3m |Technologie Mellanox |
| 35. |10 GbE |MC3309130 — 00A Mt |Pasywny kabel miedziany 1x SFP + 10Gb/s 30awg 0,5 m |Technologie Mellanox |

### <a name="switches-supported-by-mellanox"></a>Przełączniki obsługiwane przez Mellanox
Poniższa tabela zawiera listę przełączników obsługiwanych przez Mellanox. Nie zostały one przetestowane przez firmę Microsoft, ale mogą pracować z urządzeniem StorSimple.

| S. Nie. | Szybkość | Model | Opis | Marka |
| --- | --- | --- | --- | --- |
| 1. |10 |516733 – B21 |Przełącznik bloku HP pro6120XG 10GbE Ethernet |FIRM |
| 2. |10 |538113 – B21 |Moduł Pass-Through firmy HP 10GbE (PTM) |FIRM |
| 3. |10 |EN4093 |Moduł przełączania skalowalnego w programie IBM PureFlex System Fabric EN4093 10 Gigabit |IBM |
| 4. |1GbE |3020 |Blok Cisco Catalyst 3020 1GbE Switch |Cisco |
| 5. |1GbE |3020X |Blok przełącznika Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1GbE |438030 – B21 |HP 1GbE Switch module — przełącznik bloku GbE2c warstwy 2/3 Ethernet |FIRM |
| 7. |1GbE |6120G |Blok HP pro6120G/XG 1GbE Switch |FIRM |

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o składnikach i stanie sprzętu StorSimple](./storsimple-8000-monitor-hardware-status.md).