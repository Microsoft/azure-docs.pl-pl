---
title: Szyfrowanie danych platformy Azure — w ramach usługi REST — zabezpieczenia platformy Azure
description: Ten artykuł zawiera omówienie usługi Azure Data Encryption w systemie, ogólnych możliwości i ogólnych zagadnień.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 092320db9b7fe2b1f3fe142f84ad201d40dc6e2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492288"
---
# <a name="azure-data-encryption-at-rest"></a>Szyfrowanie danych platformy Azure w spoczynku

Microsoft Azure zawiera narzędzia do zabezpieczania danych zgodnie z potrzebami zabezpieczeń i zgodności firmy. Ten dokument koncentruje się na:

- Jak dane są chronione w spoczynku między Microsoft Azure
- Omawia różne składniki biorące udział w implementacji ochrony danych.
- Przegląda specjaliści i wady różnych metod ochrony zarządzania kluczami.

Szyfrowanie w spoczynku jest typowym wymogiem bezpieczeństwa. W systemie Azure organizacje mogą szyfrować dane bez ryzyka lub kosztów niestandardowego rozwiązania do zarządzania kluczami. W organizacjach istnieje możliwość, że platforma Azure całkowicie zarządza szyfrowaniem w stanie spoczynku. Ponadto organizacje mają różne opcje, aby dokładnie zarządzać szyfrowaniem i kluczami szyfrowania.

## <a name="what-is-encryption-at-rest"></a>Co to jest szyfrowanie w spoczynku?

Szyfrowanie w spoczynku to kodowanie (szyfrowanie) danych, gdy są utrwalane. Szyfrowanie w projektach REST na platformie Azure umożliwia korzystanie z szyfrowania symetrycznego w celu szybkiego szyfrowania i odszyfrowywania dużych ilości danych zgodnie z prostym modelem koncepcyjnym:

- Symetryczny klucz szyfrowania jest używany do szyfrowania danych podczas zapisywania w magazynie.
- Ten sam klucz szyfrowania jest używany do odszyfrowywania tych danych, ponieważ jest readied do użycia w pamięci.
- Dane mogą być partycjonowane i różne klucze mogą być używane dla każdej partycji.
- Klucze muszą być przechowywane w bezpiecznej lokalizacji z kontrolą dostępu opartą na tożsamości i zasadami inspekcji. Klucze szyfrowania danych są często szyfrowane za pomocą klucza szyfrowania kluczy w Azure Key Vault, aby dodatkowo ograniczyć dostęp.

W tym scenariuszu scenariusze zarządzania kluczami i kontroli, a także zapewnienia skalowalności i dostępności, wymagają dodatkowych konstrukcji. Poniżej opisano Microsoft Azure szyfrowanie przy założeniach i składnikach Rest.

## <a name="the-purpose-of-encryption-at-rest"></a>Cel szyfrowania w czasie spoczynku

Szyfrowanie w spoczynku zapewnia ochronę danych przechowywanych danych (w spoczynku). Ataki na dane przechowywane w usłudze REST obejmują próby uzyskania fizycznego dostępu do sprzętu, na którym dane są zapisywane, a następnie naruszenie zawartych w nim danych. W takim przypadku może dojść do nieprawidłowej obsługi dysku twardego serwera podczas konserwacji, co umożliwia osobie atakującej usunięcie dysku twardego. Później osoba atakująca umieści dysk twardy na komputerze w ramach kontroli, aby spróbować uzyskać dostęp do danych.

Szyfrowanie w spoczynku zostało zaprojektowane w taki sposób, aby uniemożliwić osobie atakującej dostęp do niezaszyfrowanych danych, zapewniając szyfrowanie danych na dysku. Jeśli osoba atakująca uzyska dysk twardy z zaszyfrowanymi danymi, ale nie klucze szyfrowania, osoba atakująca musi pokonać szyfrowanie, aby odczytywać dane. Ten atak jest znacznie bardziej skomplikowany i zużywa zasoby niż dostęp do nieszyfrowanych danych na dysku twardym. Z tego powodu szyfrowanie w stanie spoczynku jest zdecydowanie zalecane i jest to wymaganie o wysokim priorytecie dla wielu organizacji.

