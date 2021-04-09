---
title: Omówienie zabezpieczeń
titleSuffix: Azure Cognitive Search
description: Dowiedz się więcej o funkcjach zabezpieczeń w usłudze Azure Wyszukiwanie poznawcze, aby chronić punkty końcowe, zawartość i operacje.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097640"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Omówienie zabezpieczeń dla usługi Azure Wyszukiwanie poznawcze

W tym artykule opisano funkcje zabezpieczeń w usłudze Azure Wyszukiwanie poznawcze chroniące zawartość i operacje.

W przypadku żądań przychodzących wysyłanych do usługi wyszukiwania występuje postęp w zakresie zabezpieczeń chroniący punkt końcowy usługi wyszukiwania: od kluczy interfejsu API w żądaniu do reguł ruchu przychodzącego w zaporze, do prywatnych punktów końcowych, które w pełni chronią usługę za pośrednictwem publicznej sieci Internet.

W przypadku żądań wychodzących do innych usług żądanie dominujące jest wykonywane przez indeksatory odczytujące zawartość ze źródeł zewnętrznych. W parametrach połączenia można podać poświadczenia. Można też skonfigurować tożsamość zarządzaną, aby przeszukiwać zaufaną usługę podczas uzyskiwania dostępu do danych z usługi Azure Storage, Azure SQL, Cosmos DB lub innych źródeł danych platformy Azure. Tożsamość zarządzana zastępuje poświadczenia lub klucze dostępu w połączeniu. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [nawiązywanie połączenia ze źródłem danych przy użyciu tożsamości zarządzanej](search-howto-managed-identities-data-sources.md).

Operacje zapisu w usługach zewnętrznych są następujące: Usługa wyszukiwania zapisuje dane w plikach dziennika, a następnie zapisuje je w usłudze Azure Storage podczas tworzenia magazynów wiedzy, utrwalania pamięci podręcznej i utrwalania sesji debugowania. Inne wywołania między usługami, takie jak Cognitive Services, są wykonywane w sieci wewnętrznej.

Obejrzyj ten krótki film wideo, aby zapoznać się z omówieniem architektury zabezpieczeń i każdej kategorii funkcji.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Bezpieczeństwo sieci

<a name="service-access-and-authentication"></a>

Funkcje zabezpieczeń ruchu przychodzącego chronią punkt końcowy usługi wyszukiwania przez zwiększenie poziomu zabezpieczeń i złożoności. Najpierw wszystkie żądania wymagają klucza interfejsu API w celu uzyskania dostępu uwierzytelnionego. Następnie można opcjonalnie ustawić reguły zapory, które ograniczają dostęp do określonych adresów IP. Aby uzyskać zaawansowaną ochronę, trzecią opcją jest włączenie prywatnego linku platformy Azure w celu ochrony punktu końcowego usługi przed całym ruchem internetowym.

### <a name="public-access-using-api-keys"></a>Dostęp publiczny przy użyciu kluczy interfejsu API

Domyślnie dostęp do usługi wyszukiwania odbywa się za pośrednictwem chmury publicznej, przy użyciu uwierzytelniania opartego na kluczach dla administratorów lub dostępu do zapytań do punktu końcowego usługi wyszukiwania. Przesyłanie prawidłowego klucza jest uważane za potwierdzenie, że żądanie pochodzi od zaufanej jednostki. Uwierzytelnianie oparte na kluczach jest omówione w następnej sekcji.

### <a name="configure-ip-firewalls"></a>Konfigurowanie zapór IP

Aby dodatkowo kontrolować dostęp do usługi wyszukiwania, można utworzyć reguły zapory dla ruchu przychodzącego, które zezwalają na dostęp do określonego adresu IP lub zakresu adresów IP. Wszystkie połączenia klientów muszą mieć dozwolony adres IP lub nastąpi odmowa połączenia.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Przykładowy diagram architektury dla ograniczonego dostępu do adresów IP":::

Za pomocą portalu można [skonfigurować dostęp przychodzący](service-configure-firewall.md).

Alternatywnie możesz użyć interfejsów API REST zarządzania. Począwszy od interfejsu API w wersji 2020-03-13, przy użyciu parametru [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) można ograniczyć dostęp do usługi, IDENTYFIKUJĄC adresy IP pojedynczo lub w zakresie, który ma udzielić dostępu do usługi wyszukiwania.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Izolacja sieci za pomocą prywatnego punktu końcowego (bez ruchu internetowego)

