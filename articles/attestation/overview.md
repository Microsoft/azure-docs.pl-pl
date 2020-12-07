---
title: Omówienie zaświadczania platformy Azure
description: Omówienie zaświadczania o Microsoft Azure, rozwiązanie do zaświadczania zaufanych środowisk wykonywania (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 6a587ecbe7ff67908b22d4f2429cfdd0c511e07d
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748777"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (wersja zapoznawcza)

Zaświadczanie Microsoft Azure (wersja zapoznawcza) to ujednolicone rozwiązanie służące do zdalnego weryfikowania wiarygodności platformy i integralności danych binarnych działających w tym środowisku. Usługa obsługuje zaświadczanie platform objętych usługą Trusted platform modules (moduły TPM) wraz z możliwością zaświadczania stanu zaufanych środowisk wykonywania (TEEs), takich jak [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) Enclaves i [zabezpieczenia oparte na wirtualizacji](/windows-hardware/design/device-experiences/oem-vbs) (vbs) enclaves. 

Zaświadczanie to proces demonstrujący, że dane binarne oprogramowania zostały prawidłowo utworzone na zaufanej platformie. Zdalne jednostki uzależnione mogą wówczas mieć pewność, że tylko takie planowane oprogramowanie jest uruchomione na zaufanym sprzęcie. Zaświadczanie platformy Azure to ujednolicona usługa i struktura dla zaświadczania.

Zaświadczanie na platformie Azure umożliwia korzystanie z odcięć takich odmian, jak dane [poufne platformy Azure](../confidential-computing/overview.md) i inteligentnej ochrony brzegowej. Klienci żądali niezależnej weryfikacji lokalizacji maszyny, stan maszyny wirtualnej na tym komputerze i środowiska, w którym enclaves są uruchomione na tej maszynie wirtualnej. Zaświadczanie platformy Azure pozwoli na te i wiele dodatkowych żądań klientów.

Zaświadczanie platformy Azure odbiera dowody od jednostek obliczeniowych, włącza je do zestawu oświadczeń, weryfikuje je pod kątem konfigurowalnych zasad i tworzy kryptograficzne sprawdzanie dla aplikacji opartych na oświadczeniach (na przykład jednostki uzależnione i urzędy inspekcji).

## <a name="use-cases"></a>Przypadki zastosowań

Zaświadczanie platformy Azure zapewnia kompleksowe usługi zaświadczania dla wielu środowisk i charakterystycznych przypadków użycia.

### <a name="sgx-attestation"></a>Zaświadczanie SGX

SGX odnosi się do izolacji klasy sprzętowej, która jest obsługiwana w niektórych modelach procesorów Intel. SGX umożliwia uruchamianie kodu w przypadku oczyszczonych przedziałów znanych jako SGX enclaves. Uprawnienia dostępu i pamięci są następnie zarządzane przez sprzęt, aby zapewnić minimalną podatność na ataki z właściwą izolacją.

Aplikacje klienckie mogą być zaprojektowane tak, aby korzystały z SGX enclaves przez delegowanie zadań związanych z zabezpieczeniami, które są wykonywane w ramach tych enclaves. Takie aplikacje mogą następnie używać zaświadczania platformy Azure do rutynowego ustanawiania zaufania w enklawy i możliwości uzyskiwania dostępu do poufnych danych.

### <a name="open-enclave"></a>Otwórz enklawy
[Open enklawy](https://openenclave.io/sdk/) (OE) to zbiór bibliotek przeznaczonych do tworzenia jednego ujednoliconego abstrakcji enclaving dla deweloperów w celu tworzenia aplikacji opartych na tee. Oferuje on uniwersalny, bezpieczny model aplikacji, który minimalizuje specyfikę platformy. Firma Microsoft przegląda ją jako istotny dla siebie democratizing technologie enklawy, takie jak SGX i zwiększając ich pobór na platformie Azure.

W przypadku programu OE są spełnione określone wymagania dotyczące weryfikacji dowodów enklawy. Dzięki temu klient usługi zaświadczania o wysokim stopniu dopasowania jest uważany za usługę zaświadczania o systemie Azure.

## <a name="azure-attestation-can-run-in-a-tee"></a>Zaświadczanie platformy Azure może być uruchamiane w TEE

Zaświadczanie platformy Azure ma kluczowe znaczenie dla poufnych scenariuszy obliczeniowych, ponieważ wykonuje następujące czynności:

- Sprawdza, czy dowody enklawy są prawidłowe.
- Szacuje dowody enklawy względem zasad zdefiniowanych przez klienta.
- Zarządza zasadami specyficznymi dla dzierżaw i zapisuje je.
- Generuje i podpisuje token, który jest używany przez jednostki uzależnione do współdziałania z enklawy.

Zaświadczanie platformy Azure jest kompilowane do uruchamiania w dwóch typach środowisk:
- Zaświadczanie platformy Azure działające w SGX TEE.
- Zaświadczanie platformy Azure działające w TEE.

Klienci zaświadczania platformy Azure wyrażali wymóg, aby firma Microsoft mogła działać z zaufaną bazą obliczeniową (TCB). Ma to na celu uniemożliwienie podmiotom firmy Microsoft, takim jak Administratorzy maszyny wirtualnej, Administratorzy hostów i deweloperzy firmy Microsoft modyfikowanie żądań zaświadczania, zasad i tokenów wystawionych przez usługę Azure. Zaświadczanie platformy Azure jest również stworzone do uruchamiania w TEE, w którym funkcje zaświadczania o systemie Azure, takie jak weryfikacja ofert, generowanie tokenów i podpisywanie tokenów, są przenoszone do SGX enklawy.

## <a name="why-use-azure-attestation"></a>Dlaczego warto używać zaświadczania platformy Azure

Zaświadczanie o platformie Azure jest preferowanym wyborem dla zaświadczania TEEs, ponieważ oferuje następujące korzyści: 

- Ujednolicone środowisko zaświadczania wielu środowisk, takich jak moduły TPM, SGX enclaves i VBS enclaves 
- Usługa obejmująca wiele dzierżawców, która umożliwia konfigurowanie niestandardowych dostawców zaświadczania i zasad w celu ograniczenia generowania tokenów
- Oferuje dostawców domyślnych, którzy mogą zaświadczać o braku konfiguracji od użytkowników
- Chroni swoje dane, gdy jest używany z implementacją w SGX enklawy
- Usługa o wysokiej dostępności 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Obsługa ciągłości działania i odzyskiwania po awarii (BCDR)

[Ciągłość działania i odzyskiwanie po awarii](../best-practices-availability-paired-regions.md) (BCDR) na potrzeby zaświadczania platformy Azure umożliwia zapobieganie zakłóceniom działania usług wynikających z znaczących problemów dotyczących dostępności lub zdarzeń awarii w regionie.

Klastry wdrożone w dwóch regionach będą działać niezależnie w normalnych warunkach. W przypadku awarii lub awarii jednego regionu należy wykonać następujące czynności:

- BCDR zaświadczania o platformie Azure zapewnia bezproblemową pracę w trybie failover, w której klienci nie muszą podejmować dodatkowych kroków w celu odzyskania
- [Traffic Manager platformy Azure](../traffic-manager/index.yml) dla regionu wykryje pogorszenie sondy kondycji i przełączenie punktu końcowego na sparowany region
- Istniejące połączenia nie będą działały i otrzymają wewnętrzny błąd serwera lub problemy z przekroczeniem limitu czasu
- Wszystkie operacje płaszczyzny kontroli zostaną zablokowane. Klienci nie będą mogli tworzyć dostawców zaświadczania i aktualizować zasad w regionie podstawowym
- Wszystkie operacje płaszczyzny danych, w tym zaświadczanie wywołań, będą nadal działały w regionie podstawowym

## <a name="next-steps"></a>Następne kroki
- Informacje o [podstawowych pojęciach dotyczących zaświadczania platformy Azure](basic-concepts.md)
- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
