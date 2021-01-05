---
title: Najlepsze rozwiązania dotyczące zabezpieczeń i zgodności w usłudze Batch
description: Poznaj najlepsze rozwiązania i przydatne porady dotyczące zwiększania bezpieczeństwa przy użyciu rozwiązań Azure Batch.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: b9732ffb810a1038a6f402a46fa8b809f180f0a8
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802290"
---
# <a name="batch-security-and-compliance-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń i zgodności w usłudze Batch

Ten artykuł zawiera wskazówki i najlepsze rozwiązania dotyczące zwiększania bezpieczeństwa podczas korzystania z Azure Batch.

Domyślnie konta Azure Batch mają publiczny punkt końcowy i są dostępne publicznie. Po utworzeniu puli Azure Batch Pula jest obsługiwana w określonej podsieci sieci wirtualnej platformy Azure. Do maszyn wirtualnych w puli wsadowej uzyskuje się dostęp za pomocą publicznych adresów IP, które są tworzone przez program Batch. Węzły obliczeniowe w puli mogą komunikować się ze sobą, gdy jest to konieczne, na przykład do uruchamiania zadań o wiele wystąpień, ale węzły w puli nie mogą komunikować się z maszynami wirtualnymi spoza puli.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagram przedstawiający typowe środowisko wsadowe.":::

Dostępnych jest wiele funkcji, które ułatwiają tworzenie bezpieczniejszego wdrażania Azure Batch. Możesz ograniczyć dostęp do węzłów i zmniejszyć wykrywalność węzłów z Internetu, udostępniając [pulę bez publicznych adresów IP](batch-pool-no-public-ip-address.md). Węzły obliczeniowe mogą bezpiecznie komunikować się z innymi maszynami wirtualnymi lub z siecią lokalną przez [zainicjowanie puli w podsieci sieci wirtualnej platformy Azure](batch-virtual-network.md). Można też włączyć [prywatny dostęp z sieci wirtualnych](private-connectivity.md) z usługi obsługiwanej przez link prywatny platformy Azure.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagram przedstawiający bezpieczniejsze środowisko wsadowe.":::

## <a name="general-security-related-best-practices"></a>Ogólne najlepsze rozwiązania dotyczące zabezpieczeń

### <a name="pool-configuration"></a>Konfiguracja puli

