---
title: Karta Marketplace oferty aplikacji platformy Azure
description: Karta Marketplace służy do identyfikowania zasobów marketingowych dla oferty aplikacji platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 6113759cc68d07af4c22edf03b3274d92dcbe780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416294"
---
# <a name="azure-application-marketplace-tab"></a>Karta Witryna portalu Azure w portalu Marketplace aplikacji

Karta Marketplace służy do opisywania aplikacji platformy Azure i dostarczania zasobów marketingowych. Ta karta zawiera następujące formularze: Przegląd, Artefakty marketingowe, Zarządzanie potencjalnymi klientami i Prawne.

## <a name="overview-form"></a>Formularz przeglądowy

Formularz Przegląd zawiera pola wymagane i opcjonalne wyświetlane w następnym przechwytywaniu ekranu. Wymagane pola są oskarżane gwiazdką (*).

![Formularz przeglądowy](./media/azureapp-marketplace-overview.png)

W poniższej tabeli opisano ustawienia używane do tworzenia witryny sklepowej dla oferty.   Pola dołączone do gwiazdki są wymagane.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **Tytuł\***        | Tytuł oferty. Będzie on wyświetlany w widocznym miejscu na rynku. Maksymalna długość wynosi 50 znaków. |
| **Podsumowanie\***      | Krótkie podsumowanie oferty. Maksymalna długość wynosi 100 znaków.           |
| **Długie podsumowanie\*** | Dłuższe podsumowanie oferty (choć może być takie samo jak podsumowanie). Maksymalna długość wynosi 256 znaków.           |
| **Opis\***  | Opis oferty. Maksymalna długość wynosi 3000 znaków. Dozwolone jest proste formatowanie &lt;HTML, w tym&gt; &lt;p&gt;&gt; &lt;,&gt; &lt;em&gt;, &lt;ul , li , ol i tagi nagłówka.  |
| **Identyfikator marketingowy\*** | Unikatowy adres URL do skojarzenia z tą ofertą, zazwyczaj obejmuje nazwę organizacji i rozwiązania, maksymalną długość 50 znaków. Wybierz krótki, przyjazny identyfikator marketingowy dla swojej usługi. Będzie to używane w adresach URL marketplace dla tej oferty. Jeśli na przykład identyfikator wydawcy to "contoso", a identyfikator marketingowy to "sampleApp", adres URL oferty w portalu Azure Marketplace zostanie`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  
| **Identyfikatory subskrypcji w wersji zapoznawczej\*** | Dodaj od jednego do 100 identyfikatorów subskrypcji podglądu. Te subskrypcje z białą listą będą miały dostęp do Twojej oferty, gdy będzie ona dostępna w wersji zapoznawczej po jej opublikowaniu, zanim zostanie udostępniona.          |
| **Przydatne linki**    | Opcjonalnie można podać łącza do różnych zasobów dla użytkowników oferty, takich jak pomoc techniczna, dokumentacja, fora itp.  Zaleca się dodanie co najmniej jednego łącza do dokumentacji.            |
| **Sugerowane kategorie (maksymalnie 5)\*** | Wybierz jedną do pięciu kategorii. Wybrane kategorie są używane do mapowania oferty na kategorie produktów dostępne w witrynie Azure Marketplace i witrynie Azure Portal. Będą one wyświetlane na stronach przeglądania oraz na stronie szczegółów produktu. |
|  |  |


## <a name="marketing-artifacts"></a>Artefakty marketingowe

Formularz Artefakty marketingowe ma pola wymagane i opcjonalne wyświetlane w następnym przechwytywaniu ekranu. Wymagane pola są oskarżane gwiazdką (*).

![Formularz artefaktów marketingowych](./media/azureapp-marketplace-artifacts.png)

W poniższej tabeli opisano artefakty marketingowe.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **Small\***        | Małe logo: 40x40 pikseli w formacie PNG     |
| **Medium\***       | Średnie logo: 90x90 pikseli w formacie PNG    |
| **Large\***        | Duże logo: 115x115 pikseli w formacie PNG   |
| **Szeroki\***         | Szerokie logo: 255x115 pikseli w formacie PNG    |
| **Bohater**           | Opcjonalne logo bohatera: 815x290 pikseli w formacie PNG. **Uwaga:** Ikony bohatera nie można usunąć po przesłaniu. |
| **Zrzuty ekranu (Maks. 5)** |        Zrzuty ekranu są wyświetlane na stronie szczegółów produktu. To dobry sposób na wizualne komunikowanie tego, co robi twoja aplikacja i jak działa. Na przykład można wyświetlić diagramy architektury lub ilustracje przypadków użycia. Zrzuty ekranu są opcjonalne i są ograniczone do 5 na jednostkę SKU. Aby dodać zrzut ekranu:<ul><li>Wybierz **+ Dodaj zrzut ekranu,** aby otworzyć okno Zrzut ekranu</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalna długość 100 znaków).</li><li>**Upload** - Prześlij obraz. Musi być w formacie PNG, a rozmiar wynosi 533 x 324 piksele.</li></ul>           |
| **Dodawanie klipu wideo**      | Opcjonalnie filmy są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem, aby wizualnie komunikować, co robi aplikacja i jak to działa. Aby dodać film: <ul><li>Wybierz **+ Dodaj wideo,** aby otworzyć okno Wideo</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalna długość 100 znaków).</li><li>**Link** - Wpisz adres URL witryny, na której znajduje się film (YouTube lub Vimeo)</li><li>**Miniatura** — prześlij miniaturę. Musi być w formacie PNG, a rozmiar wynosi 533 x 324 piksele.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Przykłady artefaktów w portalu Azure Marketplace

Następne przechwytywanie ekranu pokazuje przykład wyniku wyszukiwania w Marketplace.

![Wynik wyszukiwania oferty w Marketplace](./media/azureapp-marketplace-example-browse.png)

Na poniższej ilustracji pokazano, jak oferta jest wyświetlana w portalu Marketplace po kliknięciu kafelka oferty w wynikach wyszukiwania.

![Szczegóły wyników wyszukiwania oferty w Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Przykłady artefaktów w witrynie Azure Portal

Następujące zrzuty ekranu pokazują, jak oferta jest wyświetlana w witrynie Azure Portal. Oferta aplikacji w tym przykładzie znajduje się przez przejście do **Marketplace>Wszystko>Dev + Test>Jenkins**. Oferta Jenkins zawiera logo, tytuł i nazwę wyświetlaną wydawcy.

![Przeglądaj oferty w witrynie Azure portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Przechwytywanie następnego ekranu zawiera szczegółowe informacje o aplikacji, gdy użytkownik wybierze jenkins.

![Szczegóły oferty w witrynie Azure portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

Wszystkie logo przesłane do portalu Cloud Partner Portal powinny być zgodne z wytycznymi:

- Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niską liczbę kolorów podstawowych i pomocniczych na logo.
- Kolory motywu usługi Azure Portal są białe i czarne. Unikaj używania tych kolorów jako koloru tła logo. Użyj koloru, który sprawi, że twoje logo będzie widoczne w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe, czarne lub niebieskie.
- Nie używaj gradientowego tła na logo.
- Unikaj umieszczania tekstu, nawet firmy lub nazwy marki, na logo. Wygląd i działanie logo powinno być "płaskie" i powinno unikać gradientów.
- Nie rozciągaj logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne.

>[!IMPORTANT]
>Nie możesz usunąć logo bohatera po jego przesłaniu.

Użyj następujących wskazówek dotyczących logo bohatera:

- Czarne, białe i przezroczyste tła nie są dozwolone.
- Unikaj używania światła jako tła dla logo. Nazwa wyświetlana wydawcy, tytuł planu i długie podsumowanie oferty są wyświetlane w kolorze białej czcionki i muszą wyróżniać się na tle.
- Unikaj używania większości tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, długie podsumowanie oferty i przycisk tworzenia są osadzone programowo wewnątrz logo, gdy oferta jest wyświetlana na liście.
- Dołącz nieużywane prostokątne miejsce po prawej stronie logo bohatera. To puste miejsce to 415x100 pikseli i odsunięte od lewej o 370 pikseli.


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Formularz Zarządzanie potencjalnymi klientami zawiera opcjonalne pole do konfigurowania zarządzania potencjalnymi klientami. Aby skonfigurować zarządzanie potencjalnymi klientami, wybierz miejsce docelowe potencjalnego klienta z listy rozwijanej. Następne przechwytywanie ekranu pokazuje dostępne miejsca docelowe.

![Wybierz miejsce docelowe zarządzania potencjalnymi klientami](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Wybierz ikonę informacji, aby wyświetlić ten komunikat: "Wybierz system, w którym będą przechowywane twoi potencjalni klienci. Dowiedz się, jak połączyć się z systemem CRM [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ."

Aby uzyskać więcej informacji, zobacz [Konfigurowanie potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Informacje prawne

Użyj formularza prawnego, aby dostarczyć dokumentację prawną wymaganą dla każdej oferty.

Podaj następujące informacje:

- **Adres URL\* zasad prywatności** — wpisz link do zasad ochrony prywatności aplikacji.
- **Warunki użytkowania\* ** — wprowadź warunki użytkowania aplikacji. Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować aplikację.

![Forma prawna](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Następne kroki

[Karta pomocy technicznej](./cpp-support-tab.md)
