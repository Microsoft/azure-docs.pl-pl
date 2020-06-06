---
title: Omówienie zabezpieczeń
titleSuffix: Azure Cognitive Search
description: Usługa Azure Wyszukiwanie poznawcze jest zgodna z SOC 2, HIPAA i innymi certyfikatami. Połączenie i szyfrowanie danych, uwierzytelnianie i dostęp do tożsamości za poorednictwem identyfikatorów zabezpieczeń użytkowników i grup w wyrażeniach filtru.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: fb79c3546037aabf5ce60905044901f0d5793990
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465630"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze — Omówienie

W tym artykule opisano kluczowe funkcje zabezpieczeń w usłudze Azure Wyszukiwanie poznawcze, które mogą chronić zawartość i operacje. 

+ W warstwie magazynu szyfrowanie danych w spoczynku jest podane na poziomie platformy, ale Wyszukiwanie poznawcze oferuje także opcję "podwójne szyfrowanie" dla klientów, którzy chcą mieć podwójną ochronę kluczy zarządzanych przez użytkownika i firmę Microsoft.

+ Zabezpieczenia przychodzące chronią punkt końcowy usługi wyszukiwania przy jednoczesnym zwiększeniu poziomu zabezpieczeń: od kluczy interfejsu API w żądaniu do reguł ruchu przychodzącego w zaporze do prywatnych punktów końcowych, które w pełni chronią usługę przed publicznym Internetem.

+ Zabezpieczenia wychodzące dotyczą indeksatorów, które pobierają zawartość ze źródeł zewnętrznych. W przypadku żądań wychodzących Skonfiguruj zarządzaną tożsamość, aby przeszukiwać zaufaną usługę podczas uzyskiwania dostępu do danych z usługi Azure Storage, Azure SQL, Cosmos DB lub innych źródeł danych platformy Azure. Tożsamość zarządzana zastępuje poświadczenia lub klucze dostępu w połączeniu. Zabezpieczenia wychodzące nie zostały omówione w tym artykule. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [nawiązywanie połączenia ze źródłem danych przy użyciu tożsamości zarządzanej](search-howto-managed-identities-data-sources.md).

Obejrzyj ten krótki film wideo, aby zapoznać się z omówieniem architektury zabezpieczeń i każdej kategorii funkcji.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Zaszyfrowane transmisja i magazyn

Szyfrowanie jest rozpowszechnione na platformie Azure Wyszukiwanie poznawcze, począwszy od połączeń i transmisja, rozszerzając do zawartości przechowywanej na dysku. W przypadku usług wyszukiwania w publicznej sieci Internet usługa Azure Wyszukiwanie poznawcze nasłuchuje na porcie HTTPS 443. Wszystkie połączenia klient-usługa korzystają z szyfrowania TLS 1,2. Wcześniejsze wersje (1,0 lub 1,1) nie są obsługiwane.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku

Na platformie Azure Wyszukiwanie poznawcze są przechowywane definicje indeksu i zawartość, definicje źródeł danych, definicje indeksatora, definicje zestawu umiejętności oraz mapy synonimów.

W warstwie magazynu dane są szyfrowane na dysku przy użyciu kluczy zarządzanych przez firmę Microsoft. Nie można włączać ani wyłączać szyfrowania ani wyświetlać ustawień szyfrowania w portalu lub programowo. Szyfrowanie jest w pełni wewnętrzne, bez wymiernego wpływu na indeksowanie czasu do ukończenia lub rozmiaru indeksu. Odbywa się to automatycznie na wszystkich indeksach, w tym w przypadku aktualizacji przyrostowych, do indeksu, który nie jest w pełni szyfrowany (utworzony przed stycznia 2018).

Wewnętrznie szyfrowanie jest oparte na [usłudze Azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md)przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).

> [!NOTE]
> Szyfrowanie w spoczynku zostało ogłoszone w 24 stycznia 2018 i ma zastosowanie do wszystkich warstw usług, w tym warstwy Bezpłatna, we wszystkich regionach. Aby można było przeprowadzić pełne szyfrowanie, indeksy utworzone przed tą datą muszą zostać porzucone i ponownie skompilowane w celu zaszyfrowania. W przeciwnym razie tylko nowe dane dodane po 24 stycznia są szyfrowane.

