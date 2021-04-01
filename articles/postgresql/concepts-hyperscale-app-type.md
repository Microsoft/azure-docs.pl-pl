---
title: Określanie typu aplikacji — funkcja Citus) — Azure Database for PostgreSQL
description: Zidentyfikuj swoją aplikację w celu efektywnego modelowania danych rozproszonych
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90895960"
---
# <a name="determining-application-type"></a>Określanie typu aplikacji

Uruchamianie wydajnych zapytań w grupie serwerów w ramach funkcji wieloskali (Citus) wymaga, aby tabele były prawidłowo dystrybuowane między serwerami. Zalecana dystrybucja jest różna w zależności od typu aplikacji i jej wzorców zapytań.

Istnieją rozległie dwa rodzaje aplikacji, które działają dobrze na Citus. Pierwszym krokiem modelowania danych jest zidentyfikowanie, który z nich jest bardziej zbliżony do Twojej aplikacji.

## <a name="at-a-glance"></a>Na pierwszy rzut oka

| Aplikacje z wieloma dzierżawcami                                 | Aplikacje działające w czasie rzeczywistym                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Czasami dziesiątki lub setki tabel w schemacie          | Mała liczba tabel                                |
| Zapytania dotyczące jednej dzierżawy (firmy/magazynu) w danym momencie | Stosunkowo proste zapytania analityczne z agregacjami |
| Obciążenia OLTP obsługujące klientów internetowych                    | Duża ilość pozyskiwanych danych (zwykle niemodyfikowalnych)           |
| Obciążenia OLAP obsługujące zapytania analityczne dla poszczególnych dzierżaw   | Często skoncentrowane na dużej tabeli zdarzeń            |

## <a name="examples-and-characteristics"></a>Przykłady i cechy

**Aplikacja wielodostępna**

> Są to zazwyczaj aplikacje SaaS, które obsługują inne firmy, konta lub organizacje. Większość aplikacji SaaS jest relacyjna. Mają naturalny wymiar, na którym są dystrybuowane dane między węzłami: po prostu fragmentu według identyfikatora dzierżawy \_ .
>
> Funkcja Citus) umożliwia skalowanie bazy danych do milionów dzierżawców bez konieczności ponownego tworzenia architektury aplikacji. Można zachować wymaganą semantykę relacyjną, taką jak sprzężenia, ograniczenia klucza obcego, transakcje, kwas i spójność.
>
> -   **Przykłady**: witryny sieci Web, które obsługują magazyny dla innych firm, takie jak rozwiązanie do marketingu cyfrowego lub narzędzie do automatyzacji sprzedaży.
> -   **Charakterystyki**: zapytania dotyczące pojedynczej dzierżawy zamiast dołączania informacji między dzierżawcami. Obejmuje to obciążenia OLTP obsługujące klientów sieci Web i obciążenia OLAP obsługujące kwerendy analityczne dla poszczególnych dzierżawców. Posiadanie dziesiątek lub setek tabel w schemacie bazy danych jest również wskaźnikiem modelu danych z wieloma dzierżawcami.
>
> Skalowanie aplikacji wielodostępnej przy użyciu funkcji Citus) wymaga również minimalnych zmian w kodzie aplikacji. Firma Microsoft obsługuje popularne struktury, takie jak Ruby na szynach i Django.

**Analiza w czasie rzeczywistym**

> Aplikacje wymagające ogromnej równoległości, koordynując setki rdzeni w celu szybkiego przesyłania do liczbowych, statystycznych lub zliczanych zapytań.  Fragmentowania i przekształcają zapytania SQL w wielu węzłach, funkcja wieloskalowania (Citus) umożliwia wykonywanie zapytań w czasie rzeczywistym w ramach miliardów rekordów w drugim.
>
> Tabele w modelach danych analitycznych w czasie rzeczywistym są zwykle dystrybuowane według kolumn \_ , takich jak identyfikator użytkownika, \_ Identyfikator hosta lub \_ Identyfikator urządzenia.
>
> -   **Przykłady**: dostępne dla klientów pulpity nawigacyjne analityczne wymagające czasu odpowiedzi podsekundu.
> -   **Charakterystyka**: kilka tabel, często wyśrodkowanie wokół dużej tabeli zdarzeń dotyczących urządzeń, witryn i użytkowników oraz wymaganie dużej ilości danych w większości niezmiennych. Relatywnie prosta (ale z dużym obciążeniem) zapytania analityczne obejmujące kilka agregacji i grup BYs.

Jeśli sytuacja jest podobna do każdego z powyższych przypadków, następnym krokiem jest podjęcie decyzji, jak fragmentu dane w grupie serwerów. \'Aby zapewnić wydajność, należy wybrać kolumny dystrybucji przez administratora bazy danych, które muszą być zgodne ze wzorcami dostępu typowych zapytań.

## <a name="next-steps"></a>Następne kroki

* [Wybierz kolumnę dystrybucji](concepts-hyperscale-choose-distribution-column.md) dla tabel w aplikacji, aby efektywnie rozpowszechniać dane