Wiele funkcji zabezpieczeń jest dostępnych tylko dla pul skonfigurowanych przy użyciu [konfiguracji maszyny wirtualnej](nodes-and-pools.md#configurations), a nie dla pul z konfiguracją Cloud Services. Zalecamy używanie pul konfiguracji maszyny wirtualnej, które wykorzystują [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md), jeśli jest to możliwe.

### <a name="batch-account-authentication"></a>Uwierzytelnianie konta w usłudze Batch

Dostęp do konta usługi Batch obsługuje dwie metody uwierzytelniania: klucz współużytkowany i [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Zdecydowanie zalecamy korzystanie z usługi Azure AD na potrzeby uwierzytelniania konta usług Batch. Niektóre funkcje wsadowe wymagają tej metody uwierzytelniania, łącznie z wieloma funkcjami związanymi z zabezpieczeniami opisanymi tutaj.

### <a name="batch-account-pool-allocation-mode"></a>Tryb alokacji puli kont wsadowych

Podczas tworzenia konta w usłudze Batch można wybrać jeden z dwóch [trybów alokacji puli](accounts.md#batch-accounts):

- **Usługa Batch**: opcja domyślna, w której źródłowa usługa w chmurze lub zasoby zestawu skalowania maszyn wirtualnych używane do przydzielania węzłów puli i zarządzania nimi, są tworzone w subskrypcjach wewnętrznych i nie są bezpośrednio widoczne w Azure Portal. Widoczne są tylko pule i węzły wsadowe. 
- **Subskrypcja użytkownika**: źródłowa usługa w chmurze lub zestaw skalowania maszyn wirtualnych są tworzone w tej samej subskrypcji co konto usługi Batch. Te zasoby są zatem widoczne w subskrypcji, oprócz odpowiednich zasobów usługi Batch.

W trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Tryb subskrypcji użytkownika jest wymagany, jeśli chcesz utworzyć pule usługi Batch przy użyciu Azure Reserved VM Instances, użyć Azure Policy w zasobach zestawu skalowania maszyn wirtualnych i/lub zarządzać limitem przydziału rdzeni w ramach subskrypcji (współużytkowane przez wszystkie konta usługi Batch w ramach subskrypcji). Aby utworzyć konto usługi Batch w trybie subskrypcji użytkownika, musisz również zarejestrować subskrypcję w usłudze Azure Batch i powiązać konto z usługą Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Ogranicz dostęp do punktu końcowego sieci

### <a name="batch-network-endpoints"></a>Punkty końcowe sieci partii

Należy pamiętać, że domyślnie punkty końcowe z publicznymi adresami IP są używane do komunikowania się z kontami wsadowymi, pulami wsadowymi i węzłami puli.

### <a name="batch-account-api"></a>Interfejs API konta usługi Batch

 Po utworzeniu konta usługi Batch zostanie utworzony publiczny punkt końcowy, który służy do wywoływania większości operacji dla konta za pomocą [interfejsu API REST](/rest/api/batchservice/). Punkt końcowy konta ma podstawowy adres URL w formacie `https://{account-name}.{region-id}.batch.azure.com` . Dostęp do konta usługi Batch jest zabezpieczony za pomocą komunikacji z punktem końcowym konta, który jest szyfrowany przy użyciu protokołu HTTPS, a każde żądanie uwierzytelnione przy użyciu klucza współużytkowanego lub Azure Active Directory (Azure AD).

### <a name="azure-resource-manager"></a>Azure Resource Manager

Poza operacjami specyficznymi dla konta wsadowego [operacje zarządzania](/rest/api/batchmanagement/) dotyczą jednego i wielu kont wsadowych. Do tych operacji zarządzania uzyskuje się dostęp za pośrednictwem Azure Resource Manager.

Operacje zarządzania usługą Batch za pośrednictwem Azure Resource Manager są szyfrowane przy użyciu protokołu HTTPS, a każde żądanie jest uwierzytelniane przy użyciu uwierzytelniania w usłudze Azure AD.

### <a name="batch-pool-nodes"></a>Węzły puli zadań wsadowych

Usługa Batch komunikuje się z agentem węzła usługi Batch, który działa w każdym węźle puli. Na przykład usługa instruuje agenta węzła, aby uruchomił zadanie, zatrzymać zadanie lub pobrać pliki dla zadania. Komunikacja z agentem węzła jest włączana przez co najmniej jeden moduł równoważenia obciążenia, którego liczba zależy od liczby węzłów w puli. Moduł równoważenia obciążenia przekazuje komunikację do żądanego węzła, przy czym każdy węzeł jest rozwiązywany przez unikatowy numer portu. Domyślnie usługi równoważenia obciążenia mają skojarzone z nimi publiczne adresy IP. Można również zdalnie uzyskać dostęp do węzłów puli za pośrednictwem protokołu RDP lub SSH (ten dostęp jest domyślnie włączony z komunikacją za pośrednictwem usług równoważenia obciążenia).

### <a name="restricting-access-to-batch-endpoints"></a>Ograniczanie dostępu do punktów końcowych partii 

Dostępnych jest kilka możliwości ograniczania dostępu do różnych punktów końcowych partii, szczególnie w przypadku, gdy rozwiązanie używa sieci wirtualnej. 

#### <a name="use-private-endpoints"></a>Używanie prywatnych punktów końcowych

[Link prywatny platformy Azure](../private-link/private-link-overview.md) umożliwia dostęp do usług Azure PaaS oraz hostowanych usług partnerskich i partnerów platformy Azure przez prywatny punkt końcowy w sieci wirtualnej. Możesz użyć linku prywatnego, aby ograniczyć dostęp do konta usługi Batch z poziomu sieci wirtualnej lub z dowolnej równorzędnej sieci wirtualnej. Zasoby mapowane do linku prywatnego są również dostępne lokalnie przez prywatną komunikację równorzędną za pośrednictwem sieci VPN lub Azure ExpressRoute.

Aby można było używać prywatnych punktów końcowych, konto wsadowe musi być odpowiednio skonfigurowane po utworzeniu; Konfiguracja dostępu do sieci publicznej musi być wyłączona. Po utworzeniu prywatne punkty końcowe można utworzyć i skojarzyć z kontem wsadowym. Aby uzyskać więcej informacji, zobacz [Używanie prywatnych punktów końcowych z kontami Azure Batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Tworzenie pul w sieciach wirtualnych

Węzły obliczeniowe w puli usługi Batch mogą komunikować się ze sobą, na przykład do uruchamiania zadań o wiele wystąpień, bez konieczności używania sieci wirtualnej (VNET). Jednak domyślnie węzły w puli nie mogą komunikować się z maszynami wirtualnymi spoza puli w sieci wirtualnej i mają prywatne adresy IP, takie jak serwery licencji lub serwery plików.

Aby umożliwić węzłom obliczeniowym bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można skonfigurować pulę, która będzie znajdować się w podsieci sieci wirtualnej platformy Azure.

Gdy pule mają publiczne adresy IP, podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, aby można było zaplanować zadania i wykonywać inne operacje w węzłach obliczeniowych, a komunikacja wychodząca do komunikacji z usługą Azure Storage lub innych zasobów zgodnie z potrzebami obciążeń. W przypadku pul w konfiguracji maszyny wirtualnej partia dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsu sieciowego dołączonym do węzłów obliczeniowych. Te sieciowych grup zabezpieczeń mają reguły umożliwiające włączenie:

- Ruch przychodzący TCP z adresów IP usługi Batch
- Ruch przychodzący TCP dla dostępu zdalnego
- Ruch wychodzący z dowolnego portu do sieci wirtualnej (może zostać zmieniony dla reguł sieciowej grupy zabezpieczeń na poziomie podsieci)
- Ruch wychodzący z dowolnego portu do Internetu (może zostać zmieniony dla reguł sieciowej grupy zabezpieczeń na poziomie podsieci)

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci sieci wirtualnej, ponieważ usługa Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli masz sieciowej grupy zabezpieczeń skojarzoną z podsiecią, w której wdrożone są węzły obliczeniowe usługi Batch, lub jeśli chcesz zastosować niestandardowe reguły sieciowej grupy zabezpieczeń w celu zastąpienia ustawień domyślnych, należy skonfigurować ten sieciowej grupy zabezpieczeń przy użyciu co najmniej reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, aby umożliwić komunikację usługi Batch z węzłami puli i węzłem puli do usługi Azure Storage.

Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Tworzenie pul ze statycznymi publicznymi adresami IP

Domyślnie publiczne adresy IP skojarzone z pulami są dynamiczne; są one tworzone podczas tworzenia puli i można dodawać i usuwać adresy IP w przypadku zmiany rozmiaru puli. Gdy aplikacje uruchamiane w węzłach puli muszą uzyskiwać dostęp do usług zewnętrznych, dostęp do tych usług może wymagać ograniczenia do określonych adresów IP.  W takim przypadku nie będzie można zarządzać dynamicznymi adresami IP.

Po utworzeniu puli można utworzyć statyczne zasoby publicznych adresów IP w tej samej subskrypcji co konto usługi Batch. Następnie można określić te adresy podczas tworzenia puli.

Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch z określonymi publicznymi adresami IP](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Tworzenie pul bez publicznych adresów IP

Domyślnie wszystkie węzły obliczeniowe w puli konfiguracji maszyny wirtualnej Azure Batch są przypisane co najmniej jednego publicznego adresu IP. Te punkty końcowe są używane przez usługę Batch do planowania zadań i komunikacji z węzłami obliczeniowymi, w tym dostęp wychodzący do Internetu.

Aby ograniczyć dostęp do tych węzłów i zmniejszyć wykrywalność tych węzłów z Internetu, można zainicjować obsługę administracyjną puli bez publicznych adresów IP.

Aby uzyskać więcej informacji, zobacz [Tworzenie puli bez publicznych adresów IP](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Ograniczanie dostępu zdalnego do węzłów puli

Domyślnie zadanie wsadowe umożliwia użytkownikowi węzła z łącznością sieciową łączenie się zewnętrznie z węzłem obliczeniowym w puli usługi Batch przy użyciu protokołu RDP lub SSH.

Aby ograniczyć dostęp zdalny do węzłów, należy użyć jednej z następujących metod:

- Skonfiguruj [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) , aby odmówić dostępu. Odpowiednia sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) zostanie skojarzona z pulą.
- Utwórz pulę [bez publicznych adresów IP](batch-pool-no-public-ip-address.md). Domyślnie te pule nie są dostępne poza siecią wirtualną.
- Skojarz sieciowej grupy zabezpieczeń z siecią wirtualną, aby odmówić dostępu do portów protokołu RDP lub SSH.
- Nie należy tworzyć żadnych użytkowników w węźle. Bez żadnych użytkowników węzła dostęp zdalny nie będzie możliwy.

## <a name="encrypt-data"></a>Szyfrowanie danych

### <a name="encrypt-data-in-transit"></a>Szyfruj dane podczas przesyłania

Cała komunikacja z punktem końcowym konta wsadowego (lub za pośrednictwem Azure Resource Manager) musi być używana przez protokół HTTPS. `https://`W przypadku nawiązywania połączenia z usługą Batch należy używać w adresach URL konta usługi Batch określonych w interfejsie API.

Klienci, którzy komunikują się z usługą Batch, powinni mieć skonfigurowany do używania Transport Layer Security (TLS) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Szyfruj dane partii w spoczynku

Niektóre informacje określone w interfejsie API usługi Batch, takie jak certyfikaty kont, metadane zadania i zadania i wiersze poleceń zadań, są szyfrowane automatycznie, gdy są przechowywane przez usługę Batch. Domyślnie te dane są szyfrowane przy użyciu Azure Batch kluczy zarządzanych przez platformę unikatowych dla każdego konta usługi Batch.

Możesz również zaszyfrować te dane przy użyciu [kluczy zarządzanych przez klienta](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) jest używany do generowania i przechowywania klucza z identyfikatorem klucza zarejestrowanym na koncie partii.

### <a name="encrypt-compute-node-disks"></a>Szyfrowanie dysków węzłów obliczeniowych

Węzły obliczeniowe wsadowe mają domyślnie dwa dyski: dysk systemu operacyjnego i lokalny tymczasowy dysk SSD. [Pliki i katalogi](files-and-directories.md) zarządzane przez usługi Batch znajdują się na tymczasowym dysku SSD, który jest domyślną lokalizacją plików, takich jak pliki danych wyjściowych zadań. Aplikacje zadań wsadowych mogą korzystać z lokalizacji domyślnej na dysk SSD lub dysku systemu operacyjnego.

Aby uzyskać dodatkowe zabezpieczenia, Zaszyfruj te dyski przy użyciu jednej z następujących możliwości szyfrowania dysku platformy Azure:

- [Zarządzane szyfrowanie dysków przy użyciu kluczy zarządzanych przez platformę](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [Szyfrowanie na hoście przy użyciu klucza zarządzanego przez platformę](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Usługa Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Bezpieczny dostęp do usług z węzłów obliczeniowych

Węzły usługi Batch mogą [bezpiecznie uzyskiwać dostęp do poświadczeń i wpisów tajnych](credential-access-key-vault.md) przechowywanych w [Azure Key Vault](../key-vault/general/overview.md), które mogą być używane przez aplikacje zadań do uzyskiwania dostępu do innych usług. Certyfikat jest używany do udzielenia węzłom puli dostępu do Key Vault.

## <a name="governance-and-compliance"></a>Zarządzanie i zgodność

### <a name="compliance"></a>Zgodność

Aby pomóc klientom w spełnieniu własnych obowiązków dotyczących zgodności w przypadku branżowych i rynków podlegających regulacjom na całym świecie, platforma Azure utrzymuje [duży portfel ofert zgodności](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Oferty te są oparte na różnych typach gwarancji, w tym formalnych certyfikatach, zaświadczeniu, atestacji, autoryzacji i oceny wyprodukowanych przez niezależne przedsiębiorstwa przeprowadzające inspekcje stron trzecich, a także z umownymi zmianami, samooceną i dokumentami wskazówek klienta produkowanymi przez firmę Microsoft. Zapoznaj się z [kompleksowym omówieniem ofert zgodności](https://aka.ms/AzureCompliance) , aby określić, które z nich mogą dotyczyć rozwiązań usługi Batch.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) pomaga wymuszać standardy organizacyjne i oceniać zgodność na dużą skalę. Typowe przypadki użycia Azure Policy obejmują wdrażanie ładu pod kątem spójności zasobów, zgodności z przepisami, bezpieczeństwa, kosztów i zarządzania.

W zależności od trybu alokacji puli i zasobów, do których mają być stosowane zasady, użyj Azure Policy z usługą Batch na jeden z następujących sposobów:

- Bezpośrednio, korzystając z zasobu Microsoft.Batch/batchAccounts. Można użyć podzestawu właściwości konta usługi Batch. Na przykład zasady mogą zawierać poprawne regiony konta usługi Batch, dozwolony tryb alokacji puli i czy dla kont jest włączona sieć publiczna.
- Pośrednio przy użyciu zasobu Microsoft. COMPUTE/virtualMachineScaleSets. Konta usługi Batch z trybem alokacji puli subskrypcji użytkownika mogą mieć ustawione zasady dla zasobów zestawu skalowania maszyn wirtualnych, które są tworzone w ramach subskrypcji konta usługi Batch. Na przykład dozwolone rozmiary maszyn wirtualnych i upewnij się, że niektóre rozszerzenia są uruchamiane w każdym węźle puli.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [punktem odniesienia zabezpieczeń Azure dla usługi Batch](security-baseline.md).
- Przeczytaj więcej [najlepszych rozwiązań dotyczących Azure Batch](best-practices.md).