### <a name="customer-managed-key-cmk-encryption"></a>Szyfrowanie klucza zarządzanego przez klienta (CMK)

Klienci, którzy chcą mieć dodatkową ochronę magazynu, mogą szyfrować dane i obiekty przed ich zapisaniem i zaszyfrowaniem na dysku. To podejście jest oparte na kluczu należącym do użytkownika, zarządzanym i przechowywanym za pośrednictwem Azure Key Vault niezależnie od firmy Microsoft. Szyfrowanie zawartości przed szyfrowaniem na dysku jest określane jako "podwójne szyfrowanie". Obecnie można wybiórczo selektywnie szyfrować indeksy i mapy synonimów. Aby uzyskać więcej informacji, zobacz [klucze szyfrowania zarządzane przez klienta w usłudze Azure wyszukiwanie poznawcze](search-security-manage-encryption-keys.md).

> [!NOTE]
> Szyfrowanie CMK jest ogólnie dostępne dla usług wyszukiwania utworzonych po styczniu 2019. Nie jest obsługiwana w przypadku usług bezpłatnych (udostępnionych). 
>
>Włączenie tej funkcji spowoduje zwiększenie rozmiaru indeksu i spadek wydajności zapytań. Na podstawie obserwacji do daty można oczekiwać, że w czasie wykonywania zapytań zostanie wyświetlony wzrost o 30%-60%, chociaż Rzeczywista wydajność będzie się różnić w zależności od definicji indeksu i typów zapytań. Ze względu na ten wpływ na wydajność zalecamy włączenie tej funkcji tylko w przypadku indeksów, które naprawdę wymagają tego.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Zabezpieczenia i program Endpoint Protection dla ruchu przychodzącego

Funkcje zabezpieczeń ruchu przychodzącego chronią punkt końcowy usługi wyszukiwania przez zwiększenie poziomu zabezpieczeń i złożoności. Najpierw wszystkie żądania wymagają klucza interfejsu API w celu uzyskania dostępu uwierzytelnionego. Następnie można opcjonalnie ustawić reguły zapory, które ograniczają dostęp do określonych adresów IP. Aby uzyskać zaawansowaną ochronę, trzecią opcją jest włączenie prywatnego linku platformy Azure w celu ochrony punktu końcowego usługi przed całym ruchem internetowym.

### <a name="public-access-using-api-keys"></a>Dostęp publiczny przy użyciu kluczy interfejsu API

Domyślnie dostęp do usługi wyszukiwania odbywa się za pośrednictwem chmury publicznej, przy użyciu uwierzytelniania opartego na kluczach dla administratorów lub dostępu do zapytań do punktu końcowego usługi wyszukiwania. Klucz API-Key jest ciągiem zawierającym losowo wygenerowane liczby i litery. Typ klucza (administrator lub zapytanie) określa poziom dostępu. Przesyłanie prawidłowego klucza jest uważane za potwierdzenie, że żądanie pochodzi od zaufanej jednostki. 

Istnieją dwa poziomy dostępu do usługi wyszukiwania, które są obsługiwane przez następujące klucze interfejsu API:

+ Klucz administratora (zezwala na dostęp do odczytu i zapisu dla operacji [Create-Read-Update-Delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) w usłudze wyszukiwania)

+ Klucz zapytania (zezwala na dostęp tylko do odczytu do kolekcji dokumentów indeksu)

