---
title: Ochrona danych klienta na platformie Azure
description: W tym artykule opisano sposób ochrony danych klienta przez platformę Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 630304bec17dd34befab4e5bd9f1cfdfb6505645
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811428"
---
# <a name="azure-customer-data-protection"></a>Ochrona danych klienta platformy Azure   
Domyślnie odmowa dostępu do danych klienta przez operacje firmy Microsoft i personel pomocy technicznej. W przypadku udzielenia dostępu do danych klienta wymagane jest zatwierdzenie lidera, a następnie dostęp jest dokładnie zarządzany i rejestrowany. Wymagania dotyczące kontroli dostępu są określane przez następujące zasady zabezpieczeń platformy Azure:

- Domyślnie nie ma dostępu do danych klienta.
- Brak kont użytkowników ani administratorów na maszynach wirtualnych klienta.
- Przyznaj najniższe uprawnienia wymagane do ukończenia zadania; żądania dostępu inspekcji i rejestrowania.

Personel pomocy technicznej platformy Azure ma przypisane unikatowe konta Active Directory firmowych przez firmę Microsoft. Platforma Azure opiera się na firmowym Active Directory firmy Microsoft zarządzanym przez technologię Microsoft Information Technology (MSIT) w celu kontrolowania dostępu do kluczowych systemów informacji. Wymagana jest obsługa uwierzytelniania wieloskładnikowego, a dostęp jest udzielany tylko z poziomu bezpiecznych konsol.

Wszystkie próby dostępu są monitorowane i mogą być wyświetlane za pośrednictwem podstawowego zestawu raportów.

## <a name="data-protection"></a>Ochrona danych
Platforma Azure zapewnia klientom silną ochronę danych — zarówno domyślnie, jak i w przypadku opcji klienta.

Podział **danych**: platforma Azure to usługa z wieloma dzierżawami, która oznacza, że wiele wdrożeń klientów i maszyn wirtualnych są przechowywane na tym samym sprzęcie fizycznym. Platforma Azure używa izolacji logicznej do oddzielenia danych poszczególnych klientów od danych innych użytkowników. Rozdzielenie zapewnia skalę i ekonomiczne korzyści usług wielodostępnych, a jednocześnie rygorystyczne uniemożliwia klientom uzyskanie dostępu do danych innego użytkownika.

**Ochrona danych w czasie spoczynku**: klienci są odpowiedzialni za zapewnienie, że dane przechowywane na platformie Azure są szyfrowane zgodnie ze standardami. Platforma Azure oferuje szeroką gamę możliwości szyfrowania, dzięki czemu klienci mogą wybrać rozwiązanie, które najlepiej spełnia ich potrzeby. Azure Key Vault pomaga klientom w łatwym zachowaniu kontroli nad kluczami, które są używane przez aplikacje i usługi w chmurze do szyfrowania danych. Azure Disk Encryption umożliwia klientom szyfrowanie maszyn wirtualnych. Usługa Azure szyfrowanie usługi Storage umożliwia szyfrowanie wszystkich danych umieszczonych na koncie magazynu klienta.

**Ochrona danych w tranzycie**: klienci mogą włączyć szyfrowanie ruchu między własnymi maszynami wirtualnymi i użytkownikami końcowymi. Platforma Azure chroni dane przesyłane do lub z zewnętrznych składników i danych w tranzycie wewnętrznie, na przykład między dwiema sieciami wirtualnymi. System Azure używa standardu branżowego Transport Layer Security (TLS) 1,2 lub nowszego z 2 048-bitowymi kluczami szyfrowania RSA/SHA256 zgodnie z zaleceniami CESG/NCSC, aby szyfrować komunikację między:

- Klienta i chmurę.
- Wewnętrznie między systemami i centrami danych platformy Azure.

**Szyfrowanie**: szyfrowanie danych w magazynie i w tranzycie może być wdrażane przez klientów jako najlepsze rozwiązanie w celu zapewnienia poufności i integralności danych. Klienci mogą konfigurować swoje usługi w chmurze platformy Azure w celu ochrony komunikacji z Internetu, a nawet między maszynami wirtualnymi hostowanymi na platformie Azure.

**Nadmiarowość danych**: Firma Microsoft gwarantuje, że dane są chronione w przypadku wystąpienia cybernetycznego lub fizycznego uszkodzenia centrum danych. Klienci mogą wybrać następujące opcje:

