---
title: Co to jest dedykowany moduł HSM? — Dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Dowiedz się, Azure Dedicated HSM to usługa platformy Azure, która zapewnia magazyn kluczy kryptograficznych na platformie Azure.
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
ms.openlocfilehash: 418c8f0844bf2336ce0d4a681071f237d81877ca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505721"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co to jest dedykowany moduł HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to usługa zapewniająca magazyn kluczy kryptograficznych na platformie Azure. Dedykowany moduł HSM spełnia większość najbardziej rygorystycznych wymagań dotyczących zabezpieczeń. To idealne rozwiązanie dla klientów, którzy potrzebują urządzeń zweryfikowanych w trybie FIPS 140-2 poziom 3 oraz kompletnej i wyłącznej kontroli nad urządzeniem HSM. 

 Urządzenia HSM są wdrażane globalnie w kilku regionach platformy Azure. Można je z łatwością aprowizować jako parę urządzeń i skonfigurować na potrzeby wysokiej dostępności. Urządzenia HSM można również aprowizować w kilku regionach, aby zabezpieczyć się przed pracą w trybie failover na poziomie regionu. Firma Microsoft dostarcza usługę Dedicated HSM przy użyciu urządzeń [Firmy Thales Model A790 HSM.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) To urządzenie oferuje najwyższe poziomy wydajności oraz opcje integracji usług kryptograficznych. 

Po aprowizacji urządzenia HSM są podłączane bezpośrednio do sieci wirtualnej klienta. W przypadku skonfigurowania łączności sieci VPN typu punkt-lokacja lub lokacja-lokacja dostęp do urządzeń HSM można uzyskiwać za pomocą aplikacji lokalnej i narzędzi do zarządzania. Klienci uzyskają oprogramowanie i dokumentację do konfigurowania urządzeń HSM i zarządzania nimi z portalu [pomocy technicznej firmy Thales.](https://supportportal.thalesgroup.com/csm)

## <a name="why-use-azure-dedicated-hsm"></a>Dlaczego warto używać usługi Azure Dedicated HSM?

### <a name="fips-140-2-level-3-compliance"></a>Zgodność ze standardem FIPS 140-2 poziom 3

Wiele organizacji ma rygorystyczne przepisy branżowe, które określają, że klucze kryptograficzne muszą być przechowywane w modułach HSM zweryfikowanych w standardach [FIPS 140-2 poziom 3.](https://csrc.nist.gov/publications/detail/fips/140/2/final) Azure Dedicated HSM i nowa oferta pojedynczej dzierżawy, Azure Key Vault zarządzany moduł [HSM,](https://docs.microsoft.com/azure/key-vault/managed-hsm)pomagają klientom z różnych segmentów branży, takich jak branża usług finansowych, agencje rządowe i inne, spełniają wymagania FIPS 140-2 poziom 3. Chociaż wielodostępna usługa Azure Key Vault [firmy](https://docs.microsoft.com/azure/key-vault) Microsoft używa obecnie modułów HSM zweryfikowanych w trybie FIPS 140-2 poziom 2. 

### <a name="single-tenant-devices"></a>Urządzenia dla jednej dzierżawy

Wielu naszych klientów potrzebuje urządzenia magazynu kryptograficznego obsługującego jedną dzierżawę. Usługa Azure Dedicated HSM umożliwia aprowizację urządzenia fizycznego z jednego z globalnie rozproszonych centrów danych firmy Microsoft. Po aprowizowaniu u klienta tylko ten klient ma dostęp do danego urządzenia.

### <a name="full-administrative-control"></a>Pełna kontrola administracyjna

Wielu klientów wymaga pełnej kontroli administracyjnej oraz wyłącznego dostępu do swojego urządzenia w celach administracyjnych. Po aprowizacji urządzenia tylko dany klient ma wszelki dostęp administracyjny i na poziomie aplikacji do tego urządzenia.

 Kiedy klient uzyska dostęp do urządzenia po raz pierwszy i zmieni hasło, firma Microsoft nie ma już żadnej kontroli administracyjnej nad urządzeniem. Od tego momentu klient jest rzeczywistym jedynym dzierżawcą z pełną kontrolą administracyjną i możliwością zarządzania aplikacją. Firma Microsoft zachowuje dostęp na poziomie monitorowania (nie jest to rola administratora) na potrzeby telemetrii za pośrednictwem połączenia portu szeregowego. Ten dostęp obejmuje monitory sprzętowe, takie jak temperatura, kondycja zasilacza i kondycja wentylatora. 
 
 Klient może w każdej chwili wyłączyć to monitorowanie. Jeśli jednak zostanie ono wyłączone, klient nie będzie otrzymywać proaktywnych alertów dotyczących kondycji od firmy Microsoft.

### <a name="high-performance"></a>Wysoka wydajność

Urządzenie firmy Thales zostało wybrane do tej usługi z różnych powodów. Oferuje ono obsługę szerokiej gamy algorytmów kryptograficznych, różnych systemów operacyjnych i szerokiego zakresu interfejsów API. Określony model, który został wdrożony, oferuje doskonałą wydajność dzięki obsłudze 10 000 operacji na sekundę dla szyfrowania RSA-2048. Obsługuje on 10 partycji, których można użyć na potrzeby unikatowych wystąpień aplikacji. Jest to urządzenie o małych opóźnieniach, dużej pojemności i wysokiej przepływności.

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

Azure Dedicated HSM nie jest dobrym rozwiązaniem w przypadku następującego typu scenariusza: usługi w chmurze firmy Microsoft, które obsługują szyfrowanie przy użyciu kluczy zarządzanych przez klienta (takich jak Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database i Klucz klienta dla usługi Office 365), które nie są zintegrowane z usługą Azure Dedicated HSM.

### <a name="it-depends"></a>To zależy

To, czy usługa Azure Dedicated HSM sprawdzi się w Twoim przypadku, zależy od potencjalnie złożonego połączenia wymagań i kompromisów, na które możesz lub nie możesz sobie pozwolić. Przykładem jest wymaganie zgodności ze standardem FIPS 140-2 poziom 3. To wymaganie jest typowe i Azure Dedicated HSM i nową ofertę z jedną dzierżawą, Azure Key Vault zarządzany moduł [HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm) są obecnie jedynymi opcjami do jego spełniania. Jeśli te wymagane wymagania nie są istotne, często jest to wybór między Azure Key Vault a Azure Dedicated HSM. Przed podjęciem decyzji oceń swoje wymagania.

Sytuacje, w których trzeba będzie rozważyć dostępne opcje, mogą być następujące: 

- Nowy kod działający na maszynie wirtualnej platformy Azure klienta
- SQL Server TDE na maszynie wirtualnej platformy Azure
- Szyfrowanie po stronie klienta usługi Azure Storage
- SQL Server i Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Następne kroki

Ta usługa jest wysoce wyspecjalizowana. Z tego względu zalecamy, aby szczegółowo zapoznać się z kluczowymi pojęciami opisanymi w tym zestawie dokumentacji, w tym z cenami, pomocą techniczną i umowami dotyczącymi poziomu usług. 

Przewodniki [integracji firmy Thales](https://cpl.thalesgroup.com/partners/overview) ułatwiają aprowizowanie modułów HSM w istniejącym środowisku sieci wirtualnej. Dostępne są również przewodniki z instrukcje pomagające w ustaleniu, jak skonfigurować architekturę wdrożenia.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
