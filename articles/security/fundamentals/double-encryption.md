---
title: Podwójne szyfrowanie w Microsoft Azure
description: W tym artykule opisano sposób, w jaki Microsoft Azure zapewnia podwójne szyfrowanie danych magazynowanych i przesyłanych danych.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227322"
---
# <a name="double-encryption"></a>Podwójne szyfrowanie
Podwójne szyfrowanie polega na tym, że co najmniej dwie niezależne warstwy szyfrowania są włączone, aby chronić przed naruszeniem bezpieczeństwa każdej warstwy szyfrowania. Użycie dwóch warstw szyfrowania zmniejsza ryzyko związane z szyfrowaniem danych. Na przykład:

- Błędy konfiguracji w szyfrowaniu danych
- Błędy implementacji w algorytmie szyfrowania
- Złamanie jednego klucza szyfrowania

Platforma Azure zapewnia podwójne szyfrowanie danych przechowywanych i przesyłanych danych.

## <a name="data-at-rest"></a>Dane magazynowane
Podejście firmy Microsoft do włączenia dwóch warstw szyfrowania na potrzeby przechowywania danych jest następujące:

- **Szyfrowanie dysków przy użyciu kluczy zarządzanych przez klienta**. Podajesz własny klucz do szyfrowania dysków. Możesz przenieść własne klucze do Key Vault (BYOK – Bring Your Own Key) lub wygenerować nowe klucze w Azure Key Vault, aby zaszyfrować odpowiednie zasoby.
- **Szyfrowanie infrastruktury przy użyciu kluczy zarządzanych przez platformę**.  Domyślnie dyski są automatycznie szyfrowane w stanie spoczynku przy użyciu kluczy szyfrowania zarządzanych przez platformę.

## <a name="data-in-transit"></a>Dane przesyłane
Podejście firmy Microsoft do włączenia dwóch warstw szyfrowania na potrzeby przesyłania danych to:

- **Szyfrowanie tranzytowe przy użyciu usługi Transport Layer Security (TLS) 1,2 do ochrony danych w przypadku podróży między usługami w chmurze i**. Cały ruch wychodzący z centrum danych jest szyfrowany podczas przesyłania, nawet jeśli miejsce docelowe ruchu jest innym kontrolerem domeny w tym samym regionie. TLS 1,2 to domyślny protokół zabezpieczeń, który jest używany. Protokół TLS zapewnia silne uwierzytelnianie, prywatność komunikatów i integralność (umożliwiając wykrywanie manipulowania komunikatami, przechwycenie i fałszowanie), współdziałanie, elastyczność algorytmu oraz łatwość wdrażania i używania.
- **Dodatkowa warstwa szyfrowania udostępniona w warstwie infrastruktury**. Metoda szyfrowania warstwy łącza danych przy użyciu standardu IEEE 802.1 AE MAC Security standardy (znany również jako MACsec) jest stosowana z punktu do punktu w ramach podstawowego sprzętu sieciowego. Zawsze, gdy ruch klientów platformy Azure odbywa się między centrami danych — poza granicami fizycznymi niekontrolowanymi przez firmę Microsoft (lub w imieniu firmy Microsoft) — pakiety są szyfrowane i odszyfrowywane na urządzeniach przed ich wysłaniem, zapobiegając fizycznym atakom typu "man-in-the-middle" lub "śledzenie/wiretapping". Ponieważ ta technologia jest zintegrowana z samym sprzętem sieciowym, zapewnia szyfrowanie przy użyciu stawki liniowej sprzętu sieciowego bez wymiernego wzrostu opóźnienia linków. To szyfrowanie MACsec jest domyślnie włączone dla całego ruchu platformy Azure podróżującego w obrębie regionu lub między regionami, a żadna akcja nie jest wymagana przez część klientów do włączenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [szyfrowanie jest używane na platformie Azure](encryption-overview.md).
