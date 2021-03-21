---
title: Trenowanie modelu Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu uczenie Vowpal Wabbit utworzyć model uczenia maszynowego przy użyciu wystąpienia Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657658"
---
# <a name="train-vowpal-wabbit-model"></a>Trenowanie modelu Vowpal Wabbit
W tym artykule opisano sposób używania modułu **uczenie Vowpal Wabbit** w programie Azure Machine Learning Designer do tworzenia modelu uczenia maszynowego przy użyciu Vowpal Wabbit.  

Aby użyć Vowpal Wabbit do uczenia maszynowego, sformatuj dane wejściowe zgodnie z wymaganiami Vowpal Wabbit, a następnie przygotuj dane w wymaganym formacie. Użyj tego modułu, aby określić argumenty wiersza polecenia Vowpal Wabbit. 

Po uruchomieniu potoku wystąpienie Vowpal Wabbit jest załadowane do eksperymentu czasu wykonywania, wraz z określonymi danymi. Po zakończeniu szkolenia model jest serializowany z powrotem do obszaru roboczego. Możesz użyć modelu natychmiast do oceny danych. 

Aby stopniowo przeszkolić istniejący model dla nowych danych, podłącz zapisany model do **wstępnie przeszkolonego portu wejściowego modelu Vowpal Wabbit** , **a** następnie Dodaj nowe dane do innego portu wejściowego.  

## <a name="what-is-vowpal-wabbit"></a>Co to jest Vowpal Wabbit?  

Vowpal Wabbit (VW) to szybka, równoległa platforma uczenia maszynowego opracowana na potrzeby rozproszonego przetwarzania danych przez platformę Yahoo! Research. Później został on przeprowadzony do systemu Windows i dostosowany przez Jan Langford (Microsoft Research) na potrzeby naukowych obliczeń w architekturze równoległej.  

Funkcje programu Vowpal Wabbit, które są ważne w przypadku uczenia maszynowego, obejmują funkcję ciągłej nauki (uczenie online), zmniejszenie liczby wymiarów i uczenie interaktywne. Vowpal Wabbit to również rozwiązanie problemów, gdy nie można dopasować danych modelu do pamięci.  

Głównymi użytkownikami usługi Vowpal Wabbit są analityki danych, którzy wcześniej korzystali z platformy do zadań uczenia maszynowego, takich jak Klasyfikacja, regresja, modelowanie tematów lub factorization macierzy. Otoka platformy Azure dla Vowpal Wabbit ma podobne charakterystyki wydajności do wersji lokalnej, dzięki czemu można używać zaawansowanych funkcji i natywnych wydajności Vowpal Wabbit, a także łatwo publikować przeszkolony model jako usługę operacyjną.  

Moduł [skrótu funkcji](feature-hashing.md) zawiera również funkcje zapewniane przez Vowpal Wabbit, które umożliwiają przekształcanie zestawów danych tekstowych w funkcje binarne przy użyciu algorytmu wyznaczania wartości skrótu.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Jak skonfigurować model Wabbit Vowpal  

W tej sekcji opisano, jak szkolić nowy model i jak dodawać nowe dane do istniejącego modelu.

W przeciwieństwie do innych modułów w projektancie, ten moduł określa parametry modułu i pociąga za sobą model. Jeśli masz istniejący model, możesz dodać go jako opcjonalne dane wejściowe, aby przyrostowo szkolić model.

