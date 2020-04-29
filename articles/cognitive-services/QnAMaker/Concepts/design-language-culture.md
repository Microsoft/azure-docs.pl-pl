---
title: Projektowanie dla QnA Maker języka
description: Zasób QnA Maker i wszystkie bazy wiedzy w tym zasobie obsługują jeden język. Pojedynczy język jest niezbędny do dostarczenia najlepszych wyników odpowiedzi na zapytanie.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76843424"
---
# <a name="design-knowledge-base-for-content-language"></a>Projektowanie bazy wiedzy dla języka zawartości

Zasób QnA Maker i wszystkie bazy wiedzy w tym zasobie obsługują jeden język. Pojedynczy język jest niezbędny do dostarczenia najlepszych wyników odpowiedzi na zapytanie.

## <a name="single-language-per-resource"></a>Pojedynczy język na zasób

QnA Maker zagadnienia dotyczące obsługi języka:

* Usługa QnA Maker i wszystkie jej bazy wiedzy obsługują tylko jeden język.
* Język jest jawnie ustawiany podczas tworzenia pierwszej bazy wiedzy usługi
* Język jest określany na podstawie plików i adresów URL dodanych podczas tworzenia bazy wiedzy
* Nie można zmienić języka dla żadnych innych baz wiedzy w usłudze
* Język jest używany przez usługę Wyszukiwanie poznawcze (ranga #1) i usługę QnA Maker (ranga #2) do generowania najlepszej odpowiedzi na zapytanie

## <a name="supporting-multiple-languages"></a>Obsługa wielu języków

Jeśli musisz obsługiwać system bazy wiedzy, który zawiera kilka języków, możesz wybrać jedną z następujących metod:

* Użyj [usługi tłumaczenia tekstu](../../translator/translator-info-overview.md) , aby przetłumaczyć pytanie na jeden język przed wysłaniem pytania do bazy wiedzy. Pozwala to skupić się na jakości jednego języka i jakości alternatywnych pytań i odpowiedzi.
* Utwórz zasób QnA Maker i bazę wiedzy w tym zasobie dla każdego języka. Pozwala to na zarządzanie oddzielnymi innymi pytaniami i tekstem odpowiedzi, który jest bardziej złożonych dla każdego języka. Zapewnia to znacznie większą elastyczność, ale wymaga znacznie większego kosztu konserwacji, gdy pytania lub odpowiedzi zmieniają się we wszystkich językach.

Przejrzyj [języki obsługiwane](../overview/language-support.md) przez QNA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Obsługa każdego języka za pomocą zasobu QnA Maker

* Utwórz zasób QnA Maker dla każdego języka
* Dodaj tylko pliki i adresy URL dla tego języka
* Użyj konwencji nazewnictwa dla zasobu, aby zidentyfikować język. Przykład dotyczy `qna-maker-fr` wszystkich baz wiedzy dla dokumentów francuskich

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [pojęciami](query-knowledge-base.md) dotyczącymi zapytania w bazie wiedzy.