- Magazyn w kraju/w regionie w celu zapewnienia zgodności lub opóźnienia.
- Magazyn poza krajem/poza regionem do celów związanych z bezpieczeństwem lub odzyskiwaniem po awarii.

Dane mogą być replikowane w wybranym obszarze geograficznym w celu zapewnienia nadmiarowości, ale nie można ich przesyłać poza nią. Klienci mają wiele opcji replikowania danych, w tym liczbę kopii oraz liczbę i lokalizację centrum danych replikacji.

Podczas tworzenia konta magazynu wybierz jedną z następujących opcji replikacji:

- **Magazyn lokalnie nadmiarowy (LRS)**: Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych. Magazyn LRS jest replikowany trzy razy w jednym obiekcie w pojedynczym regionie. LRS chroni dane przed normalnymi awariami sprzętowymi, ale nie z powodu awarii pojedynczej funkcji.
- **Magazyn strefowo nadmiarowy (ZRS)**: Magazyn strefowo nadmiarowy przechowuje trzy kopie danych. ZRS jest replikowana trzy razy w dwóch do trzech obiektów, aby zapewnić wyższą trwałość niż LRS. Replikacja odbywa się w jednym regionie lub w dwóch regionach. ZRS gwarantuje, że dane są trwałe w jednym regionie.
- **Magazyn Geograficznie nadmiarowy (GRS)**: Magazyn Geograficznie nadmiarowy jest domyślnie włączony dla konta magazynu podczas jego tworzenia. Magazyn GRS przechowuje sześć kopii danych. W przypadku GRS dane są replikowane trzy razy w regionie podstawowym. Twoje dane są również replikowane trzy razy w regionie dodatkowym setki kilometrów od regionu podstawowego, zapewniając najwyższy poziom trwałości. W przypadku awarii w regionie podstawowym usługa Azure Storage przejdzie w tryb failover do regionu pomocniczego. GRS gwarantuje, że dane są trwałe w dwóch oddzielnych regionach.

**Niszczenie danych**: gdy klienci usuwają dane lub opuszczają platformę Azure, firma Microsoft przestrzega rygorystycznych standardów do zastępowania zasobów magazynu przed ich ponownym użyciem, a także fizycznego zniszczenia zlikwidowanego sprzętu. Firma Microsoft wykonuje pełne usuwanie danych na żądanie klienta i zakończenie umowy.

## <a name="customer-data-ownership"></a>Własność danych klienta
Firma Microsoft nie sprawdza, zatwierdza ani nie monitoruje aplikacji wdrażanych przez klientów na platformie Azure. Ponadto firma Microsoft nie wie, jakiego rodzaju dane mają być przechowywane na platformie Azure. Firma Microsoft nie rości sobie prawa własności do danych klienta wprowadzonych na platformie Azure.

## <a name="records-management"></a>Zarządzanie rekordami
Platforma Azure ustanowiła wewnętrzne rekordy — wymagania dotyczące przechowywania danych zaplecza. Klienci są odpowiedzialni za ustalenie własnych potrzeb związanych z przechowywaniem rekordów. W przypadku rekordów przechowywanych na platformie Azure klienci są odpowiedzialni za wyodrębnianie ich danych i przechowywanie ich zawartości poza platformą Azure przez określony przez klienta okres przechowywania.

System Azure umożliwia klientom eksportowanie danych i raportów inspekcji z produktu. Eksporty są zapisywane lokalnie, aby zachować informacje o okresie przechowywania zdefiniowanym przez klienta.

## <a name="electronic-discovery-e-discovery"></a>Odnajdywanie elektroniczne (e-Discovery)
Klienci platformy Azure są odpowiedzialni za przestrzeganie wymagań dotyczących odnajdywania elektronicznego w zakresie korzystania z usług platformy Azure. Jeśli klienci platformy Azure muszą zachować swoje dane klienta, mogą eksportować i zapisywać dane lokalnie. Ponadto klienci mogą żądać eksportu danych z działu pomocy technicznej platformy Azure. Oprócz zezwalania klientom na eksportowanie danych, platforma Azure przeprowadza w sposób wewnętrzny rejestrowanie i monitorowanie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