+ [Przygotuj dane wejściowe w jednym z wymaganych formatów](#prepare-the-input-data)
+ [Uczenie nowego modelu](#create-and-train-a-vowpal-wabbit-model)
+ [Przyrostowe uczenie istniejącego modelu](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Aby szkolić model przy użyciu tego modułu, wejściowy zestaw danych musi składać się z pojedynczej kolumny tekstowej w jednym z dwóch obsługiwanych formatów: **SVMLight** lub **VW**. Nie oznacza to, że Vowpal Wabbit analizuje tylko dane tekstowe, tylko te funkcje i wartości muszą być przygotowane w wymaganym formacie plików tekstowych.  

Dane można odczytywać z dwóch rodzajów zestawów danych, plików DataSet lub tabelarycznego zestawu danych. Oba te zestawy danych muszą być w formacie SVMLight lub VW. Vowpal Wabbit format danych ma zalety, że nie wymaga formatu kolumnowego, co oszczędza miejsce podczas korzystania z danych rozrzedzonych. Aby uzyskać więcej informacji na temat tego formatu, zobacz [stronę typu wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Tworzenie i uczenie modelu Wabbit Vowpal

1. Dodaj moduł **uczenie modelu Wabbit Vowpal** do Twojego eksperymentu. 
  
2. Dodaj zestaw danych szkoleniowych i podłącz go do **danych szkoleniowych**. Jeśli zestaw danych szkoleniowych jest katalogiem zawierającym plik danych szkoleniowych, określ nazwę pliku danych szkoleniowych o nazwie **pliku danych szkoleniowych**. Jeśli zestaw danych szkoleniowych jest pojedynczym plikiem, pozostaw **nazwę pliku danych szkoleniowych** , który ma być pusty.

3. W polu tekstowym **argumenty VW** wpisz argumenty wiersza polecenia dla pliku wykonywalnego Vowpal Wabbit.

     Na przykład można dodać, *`–l`* Aby określić tempo uczenia lub *`-b`* wskazać liczbę bitów mieszania.  

     Aby uzyskać więcej informacji, zobacz sekcję [Parametry Vowpal Wabbit](#supported-and-unsupported-parameters) .  

4. **Nazwa pliku danych szkoleniowych**: wpisz nazwę pliku, który zawiera dane wejściowe. Ten argument jest używany tylko wtedy, gdy zestaw danych szkoleniowych jest katalogiem.

5. **Określ typ pliku**: wskazuje, który format jest używany przez dane szkoleniowe. Vowpal Wabbit obsługuje te dwa formaty plików wejściowych:  

    - **VW** reprezentuje wewnętrzny format używany przez Vowpal Wabbit. Aby uzyskać szczegółowe informacje, zobacz [stronę typu wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - **SVMLight** to format używany przez inne narzędzia do uczenia maszynowego. 

6. **Plik modelu do odczytu danych wyjściowych**: wybierz opcję, jeśli chcesz, aby moduł zapisywał model możliwy do odczytu w rekordach uruchomieniowych. Ten argument odpowiada `--readable_model` parametrowi w wierszu polecenia VW.  

7. **Wyjściowy odwrócony plik skrótu**: wybierz opcję, jeśli chcesz, aby moduł zapisywał odwróconą funkcję skrótu do jednego pliku w rekordach uruchomieniowych. Ten argument odpowiada `--invert_hash` parametrowi w wierszu polecenia VW.  

8. Prześlij potok.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Ponowne uczenie istniejącego modelu Wabbit Vowpal

Vowpal Wabbit obsługuje szkolenia przyrostowe przez dodanie nowych danych do istniejącego modelu. Istnieją dwa sposoby, aby uzyskać istniejący model do ponownego szkolenia:

+ Użyj danych wyjściowych innego modułu **uczenie Vowpal Wabbit** w tym samym potoku.  
  
+ Znajdź zapisany model w kategorii **zestawy danych** w lewym okienku nawigacji projektanta, a następnie przeciągnij go do potoku.  

1. Dodaj moduł **szkolenia Vowpal Wabbit model** do potoku.  
2. Połącz wcześniej szkolony model z wstępnie szkolonym portem wejściowym **modelu Vowpal Wabbit** modułu.
3. Połącz nowe dane szkoleniowe z portem wejściowym **danych szkoleniowych** modułu.
4. W okienku parametry **modelu uczenie Vowpal Wabbit** Określ format nowych danych szkoleniowych, a także nazwę pliku danych szkoleniowych, jeśli wejściowy zestaw danych jest katalogiem.
5. Wybierz **plik modelu danych wyjściowych do odczytu** i **wyjściowe opcje pliku skrótu odwrócone** , jeśli odpowiednie pliki mają być zapisane w rekordach uruchomieniowych.

6. Prześlij potok.  
7. Wybierz moduł i wybierz kolejno pozycje **zarejestruj zestaw danych** na karcie dane **wyjściowe + dzienniki** w okienku po prawej stronie, aby zachować zaktualizowany model w obszarze roboczym Azure Machine Learning.  Jeśli nie określisz nowej nazwy, zaktualizowany model zastąpi istniejący zapisany model.

## <a name="results"></a>Wyniki

+ Aby wygenerować wyniki z modelu, użyj [Vowpal oceny Wabbit model](score-vowpal-wabbit-model.md).

> [!NOTE]
> Jeśli musisz wdrożyć szkolony model w projektancie, upewnij się, że [model oceny Vowpal Wabbit](score-vowpal-wabbit-model.md) zamiast **modelu oceny** jest połączony z danymi wejściowymi [modułu danych wyjściowych usługi sieci Web](web-service-input-output.md) w potoku wnioskowania.

## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="advantages-of-vowpal-wabbit"></a>Zalety Vowpal Wabbit

Vowpal Wabbit zapewnia bardzo szybką naukę na funkcjach nieliniowych, takich jak n-gramy.  

Vowpal Wabbit używa technik *uczenia w trybie online* , takich jak pozostały gradient STOCHASTYCZNEGO (SGD), aby dopasować model do jednego rekordu w danym momencie. W ten sposób wielokrotnie wykonuje iterację w przypadku nieprzetworzonych danych i może opracowywać dobry predykcyjny szybciej niż większość innych modeli. Takie podejście zapobiega również odczytaniu wszystkich danych szkoleniowych do pamięci.  

Vowpal Wabbit konwertuje wszystkie dane na skróty, a nie tylko dane tekstowe, ale inne zmienne kategorii. Użycie skrótów sprawia, że wyszukiwanie wag regresji jest bardziej wydajne, co jest niezwykle ważne dla efektywnego gradientu stochastycznego.  

###  <a name="supported-and-unsupported-parameters"></a>Obsługiwane i nieobsługiwane parametry 

W tej sekcji opisano obsługę parametrów wiersza polecenia Vowpal Wabbit w programie Azure Machine Learning Designer. 

Ogólnie rzecz biorąc, wszystkie oprócz ograniczony zestaw argumentów są obsługiwane. Aby uzyskać pełną listę argumentów, użyj [strony typu wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

Następujące parametry nie są obsługiwane:

-   Opcje wejścia/wyjścia określone w [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Te właściwości są już konfigurowane automatycznie przez moduł.  
  
-   Ponadto każda opcja, która generuje wiele wyjść lub pobiera wiele danych wejściowych, jest niedozwolona. Należą *`--cbt`* do nich, *`--lda`* i *`--wap`* .  
  
-   Obsługiwane są tylko algorytmy uczenia nadzorowanego. W związku z tym te opcje nie są obsługiwane: *`–active`* , `--rank` *`--search`* itp. 

### <a name="restrictions"></a>Ograniczenia

Ponieważ celem usługi jest obsługa doświadczonych użytkowników z Vowpal Wabbit, dane wejściowe muszą być przygotowane wcześniej przy użyciu natywnego formatu tekstowego Vowpal Wabbit, a nie do formatu zestawu danych używanego przez inne moduły.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
