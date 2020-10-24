---
title: Dostęp indeksatora do chronionych zasobów
titleSuffix: Azure Cognitive Search
description: Omówienie pojęć dotyczących zabezpieczeń na poziomie sieci na potrzeby dostępu do danych platformy Azure przez indeksatory na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503491"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Dostęp indeksatora do zawartości chronionej przez funkcje zabezpieczeń sieci platformy Azure (Azure Wyszukiwanie poznawcze)

Indeksatory usługi Azure Wyszukiwanie poznawcze mogą wykonywać wywołania wychodzące do różnych zasobów platformy Azure podczas wykonywania. W tym artykule wyjaśniono koncepcje związane z dostępem indeksatora do zawartości chronionej przez zapory protokołu IP, prywatne punkty końcowe lub inne mechanizmy zabezpieczeń na poziomie sieci platformy Azure. Indeksator wykonuje wywołania wychodzące w dwóch sytuacjach: łączenie ze źródłami danych podczas indeksowania i nawiązywanie połączenia z hermetyzowanym kodem za pomocą zestawu umiejętności. Lista wszystkich możliwych typów zasobów, do których może uzyskać dostęp indeksator w typowym przebiegu, znajduje się w poniższej tabeli.

| Zasób | Przeznaczenie w ramach uruchomienia indeksatora |
| --- | --- |
| Azure Storage (obiekty blob, tabele, ADLS Gen 2) | Źródło danych |
| Azure Storage (obiekty blob, tabele) | Umiejętności (buforowanie wzbogaconych dokumentów i przechowywanie projekcji w sklepie wiedzy) |
| Azure Cosmos DB (różne interfejsy API) | Źródło danych |
| Azure SQL Database | Źródło danych |
| Program SQL Server na maszynach wirtualnych platformy Azure | Źródło danych |
| Wystąpienie zarządzane SQL | Źródło danych |
| Azure Functions | Hostowanie niestandardowych umiejętności interfejsu API sieci Web |
| Cognitive Services | Dołączone do zestawu umiejętności, które będą używane do rozliczania wzbogacenia poza limit 20 bezpłatnych dokumentów |

> [!NOTE]
> Zasób usługi poznawczej dołączony do zestawu umiejętności jest używany do rozliczania na podstawie wzbogaceń i zapisywana w indeksie wyszukiwania. Nie jest on używany do uzyskiwania dostępu do interfejsy API usług Cognitive Services. Dostęp z potoku wzbogacania indeksatora do interfejsy API usług Cognitive Services odbywa się za pośrednictwem wewnętrznego kanału komunikacji zabezpieczonej, w którym dane są silnie zaszyfrowane podczas przesyłania i nigdy nie są przechowywane w stanie spoczynku.

Klienci mogą zabezpieczyć te zasoby za pomocą kilku mechanizmów izolacji sieci oferowanych przez platformę Azure. Z wyjątkiem zasobu usługi poznawczej indeksatory mają ograniczoną możliwość uzyskiwania dostępu do wszystkich innych zasobów nawet wtedy, gdy są odizolowane od sieci, opisane w poniższej tabeli.

| Zasób | Ograniczenie adresów IP | Prywatny punkt końcowy |
| --- | --- | ---- |
| Azure Storage (obiekty blob, tabele, ADLS Gen 2) | Obsługiwane tylko wtedy, gdy konto magazynu i usługa wyszukiwania znajdują się w różnych regionach | Obsługiwane |
| Azure Cosmos DB — interfejs API SQL | Obsługiwane | Obsługiwane |
| Interfejs API Azure Cosmos DB Cassandra, Mongo i Gremlin | Obsługiwane | Nieobsługiwane |
| Azure SQL Database | Obsługiwane | Obsługiwane |
| Program SQL Server na maszynach wirtualnych platformy Azure | Obsługiwane | Nie dotyczy |
| Wystąpienie zarządzane SQL | Obsługiwane | Nie dotyczy |
| Azure Functions | Obsługiwane | Obsługiwane tylko w przypadku niektórych warstw usługi Azure Functions |

> [!NOTE]
> Oprócz opcji wymienionych powyżej w przypadku kont usługi Azure Storage zabezpieczonych za pomocą sieci klienci mogą korzystać z faktu, że usługa Azure Wyszukiwanie poznawcze jest [zaufaną usługą firmy Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services). Oznacza to, że określona usługa wyszukiwania może ominąć ograniczenia sieci wirtualnej lub protokołu IP na koncie magazynu i uzyskać dostęp do danych na koncie magazynu, jeśli na koncie magazynu jest włączona odpowiednia kontrola dostępu oparta na rolach. Aby uzyskać więcej informacji, zobacz [indeksatory połączeń przy użyciu wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md). Tej opcji można użyć zamiast trasy ograniczenia IP, na wypadek gdy konto magazynu lub usługa wyszukiwania nie może zostać przeniesione do innego regionu.

