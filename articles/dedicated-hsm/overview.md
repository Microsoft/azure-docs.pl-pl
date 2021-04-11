---
title: Co to jest dedykowany moduł HSM? — Dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Dowiedz się, jak dedykowany moduł HSM platformy Azure to usługa platformy Azure, która zapewnia magazyn kluczy kryptograficznych na platformie Azure.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606984"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co to jest dedykowany moduł HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to usługa zapewniająca magazyn kluczy kryptograficznych na platformie Azure. Dedykowany moduł HSM spełnia większość najbardziej rygorystycznych wymagań dotyczących zabezpieczeń. To idealne rozwiązanie dla klientów, którzy potrzebują urządzeń zweryfikowanych w trybie FIPS 140-2 poziom 3 oraz kompletnej i wyłącznej kontroli nad urządzeniem HSM. 

 Urządzenia HSM są wdrażane globalnie w kilku regionach platformy Azure. Można je z łatwością aprowizować jako parę urządzeń i skonfigurować na potrzeby wysokiej dostępności. Urządzenia HSM można również aprowizować w kilku regionach, aby zabezpieczyć się przed pracą w trybie failover na poziomie regionu. Firma Microsoft zapewnia dedykowaną usługę HSM przy użyciu [A790 modelu HSM firmy Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . To urządzenie oferuje najwyższe poziomy wydajności oraz opcje integracji usług kryptograficznych. 

Po aprowizacji urządzenia HSM są podłączane bezpośrednio do sieci wirtualnej klienta. W przypadku skonfigurowania łączności sieci VPN typu punkt-lokacja lub lokacja-lokacja dostęp do urządzeń HSM można uzyskiwać za pomocą aplikacji lokalnej i narzędzi do zarządzania. Klienci otrzymują oprogramowanie i dokumentację dotyczącą konfigurowania i zarządzania urządzeniami HSM z [portalu obsługi klienta firmy Thales](https://supportportal.thalesgroup.com/csm).

## <a name="why-use-azure-dedicated-hsm"></a>Dlaczego warto używać usługi Azure Dedicated HSM?

### <a name="fips-140-2-level-3-compliance"></a>Zgodność ze standardem FIPS 140-2 poziom 3

Wiele organizacji ma rygorystyczne regulacje branżowe, które określają, że klucze kryptograficzne muszą być przechowywane w ramach [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) sprawdzony sprzętowych modułów zabezpieczeń. Dedykowany moduł HSM platformy Azure i Nowa oferta z jedną dzierżawą, [Azure Key Vault zarządzanym modułem HSM (wersja zapoznawcza)](https://docs.microsoft.com/azure/key-vault/managed-hsm), pomaga klientom z różnych segmentów branżowych, takich jak branżowe usługi finansowe, agencje rządowe i inne spełniają wymagania standardu FIPS 140-2 na poziomie 3. Usługa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) z wieloma dzierżawcami firmy Microsoft korzysta obecnie z sprzętowych modułów zabezpieczeńego poziomu FIPS 140-2. 

### <a name="single-tenant-devices"></a>Urządzenia dla jednej dzierżawy

Wielu naszych klientów potrzebuje urządzenia magazynu kryptograficznego obsługującego jedną dzierżawę. Usługa Azure Dedicated HSM umożliwia aprowizację urządzenia fizycznego z jednego z globalnie rozproszonych centrów danych firmy Microsoft. Po aprowizowaniu u klienta tylko ten klient ma dostęp do danego urządzenia.

### <a name="full-administrative-control"></a>Pełna kontrola administracyjna

Wielu klientów wymaga pełnej kontroli administracyjnej oraz wyłącznego dostępu do swojego urządzenia w celach administracyjnych. Po aprowizacji urządzenia tylko dany klient ma wszelki dostęp administracyjny i na poziomie aplikacji do tego urządzenia.

 Kiedy klient uzyska dostęp do urządzenia po raz pierwszy i zmieni hasło, firma Microsoft nie ma już żadnej kontroli administracyjnej nad urządzeniem. Od tego momentu klient jest rzeczywistym jedynym dzierżawcą z pełną kontrolą administracyjną i możliwością zarządzania aplikacją. Firma Microsoft zachowuje dostęp na poziomie monitorowania (nie jest to rola administratora) na potrzeby telemetrii za pośrednictwem połączenia portu szeregowego. Ten dostęp obejmuje monitory sprzętowe, takie jak temperatura, kondycja zasilacza i kondycja wentylatora. 
 
 Klient może w każdej chwili wyłączyć to monitorowanie. Jeśli jednak zostanie ono wyłączone, klient nie będzie otrzymywać proaktywnych alertów dotyczących kondycji od firmy Microsoft.

### <a name="high-performance"></a>Wysoka wydajność

Urządzenie firmy Thales zostało wybrane dla tej usługi z różnych powodów. Oferuje ono obsługę szerokiej gamy algorytmów kryptograficznych, różnych systemów operacyjnych i szerokiego zakresu interfejsów API. Określony model, który został wdrożony, oferuje doskonałą wydajność dzięki obsłudze 10 000 operacji na sekundę dla szyfrowania RSA-2048. Obsługuje on 10 partycji, których można użyć na potrzeby unikatowych wystąpień aplikacji. Jest to urządzenie o małych opóźnieniach, dużej pojemności i wysokiej przepływności.

### <a name="unique-cloud-based-offering"></a>Unikatowa oferta chmurowa

Firma Microsoft zauważyła specyficzną potrzebę unikatowego zestawu klientów. Jesteśmy jedynym dostawcą usług w chmurze, który oferuje nowym klientom dedykowaną usługę modułu HSM zweryfikowaną w trybie FIPS 140-2 poziom 3 i oferującą tak duży zakres integracji aplikacji chmurowych i lokalnych.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Czy dedykowany moduł HSM platformy Azure jest dla Ciebie odpowiedni?

Azure Dedicated HSM to wyspecjalizowana usługa zaspokajająca unikatowe wymagania dużych organizacji określonego typu. Z tego względu spodziewamy się, że większość klientów platformy Azure nie będzie pasować do profilu użycia dla tej usługi. Dla wielu z nich usługa Azure Key Vault okaże się bardziej odpowiednia i ekonomiczna. Aby ułatwić podjęcie decyzji, czy ta usługa odpowiada Twoim potrzebom, określiliśmy następujące kryteria.

### <a name="best-fit"></a>Najlepsze dopasowanie

Usługa Azure Dedicated HSM jest najbardziej odpowiednia dla scenariuszy migracji metodą „lift-and-shift”, które wymagają bezpośredniego i wyłącznego dostępu do urządzeń HSM. Przykłady obejmują:

- Migrowanie aplikacji ze środowiska lokalnego do usługi Azure Virtual Machines
- Migrowanie aplikacji z usługi Amazon AWS EC2 na maszyny wirtualne, które korzystają z usługi AWS Cloud HSM Classic (firma Amazon nie oferuje tej usługi dla nowych klientów)
- Uruchamianie oprogramowania w otoce, takiego jak Apache/Ngnix SSL Offload, Oracle TDE i ADCS, w usłudze Azure Virtual Machines 

### <a name="not-a-fit"></a>Brak dopasowania

Dedykowany moduł HSM platformy Azure nie jest dobry dla następującego typu scenariusza: usługi firmy Microsoft w chmurze obsługujące szyfrowanie z kluczami zarządzanymi przez klienta (takimi jak Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database i klucz klienta pakietu Office 365), które nie są zintegrowane z dedykowanym modułem HSM platformy Azure.

### <a name="it-depends"></a>To zależy

To, czy usługa Azure Dedicated HSM sprawdzi się w Twoim przypadku, zależy od potencjalnie złożonego połączenia wymagań i kompromisów, na które możesz lub nie możesz sobie pozwolić. Przykładem jest wymaganie zgodności ze standardem FIPS 140-2 poziom 3. Ten wymóg jest typowy, a dedykowany moduł HSM platformy Azure i Nowa oferta z jedną dzierżawą, [Azure Key Vault zarządzanym modułem HSM (wersja zapoznawcza)](https://docs.microsoft.com/azure/key-vault/managed-hsm) są obecnie jedynymi opcjami dla ich spotkania. Jeśli te wymagane wymagania nie są istotne, często jest to wybór między Azure Key Vault i dedykowanym modułem HSM platformy Azure. Przed podjęciem decyzji oceń swoje wymagania.

Sytuacje, w których trzeba będzie rozważyć dostępne opcje, mogą być następujące: 

- Nowy kod działający na maszynie wirtualnej platformy Azure klienta
- SQL Server TDE na maszynie wirtualnej platformy Azure
- Szyfrowanie po stronie klienta usługi Azure Storage
- SQL Server i Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Następne kroki

Ta usługa jest wysoce wyspecjalizowana. Z tego względu zalecamy, aby szczegółowo zapoznać się z kluczowymi pojęciami opisanymi w tym zestawie dokumentacji, w tym z cenami, pomocą techniczną i umowami dotyczącymi poziomu usług. 

[Przewodniki integracji firmy Thales](https://cpl.thalesgroup.com/partners/overview) pomagają ułatwić obsługę administracyjną sprzętowych modułów zabezpieczeń w istniejącym środowisku sieci wirtualnej. Istnieją także przewodniki ułatwiające określenie sposobu konfigurowania architektury wdrożenia.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
