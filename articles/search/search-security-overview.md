---
title: Bezpieczeństwo i prywatność danych
titleSuffix: Azure Cognitive Search
description: Usługa Azure Wyszukiwanie poznawcze jest zgodna z SOC 2, HIPAA i innymi certyfikatami. Połączenie i szyfrowanie danych, uwierzytelnianie i dostęp do tożsamości za poorednictwem identyfikatorów zabezpieczeń użytkowników i grup w wyrażeniach filtru.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 68355ac4238aba3deaa951881bc164fe9dc08e28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183436"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Bezpieczeństwo i prywatność danych na platformie Azure Wyszukiwanie poznawcze

Kompleksowe funkcje zabezpieczeń i kontroli dostępu są wbudowane w usługę Azure Wyszukiwanie poznawcze, aby zapewnić, że prywatna zawartość pozostaje w ten sposób. W tym artykule wymieniono funkcje zabezpieczeń i standardy zgodności wbudowane w usługę Azure Wyszukiwanie poznawcze.

Architektura zabezpieczeń Wyszukiwanie poznawcze platformy Azure obejmuje zabezpieczenia fizyczne, zaszyfrowane transporty, zaszyfrowane magazyny i zgodność ze standardami na poziomie platformy. W ramach tej operacji usługa Azure Wyszukiwanie poznawcze akceptuje tylko uwierzytelnione żądania. Opcjonalnie można dodać kontrolę dostępu dla poszczególnych użytkowników do zawartości za poorednictwem filtrów zabezpieczeń. Ten artykuł dotyka zabezpieczeń w każdej warstwie, ale głównie koncentruje się na sposobie zabezpieczania danych i operacji na platformie Azure Wyszukiwanie poznawcze.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Zgodność ze standardami: ISO 27001, SOC 2, HIPAA

