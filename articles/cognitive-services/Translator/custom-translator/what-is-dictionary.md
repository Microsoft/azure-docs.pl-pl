---
title: Co to jest słownik? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Słownik jest dokumentem wyrównanym, który określa listę fraz lub zdań (i ich tłumaczenia), które są zawsze potrzebne do tłumaczenia usługi Microsoft Translator w taki sam sposób. Słowniki są czasami nazywane również bazami Glossaries lub terminami.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f4c3d23f6abbdc20d210e5ddda6c527d27654bb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510763"
---
# <a name="what-is-a-dictionary"></a>Co to jest słownik?

Słownik jest wyrównanym parę dokumentów, które określają listę fraz lub zdań i odpowiadające im tłumaczenia. Użyj słownika w ramach szkolenia, jeśli chcesz, aby program Microsoft Translator zawsze przetłumaczy wszystkie wystąpienia frazy źródłowej lub zdania przy użyciu tłumaczenia podanego w słowniku. Słowniki są czasami nazywane glossariesami lub terminami. Słownik można traktować jako "Kopiuj i Zamień" dla wszystkich wystawianych terminów. Ponadto usługa translatora niestandardowego firmy Microsoft kompiluje i korzysta z własnych słowników ogólnego przeznaczenia, aby zwiększyć jakość tłumaczenia. Niemniej jednak słownik dostarczony przez klienta jest poprzedni i będzie przeszukiwany w pierwszej kolejności w poszukiwaniu słów lub zdań.

Słowniki działają tylko w przypadku projektów w parach języka, które mają w pełni obsługiwany model sieci Microsoft General neuronowych Network. [Zapoznaj się z pełną listą języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Słownik fraz
W słowniku fraz jest rozróżniana wielkość liter. Jest to dokładna operacja znajdowania i zamieniania. Po dołączeniu słownika wyrazów w ramach szkolenia modelu wszystkie wymienione wyrazy lub frazy są tłumaczone w określony sposób. Pozostałe zdanie jest tłumaczone w zwykły sposób. Możesz użyć słownika frazy, aby określić frazy, które nie powinny być tłumaczone przez podanie tej samej nieprzetłumaczonej frazy w pliku źródłowym i docelowym w słowniku.

## <a name="sentence-dictionary"></a>Słownik zdań
W słowniku zdań nie jest rozróżniana wielkość liter. Słownik zdań umożliwia określenie dokładnego tłumaczenia docelowego dla zdania źródłowego. Aby można było dopasować słownik zdań, całe przesłane zdanie musi być zgodne z wpisem słownika źródłowego. Jeśli wpis słownika źródłowego jest zakończony znakiem interpunkcji, jest ignorowany podczas dopasowywania. Jeśli tylko część zdania jest zgodna, wpis nie będzie zgodny.  Po wykryciu dopasowania zostanie zwrócony wpis docelowy słownika zdań.

## <a name="dictionary-only-trainings"></a>Szkolenia dotyczące tylko słownika
Możesz nauczyć model przy użyciu tylko danych słownika. W tym celu zaznacz tylko dokument słownika (lub wiele dokumentów słownika), które chcesz dołączyć, i naciśnij pozycję Utwórz model. Ponieważ jest to szkolenie tylko do słownika, nie jest wymagana minimalna liczba zdań szkoleniowych. Model zwykle kończy szkolenia znacznie szybciej niż w przypadku standardowego szkolenia.  Modele powstające będą używać modeli bazowych firmy Microsoft do tłumaczenia z dodaniem dodanych słowników.  Nie otrzymasz raportu testowego.

>[!Note]
>W przypadku translatora niestandardowego nie są wyrównania plików słowników, dlatego ważne jest, aby w dokumentach słownika była równa Liczba zwrotów źródłowych i docelowych, a także dokładnie wyrównane.

## <a name="recommendations"></a>Zalecenia

- Słowniki nie stanowią zamiennika szkolenia modelu przy użyciu danych szkoleniowych. Zaleca się ich uniknięcie i umożliwienie systemowi uczenia się od danych szkoleniowych. Jednak gdy zdania lub rzeczowniki złożone muszą być renderowane jako-is, należy użyć słownika.
- Słownik wyrazów powinien być oszczędnie używany. Należy więc pamiętać, że gdy fraza w zdaniu zostanie zastąpiona, kontekst w tym zdaniu zostanie utracony lub ograniczony do przetłumaczenia reszty zdania. Wynikiem tego jest to, że podczas gdy fraza lub wyraz w zdaniu zostanie przetłumaczy zgodnie z podanym słownikiem, ogólna jakość tłumaczenia zdania często się odnosi.
- Słownik fraz dobrze sprawdza się w przypadku niezłożonej rzeczowników, takich jak nazwy produktów ("Microsoft SQL Server"), odpowiednie nazwy ("miasto Hamburg") lub funkcje produktu ("tabela przestawna"). Nie działa równie dobrze w przypadku czasowników lub przymiotników, ponieważ są zwykle wysoce oddzielone w źródle lub w języku docelowym. Najlepsze praktyki polegają na uniknięciu wpisów słownika zwrotów dla niczego, ale rzeczowników złożonych.
- W przypadku korzystania z słownika fraz, wielkie litery i interpunkcja są ważne. Wpisy słownika dopasowują tylko wyrazy i frazy w zdaniu wejściowym, które używają dokładnie tej samej wielkości liter i znaków interpunkcyjnych, jak określono w pliku słownika źródłowego. Ponadto tłumaczenia będą odzwierciedlać wielkie litery i znaki interpunkcyjne podane w docelowym pliku słownika. Na przykład w przypadku przeszkolonego języka angielskiego do hiszpańskiego systemu, który używa słownika frazy, który określa "US" w pliku źródłowym i "EE. UU ". w pliku docelowym. Po zażądaniu tłumaczenia zdania zawierającego wyraz "US" (bez wersalików) nie pasuje do słownika. Jeśli jednak zażądasz tłumaczenia zdania zawierającego wyraz "US" (Wielka litera), będzie on pasować do słownika, a tłumaczenie będzie zawierać "EE". UU ". Należy zauważyć, że wielkie litery i znaki interpunkcyjne w tłumaczeniu mogą być inne niż określone w pliku docelowym słownika i mogą różnić się od wielkości liter i znaków interpunkcyjnych w źródle. Są one zgodne z regułami języka docelowego.
- Przy użyciu słownika zdań, koniec zdania jest ignorowany. Na przykład, jeśli słownik źródłowy zawiera wyrażenie "this kończące się znakiem interpunkcyjny!", wówczas wszystkie żądania tłumaczenia zawierające "to zdanie kończące się znakiem interpunkcyjny" byłyby zgodne.
- Jeśli słowo pojawia się więcej niż raz w pliku słownika, system zawsze będzie używać ostatniego dostarczonego wpisu. W związku z tym słownik nie powinien zawierać wielu tłumaczeń tego samego wyrazu.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [wytycznych dotyczących formatów dokumentów](document-formats-naming-convention.md).
