---
title: Dane przetworzone z użyciem puli SQL bez serwera
description: W tym dokumencie opisano sposób obliczania przetworzonych danych podczas wykonywania zapytań dotyczących danych w usłudze Azure Storage przy użyciu puli SQL bezserwerowej.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424218"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Dane przetworzone z użyciem puli SQL bezserwerowej w usłudze Azure Synapse Analytics

Przetworzone dane to ilość danych tymczasowo przechowywanych w systemie podczas wykonywania zapytania i składa się z:

- Ilość danych odczytanych z magazynu — w tym:
  - Ilość danych odczytanych podczas odczytywania danych
  - Ilość danych odczytanych podczas odczytywania metadanych (dla formatów plików zawierających metadane, takich jak parquet)
- Ilość danych w wynikach pośrednich — dane przesyłane między węzłami podczas wykonywania zapytania, w tym transfer danych do punktu końcowego, w formacie nieskompresowanym. 
- Ilość danych zapisywana w magazynie — Jeśli używasz CETAS do eksportowania zestawu wyników do magazynu, zostanie naliczona opłata za bajty nadane i ilość danych przetworzonych dla wybranych części CETAS.

Odczytywanie plików z magazynu jest wysoce zoptymalizowane i stosowane:

- Wstępne pobieranie — co może spowodować niewielkie obciążenie ilością odczytywanych danych. Jeśli zapytanie odczytuje cały plik, nie będą naliczane żadne dodatkowe koszty. Jeśli plik jest odczytywany częściowo, podobnie jak w przypadku pierwszych N zapytań, dane zostaną odczytane przy użyciu pobierania z wyprzedzeniem.
- Zoptymalizowany Analizator CSV — Jeśli używasz PARSER_VERSION = "2.0" do odczytywania plików CSV, spowoduje to nieco większe ilości danych odczytywanych z magazynu.  Zoptymalizowany Analizator CSV odczytuje pliki równolegle w fragmentach o równym rozmiarze. Nie ma gwarancji, że fragmenty nie będą zawierać całych wierszy. Aby upewnić się, że wszystkie wiersze są analizowane, można także odczytać małe fragmenty sąsiadujących fragmentów, co spowoduje dodanie niewielkiego nakładu pracy.

## <a name="statistics"></a>Statystyki

Optymalizator zapytań puli SQL bezserwerowej opiera się na statystyce do generowania optymalnych planów wykonywania zapytań. Statystyki można utworzyć ręcznie lub zostaną one utworzone automatycznie przez pulę SQL bezserwerową. W obu przypadkach statystyki są tworzone przez wykonanie oddzielnego zapytania, które zwraca określoną kolumnę przy podanej szybkości próbkowania. To zapytanie ma skojarzoną ilość przetworzonych danych.

W przypadku uruchomienia tego samego lub innego zapytania, które byłyby korzystne dla utworzonych statystyk, statystyki będą ponownie używane, jeśli jest to możliwe, a żadne dodatkowe dane nie będą przetwarzane na potrzeby tworzenia statystyk.

Tworzenie statystyk dla kolumny Parquet spowoduje odczytanie tylko odpowiedniej kolumny z plików. Tworzenie statystyk dla kolumny CSV spowoduje odczyt i przeanalizowanie całych plików.

## <a name="rounding"></a>Zaokrąglanie

Ilość przetworzonych danych zostanie zaokrąglona do najbliższych MB na zapytanie, a co najmniej 10 MB danych zostanie przetworzonych na zapytanie.

## <a name="what-is-not-included-in-data-processed"></a>Co nie jest uwzględniane w przetworzonych danych

- Metadane na poziomie serwera (takie jak nazwy logowania, role, poświadczenia na poziomie serwera)
- Bazy danych tworzone w punkcie końcowym, w których te bazy danych zawierają tylko metadane (takie jak użytkownicy, role, schematy, widoki, TVFs wbudowane, procedury składowane, poświadczenia z zakresem bazy danych, zewnętrzne źródła danych, zewnętrzne formaty plików, tabele zewnętrzne)
  - Jeśli używasz wnioskowania schematu, fragmenty plików zostaną odczytane do nazw kolumn i typów danych
- Instrukcje języka DDL z wyjątkiem instrukcji CREATE STATISTICs, ponieważ będą przetwarzać dane z magazynu na podstawie określonego procentu próbki
- Zapytania zawierające tylko metadane

## <a name="reduce-amount-of-data-processed"></a>Zmniejsz ilość przetworzonych danych

Możesz zoptymalizować ilość przetworzonych danych i uzyskać lepszą wydajność dzięki partycjonowaniu i konwertowaniu danych do skompresowanego formatu kolumn, takiego jak Parquet.

## <a name="examples"></a>Przykłady

Załóżmy, że istnieją dwie tabele, z których każda ma te same dane w pięciu kolumnach o równym rozmiarze:

- population_csv tabelę podutworzoną przez 5 TB plików CSV
- population_parquet tabelę, której kopia zapasowa jest następująca: 1 TB plików Parquet — ta tabela jest mniejsza niż poprzednia, ponieważ Parquet zawiera dane skompresowane
- very_small_csv tabelę objętą 100 KB plików CSV

**#1 kwerendy** : wybierz pozycję sum (populacja) z population_csv

To zapytanie będzie odczytywać i analizować całe pliki, aby uzyskać wartości dla kolumny populacji. Węzły będą przetwarzać fragmenty tej tabeli, suma populacji dla każdego fragmentu zostanie przeniesiona między węzłami, a końcowa suma zostanie przeniesiona do punktu końcowego. To zapytanie będzie przetwarzać 5 TB danych i niewielkie obciążenie związane z transferem sum fragmentów.

**#2 kwerendy** : wybierz pozycję sum (populacja) z population_parquet

Wykonywanie zapytań dotyczących formatów skompresowanych i zorientowanych na kolumny, takich jak Parquet, umożliwia odczytywanie mniejszej ilości danych niż w poprzedniej kwerendzie, ponieważ bezserwerowa Pula SQL odczytuje pojedynczą skompresowaną kolumnę zamiast całego pliku. W tym przypadku 0,2 TB zostanie odczytane (pięć kolumn o równym rozmiarze, 0,2 TB). Węzły będą przetwarzać fragmenty tej tabeli, suma populacji dla każdego fragmentu zostanie przeniesiona między węzłami, a końcowa suma zostanie przeniesiona do punktu końcowego. To zapytanie będzie przetwarzać 0,2 TB i niewielkie obciążenie związane z transferem sum fragmentów.

**#3 kwerendy** : select * from population_parquet

To zapytanie odczytaje wszystkie kolumny i przeniesie wszystkie dane w nieskompresowanym formacie. Jeśli format kompresji to 5:1, zostanie przetworzony 6 TB, ponieważ odczyta 1 TB + transfer 5 TB nieskompresowanych danych.

**#4 kwerendy** : Wybierz liczbę (*) z very_small_csv

To zapytanie odczyta całe pliki. Łączny rozmiar plików w magazynie dla tej tabeli to 100 KB. Węzły będą przetwarzać fragmenty tej tabeli, suma dla każdego fragmentu zostanie przeniesiona między węzłami, a końcowa suma zostanie przetransferowana do punktu końcowego. To zapytanie będzie przetwarzać nieco więcej niż 100 KB danych. Ilość danych przetworzonych dla tego zapytania zostanie zaokrąglona do 10 MB, jak określono w [zaokrągleniu](#rounding).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zoptymalizować zapytania pod kątem wydajności, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi puli SQL bezserwerowej](best-practices-sql-on-demand.md).
