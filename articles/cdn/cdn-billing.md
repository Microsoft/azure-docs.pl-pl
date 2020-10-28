---
title: Informacje dotyczące rozliczeń Azure CDN | Microsoft Docs
description: Poznaj strukturę rozliczeń zawartości hostowanej przez usługę Azure Content Delivery Network, w tym obszary rozliczeń, opłaty za dostarczanie i zarządzanie kosztami.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779173"
---
# <a name="understanding-azure-cdn-billing"></a>Omówienie rozliczania za usługę Azure CDN

Ten często zadawane pytania przedstawia strukturę rozliczania zawartości hostowanej przez usługę Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co to jest region rozliczeniowy?
Region rozliczeniowy jest obszar geograficzny używany do określania, jaka stawka jest naliczana za dostarczanie obiektów z Azure CDN. Bieżące strefy rozliczeń i ich regiony są następujące:

- Strefa 1: Ameryka Północna, Europa, Bliski Wschód i Afryka

- Strefa 2: Azja i Pacyfik (w tym Japonia)

- Strefa 3: Ameryka Południowa

- Strefa 4: Australia i Nowa Zelandia

- Strefa 5: Indie

Aby uzyskać informacje o regionach punktów obecności (POP), zobacz [Azure CDN lokalizacje pop według regionów](./cdn-pop-locations.md). Na przykład punkt obecności znajdujący się w Meksyku znajduje się w regionie Ameryka Północna i dlatego jest uwzględniony w strefie 1. 

Aby uzyskać informacje na temat cennika Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak są naliczane opłaty za dostarczanie według regionów?
Obszar rozliczeń Azure CDN jest oparty na lokalizacji serwera źródłowego dostarczającego zawartość do użytkownika końcowego. Lokalizacja docelowa (fizyczna) klienta nie jest uważana za region rozliczenia.

Na przykład jeśli użytkownik znajdujący się w Meksyku wystawi żądanie, a to żądanie jest obsługiwany przez serwer znajdujący się w Stany Zjednoczone POP z powodu komunikacji równorzędnej lub ruchu, obszar rozliczeń będzie Stany Zjednoczone.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co to jest transakcja Azure CDN rozliczana?
Każde żądanie HTTP (S) kończące się w sieci CDN jest zdarzeniem rozliczanym, które obejmuje wszystkie typy odpowiedzi: sukces, Niepowodzenie lub inne. Jednak różne odpowiedzi mogą generować różne ilości ruchu. Na przykład *nie można modyfikować 304* i inne odpowiedzi tylko do nagłówka generują niewielki ruch, ponieważ są to niewielka odpowiedź nagłówka; Podobnie odpowiedzi na błędy (na przykład *nie znaleziono 404* ) są płatne, ale ponoszą niewielki koszt ze względu na mały ładunek odpowiedzi.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jakie inne koszty platformy Azure są skojarzone z Azure CDN użycia?
Korzystanie z Azure CDN również wiąże się z niektórymi opłatami za użycie usług używanych jako źródło dla obiektów. Te koszty są zwykle małą częścią całkowitego kosztu użycia usługi CDN.

Jeśli używasz magazynu obiektów blob platformy Azure jako źródła zawartości, naliczane są również następujące opłaty za magazyn pamięci podręcznej:

- Rzeczywiste użycie GB: rzeczywisty magazyn obiektów źródłowych.

- Transakcje: w razie potrzeby w celu wypełnienia pamięci podręcznej.

- Transfery w GB: ilość danych transferowanych w celu wypełnienia pamięci podręcznych usługi CDN.

> [!NOTE]
> Od października 2019, jeśli używasz Azure CDN firmy Microsoft, koszt transferu danych z pochodzenia hostowanego na platformie Azure do punktów obecności w sieci CDN jest bezpłatny. Azure CDN z Verizon i Azure CDN z Akamai podlegają kursom opisanym poniżej.

Aby uzyskać więcej informacji na temat rozliczeń usługi Azure Storage, zobacz [Opis rozliczeń usługi Azure Storage — przepustowość, transakcje i pojemność](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

W przypadku korzystania z *dostarczania usług hostowanych* opłaty będą naliczane w następujący sposób:

- Czas obliczeń na platformie Azure: wystąpienia obliczeniowe, które działają jako źródło.

- Transfer obliczeń na platformie Azure: transfer danych z wystąpień obliczeniowych w celu wypełnienia pamięci podręcznych Azure CDN.

Jeśli klient używa żądań zakresu bajtów (niezależnie od usługi pochodzenia), mają zastosowanie następujące zagadnienia:

- *Żądanie dotyczące zakresu bajtów* jest transakcją rozliczaną w sieci CDN. Gdy klient wystawia żądanie dotyczące zakresu bajtów, to żądanie dotyczy podzestawu (zakresu) obiektu. Usługa CDN odpowiada wyłącznie częściowej zawartości, której dotyczy żądanie. Ta część odpowiedzi jest transakcją rozliczaną, a kwota transferu jest ograniczona do rozmiaru odpowiedzi zakresu (plus nagłówki).

- Gdy żądanie dociera tylko do części obiektu (przez określenie nagłówka z zakresem bajtów), Sieć CDN może pobrać cały obiekt do swojej pamięci podręcznej. W związku z tym, mimo że transakcja rozliczana z sieci CDN dotyczy częściowej odpowiedzi, transakcja rozliczana od źródła może dotyczyć pełnego rozmiaru obiektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Ile działań związanych z transferem ma obsługiwać pamięć podręczną?
Za każdym razem, gdy punkt obecności usługi CDN musi wypełnić pamięć podręczną, wysyła żądanie do źródła dla obiektu w pamięci podręcznej. W związku z tym źródło ponosi transakcję rozliczaną za każdy chybień w pamięci podręcznej. Liczba chybień w pamięci podręcznej zależy od kilku czynników:

- Jak ma być dostępna zawartość pamięci podręcznej: Jeśli zawartość ma wysoką wartość czasu wygaśnięcia (Time-to-Live)/Expiration i jest często używana, więc pozostaje popularna w pamięci podręcznej, a następnie większość obciążenia jest obsługiwana przez sieć CDN. Typowy dobry współczynnik trafień pamięci podręcznej jest również większy niż 90%, co oznacza, że mniej niż 10% żądań klientów musi powrócić do źródła, w przypadku chybień w pamięci podręcznej lub odświeżenia obiektu.

- Liczba węzłów potrzebnych do załadowania obiektu: za każdym razem, gdy węzeł ładuje obiekt ze źródła, ponosi transakcję rozliczaną. W związku z tym większa zawartość globalna (dostępną z większej liczby węzłów) skutkuje bardziej obciążanymi transakcjami.

- Wpływ na czas wygaśnięcia: wyższy czas TTL dla obiektu oznacza, że musi być pobierany z mniejszego poziomu pochodzenia. Oznacza to również, że klienci, tacy jak przeglądarki, mogą buforować obiekt więcej, co może zmniejszyć liczbę transakcji do sieci CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Które usługi pochodzenia kwalifikują się do bezpłatnego transferu danych z Azure CDN firmy Microsoft? 
W przypadku korzystania z jednej z następujących usług platformy Azure jako źródła CDN nie zostanie naliczona opłata za transfer danych z punktu początkowego do punktów POP usługi CDN. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- System DNS platformy Azure
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Usługa Azure App Service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Usługa Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL Database
- Wystąpienie zarządzane Azure SQL
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak najlepiej Jak mogę zarządzać moimi kosztami?
Ustaw najdłuższy możliwy czas wygaśnięcia dla zawartości.