*Klucze administratora* są tworzone podczas aprowizacji usługi. Istnieją dwa klucze administracyjne, wyznaczono jako *podstawowa* i *pomocnicza* , aby zapewnić ich bezpośrednie, ale w rzeczywistości są one zamienne. Każda usługa ma dwa klucze administracyjne, aby można było ją wycofać bez utraty dostępu do usługi. [Klucz administratora można wygenerować ponownie](search-security-api-keys.md#regenerate-admin-keys) okresowo według najlepszych rozwiązań w zakresie zabezpieczeń platformy Azure, ale nie można dodać do całkowitej liczby kluczy administratora. Istnieje maksymalnie dwa klucze administratora dla każdej usługi wyszukiwania.

*Klucze zapytań* są tworzone w miarę potrzeby i są przeznaczone dla aplikacji klienckich, które wysyłają zapytania. Można utworzyć maksymalnie 50 kluczy zapytań. W polu kod aplikacji należy określić adres URL wyszukiwania i klucz API-Key, aby zezwolić na dostęp tylko do odczytu do kolekcji dokumenty określonego indeksu. Punkt końcowy, klucz interfejsu API dla dostępu tylko do odczytu i indeks docelowy określają zakres i poziom dostępu połączenia z aplikacji klienckiej.

Dla każdego żądania wymagane jest uwierzytelnianie, gdzie każde żądanie składa się z klucza obowiązkowego, operacji i obiektu. Po połączeniu ze sobą dwa poziomy uprawnień (pełne lub tylko do odczytu) oraz kontekst (na przykład operacja zapytania na indeksie) są wystarczające do zapewnienia bezpieczeństwa pełnego spektrum operacji usługi. Aby uzyskać więcej informacji na temat kluczy, zobacz [Tworzenie kluczy API i zarządzanie nimi](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Dostęp z ograniczeniami do adresów IP

Aby dodatkowo kontrolować dostęp do usługi wyszukiwania, można utworzyć reguły zapory dla ruchu przychodzącego, które zezwalają na dostęp do określonego adresu IP lub zakresu adresów IP. Wszystkie połączenia klientów muszą mieć dozwolony adres IP lub nastąpi odmowa połączenia.

Za pomocą portalu można [skonfigurować dostęp przychodzący](service-configure-firewall.md). 

Alternatywnie możesz użyć interfejsów API REST zarządzania. Interfejs API w wersji 2020-03-13 z parametrem [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) umożliwia ograniczenie dostępu do usługi przez identyfikację adresów IP, pojedynczo lub w zakresie, które mają udzielić dostępu do usługi wyszukiwania. 

### <a name="private-endpoint-no-internet-traffic"></a>Prywatny punkt końcowy (bez ruchu internetowego)

[Prywatny punkt końcowy](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwia klientowi w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). 

Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej na potrzeby połączeń z usługą wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Sieć wirtualna umożliwia bezpieczną komunikację między zasobami, z siecią lokalną oraz Internetem. 

Chociaż to rozwiązanie jest najbezpieczniejsze, korzystanie z dodatkowych usług jest kosztem dodatkowym, dlatego należy upewnić się, że masz jasne zrozumienie korzyści przed nadaniem się do niego. lub więcej informacji o kosztach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/private-link/). Aby uzyskać więcej informacji o tym, jak te składniki współpracują ze sobą, Obejrzyj wideo w górnej części tego artykułu. Pokrycie opcji prywatnego punktu końcowego zaczyna się od 5:48 do wideo. Aby uzyskać instrukcje dotyczące sposobu konfigurowania punktu końcowego, zobacz [Tworzenie prywatnego punktu końcowego dla wyszukiwanie poznawcze platformy Azure](service-create-private-endpoint.md).

## <a name="index-access"></a>Dostęp do indeksu

Na platformie Azure Wyszukiwanie poznawcze pojedynczym indeksem nie jest obiekt zabezpieczany. Zamiast tego dostęp do indeksu jest określany na poziomie warstwy usługi (dostęp do odczytu lub zapisu do usługi) wraz z kontekstem operacji.

W przypadku dostępu użytkowników końcowych można określać strukturę żądań zapytań w celu nawiązania połączenia przy użyciu klucza zapytania, co oznacza, że każdy żądania jest tylko do odczytu i zawiera określony indeks używany przez aplikację. W żądaniu zapytania nie istnieje koncepcja sprzęgania indeksów lub uzyskiwania dostępu do wielu indeksów jednocześnie, więc wszystkie żądania są kierowane do jednego indeksu według definicji. W związku z tym konstruowanie samego żądania zapytania (klucz Plus pojedynczy indeks docelowy) definiuje granicę zabezpieczeń.

Dostęp administratora i dewelopera do indeksów jest niezróżnicowany: oba muszą mieć dostęp do zapisu, aby tworzyć, usuwać i aktualizować obiekty zarządzane przez usługę. Każda osoba mająca klucz administracyjny do usługi może odczytywać, modyfikować lub usuwać dowolne indeksy w tej samej usłudze. W celu ochrony przed przypadkowym lub złośliwym usunięciem indeksów, wewnętrzna kontrola źródła dla zasobów kodu jest środkiem do odwrócenia niechcianego usunięcia lub modyfikacji indeksu. Usługa Azure Wyszukiwanie poznawcze w klastrze działa w trybie failover w celu zapewnienia dostępności, ale nie przechowuje ani nie wykonuje własnego kodu używanego do tworzenia lub ładowania indeksów.

W przypadku rozwiązań wielodostępnych wymagających granic zabezpieczeń na poziomie indeksu takie rozwiązania zwykle obejmują warstwę środkową, której klienci używają do obsługi izolacji indeksów. Aby uzyskać więcej informacji na temat wielodostępnego przypadku użycia, zobacz [wzorce projektowania dla wielodostępnych aplikacji SaaS i platformy Azure wyszukiwanie poznawcze](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Dostęp użytkowników

Sposób, w jaki użytkownik uzyskuje dostęp do indeksu i innych obiektów jest określany przez typ klucza interfejsu API w żądaniu. Większość deweloperów tworzy i przypisuje [*klucze zapytań*](search-security-api-keys.md) dla żądań wyszukiwania po stronie klienta. Klucz zapytania umożliwia dostęp tylko do odczytu do zawartości z możliwością wyszukiwania w indeksie.

Jeśli potrzebujesz szczegółowej kontroli dla poszczególnych użytkowników nad wynikami wyszukiwania, możesz utworzyć filtry zabezpieczeń dla zapytań, zwracając dokumenty skojarzone z daną tożsamością zabezpieczeń. Zamiast wstępnie zdefiniowanych ról i przypisań ról kontrola dostępu oparta na tożsamości jest implementowana jako *Filtr* , który przycina wyniki wyszukiwania dokumentów i zawartości na podstawie tożsamości. W poniższej tabeli opisano dwa podejścia do przycinania wyników wyszukiwania nieautoryzowanej zawartości.

| Podejście | Opis |
|----------|-------------|
|[Przycinanie zabezpieczeń na podstawie filtrów tożsamości](search-security-trimming-for-azure-search.md)  | Dokumentuje podstawowy przepływ pracy dotyczący implementowania kontroli dostępu do tożsamości użytkownika. Obejmuje ona Dodawanie identyfikatorów zabezpieczeń do indeksu, a następnie objaśnia filtrowanie względem tego pola, aby przyciąć wyniki zabronionej zawartości. |
|[Przycinanie zabezpieczeń oparte na tożsamościach Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Ten artykuł został rozbudowany w poprzednim artykule, co zapewnia procedurę pobierania tożsamości z usługi Azure Active Directory (AAD), jednej z [bezpłatnych usług](https://azure.microsoft.com/free/) na platformie Azure w chmurze. |

## <a name="administrative-rights"></a>Prawa administracyjne

[Dostęp oparty na rolach (RBAC)](../role-based-access-control/overview.md) to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) na potrzeby aprowizacji zasobów platformy Azure. W usłudze Azure Wyszukiwanie poznawcze Menedżer zasobów służy do tworzenia lub usuwania usługi, zarządzania kluczami interfejsu API i skalowania usługi. W związku z tym przypisania roli RBAC określają, kto może wykonywać te zadania, bez względu na to, czy korzystają one z [portalu](search-manage.md), [programu PowerShell](search-manage-powershell.md)lub [interfejsów API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

Z kolei prawa administratora do zawartości hostowanej w usłudze, takie jak możliwość tworzenia lub usuwania indeksu, są nałożone przez klucze interfejsu API zgodnie z opisem w [poprzedniej sekcji](#index-access).

> [!TIP]
> Korzystając z mechanizmów opartych na platformie Azure, można zablokować subskrypcję lub zasób, aby zapobiec przypadkowemu lub nieautoryzowanemu usunięciu usługi wyszukiwania przez użytkowników z uprawnieniami administratora. Aby uzyskać więcej informacji, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanemu usunięciu](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certyfikaty i zgodność

Usługa Azure Wyszukiwanie poznawcze ma certyfikowaną zgodność z wieloma standardami globalnymi, regionalnymi i branżowymi dla chmury publicznej i Azure Government. Aby zapoznać się z pełną listą, Microsoft Azure Pobierz oficjalny dokument dotyczący [ **ofert zgodności** ](https://aka.ms/azurecompliance) na stronie oficjalne raporty inspekcji.

## <a name="see-also"></a>Zobacz także

+ [Podstawy zabezpieczeń platformy Azure](../security/fundamentals/index.yml)
+ [Zabezpieczenia platformy Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)