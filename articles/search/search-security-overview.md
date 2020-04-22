---
title: Bezpieczeństwo i prywatność danych
titleSuffix: Azure Cognitive Search
description: Usługa Azure Cognitive Search jest zgodna z certyfikatami SOC 2, HIPAA i innymi certyfikatami. Szyfrowanie połączeń i danych, uwierzytelnianie i dostęp do tożsamości za pośrednictwem identyfikatorów zabezpieczeń użytkowników i grup w wyrażeniach filtrów.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 4db9624fbc71e48fcc10ae1d9a1d700d301248a2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759533"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Bezpieczeństwo i prywatność danych w usłudze Azure Cognitive Search

Kompleksowe funkcje zabezpieczeń i kontroli dostępu są wbudowane w usługi Azure Cognitive Search, aby zapewnić, że zawartość prywatna pozostaje w ten sposób. W tym artykule wylicza się, że funkcje zabezpieczeń i zgodność ze standardami są wbudowane w usługę Azure Cognitive Search.

Architektura zabezpieczeń usługi Azure Cognitive Search obejmuje zabezpieczenia fizyczne, szyfrowane transmisje, szyfrowaną pamięć masową i zgodność ze standardami dla całej platformy. Pod względem operacyjnym usługa Azure Cognitive Search akceptuje tylko uwierzytelnione żądania. Opcjonalnie można dodać formanty dostępu dla użytkowników zawartości za pomocą filtrów zabezpieczeń. W tym artykule otuchy dotyczące zabezpieczeń w każdej warstwie, ale koncentruje się przede wszystkim na tym, jak dane i operacje są zabezpieczone w usłudze Azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Zgodność z normami: ISO 27001, SOC 2, HIPAA

