---
title: Normalizacja danych na platformie Azure — wskaźnikowa | Microsoft Docs
description: W tym artykule wyjaśniono, jak wskaźnik na platformie Azure służy do normalizacji danych z wielu różnych źródeł i szczegółów schematu normalizacji.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 66c315132ef0ef4d320e9edd8e9bcc28b2240924
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805094"
---
# <a name="normalization-in-azure-sentinel"></a>Normalizacja na platformie Azure — wskaźnik

W tym artykule wyjaśniono normalizację schematów danych na platformie Azure, a w tym schemat połączeń sieciowych i sesji, do których jest dołączony link.

## <a name="what-is-normalization"></a>Co to jest normalizacja

Współpraca z różnymi typami danych i tabelami przedstawia wyzwania. Należy zapoznać się z wieloma różnymi typami danych i schematami, dzięki czemu można pisać i korzystać z unikatowego zestawu reguł analizy, skoroszytów i zapytań polowających dla każdej z nich, nawet w przypadku tych, które współużytkują commonalities (na przykład odnoszące się do urządzeń zapory). Korelacja między różnymi typami danych, koniecznych do badania i polowania, jest również trudna. Wskaźnik "Azure" obejmuje bezproblemowe środowisko obsługi danych z różnych źródeł w jednolitych, znormalizowanych widokach.

**Typowy model informacji o** wskaźnikach platformy Azure składa się z trzech aspektów:

- **Znormalizowane schematy** obejmują typowe zestawy przewidywalnych typów zdarzeń (tabele), które są łatwe w współpracy z programem i umożliwiają tworzenie ujednoliconych funkcji (na przykład tabela sieci). Schemat zawiera również Konwencję znormalizowanych kolumn oraz definicje standaryzacji wartości i formatu (standardowa spójna reprezentacja danych, takich jak adresy IP).

- **Parsery** mapują istniejące dane różnych typów na znormalizowany schemat. Zgodnie z modelem dane można analizować do znormalizowanego schematu w czasie wykonywania zapytania (przy użyciu funkcji) lub czasu pozyskiwania. W tej chwili obsługiwana jest tylko analiza czasu wykonywania zapytań.

- **Zawartość każdego ze znormalizowanych schematów** obejmuje reguły analizy, interaktywne skoroszyty, zapytania polowania i dodatkową zawartość.

### <a name="current-release"></a>Bieżąca wersja

W tej wersji jest dostępny [znormalizowany Podgląd połączeń sieciowych i sesji](./normalization-schema.md) (v 1.0.0). Schemat zawiera opis połączeń sieciowych lub sesji, takich jak zarejestrowane przez zapory, dane przewodowe, sieciowej grupy zabezpieczeń, Sieć, systemy proxy i bramy zabezpieczeń sieci Web.  Wybrane analizatory czasu zapytania i reguły analizy są dostępne wraz ze schematem i używają go.

Schemat jest obecnie dostępny tylko za pośrednictwem analizatorów czasu zapytania (zobacz sekcję parsery).

