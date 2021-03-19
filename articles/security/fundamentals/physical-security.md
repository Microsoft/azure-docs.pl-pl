---
title: Fizyczne zabezpieczenia centrów danych platformy Azure — Microsoft Azure | Microsoft Docs
description: W tym artykule opisano, co firma Microsoft zabezpiecza centra danych platformy Azure, w tym infrastrukturę fizyczną, zabezpieczenia i oferty zgodności.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87076330"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne
W tym artykule opisano, co firma Microsoft zabezpiecza infrastruktury platformy Azure.

## <a name="datacenter-infrastructure"></a>Infrastruktura centrum danych
Platforma Azure składa się z [globalnej rozproszonej infrastruktury centrów](https://azure.microsoft.com/global-infrastructure/)danych, wspierając tysiące usługi online i obejmujących ponad 100 wysoce zabezpieczonych udogodnień na całym świecie.

Infrastruktura została zaprojektowana w celu zapewnienia bliższych aplikacji użytkownikom na całym świecie, zachowywania miejsca zamieszkania i zapewniania kompleksowych opcji dotyczących zgodności i odporności klientów. Platforma Azure ma 58 regionów na całym świecie i jest dostępna w 140 krajach/regionach.

Region to zestaw centrów danych, które są połączone za pośrednictwem dużej i odpornej sieci. Sieć obejmuje dystrybucję zawartości, równoważenie obciążenia, nadmiarowość i [szyfrowanie warstwy łącza danych](encryption-overview.md#encryption-of-data-in-transit) domyślnie dla całego ruchu platformy Azure w obrębie regionu lub poruszania się między regionami. W przypadku więcej regionów globalnych niż każdy inny dostawca usług w chmurze platforma Azure zapewnia elastyczność wdrażania aplikacji, w których są one potrzebne.

Regiony platformy Azure są zorganizowane w lokalizacje geograficzne. Lokalizacja geograficzna platformy Azure zapewnia, że wymagania dotyczące miejsca zamieszkania, suwerenności, zgodności i odporności są honorowane w granicach geograficznych.

Obszary geograficzne umożliwiają klientom, którzy mają określone potrzeby związane z rezydencją danych i zgodnością, przechowywanie ich danych i aplikacji w bliskim sąsiedztwie. Lokalizacje geograficzne są odporne na uszkodzenia, aby przetrzymywać pełną awarię regionu, poprzez połączenie z dedykowaną infrastrukturą sieciową o dużej pojemności.

Strefy dostępności są fizycznie oddzielone w regionie świadczenia usługi Azure. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Strefy dostępności umożliwiają uruchamianie kluczowych dla działalności aplikacji z wysoką dostępnością i replikacją o małym opóźnieniu.

Na poniższej ilustracji przedstawiono sposób, w jaki strefa globalna infrastruktury platformy Azure i strefy dostępności w ramach tej samej granicy danych miejsca zamieszkania na potrzeby wysokiej dostępności, odzyskiwania po awarii i tworzenia kopii zapasowych.

![Diagram przedstawiający granicę miejsca zamieszkania danych](./media/physical-security/data-residency-boundary.png)

Rozproszone geograficznie centra danych umożliwiają firmie Microsoft zbliżenie się do klientów, zmniejszenie opóźnienia sieci i umożliwienie tworzenia kopii zapasowych i pracy awaryjnej geograficznie nadmiarowej.

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Firma Microsoft opracowuje, kompiluje i zarządza centrami danych w sposób umożliwiający ścisłą kontrolę dostępu fizycznego do obszarów, w których są przechowywane dane. Firma Microsoft rozumie znaczenie ochrony danych i pomaga w zabezpieczeniu centrów danych zawierających dane. Mamy cały dział firmy Microsoft poświęcony na projektowanie, kompilowanie i obsługiwanie fizycznych udogodnień pomocniczych platformy Azure. Ten zespół jest inwestowany z zachowaniem bezpieczeństwa fizycznego.

Firma Microsoft podejmuje warstwowe podejście do zabezpieczeń fizycznych, aby zmniejszyć ryzyko nieautoryzowanego dostępu użytkowników do danych i zasobów centrum. Centra danych zarządzane przez firmę Microsoft mają rozbudowane warstwy ochrony: zatwierdzanie dostępu na granicach budynku, na obrzeżach konstrukcyjnych, wewnątrz budynku i w podłogi centrum danych. Warstwy zabezpieczeń fizycznych są następujące:

- **Żądanie dostępu i zatwierdzenie.** Przed przystąpieniem do centrum danych należy uzyskać dostęp. Musisz podać prawidłowe uzasadnienie biznesowe dla odwiedzania, takie jak w celach zgodności lub inspekcji. Wszystkie żądania są zatwierdzane przez pracowników firmy Microsoft jako wymagające dostępu. Konieczność dostępu pozwala zachować liczbę osób potrzebnych do wykonania zadania w centrach danych do minimum. Po udzieleniu uprawnień przez firmę Microsoft osoba indywidualna ma dostęp tylko do osobnego obszaru centrum danych, na podstawie zatwierdzonego uzasadnienia biznesowego. Uprawnienia są ograniczone do określonego czasu, a następnie wygasają.

- **Obwód obiektu.** Po dojściu do centrum danych wymagane jest przechodzenie przez dobrze zdefiniowany punkt dostępu. Zazwyczaj wysokie ogrodzenia ze stali i betonu obejmują każdy centymetr obwodu. Istnieją kamery wokół centrów danych, a zespół ds. zabezpieczeń monitoruje swoje wideo przez cały czas.

- **Tworzenie wejścia.** Wejście centrum danych jest personelem specjalistów ds. bezpieczeństwa, którzy przeszedł rygorystyczne szkolenia i kontrole w tle. Tacy funkcjonariusze zabezpieczeń również rutynowie deklarują centrum danych i monitorują wideo z kamer w centrum danych.

- **Wewnątrz budynku.** Po wprowadzeniu budynku należy przekazać uwierzytelnianie dwuskładnikowe przy użyciu biometrii, aby kontynuować przechodzenie przez centrum danych. Jeśli tożsamość została zweryfikowana, możesz wprowadzić tylko część centrum danych, do której użytkownik ma dostęp. Możesz pozostać na bieżąco tylko przez czas zatwierdzenia.

- **Piętro centrum danych.** Użytkownik jest dozwolony tylko na podłogę, która została zatwierdzona do wprowadzenia. Wymagane jest przekazanie pełnego ekranu wykrywania metali ciała. Aby zmniejszyć ryzyko braku nieautoryzowanych danych wprowadzających lub opuszczających centrum dane bez naszej wiedzy, tylko zatwierdzone urządzenia mogą przełączać się do podłogi centrum danych. Ponadto kamery wideo monitorują przód i tył każdego stojaka serwera. Gdy opuszczasz podłogę z centrum danych, ponownie musisz przejść przez pełny ekran wykrywania metali ciała. Aby opuścić centrum danych, musisz przejść przez dodatkowe skanowanie zabezpieczeń.

Firma Microsoft wymaga od odwiedzających wydawania znaczków przy wyjściu z dowolnej funkcji firmy Microsoft.

## <a name="physical-security-reviews"></a>Przeglądy zabezpieczeń fizycznych
Okresowo przeprowadzamy przeglądy zabezpieczeń fizycznych obiektów, aby upewnić się, że centra danych prawidłowo zapoznają się z wymaganiami dotyczącymi zabezpieczeń platformy Azure. Personel dostawcy hostingu centrum danych nie udostępnia usługi Azure Service Management. Pracownicy nie mogą zalogować się do systemów Azure i nie mają fizycznego dostępu do pokoju i klatek kolokacji platformy Azure.

## <a name="data-bearing-devices"></a>Urządzenia z danymi
Firma Microsoft stosuje procedury najlepszych rozwiązań i wyczyszczone rozwiązanie [zgodne ze standardem NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). W przypadku dysków twardych, które nie mogą zostać wyczyszczone, używamy procesu niszczenia, który niszczy go i renderuje odzyskiwanie informacji niemożliwe. Ten proces niszczenia może mieć na celu rozintegrację, Shred, rozpylanie lub spalanie. Określamy sposób usuwania w zależności od typu elementu zawartości. Zachowujemy rekordy zniszczenia.  

## <a name="equipment-disposal"></a>Usuwanie sprzętu
Po zakończeniu okresu istnienia systemu pracownicy operacyjni firmy Microsoft stosują rygorystyczne procedury obsługi danych i usuwania sprzętu, aby zapewnić, że sprzęt zawierający dane nie zostanie udostępniony niezaufanym stronom. Używamy bezpiecznego podejścia do usuwania dysków twardych, które je obsługują. W przypadku dysków twardych, które nie mogą zostać wyczyszczone, używamy procesu niszczenia, który niszczy dysk i renderuje odzyskiwanie informacji niemożliwe. Ten proces niszczenia może mieć na celu rozintegrację, Shred, rozpylanie lub spalanie. Określamy sposób usuwania w zależności od typu elementu zawartości. Zachowujemy rekordy zniszczenia. Wszystkie usługi platformy Azure korzystają z zatwierdzonych usług zarządzania magazynem i utylizacją multimediów.

## <a name="compliance"></a>Zgodność
Firma Microsoft organizuje infrastrukturę platformy Azure i zarządza nią w celu spełnienia szerokiego zestawu międzynarodowych i branżowych standardów zgodności, takich jak ISO 27001, HIPAA, FedRAMP, SOC 1 i SOC 2. Spełniamy również standardy dotyczące kraju lub regionu, w tym Australia IRAP, BRYTYJSKIe G-Cloud i Singapur MTCS. Rygorystyczne audyty innych firm, takie jak te wykonywane przez Instytut Normalizacyjny brytyjskiej, weryfikują przestrzeganie ścisłych kontroli bezpieczeństwa obowiązujących w tych standardach.

Aby zapoznać się z pełną listą standardów zgodności, które są zgodne z platformą Azure, zobacz [oferty zgodności](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)