Można ustanowić [prywatny punkt końcowy](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwia klientowi w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md).

Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej na potrzeby połączeń z usługą wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Sieć wirtualna umożliwia bezpieczną komunikację między zasobami, z siecią lokalną oraz Internetem.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Przykładowy diagram architektury dla dostępu do prywatnego punktu końcowego":::

Chociaż to rozwiązanie jest najbezpieczniejsze, korzystanie z dodatkowych usług jest kosztem dodatkowym, dlatego należy upewnić się, że masz jasne zrozumienie korzyści przed nadaniem się do niego. lub więcej informacji o kosztach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/private-link/). Aby uzyskać więcej informacji o tym, jak te składniki współpracują ze sobą, Obejrzyj wideo w górnej części tego artykułu. Pokrycie opcji prywatnego punktu końcowego zaczyna się od 5:48 do wideo. Aby uzyskać instrukcje dotyczące sposobu konfigurowania punktu końcowego, zobacz [Tworzenie prywatnego punktu końcowego dla wyszukiwanie poznawcze platformy Azure](service-create-private-endpoint.md).

## <a name="authentication"></a>Authentication

W przypadku żądań przychodzących do usługi Search uwierzytelnianie odbywa się za pośrednictwem [obowiązkowego klucza interfejsu API](search-security-api-keys.md) (ciąg składający się z losowo wygenerowanych liczb i liter), które udowadnia żądanie pochodzi z wiarygodnego źródła. Wyszukiwanie poznawcze obecnie nie obsługuje uwierzytelniania Azure Active Directory dla żądań przychodzących.

