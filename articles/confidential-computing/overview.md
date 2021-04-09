---
title: Informacje poufne dotyczące platformy Azure — omówienie
description: Omówienie usług Azure CONFIDENTIAL (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554194"
---
# <a name="confidential-computing-on-azure"></a>Dane poufne na platformie Azure

Dane poufne platformy Azure umożliwiają izolowanie poufnych danych podczas przetwarzania w chmurze. Wiele branż wykorzystuje poufne dane obliczeniowe do ochrony danych przy użyciu funkcji poufnego przetwarzania:

- Bezpieczne dane finansowe
- Ochrona informacji pacjenta
- Uruchamianie procesów uczenia maszynowego na poufnych informacjach
- Wykonaj algorytmy dla zaszyfrowanych zestawów danych z wielu źródeł


## <a name="overview"></a>Omówienie
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Wiemy, że Zabezpieczanie danych w chmurze jest ważne. Znamy Twoje problemy. Oto kilka pytań, które mogą być używane przez naszych klientów podczas przemieszczania poufnych obciążeń do chmury: 

- Jak mogę upewnij się, że firma Microsoft nie może uzyskać dostępu do danych, które nie są szyfrowane?
- Jak mogę Zapobiegaj zagrożeniom bezpieczeństwa z uprzywilejowanych administratorów w mojej firmie?
- Jakie są inne sposoby, aby uniemożliwić innym osobom dostęp do poufnych danych klienta?

Microsoft Azure pomaga zminimalizować obszar narażony na ataki w celu uzyskania silniejszej ochrony danych. Platforma Azure oferuje już wiele narzędzi do ochrony [**danych**](../security/fundamentals/encryption-atrest.md) przechowywanych przez modele, takie jak szyfrowanie po stronie klienta i szyfrowanie po stronie serwera. Ponadto platforma Azure oferuje mechanizmy szyfrowania [**danych**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) przesyłanych za pośrednictwem protokołów Secure Protocol, takich jak TLS i https. Na tej stronie wprowadzono trzeci etap szyfrowania danych — szyfrowanie **danych jest używane**.

## <a name="introduction-to-confidential-computing"></a>Wprowadzenie do danych poufnych 

Dane poufne polegają na tym, że jest to termin branżowy zdefiniowany przez [konsorcjum, którego poufny](https://confidentialcomputing.io/) wpływ (CCC) — podstawą do definiowania i przyspieszania wdrażania poufnych danych. CCC definiuje charakter poufny jako: Ochrona danych używanych przez wykonywanie obliczeń w środowisku zaufanego środowiska wykonawczego (TEE) w oparciu o sprzęt.

TEE to środowisko, które wymusza wykonywanie tylko autoryzowanego kodu. Wszystkie dane w TEE nie mogą zostać odczytane ani naruszone przez żaden kod poza tym środowiskiem. 

### <a name="lessen-the-need-for-trust"></a>Zmniejszanie potrzeb zaufania
Uruchomione obciążenia w chmurze wymagają zaufania. To zaufanie należy nadać różnym dostawcom, włączając różne składniki aplikacji.


**Dostawcy oprogramowania aplikacji**: Ufaj oprogramowaniu, wdrażając Premium, korzystając z funkcji Open Source lub tworząc oprogramowanie w firmie.

**Dostawcy sprzętu**: Ufaj sprzętowi przy użyciu sprzętu lokalnego lub sprzętu wewnętrznego. 

**Dostawcy infrastruktury**: Ufaj dostawcom chmury lub Zarządzaj własnymi lokalnymi centrami danych.


Dane poufne na platformie Azure ułatwiają zaufanie dostawcy usług w chmurze, zmniejszając potrzebę zaufania między różnymi aspektami infrastruktury w chmurze obliczeniowej. Dane poufne platformy Azure minimalizują zaufanie do jądra systemu operacyjnego hosta, funkcji hypervisor, administratora maszyny wirtualnej i administratora hosta.

### <a name="reducing-the-attack-surface"></a>Zmniejszenie podatności na ataki
Trusted Computing Base (TCB) odnosi się do wszystkich składników sprzętowych, oprogramowania układowego i oprogramowania, które zapewniają bezpieczne środowisko. Składniki w ramach TCB są uznawane za "krytyczne". Jeśli jeden ze składników w TCB zostanie naruszony, zabezpieczenia całego systemu mogą być zagrożone. 

Niższa wartość TCB oznacza wyższe zabezpieczenia. Mniejsze ryzyko wystąpienia różnych luk w zabezpieczeniach, złośliwe oprogramowanie, ataki i złośliwe osoby. Dane poufne platformy Azure mają na celu obniżenie poziomu TCB dla obciążeń w chmurze przez oferowanie TEEs. TEEs zmniejszaj TCB do zaufanych plików binarnych, kodu i bibliotek środowiska uruchomieniowego. Gdy korzystasz z infrastruktury i usług platformy Azure do celów poufnych, możesz usunąć wszystkie firmy Microsoft z TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Korzystanie z platformy Azure na potrzeby danych poufnych opartych na chmurze <a id="cc-on-azure"></a>

Dane poufne platformy Azure umożliwiają korzystanie z funkcji danych poufnych w środowisku zwirtualizowanym. Możesz teraz używać narzędzi, oprogramowania i infrastruktury chmurowej do kompilowania na bezpiecznym sprzęcie.  

**Zapobiegaj nieautoryzowanemu dostępowi**: Uruchom poufne dane w chmurze. Dzięki temu platforma Azure zapewnia najlepszą ochronę danych, dzięki czemu nie zmienia się już dziś.

**Zgodność z przepisami**: Migruj do chmury i zapewnia pełną kontrolę nad danymi, aby zaspokoić przepisy rządowe dotyczące ochrony informacji osobistych i bezpiecznego organizacyjnego adresu IP.

**Współpraca bezpiecznych i niezaufanych**: Rozwiązywanie problemów z skalowalnością w całej branży dzięki rozciąganie danych między organizacjami, nawet konkurentami, w celu odblokowania szerokiej analizy danych i dokładniejszego wglądu w dane.

**Izolowane przetwarzanie**: oferuje nową fala produktów, które usuwają zobowiązania dotyczące prywatnych danych z przetwarzaniem niewidomym. Dane użytkownika nie mogą być nawet pobierane przez dostawcę usług. 

## <a name="get-started"></a>Rozpocznij
### <a name="azure-compute"></a>Azure Compute
Twórz aplikacje w oparciu o poufne oferty obliczeniowe IaaS na platformie Azure.
- Virtual Machines (maszyny wirtualne): [Seria DCsv2](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [organizowanie kontenerów poufnych](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Zabezpieczenia platformy Azure 
Upewnij się, że obciążenia są bezpieczne za poorednictwem metod weryfikacji i zarządzania kluczami związanymi ze sprzętem. 
- Zaświadczanie: [Zaświadczanie Microsoft Azure (wersja zapoznawcza)](../attestation/overview.md)
- Zarządzanie kluczami: zarządzane-HSM (wersja zapoznawcza)

### <a name="develop"></a>Opracowywanie
Zacznij korzystać z programowania aplikacji obsługujących enklawy i wdrażaj algorytmy poufne przy użyciu poufnej struktury inferencing.
- Pisanie aplikacji do uruchamiania na maszynach wirtualnych DCsv2: [zestaw Open-enklawy SDK](https://github.com/openenclave/openenclave)
- Poufne modele ML w środowisku uruchomieniowym ONNX: [poufne inferencing (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną DCsv2-Series i zainstaluj na niej zestaw OE.

> [!div class="nextstepaction"]
> [Wdrażanie poufnej maszyny wirtualnej w portalu Azure Marketplace](quick-create-marketplace.md)