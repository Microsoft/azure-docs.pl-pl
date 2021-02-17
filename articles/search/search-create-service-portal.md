---
title: Tworzenie usługi wyszukiwania w portalu
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować zasób platformy Azure Wyszukiwanie poznawcze w Azure Portal. Wybierz grupy zasobów, regiony i jednostki SKU lub warstwę cenową.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: 7149233782815deebebde53767a3c654ac2321bb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547761"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Szybki start: Tworzenie usługi Azure Cognitive Search w portalu

[Azure wyszukiwanie poznawcze](search-what-is-azure-search.md) to zasób platformy Azure używany do dodawania obsługi wyszukiwania pełnotekstowego do aplikacji niestandardowych. Można łatwo zintegrować ją z innymi usługami platformy Azure, które zapewniają dane lub dodatkowe przetwarzanie, z aplikacjami na serwerach sieciowych lub z oprogramowaniem działającym na innych platformach w chmurze.

Usługę wyszukiwania można utworzyć przy użyciu [Azure Portal](https://portal.azure.com/)omówionej w tym artykule. Możesz również użyć [Azure PowerShell](search-manage-powershell.md), [interfejsu wiersza polecenia platformy Azure](/cli/azure/search)lub [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![Animowany plik GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Przed rozpoczęciem

Następujące właściwości usługi są rozwiązane w okresie istnienia usługi — zmiana któregokolwiek z nich wymaga nowej usługi. Ze względu na to, że są stałe, należy wziąć pod uwagę skutki użycia podczas wypełniania każdej właściwości:

* Nazwa usługi jest częścią punktu końcowego adresu URL ([Przejrzyj wskazówki](#name-the-service) dotyczące przydatnych nazw usług).
* [Warstwa usługi](search-sku-tier.md) ma wpływ na rozliczenia i ustawia limit w górę dla pojemności. Niektóre funkcje nie są dostępne w warstwie Bezpłatna.
* Region usługi może ustalić dostępność niektórych scenariuszy. Jeśli potrzebujesz [wysokich funkcji zabezpieczeń](search-security-overview.md) lub [wzbogacania AI](cognitive-search-concept-intro.md), musisz utworzyć wyszukiwanie poznawcze platformy Azure w tym samym regionie co inne usługi lub w regionach, które udostępniają daną funkcję. 

## <a name="subscribe-free-or-paid"></a>Subskrypcja (bezpłatna lub płatna)

[Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i skorzystaj z bezpłatnych środków, aby wypróbować płatne usługi platformy Azure. Po wyczerpaniu środków zachowaj konto i nadal korzystaj z bezpłatnych usług platformy Azure, takich jak Websites. Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz również [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure. 

## <a name="find-azure-cognitive-search"></a>Znajdź Wyszukiwanie poznawcze platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Kliknij znak plus (**"+ Utwórz zasób"**) w lewym górnym rogu.

1. Użyj paska wyszukiwania, aby znaleźć "wyszukiwanie poznawcze platformy Azure" lub przejdź do zasobu za pomocą usługi **Web**  >  **Azure wyszukiwanie poznawcze**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Tworzenie zasobu w portalu" border="false":::

## <a name="choose-a-subscription"></a>Wybierz subskrypcję

Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich dla usługi wyszukiwania. W przypadku implementowania [podwójnego szyfrowania](search-security-overview.md#double-encryption) lub innych funkcji, które są zależne od tożsamości usługi zarządzanej, wybierz tę samą subskrypcję, która jest używana dla Azure Key Vault lub innych usług, dla których są używane tożsamości zarządzane.

## <a name="set-a-resource-group"></a>Ustawianie grupy zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Jest to wymagane dla usługi wyszukiwania. Jest on również przydatny do zarządzania zasobami, w tym do kosztów. Grupa zasobów może składać się z jednej usługi lub wielu używanych jednocześnie usług. Na przykład, jeśli używasz usługi Azure Wyszukiwanie poznawcze do indeksowania bazy danych Azure Cosmos DB, możesz udostępnić obie usługi w tej samej grupie zasobów na potrzeby zarządzania. 

Jeśli zasoby nie są łączone w pojedynczą grupę, a istniejące grupy zasobów są wypełniane zasobami używanymi w niepowiązanych rozwiązaniach, Utwórz nową grupę zasobów tylko dla zasobu usługi Azure Wyszukiwanie poznawcze. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Tworzenie nowej grupy zasobów" border="false":::

W miarę upływu czasu można śledzić bieżące i przewidywane koszty, a także wyświetlać opłaty dla poszczególnych zasobów. Poniższy zrzut ekranu przedstawia rodzaj informacji o kosztach, które można zobaczyć w przypadku łączenia wielu zasobów w jedną grupę.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Zarządzanie kosztami na poziomie grupy zasobów" border="false":::

> [!TIP]
> Grupy zasobów upraszczają czyszczenie, ponieważ usunięcie grupy powoduje usunięcie wszystkich znajdujących się w niej usług. Umieszczenie w tej samej grupie zasobów wszystkich projektów będących prototypami korzystającymi z wielu usług ułatwia proces czyszczenia po zakończeniu projektu.

## <a name="name-the-service"></a>Nazwij usługę

W obszarze Szczegóły wystąpienia Podaj nazwę usługi w polu **adres URL** . Nazwa jest częścią punktu końcowego adresu URL, względem którego są wydawane wywołania interfejsu API: `https://your-service-name.search.windows.net` . Na przykład jeśli chcesz, aby punkt końcowy znajdował się pod adresem `https://myservice.search.windows.net`, wpisz `myservice`.

Wymagania dotyczące nazwy usługi:

* Musi być unikatowa w obrębie przestrzeni nazw search.windows.net
* Długość musi należeć do zakresu od 2 do 60 znaków
* Musisz użyć małych liter, cyfr lub kresek ("-")
* Nie używaj kresek ("-") w pierwszych 2 znakach ani jako ostatni pojedynczy znak
* Nie można używać kolejnych kresek ("--") w dowolnym miejscu

> [!TIP]
> Jeśli uważasz, że będziesz korzystać z wielu usług, zalecamy uwzględnienie regionu (lub lokalizacji) w nazwie usługi jako konwencji nazewnictwa. Usługi w ramach tego samego regionu mogą bezpłatnie wymieniać dane, więc jeśli platforma Azure Wyszukiwanie poznawcze jest w regionie zachodnie stany USA i masz inne usługi również w regionie zachodnie stany USA, nazwa, na przykład, `mysearchservice-westus` może zaoszczędzić na stronie właściwości podczas decydowania o sposobie łączenia lub dołączania zasobów.

## <a name="choose-a-location"></a>Wybierz lokalizację

Usługa Azure Wyszukiwanie poznawcze jest dostępna w większości regionów, zgodnie z opisem na [stronie cennika](https://azure.microsoft.com/pricing/details/search/).

Ogólnie rzecz biorąc, jeśli używasz wielu usług platformy Azure, wybierz region obsługujący dane lub usługę aplikacji. W ten sposób minimalizuje lub unieważnia opłaty za dane wychodzące (nie są naliczane opłaty za dane wychodzące, gdy usługi znajdują się w tym samym regionie).

Klienci, którzy mają wymagania dotyczące ciągłości biznesowej i odzyskiwania po awarii (BCDR), powinni tworzyć usługi w [parach regionalnej](../best-practices-availability-paired-regions.md#azure-regional-pairs). Na przykład jeśli pracujesz w Ameryka Północna, możesz wybrać Wschodnie stany USA i zachodnie stany USA, Północno-środkowe stany USA i południowe Central US dla każdej usługi.

Następujące funkcje są dostępne tylko w niektórych regionach:

* Wzbogacanie AI wymaga, aby Cognitive Services znajdować się w tym samym regionie co usługa Azure Wyszukiwanie poznawcze. Najpierw wybierz [region dla Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) (lista jest mniejsza), a następnie wybierz ten sam region dla usługi wyszukiwania.

* Podwójne szyfrowanie jest dostępne tylko w określonych regionach. Aby uzyskać więcej informacji, zobacz [podwójne szyfrowanie](search-security-overview.md#double-encryption)

* Obsługa stref dostępności jest oferowana w określonych regionach w usługach utworzonych po określonych datach. Aby uzyskać więcej informacji, zobacz ["strefy dostępności" w obszarze skalowanie wydajności](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Wybierz warstwę cenową

Usługa Azure Wyszukiwanie poznawcze jest obecnie oferowana w [wielu warstwach cenowych](https://azure.microsoft.com/pricing/details/search/): bezpłatna, podstawowa, standardowa lub zoptymalizowana pod kątem magazynu. Poszczególne warstwy różnią się między sobą [pojemnością i limitami](search-limits-quotas-capacity.md). Zobacz [Wybierz warstwę cenową,](search-sku-tier.md) Aby uzyskać wskazówki.

Podstawowa i Standardowa są najbardziej typowymi opcjami dotyczącymi obciążeń produkcyjnych, ale większość klientów zaczyna się od bezpłatnej usługi. Najważniejsze różnice między warstwami to rozmiar partycji i szybkość oraz limity dotyczące liczby obiektów, które można utworzyć.

Pamiętaj, że po utworzeniu usługi nie można zmienić warstwy cenowej. Jeśli potrzebujesz warstwy wyższej lub niższej, należy ponownie utworzyć usługę.

## <a name="create-your-service"></a>Tworzenie usługi

Po podaniu niezbędnych danych wejściowych przejdź dalej i Utwórz usługę. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Przeglądanie i tworzenie usługi" border="false":::

Twoja usługa zostanie wdrożona w ciągu kilku minut. Możesz monitorować postęp za pomocą powiadomień platformy Azure. Rozważ możliwość przypięcia usługi do pulpitu nawigacyjnego w celu ułatwienia dostępu w przyszłości.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitorowanie i Przypinanie usługi" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Pobieranie klucza i punktu końcowego adresu URL

Jeśli nie korzystasz z portalu, dostęp programistyczny do nowej usługi wymaga podania punktu końcowego adresu URL oraz klucza API uwierzytelniania.

1. Na stronie **Przegląd** zlokalizuj i skopiuj punkt końcowy adresu URL po prawej stronie strony.

2. Na stronie **klucze** Skopiuj jeden z kluczy administratora (są one równoważne). Klucze interfejsu API administratora są wymagane do tworzenia, aktualizowania i usuwania obiektów w usłudze. Z kolei klucze zapytań zapewniają dostęp do odczytu do zawartości indeksu.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Strona przeglądu usługi z punktem końcowym adresu URL" border="false":::

Klucz i punkt końcowy nie są wymagane do zadań wykonywanych w portalu. Portal jest już połączony z zasobem usługi Azure Wyszukiwanie poznawcze z uprawnieniami administratora. Przewodnik po samouczku można rozpocząć od [przewodnika Szybki Start: Tworzenie indeksu wyszukiwanie poznawcze platformy Azure w portalu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skalowanie usługi

Po aprowizacji usługi można ją skalować stosownie do potrzeb. W przypadku wybrania warstwy Standardowa usługi Azure Wyszukiwanie poznawcze można skalować usługę w dwóch wymiarach: Replicas i partitions. Wybór warstwy podstawowej oznaczałby, że można dodawać wyłącznie repliki. W razie aprowizacji bezpłatnej usługi skalowanie nie jest dostępne.

***Partycje*** umożliwiają usłudze przechowywanie i przeszukiwanie większej liczby dokumentów.

***Repliki*** umożliwiają usłudze obsługę większego obciążenia zapytaniami wyszukiwania.

Dodawanie zasobów wiąże się z dodaniem opłat do rachunku miesięcznego. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) pomaga w określeniu, jaki wpływ na rozliczenia będzie miało dodanie zasobów. Pamiętaj, że możesz też dostosowywać zasoby do obciążenia. Możesz na przykład zwiększyć ilość zasobów na czas początkowego tworzenia pełnego indeksu, a następnie zmniejszyć ją do poziomu dostosowanego do potrzeb indeksowania przyrostowego.

> [!Important]
> Usługa musi mieć [2 repliki w ramach umowy SLA tylko do odczytu oraz 3 repliki w ramach umowy SLA do odczytu/zapisu](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Przejdź do strony usługi wyszukiwania w witrynie Azure Portal.
2. W okienku nawigacji po lewej stronie wybierz pozycję **Ustawienia**  >  **Skala**.
3. Użyj suwaka, aby dodać zasoby wybranego typu.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Dodawanie pojemności przy użyciu replik i partycji" border="false":::

> [!Note]
> Magazyn na partycję i szybkość zwiększają się o wyższe warstwy. Aby uzyskać więcej informacji, zobacz [pojemność i limity](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Kiedy należy dodać drugą usługę

Większość klientów używa tylko jednej usługi aprowizowanej dla danej warstwy, która zapewnia [właściwe rozmieszczenie zasobów](search-sku-tier.md). Jedna usługa może obsługiwać wiele indeksów, z zastrzeżeniem [maksymalnych limitów wybranej warstwy](search-capacity-planning.md), przy czym każdy indeks jest odizolowany od innych. Na platformie Azure Wyszukiwanie poznawcze żądania mogą być kierowane tylko do jednego indeksu, co minimalizuje ryzyko przypadkowego lub zamierzonego pobrania danych z innych indeksów w tej samej usłudze.

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

+ [Ciągłość działania i odzyskiwanie po awarii (BCDR)](../best-practices-availability-paired-regions.md). Usługa Azure Wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku awarii.

+ [Architektury z wieloma dzierżawcami](search-modeling-multitenant-saas-applications.md) czasami są wywoływane w przypadku co najmniej dwóch usług.

+ Aplikacje wdrożone globalnie mogą wymagać usług wyszukiwania w poszczególnych lokalizacjach geograficznych, aby zminimalizować opóźnienia.

> [!NOTE]
> Na platformie Azure Wyszukiwanie poznawcze nie można rozdzielić operacji indeksowania i wykonywania zapytań. w ten sposób nigdy nie utworzysz wielu usług dla rozdzielonych obciążeń. Zapytania względem indeksu zawsze dotyczą usługi, w której został utworzony (nie można utworzyć indeksu w jednej usłudze, by następnie skopiować go do innej).

Druga usługa nie jest wymagana w celu zapewnienia wysokiej dostępności. Wysoka dostępność zapytań ma miejsce wtedy, gdy używane są co najmniej 2 repliki w tej samej usłudze. Aktualizacje repliki są sekwencyjne, co oznacza, że co najmniej jeden z nich działa po przewróceniu aktualizacji usługi. Aby uzyskać więcej informacji na temat czasu [pracy, zobacz umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Następne kroki

Po zainicjowaniu obsługi administracyjnej można kontynuować w portalu, aby utworzyć swój pierwszy indeks.

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w portalu](search-get-started-portal.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)