Dane można analizować w dodatkowych reprezentacjach i używać [modelu nazewnictwa jednostek OSSEM](https://ossemproject.com/cdm/entities/intro.html#) do tworzenia kolumn, które będą spójne z istniejącymi i przyszłymi znormalizowanymi tabelami.

## <a name="normalized-schema-and-parsing"></a>Znormalizowany schemat i analiza

### <a name="how-our-normalized-schemas-are-defined"></a>Jak są zdefiniowane nasze znormalizowane schematy

Wskaźnik na platformie Azure jest zgodny ze standardowym modelem informacyjnym [metadanych Open Source (OSSEM)](https://ossemproject.com/intro.html) , co umożliwia przewidywalną korelację między znormalizowanymi tabelami. OSSEM to projekt, który koncentruje się głównie na dokumentacji i standaryzacji dzienników zdarzeń zabezpieczeń z różnych źródeł danych i systemów operacyjnych. Ponadto projekt zawiera model wspólnych informacji (CIM), który może być używany przez inżynierów danych podczas procedur normalizacji danych, aby umożliwić analitykom zabezpieczeń wykonywanie zapytań i analizowanie danych w różnych źródłach danych.

[OSSEM CIM](https://ossemproject.com/cdm/intro.html) definiuje zestaw jednostek (na przykład: File, host, IP, Process) i definiuje zestaw atrybutów dla każdej takiej jednostki. Ponadto model CIM definiuje zestaw tabel (na przykład tabelę [sesji sieci](https://ossemproject.com/cdm/tables/network_session.html) ), które używają odpowiednich atrybutów z tych jednostek, umożliwiając bezproblemowe i przewidywalne korelację. Należy zauważyć, że jednostki mogą być zagnieżdżane (na przykład jednostka źródłowa może zawierać obiekt pliku, który będzie miał atrybut nazwy).

Aby dowiedzieć się więcej na temat struktury jednostek OSSEM, zapoznaj się z [oficjalnym odwołaniem do OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Jak są implementowane znormalizowane schematy na platformie Azure — wskaźnik

W implementacji modelu CIM OSSEM na platformie Azure — przedstawiamy reprezentację OSSEM do Log Analyticsej reprezentacji tabelarycznej, niezależnie od tego, czy ta reprezentacja jest tabelą wbudowaną, czy też została utworzona za pomocą analizatorów czasu zapytania lub funkcji, które mapują istniejące dane na tę reprezentację. W przypadku reprezentacji tabelarycznej są łączone nazwy jednostek OSSEM i nazwy atrybutów i są one mapowane zbiorczo na nazwę pojedynczej kolumny. Na przykład jednostka źródłowa zawierająca jednostkę pliku zawierającą jednostkę skrótu zawierającą atrybut MD5 zostanie zaimplementowana jako następująca kolumna Log Analytics: SrcFileHashMd5. (OSSEM domyślnie używa *snake_case* , podczas gdy platforma Azure i log Analytics używają *PascalCase*. W OSSEM takie kolumny byłyby src_file_hash_md5.)

Dodatkowe pola niestandardowe mogą znajdować się w implementacji wskaźnikowej platformy Azure, ze względu na wymagania dotyczące platformy Log Analytics i przypadki użycia, które są specyficzne dla klientów usługi Azure wskaźnikowej.

### <a name="schema-reference"></a>Odwołanie do schematu

Aby dowiedzieć się więcej, zobacz [dokument referencyjny schematu](./normalization-schema.md), a także oficjalną [dokumentację projektu OSSEM](https://ossemproject.com/cdm/intro.html).

Odwołanie do schematu zawiera również standaryzację wartości i formatu. Pola źródłowe, oryginalne lub przeanalizowane, mogą nie być w formacie standardowym ani używać standardowej listy wartości schematu, dlatego należy je przekonwertować na standardową reprezentację schematu, aby można było w pełni znormalizować.

## <a name="parsers"></a>Parsery

### <a name="what-is-parsing"></a>Co to jest analiza

Po udostępnieniu podstawowego zestawu zdefiniowanych znormalizowanych tabel będzie konieczne przekształcenie (analizowanie/mapowanie) danych do tych tabel. Oznacza to, że wyodrębnisz określone dane ze swojej pierwotnej postaci do dobrze znanych kolumn w znormalizowanym schemacie. Analiza na platformie Azure wskaźnikowa odbywa się w **czasie wykonywania zapytań** — analizatory czasu są kompilowane jako log Analytics funkcje użytkownika (za pomocą Kusto Query Language-KQL), które przekształcają dane w istniejących tabelach (takich jak CommonSecurityLog, tabele dzienników niestandardowych, dziennik systemowy) w schemat znormalizowanych tabel.

Inne rodzaje analiz, które nie są jeszcze obsługiwane w przypadku platformy Azure, są w **czasie** pozyskiwania, co umożliwia zbieranie danych bezpośrednio do znormalizowanych tabel, ponieważ są one pozyskiwane ze źródeł danych. Analiza czasu pozyskiwania zapewnia lepszą wydajność, ponieważ model danych jest wysyłany bezpośrednio bez konieczności używania funkcji.

### <a name="using-query-time-parsers"></a>Korzystanie z analizatorów czasu zapytania

#### <a name="installing-a-parser"></a>Instalowanie parsera

Dostępne analizatory czasu zapytania są dostępne w [oficjalnym repozytorium GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))usługi Azure. Każdy parser ma wersję, która umożliwia klientom łatwe używanie i monitorowanie przyszłych aktualizacji. Aby zainstalować parser:

1. Skopiuj odpowiednią zawartość parsera z każdego odpowiedniego pliku KQL w powyższym linku GitHub do schowka

1. W portalu Azure, Otwórz stronę Dzienniki i wklej zawartość pliku KQL na ekranie dzienników, a następnie kliknij przycisk **Zapisz**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Zainstaluj nowy parser":::

1. W oknie dialogowym Zapisywanie Wypełnij pola w następujący sposób:
    1. **Nazwa**: zaleca się używanie tej samej wartości, która jest używana w polu **alias funkcji** (w powyższym przykładzie, *CheckPoint_Network_NormalizedParser*)
    
    1. **Zapisz jako**: Wybierz **funkcję**

    1. **Alias funkcji**: powinien być nazwany w następującej konwencji nazewnictwa — *PRODUCT_Network_NormalizedParser* (w powyższym przykładzie *CheckPoint_Network_NormalizedParser*).

    1. **Kategoria**: możesz wybrać istniejącą kategorię lub utworzyć nową kategorię (na przykład *NormalizedNetworkSessionsParsers*).
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Zapisz parser":::

Aby prawidłowo używać analizatorów, należy również zainstalować pusty Analizator schematu sieciowego (w którym jest tworzony pusty Widok tabelaryczny wszystkich pól schematu sesji sieci) i meta-parser sieciowy (który razem wszystkie włączone analizatory mają utworzyć pojedynczy widok danych z różnych źródeł w schemacie sieciowym). Instalowanie tych dwóch analizatorów odbywa się w podobny sposób do powyższych kroków.

Po zapisaniu funkcji zapytania może być konieczne zamknięcie Eksploratora zapytań i ponowne otwarcie go w celu odzwierciedlenia nowej funkcji.

#### <a name="using-the-parsers"></a>Korzystanie z analizatorów

Po włączeniu można użyć meta-parser do zapytania ujednoliconego widoku na wszystkich aktualnie włączonych analizatorów. Aby to zrobić, przejdź na stronę dzienników wskaźnikowych platformy Azure i wykonaj zapytanie dotyczące meta-parsera:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Zbadaj parser":::
 
Możesz również uzyskać dostęp do meta-parsera lub poszczególnych analizatorów przy użyciu Eksploratora zapytań na stronie dzienniki, klikając pozycję "Eksplorator zapytań":

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Eksplorator zapytań":::

W okienku po prawej stronie rozwiń sekcję "zapisane zapytania" i Znajdź folder "NormalizedNetworkParsers" (lub nazwę kategorii wybraną podczas tworzenia analizatorów):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Znajdź parser":::

Można klikać poszczególne parsery i zobaczyć podstawową funkcję, która używa, i uruchamiać ją (lub uzyskiwać dostęp bezpośrednio przez jej alias, zgodnie z powyższym opisem). Należy zauważyć, że niektóre analizatory mogą przechowywać oryginalne pola obok znormalizowanych pól dla wygody. Można to łatwo edytować w zapytaniu analizatora składni.

#### <a name="customizing-parsers"></a>Dostosowywanie analizatorów

Można powtórzyć powyższe kroki (Znajdowanie analizatora w Eksploratorze zapytań), kliknąć odpowiedni parser i zapoznać się z jego implementacją funkcji.
Na przykład możesz zdecydować się na edytowanie meta-parsera w celu dodania/usunięcia poszczególnych analizatorów.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Dostosowywanie parsera":::
 
Po zmianie funkcji kliknij pozycję "Zapisz" ponownie i Użyj tej samej nazwy, aliasu i kategorii. Zostanie otwarte okno dialogowe przesłonięcia — naciśnij przycisk "OK":

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Czy na pewno":::

#### <a name="additional-information"></a>Dodatkowe informacje

Dowiedz się więcej o [zapisanych zapytaniach](../azure-monitor/log-query/example-queries.md) (implementacja analizatorów czasu zapytania) w log Analytics.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono informacje o schemacie normalizacji na platformie Azure. Aby zapoznać się ze schematem referencyjnym, zobacz [Informacje o schemacie normalizacji danych na platformie Azure](./normalization-schema.md).

* [Blog dotyczący platformy Azure](https://aka.ms/azuresentinelblog). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.