Szyfrowanie w spoczynku może być również wymagane przez organizację potrzebną do zarządzania danymi i ich zgodnością. Regulacje branżowe i rządowe, takie jak HIPAA, PCI i FedRAMP, stanowią szczegółowe zabezpieczenia dotyczące ochrony danych i wymagań dotyczących szyfrowania. Szyfrowanie w spoczynku jest obowiązkowym środkiem wymaganym do zgodności z niektórymi z tych rozporządzeń. Aby uzyskać więcej informacji na temat podejścia firmy Microsoft do weryfikacji zgodności ze standardem FIPS 140-2, zobacz [publikację Federal Information Processing Standard (FIPS) 140-2](/microsoft-365/compliance/offering-fips-140-2).

Oprócz spełnienia wymagań dotyczących zgodności i przepisów prawnych szyfrowanie w spoczynku zapewnia ochronę kompleksową. Microsoft Azure zapewnia zgodną platformę dla usług, aplikacji i danych. Zapewnia również kompleksowe funkcje i zabezpieczenia fizyczne, kontrolę dostępu do danych i inspekcję. Jednak ważne jest, aby zapewnić dodatkowe "nakładające się" środki zabezpieczeń w przypadku niepowodzenia jednego z pozostałych środków zabezpieczeń, a szyfrowanie w spoczynku zapewnia takie środki bezpieczeństwa.

Firma Microsoft jest zaangażowana w szyfrowanie opcji REST w ramach usług Cloud Services i zapewnia klientom kontrolę nad kluczami szyfrowania i dziennikami użycia klucza. Ponadto firma Microsoft pracuje nad szyfrowaniem domyślnie wszystkich danych klienta.

## <a name="azure-encryption-at-rest-components"></a>Szyfrowanie platformy Azure w składnikach REST

Jak opisano wcześniej, celem szyfrowania w czasie spoczynku jest to, że dane utrwalane na dysku są szyfrowane przy użyciu tajnego klucza szyfrowania. Aby osiągnąć ten cel, należy podać bezpieczny sposób tworzenia kluczy, magazynu, kontroli dostępu i zarządzania kluczami szyfrowania. Chociaż szczegółowe informacje mogą się różnić, szyfrowanie usług platformy Azure w ramach implementacji REST można znaleźć w temacie przedstawionym na poniższym diagramie.

