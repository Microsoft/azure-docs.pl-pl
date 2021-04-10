---
title: Jak dokumentować źródła danych w Azure Data Catalog
description: Artykuł jak wyróżniać sposób dokumentowania zasobów danych w Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 21168e403ffa8fb1472a5bfcac1fcd2079a52e2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674924"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Jak dokumentować źródła danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Innymi słowy, **Azure Data Catalog** to pomoc dla *osób, które* odnajdują i korzystają ze źródeł danych oraz pomagają organizacjom w uzyskaniu większej korzyści z istniejących danych.

Gdy źródło danych jest zarejestrowane w **Azure Data Catalog**, jego metadane są kopiowane i indeksowane przez usługę, ale wątek nie kończy w tym miejscu. **Azure Data Catalog** umożliwia również użytkownikom udostępnienie swojej kompletnej dokumentacji, która może opisywać użycie i typowe scenariusze dla źródła danych.

W temacie [jak dodawać adnotacje do źródeł danych](data-catalog-how-to-annotate.md)można dowiedzieć się, że eksperci znający źródło danych mogą dodać do nich adnotacje ze znacznikami i opisami. Portal **Azure Data Catalog** zawiera bogaty Edytor tekstu, dzięki któremu użytkownicy mogą w pełni udokumentować zasoby danych i kontenery. Edytor obejmuje formatowanie akapitu, takie jak nagłówki, formatowanie tekstu, listy punktowane, listy numerowane i tabele.

Tagi i opisy doskonale sprawdzają się w przypadku prostych adnotacji. Jednak aby pomóc konsumentom danych lepiej zrozumieć użycie źródła danych i scenariusze biznesowe dla źródła danych, ekspert może dostarczyć kompletną, szczegółową dokumentację. Można łatwo udokumentować źródło danych. Wybierz element zawartości lub kontener danych, a następnie wybierz pozycję **Dokumentacja**.

![Karta dokumentacja w Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentowanie zasobów danych
Korzystanie z dokumentacji **Azure Data Catalog** umożliwia korzystanie z Data Catalog jako repozytorium zawartości do tworzenia pełnych opisów zasobów danych. Możesz eksplorować szczegółową zawartość opisującą kontenery i tabele. Jeśli masz już zawartość w innym repozytorium zawartości, na przykład w programie SharePoint lub udziale plików, możesz dodać do linków do dokumentacji zasobów, aby odwołać się do istniejącej zawartości. Ta funkcja sprawia, że istniejące dokumenty są bardziej wykrywalne.

> [!NOTE]
> Dokumentacja nie jest uwzględniona w indeksie wyszukiwania.
>

![Karta dokumentacja i hiperłącze do linku sieci Web](media/data-catalog-documentation/data-catalog-documentation2.png)

Poziom dokumentacji może się wahać od opisu charakterystyki i wartości kontenera zasobów danych do szczegółowego opisu schematu tabeli w kontenerze. Udostępniony poziom dokumentacji powinien być uzależniony od potrzeb Twojej firmy. Ogólnie rzecz biorąc, Oto kilka informatyków i wad dokumentujących zasoby danych:

* Dokumentowanie tylko kontenera: cała zawartość znajduje się w jednym miejscu, ale może nie być konieczna, aby użytkownicy mogli podejmować świadome decyzje.
* Dokumentuj tylko tabele: zawartość jest specyficzna dla tego obiektu, ale użytkownicy mają wiele miejsc do dokumentów.
* Kontenery dokumentów i tabele: najbardziej kompleksowe podejście, ale może spowodować dalsze konserwacje dokumentów.

## <a name="summary"></a>Podsumowanie
Dokumentowanie źródeł danych za pomocą **Azure Data Catalog** może tworzyć opisy zasobów danych w tyle, ile potrzebujesz.  Korzystając z linków, można połączyć się z zawartością przechowywaną w istniejącym repozytorium zawartości, co spowoduje jednoczesne przeniesienie istniejących dokumentów i zasobów danych. Gdy użytkownicy będą odkrywać odpowiednie zasoby danych, mogą mieć pełny zestaw dokumentacji.
