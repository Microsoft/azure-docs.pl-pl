---
title: Azure File Sync warstw w chmurze | Microsoft Docs
description: Szczegółowe informacje o tym, jak zasady dotyczące daty i ilości wolnego miejsca współpracują ze sobą w różnych scenariuszach.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797368"
---
# <a name="cloud-tiering-policies"></a>Zasady warstw w chmurze

Warstwy w chmurze mają dwie zasady, które określają,  które pliki są warstwowe w chmurze: zasady wolnego miejsca na woluminie i **zasady daty**.

Zasady **wolnego miejsca na woluminie** zapewniają, że określony procent woluminu lokalnego, na którym znajduje się punkt końcowy serwera, jest zawsze wolny. 

Zasady **daty warstwy** plików ostatnio używane x dni temu lub nowszej. Zawsze pierwszeństwo będą miały zasady wolnego miejsca na woluminie. Jeśli na woluminie nie ma wystarczającej ilości wolnego miejsca do przechowywania tak wielu dni plików, jak opisano w zasadach daty, program Azure File Sync zastąpi zasady daty i będzie kontynuować przechowywanie warstw najchłodniejszych plików do momentu, gdy zostanie osiągnięty procent wolnego miejsca na woluminie.

## <a name="how-both-policies-work-together"></a>Jak obie zasady współpracują ze sobą

Użyjemy przykładu, aby zilustrować sposób działania tych zasad: Załóżmy, że skonfigurowano usługę Azure File Sync na woluminie lokalnym o pojemności 500 GB, a obsługę warstw w chmurze nigdy nie włączono. Oto pliki w twoim udziałach plików:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Serwer i udział plików platformy Azure
|Plik 4    | 1 rok, 2 dni temu | 130 GB | Serwer i udział plików platformy Azure
|Plik 5    | 2 lata, 1 dzień temu | 140 GB | Serwer i udział plików platformy Azure

**Zmień 1:** Włączono obsługę warstw w chmurze, ustawiono zasady wolnego miejsca na woluminie na 20% i wyłączono zasady daty. Dzięki tej konfiguracji warstwy w chmurze zapewniają, że 20% (w tym przypadku 100 GB) miejsca jest wolne i dostępne na komputerze lokalnym. W związku z tym łączna pojemność lokalnej pamięci podręcznej wynosi 400 GB. To 400 GB będzie przechowywać ostatnio używane pliki na woluminie lokalnym.

W przypadku tej konfiguracji w lokalnej pamięci podręcznej będą przechowywane tylko pliki od 1 do 4, a plik 5 będzie warstwowy. Jest to tylko 370 GB z 400 GB, które mogą być używane. Plik 5 ma rozmiar 140 GB i przekracza limit 400 GB, jeśli był lokalnie buforowany. 

**Zmień 2:** Załóżmy, że użytkownik uzyskuje dostęp do pliku 5. Dzięki temu plik 5 jest ostatnio dostępnym plikiem w udziału. W związku z tym plik 5 będzie przechowywany w lokalnej pamięci podręcznej i będzie mieścił się w granicach limitu 400 GB, a plik 4 będzie warstwowy. W poniższej tabeli przedstawiono miejsce przechowywania plików z następującymi aktualizacjami:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 5    | 2 godz. temu | 140 GB | Serwer i udział plików platformy Azure
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Serwer i udział plików platformy Azure
|Plik 4    | 1 rok, 2 dni temu | 130 GB | Udział plików platformy Azure, lokalnie warstwowy

**Zmień 3:** Załóżmy, że zaktualizowano zasady tak, aby zasady warstw oparte na dacie 60 dni, a zasady wolnego miejsca na woluminie wynosiły 70%. Teraz w lokalnej pamięci podręcznej można przechowywać tylko do 150 GB. Mimo że dostęp do pliku 2 był uzyskiwany mniej niż 60 dni temu, zasady wolnego miejsca na woluminie zastąpią zasady daty, a plik 2 jest warstwowy, aby zachować 70% wolnego miejsca lokalnego.

**Zmień 4:** Jeśli zasady dotyczące wolnego miejsca na woluminie zostały zmienione na 20%, a następnie wszystkie pliki zmieściły się na dysku lokalnym przy zachowaniu zasad warstw w chmurze, tabela będzie wyglądać `Invoke-StorageSyncFileRecall` tak:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 5    | 1 godz. temu  | 140 GB | Serwer i udział plików platformy Azure
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Udział plików platformy Azure, lokalnie warstwowy
|Plik 4    | 1 rok, 2 dni temu | 130 GB | Udział plików platformy Azure, lokalnie warstwowy

W takim przypadku pliki 1, 2 i 5 byłyby lokalnie buforowane, a pliki 3 i 4 byłyby warstwowe. Ponieważ zasady daty mają 60 dni, pliki 3 i 4 są warstwowe, mimo że zasady wolnego miejsca na woluminie zezwalają na lokalne maksymalnie 400 GB.

> [!NOTE] 
> Pliki nie są automatycznie przywołyowane, gdy klienci zmieniają zasady ilości wolnego miejsca na mniejszą wartość (na przykład z 20% do 10%) lub zmień zasady daty na większą wartość (na przykład z 20 dni do 50 dni).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Wiele punktów końcowych serwera na woluminie lokalnym

Obsługę warstw w chmurze można włączyć dla wielu punktów końcowych serwera na jednym woluminie lokalnym. W przypadku tej konfiguracji należy ustawić ilość wolnego miejsca na woluminie na taką samą ilość dla wszystkich punktów końcowych serwera na tym samym woluminie. Jeśli ustawisz różne zasady wolnego miejsca dla kilku punktów końcowych serwera na tym samym woluminie, pierwszeństwo będzie miała największa wartość procentowa wolnego miejsca woluminu. Ta zasada jest nazywana **efektywnymi zasadami wolnego miejsca na woluminie**. Jeśli na przykład masz trzy punkty końcowe serwera na tym samym woluminie lokalnym, jeden ustawiony na 15%, drugi na 20%, a trzeci na 30%, wszystkie zaczną warstwie najchłodniejszych plików, gdy mają mniej niż 30% wolnego miejsca.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie warstw w chmurze](file-sync-monitor-cloud-tiering.md)