Żądania wychodzące wykonywane przez indeksatora podlegają uwierzytelnianiu przez usługę zewnętrzną. Podusługa indeksatora w Wyszukiwanie poznawcze może być zaufaną usługą na platformie Azure, łącząc się z innymi usługami przy użyciu tożsamości zarządzanej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia indeksatora ze źródłem danych przy użyciu tożsamości zarządzanej](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Autoryzacja

Wyszukiwanie poznawcze udostępnia różne modele autoryzacji do zarządzania zawartością i zarządzania usługami. 

### <a name="authorization-for-content-management"></a>Autoryzacja do zarządzania zawartością

Autoryzacja do zawartości i operacje związane z zawartością to uprawnienia do zapisu, które są przekazywane za pomocą [klucza interfejsu API](search-security-api-keys.md) dostarczonego w żądaniu. Klucz interfejsu API jest mechanizmem uwierzytelniania, ale również zezwala na dostęp w zależności od typu klucza interfejsu API.

+ Klucz administratora (zezwala na dostęp do odczytu i zapisu dla operacji Create-Read-Update-Delete w usłudze Search), utworzony podczas aprowizacji usługi

+ Klucz zapytania (umożliwia dostęp tylko do odczytu do kolekcji dokumentów indeksu) utworzony w razie potrzeby i przeznaczony dla aplikacji klienckich, które wysyłają zapytania

W obszarze kod aplikacji należy określić punkt końcowy i klucz interfejsu API, aby umożliwić dostęp do zawartości i opcji. Punktem końcowym może być sama usługa, kolekcja indeksów, określony indeks, kolekcja dokumentów lub określony dokument. W łańcuchu połączonym ze sobą punkt końcowy, operacja (na przykład żądanie utworzenia lub aktualizacji) oraz poziom uprawnień (prawa pełna lub tylko do odczytu oparte na kluczu) stanowią formułę zabezpieczeń, która chroni zawartość i operacje.

### <a name="controlling-access-to-indexes"></a>Kontrolowanie dostępu do indeksów

Na platformie Azure Wyszukiwanie poznawcze pojedynczym indeksem nie jest obiekt zabezpieczany. Zamiast tego dostęp do indeksu jest określany na poziomie warstwy usługi (dostęp do odczytu lub zapisu na podstawie podanego klucza interfejsu API) wraz z kontekstem operacji.

W przypadku dostępu tylko do odczytu można przeprowadzić strukturę żądań zapytań w celu nawiązania połączenia przy użyciu [klucza zapytania](search-security-rbac.md)i dołączyć określony indeks używany przez aplikację. W żądaniu zapytania nie istnieje koncepcja sprzęgania indeksów lub uzyskiwania dostępu do wielu indeksów jednocześnie, więc wszystkie żądania są kierowane do jednego indeksu według definicji. W związku z tym konstruowanie samego żądania zapytania (klucz Plus pojedynczy indeks docelowy) definiuje granicę zabezpieczeń.

Dostęp administratora i dewelopera do indeksów jest niezróżnicowany: oba muszą mieć dostęp do zapisu, aby tworzyć, usuwać i aktualizować obiekty zarządzane przez usługę. Każda osoba mająca [klucz administracyjny](search-security-rbac.md) do usługi może odczytywać, modyfikować lub usuwać dowolne indeksy w tej samej usłudze. W celu ochrony przed przypadkowym lub złośliwym usunięciem indeksów, wewnętrzna kontrola źródła dla zasobów kodu jest środkiem do odwrócenia niechcianego usunięcia lub modyfikacji indeksu. Usługa Azure Wyszukiwanie poznawcze w klastrze działa w trybie failover w celu zapewnienia dostępności, ale nie przechowuje ani nie wykonuje własnego kodu używanego do tworzenia lub ładowania indeksów.

W przypadku rozwiązań wielodostępnych wymagających granic zabezpieczeń na poziomie indeksu takie rozwiązania zwykle obejmują warstwę środkową, której klienci używają do obsługi izolacji indeksów. Aby uzyskać więcej informacji na temat wielodostępnego przypadku użycia, zobacz [wzorce projektowania dla wielodostępnych aplikacji SaaS i platformy Azure wyszukiwanie poznawcze](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Kontrolowanie dostępu do dokumentów

Jeśli potrzebujesz szczegółowej kontroli dla poszczególnych użytkowników nad wynikami wyszukiwania, możesz utworzyć filtry zabezpieczeń dla zapytań, zwracając dokumenty skojarzone z daną tożsamością zabezpieczeń. 

Ze koncepcyjnym odpowiednikiem "zabezpieczenia na poziomie wiersza" Autoryzacja zawartości w indeksie nie jest natywnie obsługiwana przy użyciu wstępnie zdefiniowanych ról lub przypisań ról, które są mapowane na jednostki w Azure Active Directory. Wszelkie uprawnienia użytkownika dotyczące danych w systemach zewnętrznych, takich jak Cosmos DB, nie są transferowane z danymi, ponieważ są one indeksowane przez Wyszukiwanie poznawcze.

Obejścia dla rozwiązań, które wymagają "zabezpieczeń na poziomie wiersza", obejmuje utworzenie pola w źródle danych, które reprezentuje grupę zabezpieczeń lub tożsamość użytkownika, a następnie użycie filtrów w Wyszukiwanie poznawcze, aby selektywnie przycinał wyniki wyszukiwania dokumentów i zawartości na podstawie tożsamości. W poniższej tabeli opisano dwa podejścia do przycinania wyników wyszukiwania nieautoryzowanej zawartości.

| Podejście | Opis |
|----------|-------------|
|[Przycinanie zabezpieczeń na podstawie filtrów tożsamości](search-security-trimming-for-azure-search.md)  | Dokumentuje podstawowy przepływ pracy dotyczący implementowania kontroli dostępu do tożsamości użytkownika. Obejmuje ona Dodawanie identyfikatorów zabezpieczeń do indeksu, a następnie objaśnia filtrowanie względem tego pola, aby przyciąć wyniki zabronionej zawartości. |
|[Przycinanie zabezpieczeń oparte na tożsamościach Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Ten artykuł został rozbudowany w poprzednim artykule zawierającym kroki pobierania tożsamości z usługi Azure Active Directory (Azure AD), jednej z [bezpłatnych usług](https://azure.microsoft.com/free/) na platformie Azure w chmurze. |

### <a name="authorization-for-service-management"></a>Autoryzacja do zarządzania usługami

Operacje zarządzania usługami są autoryzowane za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md). Azure RBAC to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) na potrzeby aprowizacji zasobów platformy Azure. 

W usłudze Azure Wyszukiwanie poznawcze Menedżer zasobów służy do tworzenia lub usuwania usługi, zarządzania kluczami interfejsu API i skalowania usługi. W związku z tym przypisania ról platformy Azure określają, kto może wykonywać te zadania, niezależnie od tego, czy korzystają one z [portalu](search-manage.md), [programu PowerShell](search-manage-powershell.md)czy [interfejsów API REST zarządzania](/rest/api/searchmanagement/search-howto-management-rest-api).

[Trzy role podstawowe](search-security-rbac.md#management-tasks-by-role) są zdefiniowane w celu administrowania usługą wyszukiwania. Przypisania ról można wykonać przy użyciu dowolnej obsługiwanej metodologii (portalu, programu PowerShell itd.) i są one honorowane w całej usłudze. Role właściciela i współautora mogą wykonywać różne funkcje administracyjne. Rolę czytelnika można przypisać do użytkowników, którzy wyświetlają tylko istotne informacje.

> [!Note]
> Korzystając z mechanizmów opartych na platformie Azure, można zablokować subskrypcję lub zasób, aby zapobiec przypadkowemu lub nieautoryzowanemu usunięciu usługi wyszukiwania przez użytkowników z uprawnieniami administratora. Aby uzyskać więcej informacji, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanemu usunięciu](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Ochrona danych

W warstwie magazynu szyfrowanie danych jest wbudowane dla całej zawartości zarządzanej przez usługę, która została zapisana na dysku, w tym indeksów, map synonimów i definicji indeksatorów, źródeł danych i umiejętności. Opcjonalnie można dodać klucze zarządzane przez klienta (CMK) na potrzeby dodatkowej szyfrowania indeksowanej zawartości. W przypadku usług utworzonych po sierpniu 1 2020 szyfrowanie CMK rozciąga się na dane na dyskach tymczasowych, aby uzyskać pełne "podwójne szyfrowanie" indeksowanej zawartości.

### <a name="data-in-transit"></a>Dane przesyłane

Na platformie Azure Wyszukiwanie poznawcze szyfrowanie rozpoczyna się z połączeniami i transmisjami oraz rozciąga się na zawartość przechowywaną na dysku. W przypadku usług wyszukiwania w publicznej sieci Internet usługa Azure Wyszukiwanie poznawcze nasłuchuje na porcie HTTPS 443. Wszystkie połączenia klient-usługa korzystają z szyfrowania TLS 1,2. Wcześniejsze wersje (1,0 lub 1,1) nie są obsługiwane.

### <a name="encrypted-data-at-rest"></a>Zaszyfrowane dane przechowywane

W przypadku danych obsługiwanych wewnętrznie przez usługę wyszukiwania w poniższej tabeli opisano [modele szyfrowania danych](../security/fundamentals/encryption-models.md). Niektóre funkcje, takie jak magazyn wiedzy, wzbogacanie przyrostowe i indeksowanie oparte na indeksatorach, odczytywanie i zapisywanie struktur danych w innych usługach platformy Azure. Te usługi mają własne poziomy obsługi szyfrowania oddzielone od Wyszukiwanie poznawcze platformy Azure.

| Model | Ponownie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Wymagania&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Ograniczenia | Dotyczy |
|------------------|-------|-------------|--------------|------------|
| szyfrowanie po stronie serwera | Klucze zarządzane przez firmę Microsoft | Brak (wbudowane) | Brak, dostępne we wszystkich warstwach, we wszystkich regionach, dla zawartości utworzonej po styczniu 24 2018. | Zawartość (indeksy i mapy synonimów) i definicje (indeksatory, źródła danych, umiejętności) |
| szyfrowanie po stronie serwera | klucze zarządzane przez klienta | Azure Key Vault | Dostępne w warstwach rozliczanych we wszystkich regionach dla zawartości utworzonej po styczniu 2019. | Zawartość (indeksy i mapy synonimów) na dyskach danych |
| podwójne szyfrowanie po stronie serwera | klucze zarządzane przez klienta | Azure Key Vault | Dostępne w warstwach rozliczanych w wybranych regionach w usługach wyszukiwania po 1 2020 sierpnia. | Zawartość (indeksy i mapy synonimów) na dyskach danych i dyskach tymczasowych |

### <a name="service-managed-keys"></a>Klucze zarządzane przez usługę

Szyfrowanie zarządzane przez usługę jest operacją wewnętrzną firmy Microsoft opartą na [platformie Azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md)przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Odbywa się to automatycznie na wszystkich indeksach, w tym na aktualizacjach przyrostowych indeksów, które nie są w pełni szyfrowane (zostały utworzone przed stycznia 2018).

### <a name="customer-managed-keys-cmk"></a>Klucze zarządzane przez klienta (CMK)

Klucze zarządzane przez klienta wymagają dodatkowej usługi do obciążania, Azure Key Vault, która może znajdować się w innym regionie, ale w ramach tej samej subskrypcji, co platforma Azure Wyszukiwanie poznawcze. Włączenie szyfrowania CMK spowoduje zwiększenie rozmiaru indeksu i spadek wydajności zapytań. Na podstawie obserwacji do daty można oczekiwać, że w czasie wykonywania zapytań zostanie wyświetlony wzrost o 30%-60%, chociaż Rzeczywista wydajność będzie się różnić w zależności od definicji indeksu i typów zapytań. Ze względu na ten wpływ na wydajność zalecamy włączenie tej funkcji tylko w przypadku indeksów, które naprawdę wymagają tego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kluczy szyfrowania zarządzanych przez klienta w usłudze Azure wyszukiwanie poznawcze](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Podwójne szyfrowanie

Na platformie Azure Wyszukiwanie poznawcze podwójne szyfrowanie jest rozszerzeniem CMK. Należy zrozumieć, że jest to szyfrowanie dwustronne (raz przez CMK i ponownie przez klucze zarządzane przez usługę) oraz kompleksowy zakres, obejmujący magazyn długoterminowy, który jest zapisywany na dysku z danymi, i krótkoterminowe przechowywanie zapisywanych na dyskach tymczasowych. Różnica między CMK przed sierpnia 1 2020 i po nim, a co czyni CMK funkcją podwójnego szyfrowania w usłudze Azure Wyszukiwanie poznawcze, to dodatkowe szyfrowanie danych na dyskach tymczasowych.

Podwójne szyfrowanie jest obecnie dostępne w nowych usługach, które są tworzone w tych regionach po 1 sierpnia:

+ Zachodnie stany USA 2
+ East US
+ South Central US
+ US Gov Wirginia
+ US Gov Arizona

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

### <a name="api-keys"></a>Klucze interfejsu API

Zależność od uwierzytelniania opartego na kluczach interfejsu API oznacza, że należy zaplanować ponowne generowanie klucza administratora w regularnych odstępach czasu zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń platformy Azure. Istnieje maksymalnie dwa klucze administratora dla każdej usługi wyszukiwania. Aby uzyskać więcej informacji na temat zabezpieczania kluczy interfejsu API i zarządzania nimi, zobacz [Tworzenie kluczy interfejsu API i zarządzanie nimi](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Dzienniki aktywności i diagnostyczne

Wyszukiwanie poznawcze nie rejestruje tożsamości użytkowników, dlatego nie można odwołać się do dzienników w celu uzyskania informacji o określonym użytkowniku. Jednak usługa wykonuje operacje Create-Read-Update-Delete, które mogą być skorelowane z innymi dziennikami, aby zrozumieć Agencję określonych działań.

Korzystając z alertów i infrastruktury rejestrowania na platformie Azure, można pobrać wartości skoków woluminów zapytań lub inne akcje odbiegające od oczekiwanych obciążeń. Aby uzyskać więcej informacji na temat konfigurowania dzienników, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md) i [monitorowanie żądań zapytań](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certyfikaty i zgodność

Usługa Azure Wyszukiwanie poznawcze uczestniczy w regularnych inspekcjach i ma certyfikat dla wielu globalnych, regionalnych i branżowych standardów dla chmury publicznej i Azure Government. Aby zapoznać się z pełną listą, Microsoft Azure Pobierz oficjalny dokument dotyczący [ **ofert zgodności**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na stronie oficjalne raporty inspekcji.

Aby zapewnić zgodność, można użyć [Azure Policy](../governance/policy/overview.md) do wdrożenia najlepszych rozwiązań dotyczących [zabezpieczeń platformy Azure](../security/benchmarks/introduction.md). Usługa Azure Security test jest zbiorem zaleceń dotyczących zabezpieczeń, które zostały zamapowane na funkcje kontroli zabezpieczeń, które mapują na kluczowe akcje, które należy podjąć w celu ograniczenia zagrożeń dla usług i danych. Obecnie istnieją 11 kontroli zabezpieczeń, w tym [zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md), [Rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md)oraz [Ochrona danych](../security/benchmarks/security-control-data-protection.md) .

Azure Policy to funkcja wbudowana w platformę Azure, która ułatwia zarządzanie zgodnością wielu standardów, w tym z cenami testów zabezpieczeń platformy Azure. W przypadku dobrze znanych wzorców Azure Policy udostępnia wbudowane definicje, które zawierają zarówno kryteria, jak i odpowiedzi z możliwością podejmowania działań, które nie są zgodne.

W przypadku usługi Azure Wyszukiwanie poznawcze istnieje obecnie jedna wbudowana definicja. Służy do rejestrowania diagnostycznego. Za pomocą tego wbudowanego programu można przypisać zasady, które identyfikują dowolną usługę wyszukiwania, w której brakuje rejestrowania diagnostycznego, a następnie włącza ją. Aby uzyskać więcej informacji, zobacz [Azure Policy kontroli zgodności z przepisami dla wyszukiwanie poznawcze platformy Azure](security-controls-policy.md).

## <a name="see-also"></a>Zobacz też

+ [Podstawy zabezpieczeń platformy Azure](../security/fundamentals/index.yml)
+ [Zabezpieczenia platformy Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)