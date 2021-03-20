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
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 6eb82400b2f598b4b54da4b238f4bce71d83de6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90970199"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co to jest dedykowany moduł HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to usługa zapewniająca magazyn kluczy kryptograficznych na platformie Azure. Dedykowany moduł HSM spełnia większość najbardziej rygorystycznych wymagań dotyczących zabezpieczeń. To idealne rozwiązanie dla klientów, którzy potrzebują urządzeń zweryfikowanych w trybie FIPS 140-2 poziom 3 oraz kompletnej i wyłącznej kontroli nad urządzeniem HSM. 

 Urządzenia HSM są wdrażane globalnie w kilku regionach platformy Azure. Można je z łatwością aprowizować jako parę urządzeń i skonfigurować na potrzeby wysokiej dostępności. Urządzenia HSM można również aprowizować w kilku regionach, aby zabezpieczyć się przed pracą w trybie failover na poziomie regionu. Firma Microsoft dostarcza usługę Dedicated HSM przy użyciu urządzenia [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) od firmy Gemalto. To urządzenie oferuje najwyższe poziomy wydajności oraz opcje integracji usług kryptograficznych. 

Po aprowizacji urządzenia HSM są podłączane bezpośrednio do sieci wirtualnej klienta. W przypadku skonfigurowania łączności sieci VPN typu punkt-lokacja lub lokacja-lokacja dostęp do urządzeń HSM można uzyskiwać za pomocą aplikacji lokalnej i narzędzi do zarządzania. Klienci mogą uzyskać oprogramowanie i dokumentację dotyczące konfiguracji urządzeń HSM i zarządzania nimi z portalu pomocy technicznej firmy Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Dlaczego warto używać usługi Azure Dedicated HSM?

### <a name="fips-140-2-level-3-compliance"></a>Zgodność ze standardem FIPS 140-2 poziom 3

Wiele organizacji musi spełniać wymogi rygorystycznych przepisów branżowych, które żądają, aby kryptograficzny magazyn kluczy był zgodny ze standardem [FIPS 140-2 poziom 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). Wielodostępna usługa Azure Key Vault firmy Microsoft obecnie zapewnia zgodność z certyfikatem FIPS 140-2 poziom 2. Usługa Azure Dedicated HSM zaspokaja rzeczywistą potrzebę branży usług finansowych, instytucji rządowych i innych organizacji, które muszą spełniać wymagania certyfikatu FIPS 140-2 poziom 3.

### <a name="single-tenant-devices"></a>Urządzenia dla jednej dzierżawy

Wielu naszych klientów potrzebuje urządzenia magazynu kryptograficznego obsługującego jedną dzierżawę. Usługa Azure Dedicated HSM umożliwia aprowizację urządzenia fizycznego z jednego z globalnie rozproszonych centrów danych firmy Microsoft. Po aprowizowaniu u klienta tylko ten klient ma dostęp do danego urządzenia.

### <a name="full-administrative-control"></a>Pełna kontrola administracyjna

Wielu klientów wymaga pełnej kontroli administracyjnej oraz wyłącznego dostępu do swojego urządzenia w celach administracyjnych. Po aprowizacji urządzenia tylko dany klient ma wszelki dostęp administracyjny i na poziomie aplikacji do tego urządzenia.

 Kiedy klient uzyska dostęp do urządzenia po raz pierwszy i zmieni hasło, firma Microsoft nie ma już żadnej kontroli administracyjnej nad urządzeniem. Od tego momentu klient jest rzeczywistym jedynym dzierżawcą z pełną kontrolą administracyjną i możliwością zarządzania aplikacją. Firma Microsoft zachowuje dostęp na poziomie monitorowania (nie jest to rola administratora) na potrzeby telemetrii za pośrednictwem połączenia portu szeregowego. Ten dostęp obejmuje monitory sprzętowe, takie jak temperatura, kondycja zasilacza i kondycja wentylatora. 
 
 Klient może w każdej chwili wyłączyć to monitorowanie. Jeśli jednak zostanie ono wyłączone, klient nie będzie otrzymywać proaktywnych alertów dotyczących kondycji od firmy Microsoft.

### <a name="high-performance"></a>Wysoka wydajność

Urządzenia firmy Gemalto zostało wybrane do tej usługi z różnych powodów. Oferuje ono obsługę szerokiej gamy algorytmów kryptograficznych, różnych systemów operacyjnych i szerokiego zakresu interfejsów API. Określony model, który został wdrożony, oferuje doskonałą wydajność dzięki obsłudze 10 000 operacji na sekundę dla szyfrowania RSA-2048. Obsługuje on 10 partycji, których można użyć na potrzeby unikatowych wystąpień aplikacji. Jest to urządzenie o małych opóźnieniach, dużej pojemności i wysokiej przepływności.

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

To, czy usługa Azure Dedicated HSM sprawdzi się w Twoim przypadku, zależy od potencjalnie złożonego połączenia wymagań i kompromisów, na które możesz lub nie możesz sobie pozwolić. Przykładem jest wymaganie zgodności ze standardem FIPS 140-2 poziom 3. To wymaganie jest często spotykane, a usługa Dedicated HSM jest obecnie jedyną opcją, która je spełnia. Jeśli te narzucone wymagania nie są istotne, często decyzja sprowadza się do wyboru między usługami Azure Key Vault i Dedicated HSM. Przed podjęciem decyzji oceń swoje wymagania.

Sytuacje, w których trzeba będzie rozważyć dostępne opcje, mogą być następujące: 

- Nowy kod działający na maszynie wirtualnej platformy Azure klienta
- SQL Server TDE na maszynie wirtualnej platformy Azure
- Szyfrowanie po stronie klienta usługi Azure Storage
- SQL Server i Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Następne kroki

Ta usługa jest wysoce wyspecjalizowana. Z tego względu zalecamy, aby szczegółowo zapoznać się z kluczowymi pojęciami opisanymi w tym zestawie dokumentacji, w tym z cenami, pomocą techniczną i umowami dotyczącymi poziomu usług. 

[Przewodniki na temat integracji firmy Gemalto](https://safenet.gemalto.com/partners/microsoft/) ułatwią aprowizację modułów HSM w istniejącym środowisku sieci wirtualnej. Dostępne są również przewodniki z instrukcjami pomocne w określeniu sposobu konfiguracji architektury wdrożenia.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