Usługa Azure Cognitive Search posiada certyfikaty dla następujących standardów, zgodnie [z zapowiedzią z czerwca 2018 r.:](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Zgodność z SOC 2 Typu 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Aby uzyskać pełny raport, przejdź do [platformy Azure — i raportu typu II usługi Azure Government SOC 2.](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) 
+ [Ustawa o przenoszeniu i odpowiedzialności ubezpieczeń zdrowotnych (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Część 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST (HITRUST)](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS poziom 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

Zgodność ze standardami ma zastosowanie do ogólnie dostępnych funkcji. Funkcje w wersji zapoznawczej są certyfikowane po przejściu do ogólnej dostępności i nie mogą być używane w rozwiązaniach spełniających surowe wymagania norm. Certyfikacja zgodności jest udokumentowana w [przeglądarce zgodności platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i Centrum [zaufania.](https://www.microsoft.com/en-us/trustcenter) 

## <a name="encrypted-transmission-and-storage"></a>Szyfrowana transmisja i przechowywanie

Szyfrowanie rozciąga się na cały potok indeksowania: od połączeń, przez transmisję i aż do indeksowanych danych przechowywanych w usłudze Azure Cognitive Search.

| Warstwa zabezpieczeń | Opis |
|----------------|-------------|
| Szyfrowanie podczas transferu <br>(HTTPS/SSL/TLS) | Usługa Azure Cognitive Search nasłuchuje na porcie HTTPS 443. Na całej platformie połączenia z usługami platformy Azure są szyfrowane. <br/><br/>Wszystkie interakcje usługi Azure Cognitive Search z usługą klienta używają szyfrowania SSL/TLS 1.2. Wcześniejsze wersje (1.0 lub 1.1) nie są obsługiwane.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez firmę Microsoft | Szyfrowanie jest w pełni internalizacji w procesie indeksowania, bez wymiernego wpływu na indeksowanie czasu do zakończenia lub rozmiar indeksu. Występuje automatycznie na wszystkich indeksowania, w tym na przyrostowe aktualizacje indeksu, który nie jest w pełni zaszyfrowany (utworzony przed styczniem 2018).<br><br>Wewnętrznie szyfrowanie jest oparte na [szyfrowaniu usługi Azure Storage Service,](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)przy użyciu 256-bitowego [szyfrowania AES.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)<br><br> Szyfrowanie jest wewnętrzne w usłudze Azure Cognitive Search, z certyfikatami i kluczami szyfrowania zarządzanymi wewnętrznie przez firmę Microsoft i powszechnie stosowanymi. Nie można włączać ani wyłączać szyfrowania, zarządzać lub zastępować własnych kluczy ani wyświetlać ustawień szyfrowania w portalu ani programowo.<br><br>Szyfrowanie w spoczynku zostało ogłoszone 24 stycznia 2018 r. i dotyczy wszystkich warstw usług, w tym warstwy bezpłatnej, we wszystkich regionach. Aby zapewnić pełne szyfrowanie, indeksy utworzone przed tą datą muszą zostać usunięte i przebudowane, aby można było je uzyskać. W przeciwnym razie tylko nowe dane dodane po 24 stycznia są szyfrowane.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez klienta | Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest teraz ogólnie dostępne dla usług wyszukiwania utworzonych w styczniu 2019 r. lub później. Nie jest obsługiwana w bezpłatnych (udostępnionych) usługach.<br><br>Indeksy usługi Azure Cognitive Search i mapy synonimów mogą być teraz szyfrowane za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault. Aby dowiedzieć się więcej, zobacz [Zarządzanie kluczami szyfrowania w usłudze Azure Cognitive Search](search-security-manage-encryption-keys.md).<br><br>Ta funkcja nie zastępuje domyślnego szyfrowania w spoczynku, ale jest stosowana oprócz niej.<br><br>Włączenie tej funkcji zwiększy rozmiar indeksu i obniży wydajność kwerendy. Na podstawie dotychczasowych obserwacji można oczekiwać, aby zobaczyć wzrost o 30%-60% w czasie kwerendy, chociaż rzeczywista wydajność będzie się różnić w zależności od definicji indeksu i typów zapytań. Ze względu na ten wpływ na wydajność zaleca się włączenie tej funkcji tylko w indeksach, które naprawdę jej wymagają.

## <a name="azure-wide-user-access-controls"></a>Kontrolki dostępu użytkowników w całej usłudze Azure

Kilka mechanizmów zabezpieczeń są dostępne w całej platformie Azure, a tym samym automatycznie dostępne dla zasobów usługi Azure Cognitive Search, które tworzysz.

+ [Blokuje na poziomie subskrypcji lub zasobu, aby zapobiec usunięciu](../azure-resource-manager/management/lock-resources.md)
+ [Kontrola dostępu oparta na rolach (RBAC) w celu kontrolowania dostępu do informacji i operacji administracyjnych](../role-based-access-control/overview.md)

Wszystkie usługi platformy Azure obsługują kontroli dostępu oparte na rolach (RBAC) do ustawiania poziomów dostępu konsekwentnie we wszystkich usługach. Na przykład wyświetlanie poufnych danych, takich jak klucz administratora, jest ograniczona do ról właściciel i współautor. Jednak wyświetlanie stanu usługi jest dostępne dla członków dowolnej roli. RBAC udostępnia role właściciela, współautora i czytelnika. Domyślnie wszyscy administratorzy usług są członkami roli Właściciel.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Dostęp do punktu końcowego

### <a name="public-access"></a>Dostęp publiczny

Usługa Azure Cognitive Search dziedziczy zabezpieczenia platformy Azure i zapewnia własne uwierzytelnianie oparte na kluczu. Api-key to ciąg składający się z losowo generowanych cyfr i liter. Typ klucza (administratora lub kwerendy) określa poziom dostępu. Przesyłanie prawidłowego klucza jest uważane za dowód, że żądanie pochodzi od zaufanej jednostki. 

Istnieją dwa poziomy dostępu do usługi wyszukiwania, włączone przez dwa typy kluczy:

* Dostęp administratora (ważny dla każdej operacji odczytu i zapisu w usłudze)
* Dostęp do kwerend (prawidłowy dla operacji tylko do odczytu, takich jak kwerendy, względem kolekcji dokumentów indeksu)

*Klucze administracyjne* są tworzone podczas inicjowania obsługi administracyjnej usługi. Istnieją dwa klucze administratora, wyznaczone jako *podstawowe* i *pomocnicze,* aby utrzymać je prosto, ale w rzeczywistości są wymienne. Każda usługa ma dwa klucze administratora, dzięki czemu można przerzucić jeden bez utraty dostępu do usługi. Można okresowo [ponownie wygenerować klucz administratora](search-security-api-keys.md#regenerate-admin-keys) zgodnie z najlepszymi rozwiązaniami dotyczących zabezpieczeń platformy Azure, ale nie można dodać do całkowitej liczby kluczy administratora. Istnieją maksymalnie dwa klucze administratora na usługę wyszukiwania.

*Klucze kwerendy* są tworzone zgodnie z potrzebami i są przeznaczone dla aplikacji klienckich, które wystawiają kwerendy. Można utworzyć maksymalnie 50 kluczy zapytań. W kodzie aplikacji można określić adres URL wyszukiwania i klucz interfejsu api kwerendy, aby umożliwić dostęp tylko do odczytu do kolekcji dokumentów określonego indeksu. Razem punkt końcowy, klucz interfejsu API dla dostępu tylko do odczytu i indeks docelowy zdefiniować zakres i poziom dostępu połączenia z aplikacji klienckiej.

Uwierzytelnianie jest wymagane dla każdego żądania, gdzie każde żądanie składa się z klucza obowiązkowego, operacji i obiektu. Po połączeniu w łańcuchu dwa poziomy uprawnień (pełny lub tylko do odczytu) oraz kontekst (na przykład operacja kwerendy w indeksie) są wystarczające do zapewnienia zabezpieczeń pełnego spektrum operacji usługi. Aby uzyskać więcej informacji o kluczach, zobacz [Tworzenie kluczy interfejsu API i zarządzanie nimi](search-security-api-keys.md).

### <a name="restricted-access"></a>Ograniczony dostęp

Gdy masz usługę publiczną i chcesz ograniczyć korzystanie z usługi, możesz użyć reguły ograniczenia IP w wersji interfejsu API management REST: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule pozwala ograniczyć dostęp do usługi, identyfikując adresy IP, indywidualnie lub w zakresie, który chcesz udzielić dostępu do usługi wyszukiwania. 

### <a name="private-access"></a>Dostęp prywatny

[Prywatne punkty końcowe](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) dla usługi Azure Cognitive Search umożliwiają klientowi w sieci wirtualnej bezpieczny dostęp do danych w indeksie wyszukiwania za pośrednictwem [łącza prywatnego.](https://docs.microsoft.com/azure/private-link/private-link-overview) Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu.

[Usługa Azure Virtual Network (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) umożliwia bezpieczną komunikację między zasobami za pomocą sieci lokalnej, a także Internetu. 

## <a name="index-access"></a>Dostęp do indeksu

W usłudze Azure Cognitive Search pojedynczy indeks nie jest obiektem zabezpieczanym. Zamiast tego dostęp do indeksu jest określany w warstwie usługi (dostęp do odczytu lub zapisu), wraz z kontekstem operacji.

W przypadku dostępu użytkownika końcowego można struktury żądań kwerendy do łączenia się przy użyciu klucza kwerendy, który sprawia, że każde żądanie tylko do odczytu i dołączyć określonego indeksu używanego przez aplikację. W żądaniu kwerendy nie istnieje pojęcie dołączania indeksów lub uzyskiwania dostępu do wielu indeksów jednocześnie, więc wszystkie żądania są kierowane na pojedynczy indeks z definicji. W związku z tym konstrukcja samego żądania kwerendy (klucz plus pojedynczy indeks docelowy) definiuje granicę zabezpieczeń.

Administrator i deweloper dostęp do indeksów jest niezróżniły: zarówno potrzebują dostępu do zapisu do tworzenia, usuwania i aktualizowania obiektów zarządzanych przez usługę. Każda osoba, która ma klucz administratora usługi, może odczytać, zmodyfikować lub usunąć dowolny indeks w tej samej usłudze. W celu ochrony przed przypadkowym lub złośliwym usunięciem indeksów osobowa kontrola źródła zasobów kodu jest środkiem zaradczym w przypadku odwrócenia niechcianego usunięcia lub modyfikacji indeksu. Usługa Azure Cognitive Search ma funkcję failover w klastrze, aby zapewnić dostępność, ale nie przechowuje ani nie wykonuje zastrzeżonego kodu używanego do tworzenia lub ładowania indeksów.

W przypadku rozwiązań wielodostępnych wymagających granic zabezpieczeń na poziomie indeksu takie rozwiązania zazwyczaj obejmują warstwę środkową, której klienci używają do obsługi izolacji indeksu. Aby uzyskać więcej informacji na temat przypadku użycia wielodostępnego, zobacz [Wzorce projektowania dla wielodostępnych aplikacji SaaS i usługi Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Authentication

### <a name="admin-access"></a>Dostęp administratora

[Dostęp oparty na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) określa, czy masz dostęp do kontroli nad usługą i jej zawartością. Jeśli jesteś właścicielem lub współautorem usługi Azure Cognitive Search, możesz użyć portalu lub modułu **PowerShell Az.Search** do tworzenia, aktualizowania lub usuwania obiektów w usłudze. Można również użyć [interfejsu API REST zarządzania funkcją rozpoznawania funkcji Azure Cognitive Search Management](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Dostęp użytkowników

Domyślnie dostęp użytkownika do indeksu jest określany przez klucz dostępu w żądaniu kwerendy. Większość deweloperów tworzy i przypisuje [*klucze zapytań*](search-security-api-keys.md) dla żądań wyszukiwania po stronie klienta. Klucz zapytania udziela dostępu do odczytu do całej zawartości w indeksie.

Jeśli wymagana jest szczegółowa kontrola nad zawartością dla użytkownika, można utworzyć filtry zabezpieczeń na zapytaniach, zwracając dokumenty skojarzone z daną tożsamością zabezpieczeń. Zamiast wstępnie zdefiniowanych ról i przypisań ról kontrola dostępu oparta na tożsamości jest implementowana jako *filtr,* który przycina wyniki wyszukiwania dokumentów i zawartości na podstawie tożsamości. W poniższej tabeli opisano dwa podejścia do przycinania wyników wyszukiwania nieautoryzowanej zawartości.

| Podejście | Opis |
|----------|-------------|
|[Przycinanie zabezpieczeń na podstawie filtrów tożsamości](search-security-trimming-for-azure-search.md)  | Dokumenty podstawowy przepływ pracy do implementowania kontroli dostępu do tożsamości użytkownika. Obejmuje dodanie identyfikatorów zabezpieczeń do indeksu, a następnie wyjaśnia filtrowanie względem tego pola, aby przyciąć wyniki zakazanej zawartości. |
|[Przycinanie zabezpieczeń na podstawie tożsamości usługi Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | W tym artykule rozwija się poprzedni artykuł, podając kroki pobierania tożsamości z usługi Azure Active Directory (AAD), jednej z [bezpłatnych usług](https://azure.microsoft.com/free/) na platformie azure w chmurze. |

## <a name="table-permissioned-operations"></a>Tabela: Operacje z uprawnieniami

W poniższej tabeli podsumowano operacje dozwolone w usłudze Azure Cognitive Search i który klucz odblokowuje dostęp do określonej operacji.

| Operacja | Uprawnienia |
|-----------|-------------------------|
| Tworzenie usługi | Posiadacz subskrypcji platformy Azure|
| Skalowanie usługi | Klucz administratora, właściciel rbac lub współautor zasobu  |
| Usuwanie usługi | Klucz administratora, właściciel rbac lub współautor zasobu |
| Tworzenie, modyfikowanie, usuwanie obiektów w usłudze: <br>Indeksy i części składowe (w tym definicje analizatorów, profile oceniania, opcje CORS), indeksatory, źródła danych, synonimy, sugesty. | Klucz administratora, właściciel rbac lub współautor zasobu  |
| Zapytanie o indeks | Klucz administratora lub kwerendy (RBAC nie dotyczy) |
| Informacje o systemie kwerend, takie jak zwracanie statystyk, zliczeń i list obiektów. | Klucz administratora, RBAC na zasobie (właściciel, współautor, czytnik) |
| Zarządzanie kluczami administratora | Klucz administracyjny, właściciel RBAC lub współautor zasobu. |
| Zarządzanie kluczami kwerend |  Klucz administracyjny, właściciel RBAC lub współautor zasobu.  |

## <a name="physical-security"></a>Zabezpieczenia fizyczne

Centra danych firmy Microsoft zapewniają wiodące w branży zabezpieczenia fizyczne i są zgodne z szeroką gamą standardów i przepisów. Aby dowiedzieć się więcej, przejdź na stronę [Globalne centra danych](https://www.microsoft.com/cloud-platform/global-datacenters) lub obejrzyj krótki film wideo na temat zabezpieczeń centrów danych.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zobacz też

+ [Wprowadzenie .NET (pokazuje przy użyciu klucza administratora, aby utworzyć indeks)](search-create-index-dotnet.md)
+ [Wprowadzenie REST (pokazuje użycie klucza administratora do utworzenia indeksu)](search-create-index-rest-api.md)
+ [Kontrola dostępu oparta na tożsamościach przy użyciu filtrów usługi Azure Cognitive Search](search-security-trimming-for-azure-search.md)
+ [Kontrola dostępu oparta na tożsamości usłudze Active Directory przy użyciu filtrów usługi Azure Cognitive Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Cognitive Search](search-filters.md)