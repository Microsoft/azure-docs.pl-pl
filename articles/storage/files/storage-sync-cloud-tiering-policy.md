---
title: Azure File Sync zasad obsługi warstw w chmurze | Microsoft Docs
description: Szczegółowe informacje o tym, jak zasady wolnego miejsca na dacie i woluminie współpracują ze sobą w różnych scenariuszach.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204433"
---
# <a name="cloud-tiering-policies"></a>Zasady obsługi warstw w chmurze

Obsługa warstw w chmurze obejmuje dwie zasady, które określają, które pliki są warstwowe w chmurze: **zasady wolnego miejsca na woluminie** i **zasady dotyczące daty**.

**Zasady wolnego miejsca na woluminie** zapewniają, że określony procent lokalnego woluminu, na którym znajduje się punkt końcowy serwera, jest zawsze wolny. 

Liczba ostatnio używanych plików z warstwami **zasad** — x dni temu lub później. Zasady wolnego miejsca na woluminie zawsze mają pierwszeństwo; w przypadku braku wystarczającej ilości wolnego miejsca na woluminie do przechowywania tylu plików, jak opisano w zasadach dotyczących dat, Azure File Sync zastąpi zasady dotyczące dat i kontynuuje warstwowe pliki Coldest do momentu spełnienia wartości procentowej ilości wolnego miejsca na woluminie.

## <a name="how-both-policies-work-together"></a>Współdziałanie obu zasad

Będziemy używać przykładu do zilustrowania sposobu działania tych zasad: Załóżmy, że Azure File Sync skonfigurowany na woluminie lokalnym 500 GB, a obsługa warstw w chmurze nigdy nie została włączona. Są to pliki w udziale plików:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Serwer i udział plików platformy Azure
|4. plik    | 1 rok, 2 dni temu | 130 GB | Serwer i udział plików platformy Azure
|Plik 5    | 2 lata, 1 dzień temu | 140 GB | Serwer i udział plików platformy Azure

**Zmień 1:** Włączono obsługę warstw w chmurze, ustaw zasady wolnego miejsca na woluminie o wartości 20% i zachowane zasady daty. W przypadku tej konfiguracji Obsługa warstw w chmurze zapewnia 20% (w tym przypadku 100 GB) wolnego miejsca i dostępne na komputerze lokalnym. W związku z tym całkowita pojemność lokalnej pamięci podręcznej wynosi 400 GB. Do 400 GB będą przechowywane ostatnio i często używane pliki na woluminie lokalnym.

W przypadku tej konfiguracji tylko pliki od 1 do 4 byłyby przechowywane w lokalnej pamięci podręcznej, a plik 5 będzie warstwowy. Jest to tylko 370 GB z 400 GB, które mogą być używane. Plik 5 to 140 GB i spowodowałoby przekroczenie limitu 400 GB, jeśli został on zapisany lokalnie. 

**Zmień 2:** Załóżmy, że użytkownik uzyskuje dostęp do pliku 5. Powoduje to, że plik 5 ostatnio używanego pliku w udziale. W związku z tym plik 5 zostałby zapisany w lokalnej pamięci podręcznej i będzie pasować do limitu 400 GB, a plik 4 zostanie warstwowy. W poniższej tabeli przedstawiono, gdzie są przechowywane pliki z następującymi aktualizacjami:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 5    | 2 godz. temu | 140 GB | Serwer i udział plików platformy Azure
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Serwer i udział plików platformy Azure
|4. plik    | 1 rok, 2 dni temu | 130 GB | Udział plików platformy Azure, warstwowy lokalnie

**Zmień 3:** Załóżmy, że Zaktualizowano zasady w taki sposób, aby zasady dotyczące warstw oparte na dacie były 60 dni, a zasady wolnego miejsca na woluminie to 70%. Teraz można przechowywać tylko do 150 GB w lokalnej pamięci podręcznej. Chociaż do pliku 2 uzyskano dostęp mniej niż 60 dni temu, zasady wolnego miejsca na woluminie zastąpią zasady dotyczące dat, a plik 2 zostanie warstwowy, aby zachować wartość 70% lokalnego wolnego miejsca.

**Zmień 4:** Jeśli zasady wolnego miejsca na woluminie zostały zmienione na 20%, a następnie użyte `Invoke-StorageSyncFileRecall` do odwoływania wszystkich plików pasujących do dysku lokalnego przy zachowaniu zasad obsługi warstw w chmurze, tabela będzie wyglądać następująco:

|Nazwa pliku |Czas ostatniego dostępu  |Rozmiar pliku  |Przechowywane w |
|----------|------------------|-----------|----------|
|Plik 5    | 1 godzinę temu  | 140 GB | Serwer i udział plików platformy Azure
|Plik 1    | 2 dni temu  | 10 GB | Serwer i udział plików platformy Azure
|Plik 2    | 10 dni temu | 30 GB | Serwer i udział plików platformy Azure
|Plik 3    | 1 rok temu | 200 GB | Udział plików platformy Azure, warstwowy lokalnie
|4. plik    | 1 rok, 2 dni temu | 130 GB | Udział plików platformy Azure, warstwowy lokalnie

W takim przypadku pliki 1, 2 i 5 byłyby przechowywane lokalnie w pamięci podręcznej, a pliki 3 i 4 byłyby warstwowe. Ponieważ zasady daty są 60 dni, pliki 3 i 4 są warstwami, nawet jeśli zasady wolnego miejsca na woluminie umożliwiają lokalne 400 GB.

> [!NOTE] 
> Pliki nie są automatycznie wywoływane, gdy klienci zmieniają zasadę wolnego miejsca na woluminie na mniejszą wartość (na przykład z 20% do 10%) lub Zmień zasady daty na większą wartość (na przykład od 20 dni do 50 dni).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Wiele punktów końcowych serwera na woluminie lokalnym

Obsługę warstw w chmurze można włączyć dla wielu punktów końcowych serwera na pojedynczym woluminie lokalnym. W przypadku tej konfiguracji należy ustawić wolne miejsce na woluminie na tę samą wartość dla wszystkich punktów końcowych serwera na tym samym woluminie. Jeśli ustawisz różne zasady wolnego miejsca na woluminie dla kilku punktów końcowych serwera na tym samym woluminie, pierwszeństwo ma największy procent wolnego miejsca na woluminie. Jest to nazywane **zasadami wolnego miejsca na woluminie**. Na przykład jeśli masz trzy punkty końcowe serwera na tym samym woluminie lokalnym, jeden z nich ma wartość 15%, drugi ma wartość 20%, a trzeci ustawiony na 30%, wszystkie te pliki będą znajdować się w warstwach Coldest, gdy będzie dostępna mniej niż 30% wolnego miejsca.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie warstw chmury](storage-sync-monitor-cloud-tiering.md)