Podczas wybierania mechanizmu bezpiecznego dostępu, którego indeksator powinien używać, należy wziąć pod uwagę następujące ograniczenia:

- Indeksator nie może połączyć się z [punktem końcowym usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). Publiczne punkty końcowe z poświadczeniami, prywatnymi punktami końcowymi, zaufaną usługą i adresami IP są jedynymi obsługiwanymi metodologiami dla połączeń indeksatora.
- Nie można zainicjować obsługi administracyjnej usługi wyszukiwania w określonej sieci wirtualnej, działającej natywnie na maszynie wirtualnej. Ta funkcja nie będzie oferowana przez usługę Azure Wyszukiwanie poznawcze.
- Gdy indeksatory wykorzystują (wychodzące) prywatne punkty końcowe do uzyskiwania dostępu do zasobów, mogą zostać naliczone dodatkowe [opłaty za linki prywatne](https://azure.microsoft.com/pricing/details/search/) .

## <a name="indexer-execution-environment"></a>Środowisko wykonywania indeksatora

Indeksatory usługi Azure Wyszukiwanie poznawcze są w stanie skutecznie wyodrębnić zawartość ze źródeł danych, dodając wzbogacanie do wyodrębnionej zawartości, opcjonalnie generując projekcje przed zapisaniem wyników w indeksie wyszukiwania. W zależności od liczby obowiązków przypisanych do indeksatora można uruchomić w jednym z dwóch środowisk:

- Środowisko prywatne dla określonej usługi wyszukiwania. Indeksatory działające w takich środowiskach współużytkują zasoby z innymi obciążeniami (takimi jak inne zainicjowane przez klienta indeksowanie lub wykonywanie zapytań). Zazwyczaj tylko indeksatory, które wykonują indeksowanie oparte na tekście (na przykład nie używają zestawu umiejętności) w tym środowisku.
- Środowisko z wieloma dzierżawcami obsługujące indeksatory, które są intensywnie obciążające zasoby, takie jak te z umiejętności. To środowisko służy do odciążania przetwarzania obliczeniowego z dużą ilością, pozostawiając zasoby specyficzne dla usługi dostępne dla operacji rutynowych. To środowisko z wieloma dzierżawcami jest zarządzane i zabezpieczone przez firmę Microsoft bez dodatkowych kosztów dla klienta.

Dla każdego uruchomienia indeksatora usługa Azure Wyszukiwanie poznawcze określa najlepsze środowisko, w którym można uruchomić indeksator. Jeśli używasz zapory IP w celu kontrolowania dostępu do zasobów platformy Azure, wiedza o środowiskach wykonywania pomoże Ci skonfigurować zakres adresów IP obejmujący oba te elementy.

## <a name="granting-access-to-indexer-ip-ranges"></a>Udzielanie dostępu do zakresów adresów IP indeksatora

Jeśli zasób, do którego próbuje uzyskać dostęp, jest ograniczony tylko do pewnego zestawu zakresów adresów IP, należy rozwinąć zestaw, aby uwzględnić możliwe zakresy adresów IP, z których może pochodzić żądanie indeksatora. Jak wspomniano powyżej, istnieją dwa możliwe środowiska, w których są uruchamiane indeksatory i z których mogą pochodzić żądania dostępu. Aby można było korzystać z dostępu indeksatora, należy dodać adresy IP **obu** środowisk.

- Aby uzyskać adres IP określonego środowiska prywatnego usługi wyszukiwania `nslookup` (lub `ping` ) w pełni kwalifikowaną nazwę domeny (FQDN) usługi wyszukiwania. Przykładem może być nazwa FQDN usługi wyszukiwania w chmurze publicznej `<service-name>.search.windows.net` . Te informacje są dostępne na Azure Portal.
- Adresy IP środowisk wielodostępnych są dostępne za pośrednictwem `AzureCognitiveSearch` znacznika usługi. [Znaczniki usługi platformy Azure](../virtual-network/service-tags-overview.md) mają opublikowany zakres adresów IP dla każdej usługi — jest on dostępny za pośrednictwem [interfejsu API odnajdowania (wersja zapoznawcza)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) lub [pliku JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). W obu przypadkach zakresy adresów IP są podzielone według regionu — można wybrać tylko zakresy adresów IP przypisane do regionu, w którym Zainicjowano obsługę usługi wyszukiwania.

W przypadku niektórych źródeł danych można używać bezpośrednio samego tagu usługi zamiast wyliczania listy zakresów adresów IP (adres IP usługi wyszukiwania nadal musi być używany jawnie). Te źródła danych ograniczają dostęp za pomocą sposobu konfigurowania [reguły sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md), która natywnie obsługuje Dodawanie znacznika usługi, w przeciwieństwie do reguł IP, takich jak te oferowane przez usługę Azure Storage, Cosmos DB, Azure SQL i tak dalej. Źródła danych, które obsługują możliwość `AzureCognitiveSearch` bezpośredniego użycia znacznika usługi, oprócz adresu IP usługi wyszukiwania:

- [SQL Server w usłudze Azure Virtual Machines](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Wystąpienia zarządzane SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Aby uzyskać więcej informacji na temat tej opcji łączności, zobacz [indeksatory połączeń za pośrednictwem zapory IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Udzielanie dostępu za pośrednictwem prywatnych punktów końcowych

Indeksatory mogą korzystać z [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) w celu uzyskiwania dostępu do zasobów, do których jest blokowany dostęp do wybranych sieci wirtualnych lub nie mają włączonej opcji dostępu publicznego.
Ta funkcja jest dostępna tylko w usługach wyszukiwania do rozliczeń, z ograniczeniami liczby prywatnych punktów końcowych, które zostały utworzone. Aby uzyskać więcej informacji, zobacz [limity usługi](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Krok 1. Tworzenie prywatnego punktu końcowego dla bezpiecznego zasobu

Klienci powinni wywoływać operację zarządzania wyszukiwaniem, [interfejs API metodę createorupdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) dla **zasobu udostępnionego linku prywatnego**, aby utworzyć połączenie prywatnego punktu końcowego z bezpiecznym zasobem (na przykład konto magazynu). Ruch przesyłany przez to (wychodzące) połączenie z prywatnym punktem końcowym będzie pochodzić tylko z sieci wirtualnej, która znajduje się w określonym środowisku wykonywania indeksatora "Private" usługi wyszukiwania.

Usługa Azure Wyszukiwanie poznawcze sprawdzi, czy wywołujący ten interfejs API mają uprawnienia kontroli dostępu do usługi Azure RBAC do zatwierdzania żądań połączeń prywatnych punktów końcowych do bezpiecznego zasobu. Jeśli na przykład zażądasz prywatnego połączenia punktu końcowego z kontem magazynu z uprawnieniami tylko do odczytu, to wywołanie zostanie odrzucone.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Krok 2. Zatwierdź połączenie prywatnego punktu końcowego

Po zakończeniu operacji (asynchronicznej), która tworzy zasób udostępnionego linku prywatnego, połączenie z prywatnym punktem końcowym zostanie utworzone w stanie "oczekiwanie". Nie ma jeszcze żadnych przepływów ruchu nad połączeniem.
Następnie klient powinien zlokalizować to żądanie w bezpiecznym zasobie i "zatwierdzić". Zazwyczaj można to zrobić za pośrednictwem Azure Portal lub za pośrednictwem [interfejsu API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Krok 3: Wymuś uruchamianie indeksatorów w środowisku "prywatnym"

Zatwierdzony prywatny punkt końcowy umożliwia wychodzące wywołania z usługi wyszukiwania do zasobu, który ma pewne ograniczenia dostępu na poziomie sieci (na przykład źródło danych konta magazynu skonfigurowane tylko do uzyskiwania dostępu z niektórych sieci wirtualnych).
Oznacza to, że każdy indeksator, który jest w stanie skontaktować się ze źródłem danych za pośrednictwem prywatnego punktu końcowego, powiedzie się.
Jeśli prywatny punkt końcowy nie jest zatwierdzony lub jeśli indeksator nie korzysta z połączenia prywatnego punktu końcowego, przebieg indeksatora zostanie zakończony w `transientFailure` .

Aby umożliwić indeksatorom dostęp do zasobów za pośrednictwem połączeń prywatnych punktów końcowych, należy ustawić `executionEnvironment` indeksator, aby `"Private"` upewnić się, że wszystkie uruchomienia indeksatora będą mogły korzystać z prywatnego punktu końcowego. Wynika to z faktu, że prywatne punkty końcowe są obsługiwane w środowisku określonym przez usługę wyszukiwania prywatnego.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Te kroki są szczegółowo opisane w tematach [połączeń indeksatora za pomocą prywatnego punktu końcowego](search-indexer-howto-access-private.md).
Gdy masz zatwierdzony prywatny punkt końcowy do zasobu, indeksatory ustawione jako *prywatne* próbują uzyskać dostęp za pośrednictwem połączenia prywatnego punktu końcowego.

### <a name="limits"></a>Limity

Aby zapewnić optymalną wydajność i stabilność usługi wyszukiwania, nałożono ograniczenia (według warstwy usługi wyszukiwania) w następujących wymiarach:

- Rodzaje indeksatorów, które mogą być ustawione jako *prywatne*.
- Liczba udostępnionych zasobów linków prywatnych, które można utworzyć.
- Liczba różnych typów zasobów, dla których można utworzyć udostępnione zasoby linku prywatnego.

Limity te są udokumentowane w [limitach usługi](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Następne kroki

- [Połączenia indeksatora za poorednictwem zapór protokołu IP](search-indexer-howto-access-ip-restricted.md)
- [Połączenia indeksatora przy użyciu wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md)
- [Połączenia indeksatora z prywatnym punktem końcowym](search-indexer-howto-access-private.md)