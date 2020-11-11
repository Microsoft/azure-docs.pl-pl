---
title: Oferty dotyczące najlepszych rozwiązań — Microsoft Commercial Marketplace
description: Dowiedz się więcej na temat najlepszych rozwiązań dotyczących Microsoft AppSource i ofert w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492093"
---
# <a name="offer-listing-best-practices"></a>Najlepsze rozwiązania dotyczące ofert

Ten artykuł zawiera sugestie dotyczące tworzenia i angażowania komercyjnych ofert rynkowych firmy Microsoft. W poniższych tabelach przedstawiono najlepsze rozwiązania dotyczące kończenia informacji o ofercie w centrum partnerskim. Aby uzyskać analizę sposobu działania ofert, przejdź do [pulpitu nawigacyjnego usługi Marketplace Insights](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) w centrum partnerskim. 

## <a name="online-store-offer-details"></a>Szczegóły oferty sklepu online

| Ustawienie | Najlepsze rozwiązanie |
|:--- |:--- |  
| Nazwa oferty | W przypadku aplikacji podaj czytelny tytuł zawierający słowa kluczowe wyszukiwania, aby pomóc klientom w znalezieniu oferty. <br> <br> W przypadku usług doradczych postępuj zgodnie z tym formatem: [nazwa oferty: [Duration] [typ oferty] (na przykład Contoso: implementacja 2-tygodniowa) |
| Opis oferty | Podaj jasny opis, który opisuje swoją pozycję oferty w pierwszych kilku zdaniach.  Należy pamiętać, że te zdania mogą być używane w wynikach wyszukiwarki. Podstawowe składniki Twojej pozycji wartości powinny obejmować: <ul> <li>Opis produktu lub rozwiązania. </li> <li> Użytkownik korzystający z produktu lub rozwiązania. </li> <li> Klient wymaga lub nie korzysta z adresów produktów lub rozwiązań. </li> </ul> <br> Jeśli to możliwe, korzystaj z słów w standardzie branżowym lub w oparciu o korzyści.  Nie należy polegać na funkcjach i funkcjach, aby sprzedawać produkt.  Zamiast tego należy skoncentrować się na dostarczanej wartości. <br> <br> Aby zapoznać się z listą usług doradczych, zastanów się w tym miejscu. |

> [!IMPORTANT]
> Upewnij się, że nazwa oferty i opis oferty są zgodne ze znakami **[towarowymi firmy Microsoft i wskazówkami](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** i innymi istotnymi wskazówkami specyficznymi dla produktu w przypadku odwoływania się do znaków towarowych firmy Microsoft oraz nazw oprogramowania, produktów i usług firmy Microsoft.

## <a name="online-store-listing-details"></a>Szczegóły aukcji ze sklepu online

Kategorie i branże dla innego sklepu online będą miały zastosowanie do różnych typów ofert.

| Sklep online | Kategorie <br>według sklepu online | Kategorie <br>według sklepu online | Branże <br> dla AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Typ oferty**   |  **Azure Marketplace**  | **AppSource**  |
| Aplikacja platformy Azure | X | |
| Kontener | X | |
| Usługi konsultingowe | | | X |
| Dynamics 365 Customer Engagement & platformę energetyczną | | X | X |
| Dynamics 365 finanse & Zarządzanie łańcuchem dostaw | | X | X | 
| Dynamics 365 Business Central | | X | X |
| Moduły IoT Edge | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Maszyna wirtualna platformy Azure |  X |    |

### <a name="categories"></a>Kategorie

Microsoft AppSource i Azure Marketplace to sklepy online, które oferują różne typy rozwiązań. Portal Azure Marketplace oferuje rozwiązania IT stworzone na platformie Azure lub w systemie.  Microsoft AppSource oferować rozwiązania biznesowe, takie jak aplikacje branżowe SaaS, dodatki Dynamics 365, dodatki Microsoft 365 i aplikacje platformy.

Kategorie i podkategorie są mapowane na każdy sklep online na podstawie typu rozwiązania. Twoja oferta zostanie opublikowana w witrynie Microsoft AppSource lub Azure Marketplace w zależności od typu oferty, możliwości transakcji dla wyboru oferty i kategorii/podkategorii. 

Wybierz kategorie i podkategorie, które najlepiej dopasowują się do typu rozwiązania. Dostępne są następujące opcje:

* Do dwóch kategorii, w tym podstawowej i pomocniczej kategorii (opcjonalnie).
* Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli nie wybrano żadnej podkategorii, oferta nadal będzie wykrywalna tylko w wybranej kategorii.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>Ważne: SaaSe oferty i dodatki Microsoft 365

Zobacz artykuł dotyczący tworzenia pakietów [w komercyjnej witrynie Marketplace,](marketplace-commercial-transaction-capabilities-and-considerations.md) Aby uzyskać szczegółowe informacje dotyczące sposobu, w jaki usługa Transact może mieć wpływ na sposób wyświetlania i kupowania oferty przez klientów w portalu Marketplace. W przypadku ofert SaaS oferta oferty oraz wybór kategorii będą określać sklep online, w którym Twoja oferta zostanie opublikowana.


| Oferta SaaS    | Oferta SaaS   | Oferta SaaS  | Oferta SaaS   | Oferta SaaS   | Oferta SaaS   | Oferta SaaS    | Odpowiedni sklep online| Odpowiedni sklep online |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Naliczanie opłat | Dodatki Microsoft 365 | Skontaktuj się z nami | Transact (co najmniej 1 plan) | Plan tylko prywatny | Plan tylko publiczny | Publiczne plany prywatne & | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1, 2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. W zależności od kategorii/podkategorii i wyboru branżowego
2. Oferty z planami prywatnymi zostaną opublikowane w Azure Portal


### <a name="industries"></a>Branże

Wybór branżowy ma zastosowanie tylko w przypadku ofert opublikowanych w usługach AppSource i konsultingowych opublikowanych w witrynie Azure Marketplace.  Wybierz pozycję branże i/lub pionowe, jeśli Twoja oferta dotyczy potrzeb specyficznych dla branż, wywołując specyficzne dla branży funkcje w opisie oferty. Można wybrać maksymalnie dwie (2) branże i dwie (2) pionowe dla wybranych branż.

>[!Note]
>W przypadku ofert dotyczących usług konsultacyjnych w portalu Azure Marketplace nie ma żadnych pionowych w branży.

| **Branże** |  **Pionowe** |
| :------------------- | :----------------|
| **Rolnictwo** | |
| **Architektura & konstrukcja** | |
| **Motoryzacja** | |
| **Dystrybucja** | Handel hurtowy <br> Wysyłka pakietu & Parcel |  
| **Education** | Szkolnictwo wyższe <br> Podstawowa & dodatkowej platformy edu/K-12 <br> Biblioteki & muzea |
| **Usługi finansowe** | Bankowość & — rynki kapitałowe <br> Zakład | 
| **Instytucje rządowe** |  Inteligencja & obrony <br> Rząd cywilny <br> Bezpieczeństwo publiczne & sprawiedliwości |
| **Opieka zdrowotna** | Płatnik kondycji <br> Dostawca kondycji <br> Pharmaceuticals | 
| **Hotelarstwo & podróży** | Transport & podróże <br> Hotele & wypoczynek <br> Restauracje & żywność Services | 
| **Zasoby & produkcyjnego** | Chemical & Agrochemical <br> Produkcja dyskretna <br> Energetyczna | 
| **Komunikacja & Media** | Multimedia & rozrywki <br> Telekomunikacja | 
| **Inne branże sektora publicznego** | Leśnictwo & rybołówstwo <br> Non profit | 
| **Profesjonalne usługi** | Profesjonalne usługi partnerskie <br> Informacje prawne | 
| **Nieruchomości** | |

Tylko Microsoft AppSource branży:

| **Branża** |  **Pionowe** |
| :------------------- | :----------------|
| **Towary konsumenckie detaliczne &** | Detalicznych <br> Towary konsumenckie |

### <a name="applicable-products"></a>Odpowiednie produkty

Wybierz odpowiednie produkty, z którymi aplikacja działa, aby oferta była wyświetlana w obszarze wybrane produkty w programie AppSource.

### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Słowa kluczowe mogą pomóc klientom w znalezieniu oferty podczas wyszukiwania. Zidentyfikuj najważniejsze słowa kluczowe wyszukiwania dla swojej oferty, Dołącz je do podsumowania oferty oraz opis, a także w sekcji słowo kluczowe w sekcji Szczegóły oferty.

## <a name="online-store-marketing-details"></a>Szczegóły marketingu sklepu online
| Ustawienie | Najlepsze rozwiązanie |
|:--- |:--- |  
| Logo oferty (format PNG, od 216 × 216 do 350 x 350 px): Strona szczegółów aplikacji | Projektuj i Optymalizuj logo dla nośnika cyfrowego:<br>Przekaż logo w formacie PNG do strony z listą szczegółów aplikacji oferty. Centrum partnerskie zmieni rozmiar na wymagane rozmiary logo. |
| Logo oferty (format PNG, 48 × 48 pikseli): Strona wyszukiwania | Centrum partnerskie spowoduje wygenerowanie tego logo z poziomu przesłanego dużego logo. Opcjonalnie można zastąpić ten inny obraz później. |
| "Dowiedz się więcej" | Uwzględnij dodatkowe zasoby sprzedaży i marketingu w obszarze "Dowiedz się więcej", Oto kilka przykładów:<ul><li>Oficjalne dokumenty</li><li> broszury</li><li>listy kontrolne lub</li><li> Prezentacje programu PowerPoint</li></ul><br>Zapisz wszystkie pliki w formacie PDF. Celem tego celu jest zaplanowanie klientów, a nie ich sprzedawanie.<br><br>Dodaj link do strony docelowej aplikacji do wszystkich dokumentów i Dodaj parametry adresu URL, aby ułatwić Ci śledzenie odwiedzających i prób. |
| Wideo: AppSource, usługi konsultingowe i oferty SaaS | Najmocne wideo komunikuje wartość oferty w formie narracji:<ul> <li> Utwórz klienta, a nie jego firmę, Hero historię. </li> <li> Film wideo powinien dotyczyć najważniejszych wyzwań i celów klienta docelowego. </li> <li> Zalecana długość: 60-90 sekund.</li> <li> Uwzględnij słowa kluczowe wyszukiwania, które używają nazwy wideo. </li> <li> Rozważ dodanie dodatkowych filmów wideo, takich jak instrukcje, wprowadzenie lub Opinie klientów. </li> </ul> |
| Zrzuty ekranu (1280 &nbsp; &times; &nbsp; 720) | Dodaj maksymalnie pięć zrzutów ekranu:<br>Uwzględnij słowa kluczowe wyszukiwania w nazwach plików. |

## <a name="link-to-your-offer-page-from-your-website"></a>Link do strony oferty z witryny sieci Web

Po dołączeniu z usługi AppSource lub identyfikatora portalu Azure Marketplace w witrynie do listy w portalu komercyjnym można obsługiwać silną analizę i raportowanie, dołączając następujące parametry zapytania na końcu adresu URL:
* **src** : Uwzględnij źródło, z którego jest kierowany ruch do AppSource (na przykład witryna sieci Web, LinkedIn lub Facebook).
* **mktcmpid** : Identyfikator kampanii marketingowej, który może zawierać maksymalnie 16 znaków w dowolnej kombinacji liter, cyfr, podkreśleń i łączników (na przykład *blogpost_12* ).

Następujący przykładowy adres URL zawiera oba z powyższych parametrów zapytania: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Dodając parametry do adresu URL AppSource, możesz sprawdzić efektywność kampanii na pulpicie nawigacyjnym analizy w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [korzyściach z używania komercyjnego portalu Marketplace](./gtm-your-marketplace-benefits.md).

Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć i skonfigurować swoją ofertę.