Usługa Azure Wyszukiwanie poznawcze ma certyfikat dla następujących standardów, zgodnie z [ogłoszeniem w czerwcu 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Zgodność SOC 2 typu 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Aby uzyskać pełny raport, przejdź do [raportu Azure-i Azure Government SOC 2 Type II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Przenośność i odpowiedzialność z tytułu odpowiedzialności ubezpieczeniowej (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR część 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS poziom 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

Zgodność ze standardami dotyczy ogólnie dostępnych funkcji. Funkcje w wersji zapoznawczej są certyfikowane, gdy przechodzą do ogólnej dostępności i nie mogą być używane w rozwiązaniach mających ścisłe wymagania dotyczące standardów. Certyfikacja zgodności jest udokumentowana w temacie [Omówienie zgodności Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Zaszyfrowana transmisja i magazyn

Szyfrowanie rozciąga się w całym potoku indeksowania: od połączeń, transmisji i w dół do indeksowanych danych przechowywanych w usłudze Azure Wyszukiwanie poznawcze.

| Warstwa zabezpieczeń | Opis |
|----------------|-------------|
| Szyfrowanie podczas transferu <br>(HTTPS/TLS) | Usługa Azure Wyszukiwanie poznawcze nasłuchuje na porcie HTTPS 443. Na całej platformie połączenia z usługami platformy Azure są szyfrowane. <br/><br/>Wszystkie interakcje z usługą Azure Wyszukiwanie poznawcze na klientach używają szyfrowania TLS 1,2. Wcześniejsze wersje (1,0 lub 1,1) nie są obsługiwane.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez firmę Microsoft | Szyfrowanie jest w pełni wewnętrzne w procesie indeksowania, bez wymiernego wpływu na indeksowanie czasu do ukończenia lub rozmiaru indeksu. Odbywa się to automatycznie na wszystkich indeksach, w tym w przypadku aktualizacji przyrostowych, do indeksu, który nie jest w pełni szyfrowany (utworzony przed stycznia 2018).<br><br>Wewnętrznie szyfrowanie jest oparte na [usłudze Azure szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Szyfrowanie jest wewnętrzne dla Wyszukiwanie poznawcze platformy Azure, z certyfikatami i kluczami szyfrowania, które są zarządzane wewnętrznie przez firmę Microsoft i stosowane uniwersalnie. Nie można włączać ani wyłączać szyfrowania, zarządzać nimi ani zastępować własnych kluczy lub wyświetlać ustawienia szyfrowania w portalu lub programowo.<br><br>Szyfrowanie w spoczynku zostało ogłoszone w 24 stycznia 2018 i ma zastosowanie do wszystkich warstw usług, w tym warstwy Bezpłatna, we wszystkich regionach. Aby można było przeprowadzić pełne szyfrowanie, indeksy utworzone przed tą datą muszą zostać porzucone i ponownie skompilowane w celu zaszyfrowania. W przeciwnym razie tylko nowe dane dodane po 24 stycznia są szyfrowane.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez klienta | Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest teraz ogólnie dostępne dla usług wyszukiwania utworzonych w dniu lub po stycznia 2019. Nie jest obsługiwana w przypadku usług bezpłatnych (udostępnionych).<br><br>Usługi Azure Wyszukiwanie poznawcze Indexes i mapy synonimów mogą teraz być szyfrowane w spoczynku z kluczami zarządzanymi przez klienta w programie Azure Key Vault. Aby dowiedzieć się więcej, zobacz [Zarządzanie kluczami szyfrowania w usłudze Azure wyszukiwanie poznawcze](search-security-manage-encryption-keys.md).<br><br>Ta funkcja nie zastępuje domyślnego szyfrowania w spoczynku, ale zamiast tego stosuje się do niego.<br><br>Włączenie tej funkcji spowoduje zwiększenie rozmiaru indeksu i spadek wydajności zapytań. Na podstawie obserwacji do daty można oczekiwać, że w czasie wykonywania zapytań zostanie wyświetlony wzrost o 30%-60%, chociaż Rzeczywista wydajność będzie się różnić w zależności od definicji indeksu i typów zapytań. Ze względu na ten wpływ na wydajność zalecamy włączenie tej funkcji tylko w przypadku indeksów, które naprawdę wymagają tego.

## <a name="azure-wide-user-access-controls"></a>Kontrola dostępu użytkowników w całej platformie Azure

Dostępne są różne mechanizmy zabezpieczeń platformy Azure, które są automatycznie dostępne dla utworzonych zasobów Wyszukiwanie poznawcze platformy Azure.

+ [Blokuje na poziomie subskrypcji lub zasobu, aby zapobiec usunięciu](../azure-resource-manager/management/lock-resources.md)
+ [Access Control oparte na rolach (RBAC) do kontrolowania dostępu do informacji i operacji administracyjnych](../role-based-access-control/overview.md)

Wszystkie usługi platformy Azure obsługują funkcję kontroli dostępu opartej na rolach (RBAC) na potrzeby spójnego ustawiania poziomów dostępu dla wszystkich usług. Na przykład wyświetlanie poufnych danych, takich jak klucz administratora, jest ograniczone do ról właściciela i współautora. Wyświetlanie stanu usługi jest jednak dostępne dla członków dowolnej roli. RBAC oferuje role właściciela, współautora i czytelnika. Domyślnie wszyscy Administratorzy usługi są członkami roli właściciela.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Dostęp do punktu końcowego

### <a name="public-access"></a>Dostęp publiczny

Usługa Azure Wyszukiwanie poznawcze dziedziczy zabezpieczenia platformy Azure i zapewnia własne uwierzytelnianie oparte na kluczach. Klucz API-Key jest ciągiem zawierającym losowo wygenerowane liczby i litery. Typ klucza (administrator lub zapytanie) określa poziom dostępu. Przesyłanie prawidłowego klucza jest uważane za potwierdzenie, że żądanie pochodzi od zaufanej jednostki. 

Istnieją dwa poziomy dostępu do usługi wyszukiwania, które są obsługiwane przez dwa typy kluczy:

* Dostęp administratora (prawidłowy dla każdej operacji odczytu i zapisu do usługi)
* Dostęp do zapytań (prawidłowy dla operacji tylko do odczytu, takich jak zapytania, względem kolekcji dokumentów indeksu)

*Klucze administratora* są tworzone podczas aprowizacji usługi. Istnieją dwa klucze administracyjne, wyznaczono jako *podstawowa* i *pomocnicza* , aby zapewnić ich bezpośrednie, ale w rzeczywistości są one zamienne. Każda usługa ma dwa klucze administracyjne, aby można było ją wycofać bez utraty dostępu do usługi. [Klucz administratora można wygenerować ponownie](search-security-api-keys.md#regenerate-admin-keys) okresowo według najlepszych rozwiązań w zakresie zabezpieczeń platformy Azure, ale nie można dodać do całkowitej liczby kluczy administratora. Istnieje maksymalnie dwa klucze administratora dla każdej usługi wyszukiwania.

*Klucze zapytań* są tworzone w miarę potrzeby i są przeznaczone dla aplikacji klienckich, które wysyłają zapytania. Można utworzyć maksymalnie 50 kluczy zapytań. W polu kod aplikacji należy określić adres URL wyszukiwania i klucz API-Key, aby zezwolić na dostęp tylko do odczytu do kolekcji dokumenty określonego indeksu. Punkt końcowy, klucz interfejsu API dla dostępu tylko do odczytu i indeks docelowy określają zakres i poziom dostępu połączenia z aplikacji klienckiej.

Dla każdego żądania wymagane jest uwierzytelnianie, gdzie każde żądanie składa się z klucza obowiązkowego, operacji i obiektu. Po połączeniu ze sobą dwa poziomy uprawnień (pełne lub tylko do odczytu) oraz kontekst (na przykład operacja zapytania na indeksie) są wystarczające do zapewnienia bezpieczeństwa pełnego spektrum operacji usługi. Aby uzyskać więcej informacji na temat kluczy, zobacz [Tworzenie kluczy API i zarządzanie nimi](search-security-api-keys.md).

### <a name="restricted-access"></a>Ograniczony dostęp

Jeśli masz usługę publiczną i chcesz ograniczyć korzystanie z usługi, możesz użyć reguły ograniczeń adresów IP w wersji interfejsu API REST zarządzania: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule umożliwia ograniczenie dostępu do usługi przez identyfikację adresów IP, pojedynczo lub w zakresie, które mają udzielić dostępu do usługi wyszukiwania. 

### <a name="private-access"></a>Dostęp prywatny

[Prywatne punkty końcowe](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) dla systemu Azure wyszukiwanie poznawcze umożliwiają klientowi w sieci wirtualnej bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](https://docs.microsoft.com/azure/private-link/private-link-overview). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

[Usługa Azure Virtual Network (VNET)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) umożliwia bezpieczną komunikację między zasobami, z siecią lokalną oraz Internetem. 

## <a name="index-access"></a>Dostęp do indeksu

Na platformie Azure Wyszukiwanie poznawcze pojedynczym indeksem nie jest obiekt zabezpieczany. Zamiast tego, dostęp do indeksu jest określany w warstwie usług (dostęp do odczytu lub zapisu) wraz z kontekstem operacji.

W przypadku dostępu użytkowników końcowych można określać strukturę żądań zapytań w celu nawiązania połączenia przy użyciu klucza zapytania, co oznacza, że każdy żądania jest tylko do odczytu i zawiera określony indeks używany przez aplikację. W żądaniu zapytania nie istnieje koncepcja sprzęgania indeksów lub uzyskiwania dostępu do wielu indeksów jednocześnie, więc wszystkie żądania są kierowane do jednego indeksu według definicji. W związku z tym konstruowanie samego żądania zapytania (klucz Plus pojedynczy indeks docelowy) definiuje granicę zabezpieczeń.

Dostęp administratora i dewelopera do indeksów jest niezróżnicowany: oba muszą mieć dostęp do zapisu, aby tworzyć, usuwać i aktualizować obiekty zarządzane przez usługę. Każda osoba mająca klucz administracyjny do usługi może odczytywać, modyfikować lub usuwać dowolne indeksy w tej samej usłudze. W celu ochrony przed przypadkowym lub złośliwym usunięciem indeksów, wewnętrzna kontrola źródła dla zasobów kodu jest środkiem do odwrócenia niechcianego usunięcia lub modyfikacji indeksu. Usługa Azure Wyszukiwanie poznawcze w klastrze działa w trybie failover w celu zapewnienia dostępności, ale nie przechowuje ani nie wykonuje własnego kodu używanego do tworzenia lub ładowania indeksów.

W przypadku rozwiązań wielodostępnych wymagających granic zabezpieczeń na poziomie indeksu takie rozwiązania zwykle obejmują warstwę środkową, której klienci używają do obsługi izolacji indeksów. Aby uzyskać więcej informacji na temat wielodostępnego przypadku użycia, zobacz [wzorce projektowania dla wielodostępnych aplikacji SaaS i platformy Azure wyszukiwanie poznawcze](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Uwierzytelnianie

### <a name="admin-access"></a>Dostęp administratora

[Dostęp oparty na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) określa, czy masz dostęp do kontrolek usługi i jej zawartości. Jeśli jesteś właścicielem lub współautorem usługi Azure Wyszukiwanie poznawcze, możesz użyć portalu lub modułu PowerShell **AZ. Search** do tworzenia, aktualizowania lub usuwania obiektów w usłudze. Można również użyć [interfejsu API REST usługi Azure wyszukiwanie poznawcze Management](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Dostęp użytkowników

Domyślnie dostęp użytkownika do indeksu jest określany przez klucz dostępu w żądaniu zapytania. Większość deweloperów tworzy i przypisuje [*klucze zapytań*](search-security-api-keys.md) dla żądań wyszukiwania po stronie klienta. Klucz zapytania umożliwia dostęp do odczytu do całej zawartości w indeksie.

Jeśli potrzebujesz szczegółowej kontroli nad zawartością, możesz utworzyć filtry zabezpieczeń dla zapytań, zwracając dokumenty skojarzone z daną tożsamością zabezpieczeń. Zamiast wstępnie zdefiniowanych ról i przypisań ról kontrola dostępu oparta na tożsamości jest implementowana jako *Filtr* , który przycina wyniki wyszukiwania dokumentów i zawartości na podstawie tożsamości. W poniższej tabeli opisano dwa podejścia do przycinania wyników wyszukiwania nieautoryzowanej zawartości.

| Podejście | Opis |
|----------|-------------|
|[Przycinanie zabezpieczeń na podstawie filtrów tożsamości](search-security-trimming-for-azure-search.md)  | Dokumentuje podstawowy przepływ pracy dotyczący implementowania kontroli dostępu do tożsamości użytkownika. Obejmuje ona Dodawanie identyfikatorów zabezpieczeń do indeksu, a następnie objaśnia filtrowanie względem tego pola, aby przyciąć wyniki zabronionej zawartości. |
|[Przycinanie zabezpieczeń oparte na tożsamościach Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Ten artykuł został rozbudowany w poprzednim artykule, co zapewnia procedurę pobierania tożsamości z usługi Azure Active Directory (AAD), jednej z [bezpłatnych usług](https://azure.microsoft.com/free/) na platformie Azure w chmurze. |

## <a name="table-permissioned-operations"></a>Tabela: operacje z uprawnieniami

Poniższa tabela zawiera podsumowanie operacji dozwolonych w usłudze Azure Wyszukiwanie poznawcze i odblokowanie dostępu do określonej operacji.

| Operacja | Uprawnienia |
|-----------|-------------------------|
| Tworzenie usługi | Posiadacz subskrypcji platformy Azure|
| Skalowanie usługi | Klucz administracyjny, właściciel RBAC lub współautor zasobu  |
| Usuwanie usługi | Klucz administracyjny, właściciel RBAC lub współautor zasobu |
| Tworzenie, modyfikowanie i usuwanie obiektów w usłudze: <br>Indeksy i części składników (w tym definicje analizatora, profile oceniania, opcje CORS), indeksatory, źródła danych, synonimy, sugestie. | Klucz administracyjny, właściciel RBAC lub współautor zasobu  |
| Tworzenie zapytań względem indeksu | Administrator lub klucz zapytania (RBAC nie dotyczy) |
| Badaj informacje o systemie, takie jak zwracanie statystyk, liczników i list obiektów. | Klucz administratora, RBAC dla zasobu (właściciel, współautor, czytelnik) |
| Zarządzaj kluczami administratora | Klucz administracyjny, właściciel RBAC lub współautor zasobu. |
| Zarządzanie kluczami zapytań |  Klucz administracyjny, właściciel RBAC lub współautor zasobu.  |

## <a name="physical-security"></a>Zabezpieczenia fizyczne

Centra danych firmy Microsoft zapewniają wiodące w branży zabezpieczenia fizyczne i są zgodne z obszernym portfelem standardów i przepisów. Aby dowiedzieć się więcej, przejdź do strony [Globalne centra danych](https://www.microsoft.com/cloud-platform/global-datacenters) lub obejrzyj krótkie wideo w zabezpieczeniach centrum danych.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zobacz także

+ [Wprowadzenie do platformy .NET (w celu utworzenia indeksu przy użyciu klucza administratora)](search-create-index-dotnet.md)
+ [Wprowadzenie do usługi REST (w celu utworzenia indeksu przy użyciu klucza administratora)](search-create-index-rest-api.md)
+ [Kontrola dostępu oparta na tożsamościch za pomocą filtrów Wyszukiwanie poznawcze platformy Azure](search-security-trimming-for-azure-search.md)
+ [Active Directory kontroli dostępu opartej na tożsamościach przy użyciu filtrów Wyszukiwanie poznawcze platformy Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)