![Składniki](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Lokalizacją przechowywania kluczy szyfrowania i kontroli dostępu do tych kluczy jest centralne szyfrowanie w modelu Rest. Klucze muszą być wysoce zabezpieczone, ale mogą być zarządzane przez określonych użytkowników i dostępne dla określonych usług. W przypadku usług Azure Azure Key Vault jest zalecanym rozwiązaniem magazynu kluczy i oferuje wspólne środowisko zarządzania w ramach usług. Klucze są przechowywane i zarządzane w magazynach kluczy, a dostęp do magazynu kluczy można udzielić użytkownikom lub usługom. Azure Key Vault obsługuje tworzenie kluczy lub importowanie kluczy klienta do użytku w scenariuszach z kluczami szyfrowania zarządzanymi przez klienta.

### <a name="azure-active-directory"></a>Azure Active Directory

Uprawnienia do korzystania z kluczy przechowywanych w Azure Key Vault w celu zarządzania nimi lub uzyskiwania do nich dostępu w celu szyfrowania podczas szyfrowania i odszyfrowywania w czasie spoczynku można nadać Azure Active Directory kontom.

### <a name="key-hierarchy"></a>Hierarchia kluczy

W implementacji REST jest używany więcej niż jeden klucz szyfrowania. Przechowywanie klucza szyfrowania w Azure Key Vault zapewnia bezpieczny dostęp do klucza i centralne zarządzanie kluczami. Jednak lokalny dostęp usługi do kluczy szyfrowania jest bardziej wydajny w przypadku szyfrowania zbiorczego i odszyfrowywania niż korzystanie z Key Vault dla każdej operacji na danych, co pozwala na silniejsze szyfrowanie i lepszą wydajność. Ograniczenie użycia jednego klucza szyfrowania zmniejsza ryzyko naruszenia klucza i koszt ponownego szyfrowania, gdy klucz musi zostać zastąpiony. Funkcje szyfrowania platformy Azure w modelach REST używają hierarchii kluczy składającej się z następujących typów kluczy, aby rozwiązać wszystkie te wymagania:

- **Klucz szyfrowania danych (undek)** — symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych.  Pojedynczy zasób może mieć wiele partycji i wiele kluczy szyfrowania danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza sprawia, że ataki analizy kryptograficznej są trudniejsze. Dostęp do DEKs jest wymagany przez dostawcę zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok. Gdy klucz szyfrowania danych jest zastępowany nowym kluczem, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.
- **Klucz szyfrowania klucza (KEK)** — klucz szyfrowania służący do szyfrowania kluczy szyfrowania danych. Użycie klucza szyfrowania klucza, który nigdy nie opuszcza Key Vault pozwala na szyfrowanie kluczy szyfrowania danych i sterowanie nimi. Jednostka, która ma dostęp do elementu KEK, może być różna od jednostki, która wymaga tego elementu. Jednostka może przetworzyć dostęp do tego klucza, aby ograniczyć dostęp do każdego szyfrowania danych do określonej partycji. Ponieważ KEK jest wymagany do odszyfrowania DEKs, KEK jest efektywnie jednym punktem, przez który DEKs może być skutecznie usunięty przez usunięcie KEK.

Klucze szyfrowania danych szyfrowane za pomocą kluczy szyfrowania kluczy są przechowywane oddzielnie, a tylko jednostka mająca dostęp do klucza szyfrowania klucza może odszyfrować te klucze szyfrowania danych. Obsługiwane są różne modele magazynu kluczy. Aby uzyskać więcej informacji, zobacz [modele szyfrowania danych](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Szyfrowanie w usługach w chmurze firmy Microsoft

Usługi Microsoft Cloud są używane we wszystkich trzech modelach chmur: IaaS, PaaS, SaaS. Poniżej znajdują się przykłady ich dopasowania do poszczególnych modeli:

- Usługi oprogramowania, określane jako oprogramowanie jako serwer lub SaaS, które zawierają aplikacje udostępniane przez chmurę, takie jak Microsoft 365.
- Usługi platformy, do których klienci wykorzystują chmurę w swoich aplikacjach, przy użyciu chmury dla takich elementów jak Storage, Analytics i Service Bus.
- Usługi infrastruktury lub infrastruktura jako usługa (IaaS), w której klienci wdrażają systemy operacyjne i aplikacje hostowane w chmurze i mogą korzystać z innych usług w chmurze.

### <a name="encryption-at-rest-for-saas-customers"></a>Szyfrowanie w spoczynku dla klientów SaaS

Klienci korzystający z oprogramowania jako usługi (SaaS) zazwyczaj mają szyfrowanie włączone lub dostępne w każdej usłudze. W Microsoft 365 dostępne są różne opcje umożliwiające klientom Weryfikowanie lub Włączanie szyfrowania w stanie spoczynku. Aby uzyskać informacje na temat usług Microsoft 365 Services, zobacz [szyfrowanie w programie Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Szyfrowanie w spoczynku dla klientów PaaS

Dane klienta platformy jako usługi (PaaS) są zwykle przechowywane w usłudze magazynu, takiej jak Blob Storage, ale mogą być również buforowane lub przechowywane w środowisku wykonywania aplikacji, na przykład na maszynie wirtualnej. Aby wyświetlić dostępne dla Ciebie opcje szyfrowania, zapoznaj się z poniższą tabelą dotyczącą używanych platform magazynu i aplikacji.

### <a name="encryption-at-rest-for-iaas-customers"></a>Szyfrowanie w spoczynku dla klientów IaaS

Klienci korzystający z infrastruktury jako usługi (IaaS) mogą mieć różne usługi i aplikacje. Usługi IaaS umożliwiają szyfrowanie przechowywanych maszyn wirtualnych i wirtualnych dysków twardych platformy Azure przy użyciu Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Zaszyfrowany magazyn

Podobnie jak w przypadku PaaS, rozwiązania IaaS mogą korzystać z innych usług platformy Azure, które przechowują dane szyfrowane w spoczynku. W takich przypadkach można włączyć szyfrowanie w obsłudze REST, zgodnie z poszczególnymi wykorzystanymi usługami platformy Azure. W poniższej tabeli wymieniono główne magazyny, usługi i platformy aplikacji oraz model szyfrowania obsługiwanego w spoczynku.

#### <a name="encrypted-compute"></a>Zaszyfrowane obliczenia

Wszystkie Managed Disks, migawki i obrazy są szyfrowane przy użyciu szyfrowanie usługi Storage przy użyciu klucza zarządzanego przez usługę. Pełniejsze szyfrowanie w rozwiązaniu REST gwarantuje, że dane nigdy nie są utrwalane w postaci niezaszyfrowanej. Podczas przetwarzania danych na maszynie wirtualnej dane mogą być utrwalane w pliku stronicowania systemu Windows lub w systemie Linux, zrzucie awaryjnego lub w dzienniku aplikacji. Aby zapewnić szyfrowanie danych w spoczynku, aplikacje IaaS mogą używać Azure Disk Encryption na maszynie wirtualnej platformy Azure IaaS (Windows lub Linux) i na dysku wirtualnym.

#### <a name="custom-encryption-at-rest"></a>Niestandardowe szyfrowanie w spoczynku

Zaleca się, aby zawsze, gdy to możliwe, IaaS aplikacje wykorzystują Azure Disk Encryption i szyfrowanie w opcjach REST zapewnianych przez wszystkie wykorzystane usługi platformy Azure. W niektórych przypadkach, takich jak nietypowe wymagania dotyczące szyfrowania lub magazyn oparty na systemie innym niż Azure, Deweloper aplikacji IaaS może wymagać zaimplementowania szyfrowania w samym czasie. Deweloperzy rozwiązań IaaS można lepiej zintegrować z usługą Azure Management i oczekiwaniami klientów, wykorzystując pewne składniki platformy Azure. W szczególności deweloperzy powinni używać usługi Azure Key Vault, aby zapewnić bezpieczny Magazyn kluczy, a także zapewnić klientom spójne opcje zarządzania kluczami w przypadku większości usług platformy Azure. Dodatkowo rozwiązania niestandardowe powinny używać tożsamości usługi Azure-Managed, aby umożliwić kontom usługi dostęp do kluczy szyfrowania. Informacje dla deweloperów dotyczące Azure Key Vault i tożsamości usługi zarządzanej znajdują się w odpowiednich zestawach SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Obsługa modelu szyfrowania dostawców zasobów platformy Azure

Usługi Microsoft Azure Each obsługują co najmniej jedno szyfrowanie w modelach Rest. W przypadku niektórych usług jednak może nie być stosowane co najmniej jeden model szyfrowania. W przypadku usług obsługujących scenariusze związane z kluczami zarządzanymi przez klienta mogą być obsługiwane tylko podzbiór typów kluczy, które Azure Key Vault obsługiwane przez klucze szyfrowania kluczy. Ponadto usługi mogą wydać wsparcie dla tych scenariuszy i typów kluczy w różnych harmonogramach. W tej sekcji opisano szyfrowanie w czasie trwania tego zapisu dla każdej głównej usługi Azure Data Storage.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Każdy klient korzystający z funkcji infrastruktury jako usługi (IaaS) platformy Azure może uzyskać szyfrowanie w spoczynku dla maszyn wirtualnych IaaS i dysków za pośrednictwem Azure Disk Encryption. Więcej informacji na temat usługi Azure Disk Encryption można znaleźć w [dokumentacji Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure Storage

Wszystkie usługi Azure Storage (BLOB Storage, queue storage, Table Storage i Azure Files) obsługują szyfrowanie po stronie serwera. Niektóre usługi obsługują dodatkowo klucze zarządzane przez klienta i szyfrowanie po stronie klienta.

- Po stronie serwera: wszystkie usługi magazynu platformy Azure domyślnie umożliwiają szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę, które są niewidoczne dla aplikacji. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md). Usługa Azure Blob Storage i Azure Files obsługują również klucze zarządzane przez klienta RSA 2048-bitowe w programie Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Po stronie klienta: obiekty blob, tabele i kolejki platformy Azure obsługują szyfrowanie po stronie klienta. W przypadku korzystania z szyfrowania po stronie klienta program szyfruje dane i przekazuje je jako zaszyfrowany obiekt BLOB. Zarządzanie kluczami jest wykonywane przez klienta. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database obecnie obsługuje szyfrowanie w stanie spoczynku dla usług zarządzanych przez firmę Microsoft i scenariuszy szyfrowania po stronie klienta.

Obsługa szyfrowania serwera jest obecnie udostępniana za pomocą funkcji SQL o nazwie Transparent Data Encryption. Gdy Azure SQL Database klient włącza klucz TDE, są automatycznie tworzone i zarządzane dla nich. Szyfrowanie w spoczynku można włączyć na poziomie bazy danych i serwera. Od czerwca 2017 [transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) jest domyślnie włączone dla nowo utworzonych baz danych. Azure SQL Database obsługuje RSA 2048-bitowe klucze zarządzane przez klienta w Azure Key Vault. Aby uzyskać więcej informacji, zobacz [transparent Data Encryption z obsługą Bring Your Own Key w przypadku Azure SQL Database i magazynu danych](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

Szyfrowanie po stronie klienta Azure SQL Database danych jest obsługiwane za pomocą funkcji [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . Always Encrypted używa klucza utworzonego i przechowywanego przez klienta. Klienci mogą przechowywać klucz główny w magazynie certyfikatów systemu Windows, Azure Key Vault lub lokalnych sprzętowych modułach zabezpieczeń. Za pomocą SQL Server Management Studio użytkownicy SQL wybierają klucz, którego chcesz użyć do zaszyfrowania kolumny.

## <a name="conclusion"></a>Wniosek

Ochrona danych klienta przechowywanych w ramach usług platformy Azure ma najważniejsze znaczenie dla firmy Microsoft. Wszystkie usługi hostowane na platformie Azure są zobowiązane do zapewniania szyfrowania w opcjach Rest. Usługi platformy Azure obsługują klucze zarządzane przez usługę, klucze zarządzane przez klienta lub szyfrowanie po stronie klienta. Usługi platformy Azure w szerokim zakresie rozszerzają szyfrowanie w stanie spoczynku i w nadchodzących miesiącach są planowane nowe opcje dotyczące wersji zapoznawczej i ogólnej dostępności.

## <a name="next-steps"></a>Następne kroki

- Zobacz [modele szyfrowania danych](encryption-models.md) , aby dowiedzieć się więcej na temat kluczy zarządzanych przez usługę i kluczy zarządzanych przez klienta.
- Dowiedz się, w jaki sposób platforma Azure używa [podwójnego szyfrowania](double-encryption.md) , aby ograniczyć zagrożenia związane z szyfrowaniem danych.
- Dowiedz się, co firma Microsoft gwarantuje, aby zapewnić [integralność i bezpieczeństwo platformy](platform.md) hostów przechodzących przez proces tworzenia oprogramowania, integracji, operacjonalizacji i napraw potoki.
