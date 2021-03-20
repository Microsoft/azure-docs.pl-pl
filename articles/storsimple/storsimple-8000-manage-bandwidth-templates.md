---
title: Zarządzanie szablonami przepustowości dla serii StorSimple 8000 | Microsoft Docs
description: Opisuje sposób zarządzania szablonami przepustowości StorSimple, które umożliwiają kontrolę wykorzystania przepustowości.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005898"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Zarządzanie szablonami przepustowości StorSimple przy użyciu usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie

Szablony przepustowości umożliwiają skonfigurowanie użycia przepustowości sieci w wielu codziennych harmonogramach w celu warstwy danych z urządzenia StorSimple do chmury.

Harmonogramy ograniczania przepustowości umożliwiają wykonywanie:

* Określ dostosowane harmonogramy przepustowości w zależności od użycia sieci obciążenia.
* Scentralizowane i bezproblemowe zarządzanie harmonogramami na wielu urządzeniach.

> [!NOTE]
> Ta funkcja jest dostępna tylko dla urządzeń fizycznych StorSimple (modele 8100 i 8600), a nie dla urządzeń w chmurze StorSimple (modele 8010 i 8020).


## <a name="the-bandwidth-templates-blade"></a>Blok szablonów przepustowości

Blok **Szablony przepustowości** ma wszystkie szablony przepustowości dla usługi w formacie tabelarycznym i zawiera następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do szablonu przepustowości podczas jego tworzenia.
* **Schedule** — liczba harmonogramów zawartych w danym szablonie przepustowości.
* **Używane przez** — liczba woluminów korzystających z szablonów przepustowości.

Możesz również znaleźć dodatkowe informacje pomocne w konfigurowaniu szablonów przepustowości w programie:

* [Pytania i odpowiedzi dotyczące szablonów przepustowości](#questions-and-answers-about-bandwidth-templates)
* [Najlepsze rozwiązania dotyczące szablonów przepustowości](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Dodawanie szablonu przepustowości

Wykonaj następujące kroki, aby utworzyć nowy szablon przepustowości.

#### <a name="to-add-a-bandwidth-template"></a>Aby dodać szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager, kliknij pozycję **Szablony przepustowości** , a następnie kliknij pozycję **+ Dodaj szablon przepustowości**.

    ![Kliknij pozycję + Dodaj szablon przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. W bloku **Dodaj szablon przepustowości** wykonaj następujące czynności:
   
    1. Określ unikatową nazwę szablonu przepustowości.
    2. Zdefiniuj harmonogram przepustowości. Aby utworzyć harmonogram:
   
        1. Z listy rozwijanej wybierz **dni** tygodnia, w których harmonogram jest skonfigurowany. Można wybrać wiele dni.        
        
        2. Wprowadź **godzinę rozpoczęcia** w formacie _gg: mm_ . Dzieje się tak po rozpoczęciu harmonogramu.

        3. Wprowadź **godzinę zakończenia** w formacie _gg: mm_ . Jest to planowane, gdy harmonogram zostanie zatrzymany.
      
           > [!NOTE]
           > Nakładające się harmonogramy są niedozwolone. Jeśli godziny rozpoczęcia i zakończenia spowodują nakładanie się harmonogramu, zobaczysz komunikat o błędzie.

        4. Określ **stawkę przepustowości**. Jest to przepustowość w megabitach na sekundę (MB/s) używana przez urządzenie StorSimple w operacjach obejmujących chmurę (operacje przekazywania i pobierania). W tym polu podaj liczbę z zakresu od 1 do 1000.

            ![Definiowanie harmonogramu przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Powtórz powyższe kroki, aby zdefiniować wiele harmonogramów dla szablonu do momentu ukończenia.

        5. Kliknij przycisk **Dodaj** , aby rozpocząć tworzenie szablonu przepustowości. Utworzony szablon zostanie dodany do listy szablonów przepustowości.
      

## <a name="edit-a-bandwidth-template"></a>Edytowanie szablonu przepustowości

Aby edytować szablon przepustowości, wykonaj następujące czynności.

### <a name="to-edit-a-bandwidth-template"></a>Aby edytować szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i z menu kontekstowego wybierz polecenie **Usuń**.
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**. Należy usunąć szablon przepustowości. 
4. Lista szablonów przepustowości, które są aktualizowane w celu odzwierciedlenia usunięcia.

> [!NOTE]
> Nie można zapisać zmian, jeśli zmodyfikowany harmonogram pokrywa się z istniejącym harmonogramem w modyfikowanym szablonie przepustowości.

## <a name="delete-a-bandwidth-template"></a>Usuwanie szablonu przepustowości

Wykonaj następujące kroki, aby usunąć szablon przepustowości.

#### <a name="to-delete-a-bandwidth-template"></a>Aby usunąć szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i z menu kontekstowego wybierz polecenie Usuń.
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**. Należy usunąć szablon przepustowości.
4. Lista szablonów przepustowości, które są aktualizowane w celu odzwierciedlenia usunięcia.

Jeśli szablon jest używany przez jakiekolwiek woluminy, nie będzie można go usunąć. Zostanie wyświetlony komunikat o błędzie informujący o tym, że szablon jest w użyciu. Zostanie wyświetlone okno dialogowe komunikat o błędzie z informacją o tym, że wszystkie odwołania do szablonu powinny zostać usunięte.

Wszystkie odwołania do szablonu można usunąć, uzyskując dostęp do strony **kontenery woluminów** i modyfikując kontenery woluminów używające tego szablonu, aby używały innego szablonu lub użyć ustawienia przepustowości niestandardowej lub nieograniczonej. Po usunięciu wszystkich odwołań można usunąć szablon.

## <a name="use-a-default-bandwidth-template"></a>Użyj domyślnego szablonu przepustowości

Domyślny szablon przepustowości jest dostarczany i domyślnie używany przez kontenery woluminów w celu wymuszenia kontroli przepustowości podczas uzyskiwania dostępu do chmury. Szablon domyślny służy również jako przygotowana Dokumentacja dla użytkowników, którzy tworzą własne szablony. Szczegóły tego szablonu domyślnego:

* **Nazwa** — nieograniczone noclegiy i weekendy
* **Schedule** — pojedynczy harmonogram od poniedziałku do piątku, który stosuje stawkę przepustowości wynoszącą 1 MB/s między 8 am i 5-godzinnym urządzeniem. Przepustowość jest ustawiona na nieograniczone dla pozostałej części tygodnia.

Szablon domyślny można edytować. Śledzenie użycia tego szablonu (w tym edytowane wersje) jest śledzone.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Utwórz szablon przepustowości całodziennej, który jest uruchamiany w określonym czasie

Postępuj zgodnie z tą procedurą, aby utworzyć harmonogram, który jest uruchamiany w określonym czasie i uruchomiony cały dzień. W tym przykładzie harmonogram zaczyna się o godzinie 9 rano i działa do 9 godzin następnego rano. Należy pamiętać, że czasy rozpoczęcia i zakończenia danego harmonogramu muszą być zawarte w tym samym harmonogramie 24-godzinnym i nie mogą obejmować wielu dni. Jeśli konieczne jest skonfigurowanie szablonów przepustowości obejmujących wiele dni, należy użyć wielu harmonogramów (jak pokazano w przykładzie).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Aby utworzyć szablon przepustowości całodziennej

1. Utwórz harmonogram, który zaczyna się od 9 rano i działa do północy.
2. Dodaj kolejny harmonogram. Skonfiguruj drugi harmonogram do uruchamiania od północy do 9 rano.
3. Zapisz szablon przepustowości.

Harmonogram złożony rozpocznie się po wybraniu i uruchomieniu cały dzień.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Pytania i odpowiedzi dotyczące szablonów przepustowości

**P**. Co się dzieje z kontrolami przepustowości w przypadku zaplanowania? (Harmonogram został zakończony i jeszcze nie uruchomiono innego.)

**A**. W takich przypadkach nie będą stosowane żadne kontrolki przepustowości. Oznacza to, że urządzenie może korzystać z nieograniczonej przepustowości podczas warstwowych danych w chmurze.

**P**. Czy można modyfikować szablony przepustowości na urządzeniu w trybie offline?

**A**. Jeśli odpowiednie urządzenie jest w trybie offline, nie będzie można modyfikować szablonów przepustowości w kontenerach woluminów.

**P**. Czy można edytować szablon przepustowości skojarzony z kontenerem woluminów, gdy skojarzone woluminy są w trybie offline?

**A**. Istnieje możliwość zmodyfikowania szablonu przepustowości skojarzonego z kontenerem woluminów, którego woluminy są w trybie offline. Należy pamiętać, że gdy woluminy są w trybie offline, żadne dane nie zostaną warstwą z urządzenia do chmury.

**P**. Czy można usunąć szablon domyślny?

**A**. Mimo że można usunąć szablon domyślny, nie jest to dobrym pomysłem. Śledzenie użycia szablonu domyślnego, w tym edytowane wersje, jest śledzone. Dane śledzenia są analizowane i w miarę upływu czasu są używane do ulepszania szablonu domyślnego.

**P**. Jak ustalić, czy należy zmodyfikować szablony przepustowości?

**A**. Jednym ze znaków koniecznych do zmodyfikowania szablonów przepustowości jest to, że po rozpoczęciu pracy z siecią jest wolniejsze lub wielokrotne podlewka w ciągu dnia. W takim przypadku należy monitorować magazyn i sieć użycia, przeglądając wykresy wydajności operacji we/wy i przepływności sieci.

W obszarze dane dotyczące przepływności sieci określ godzinę i kontenery woluminów, w których występuje wąskie gardło sieci. Jeśli tak się stanie, gdy dane są warstwowe w chmurze (Pobierz te informacje z wydajności we/wy dla wszystkich kontenerów dla urządzenia do chmury), należy zmodyfikować szablony przepustowości skojarzone z kontenerami woluminów.

Po użyciu zmodyfikowanych szablonów należy ponownie monitorować sieć w celu uzyskania znaczących opóźnień. Jeśli nadal istnieją, konieczne będzie ponowne odwiedzenie szablonów przepustowości.

**P**. Co się stanie, jeśli wiele kontenerów woluminów na moim urządzeniu ma harmonogramy, które nakładają się na siebie, ale różne limity mają zastosowanie do każdego

**A**. Załóżmy, że masz urządzenie z 3 kontenerami woluminów. Harmonogramy skojarzone z tymi kontenerami są całkowicie nakładane. W przypadku każdego z tych kontenerów używane są limity przepustowości odpowiednio 5, 10 i 15 MB/s. Gdy wszystkie te kontenery są wykonywane jednocześnie we/wy, można zastosować co najmniej 3 limity przepustowości: w tym przypadku 5 MB/s jako te wychodzące żądania we/wy współdzielą tę samą kolejkę.

## <a name="best-practices-for-bandwidth-templates"></a>Najlepsze rozwiązania dotyczące szablonów przepustowości

Postępuj zgodnie z następującymi najlepszymi rozwiązaniami dotyczącymi urządzenia StorSimple:

* Skonfiguruj na urządzeniu szablony przepustowości, aby umożliwić ograniczanie przepustowości sieci przez urządzenie w różnych porach dnia. Te szablony przepustowości używane z harmonogramami kopii zapasowych mogą efektywnie wykorzystać dodatkową przepustowość sieci dla operacji w chmurze w godzinach poza godzinami szczytu.
* Oblicz rzeczywistą przepustowość wymaganą dla określonego wdrożenia na podstawie rozmiaru wdrożenia i wymaganego celu czasu odzyskiwania (RTO).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

