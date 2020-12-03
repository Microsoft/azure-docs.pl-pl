---
title: Zrozumienie funkcji słownika biznesowego w usłudze Azure kontrolą (wersja zapoznawcza)
description: W tym artykule wyjaśniono, co słownik biznesowy znajduje się w usłudze Azure kontrolą.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555156"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Zrozumienie funkcji słownika biznesowego w usłudze Azure kontrolą

Ten artykuł zawiera omówienie funkcji słownik biznesowy w usłudze Azure kontrolą. 

## <a name="business-glossary"></a>Słownik biznesowy

Słownik zawiera słownictwo dla użytkowników firmowych.  Zawiera warunki biznesowe, które mogą być ze sobą powiązane i umożliwiają ich kategoryzowanie w różnych kontekstach. Te warunki mogą być następnie mapowane na zasoby, takie jak baza danych, tabele, kolumny itd. Pomaga to w pokryciu żargon technicznych skojarzonych z repozytoriami danych i umożliwia użytkownikowi biznesowemu odnajdywanie i praca z danymi w słowniku, który jest bardziej znany.


Słownik biznesowy to zbiór warunków. Każdy termin reprezentuje obiekt w organizacji i bardzo prawdopodobnie istnieje wiele warunków reprezentujących ten sam obiekt. Klient może być również nazywany klientem, nabywcą lub nabywcą. Te wiele warunków ma związek ze sobą. Relacja między tymi warunkami może być jedną z następujących:

- Synonimy — różne terminy o tej samej definicji
- powiązana — inna nazwa z podobną definicją

Ten sam termin może również oznaczać wiele obiektów firmowych. Należy pamiętać, że każdy termin jest dobrze zdefiniowany i zrozumiały w organizacji.

## <a name="custom-attributes"></a>Atrybuty niestandardowe

Usługa Azure kontrolą obsługuje osiem wbudowanych atrybutów dla dowolnego terminu na słownik biznesowy:
- Nazwa
- Definicja
- Stewards danych
- Eksperci danych
- Wytycznym dotyczącym skrótowców
- Synonimy
- Powiązane warunki
- Zasoby

Tych atrybutów nie można edytować ani usunąć. Jednak te atrybuty nie są wystarczające, aby całkowicie definiować termin w organizacji. Aby rozwiązać ten problem, kontrolą udostępnia funkcję, w której można zdefiniować atrybuty niestandardowe dla słownika.

## <a name="term-templates"></a>Szablony terminów

Szablony terminów zawierają atrybuty niestandardowe słowników, które mają być logicznie zgrupowane razem w katalogu. Ta funkcja umożliwia grupowanie wszystkich odpowiednich atrybutów niestandardowych razem z szablonem, a następnie zastosowanie szablonu podczas tworzenia terminu słownika. Na przykład wszystkie powiązane z finansami niestandardowe atrybuty, takie jak centrum kosztów, centrum zysku, kod księgowości mogą być pogrupowane w szablonach finanse szablonu warunku, a szablon finansowy może służyć do tworzenia warunków słownika finansowego.

Wszystkie atrybuty standardowe są pogrupowane w domyślnym szablonie systemowym. Każdy utworzony szablon warunku będzie zawierał te atrybuty wraz z wszelkimi dodatkowymi atrybutami niestandardowymi utworzonymi w ramach procesu tworzenia szablonu.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Słownik vs — etykiety czułości

Warunki słownikowe, klasyfikacje i etykiety są adnotacjami do zasobu danych, a każdy z nich ma inne znaczenie w kontekście katalogu. 

### <a name="glossary"></a>Słownik

Jak wspomniano powyżej, pojęcie słownika biznesowego definiuje słownictwo firmy dla organizacji i pomaga w mostkowania przerwy między różnymi działami w firmie.

### <a name="classifications"></a>Klasyfikacje

Klasyfikacje to adnotacje, które można przypisać do jednostek. Elastyczność klasyfikacji umożliwia korzystanie z nich w wielu scenariuszach, takich jak:

- zrozumienie charakteru danych przechowywanych w zasobach danych
- Definiowanie zasad kontroli dostępu

Kontrolą ma więcej niż 100 klasyfikatorów systemowych i można zdefiniować własne klasyfikatory w wykazie. W ramach procesu skanowania automatycznie wykrywamy te klasyfikacje i stosują je do zasobów i schematów danych. Można jednak je zastąpić w dowolnym momencie. Zastąpienia przez ludzi nigdy nie są zastępowane przez automatyczne skanowanie.

### <a name="sensitivity-labels"></a>Etykiety poufności

Etykiety czułości są rodzajem adnotacji, która pozwala na klasyfikowanie i ochronę danych organizacji, bez zakłócania produktywności i współpracy. Etykiety czułości są używane do identyfikowania kategorii typów klasyfikacji w danych organizacji i grupowania zasad, które mają być stosowane do każdej kategorii. Kontrolą wykorzystuje te same typy informacji poufnych co Microsoft 365, co pozwala rozciągnąć istniejące zasady zabezpieczeń i ochronę całej zawartości i danych. Te same etykiety mogą być współużytkowane przez Microsoft Office produkty i zasoby danych w kontrolą.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie szablonami terminów](how-to-manage-term-templates.md)
- [Przeglądanie wykazu danych w usłudze Azure kontrolą](how-to-browse-catalog.md)
