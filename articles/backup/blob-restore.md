---
title: Przywracanie obiektów blob platformy Azure
description: Dowiedz się, jak przywrócić obiekty blob platformy Azure (w wersji zapoznawczej).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746497"
---
# <a name="restore-azure-blobs-in-preview"></a>Przywracanie obiektów blob platformy Azure (w wersji zapoznawczej)

Blokowe obiekty blob na kontach magazynu z skonfigurowaną operacyjną kopią zapasową można przywrócić do dowolnego punktu w czasie w ramach zakresu przechowywania. Ponadto można ograniczyć zakres przywracania do wszystkich blokowych obiektów BLOB na koncie magazynu lub do podzestawu obiektów BLOB.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Obiekty blob zostaną przywrócone do tego samego konta magazynu. Dlatego obiekty blob, które przeszły zmiany od czasu przywrócenia, zostaną nadpisywane.
- Tylko blokowe obiekty blob w standardowym koncie magazynu ogólnego przeznaczenia w wersji 2 można przywrócić w ramach operacji przywracania. Nie są przywracane obiekty blob, stronicowe obiekty blob i blokowe obiekty blob w warstwie Premium.
- Gdy zadanie przywracania jest w toku, nie można odczytywać ani zapisywać obiektów BLOB w magazynie.
- Nie można przywrócić obiektu BLOB z aktywną dzierżawą. Jeśli obiekt BLOB z aktywną dzierżawą zostanie uwzględniony w zakresie obiektów BLOB do przywrócenia, operacja przywracania zakończy się niepowodzeniem automatycznie. Przerwij wszystkie aktywne dzierżawy przed rozpoczęciem operacji przywracania.
- Migawki nie są tworzone ani usuwane w ramach operacji przywracania. Tylko podstawowy obiekt BLOB zostanie przywrócony do poprzedniego stanu.
- W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji **usuwania kontenera** nie można przywrócić tego kontenera przy użyciu operacji przywracania. Zamiast usuwać cały kontener, Usuń pojedyncze obiekty blob, jeśli chcesz je później przywrócić. Ponadto firma Microsoft zaleca włączenie usuwania nietrwałego dla kontenerów oprócz operacyjnych kopii zapasowej w celu ochrony przed przypadkowym usunięciem kontenerów.
- Zapoznaj się z [matrycą pomocy technicznej](blob-backup-support-matrix.md) , aby uzyskać wszystkie ograniczenia i obsługiwane scenariusze.

## <a name="restore-blobs"></a>Przywracanie obiektów BLOB

Przywracanie można zainicjować za pomocą centrum kopii zapasowych.

1. W centrum kopii zapasowych przejdź do pozycji **Przywróć** na górnym pasku.

    ![Przywracanie w centrum kopii zapasowych](./media/blob-restore/backup-center-restore.png)

1. Na karcie **Inicjowanie przywracania** wybierz pozycję **obiekty blob platformy Azure (Azure Storage)** jako typ źródła danych i wybierz **wystąpienie kopii zapasowej** , które chcesz przywrócić. Wystąpienie kopii zapasowej to konto magazynu zawierające obiekty blob, które mają zostać przywrócone.

     ![Wybierz wystąpienie kopii zapasowej](./media/blob-restore/select-backup-instance.png)

1. Na karcie **Wybierz punkt odzyskiwania** wybierz datę i godzinę, z których chcesz przywrócić dane. Można również użyć suwaka, aby wybrać punkt w czasie, z którego ma zostać przywrócone. Dymek informacji obok daty pokazuje prawidłowy czas trwania, z którego można przywrócić dane. Operacyjna kopia zapasowa dla obiektów blob, które są ciągłej kopii zapasowej, zapewnia szczegółową kontrolę nad punktami odzyskiwania danych.

    >[!NOTE]
    > Czas przedstawiony tutaj to czas lokalny.

    ![Data i godzina przywracania](./media/blob-restore/date-and-time.png)

1. Na karcie **przywracanie parametrów** wybierz, czy chcesz przywrócić wszystkie obiekty blob na koncie magazynu, określonych kontenerach lub podzbiorze obiektów BLOB przy użyciu dopasowania prefiksu. W przypadku używania dopasowania prefiksu można określić maksymalnie 10 zakresów prefiksów lub ścieżki. Więcej szczegółowych informacji na temat używania dopasowania prefiksu znajduje się [tutaj](#use-prefix-match-for-restoring-blobs).

    ![Parametry przywracania](./media/blob-restore/restore-parameters.png)

    Wybierz jedną z następujących opcji:

    - **Przywróć wszystkie obiekty blob na koncie magazynu**: przy użyciu tej opcji przywraca wszystkie blokowe obiekty blob na koncie magazynu, przetaczając je z powrotem do wybranego punktu w czasie. Przywrócenie kont magazynu zawierających duże ilości danych lub monitor o dużym obciążeniu może trwać dłużej.

    - **Przeglądaj i Przywróć wybrane kontenery**: za pomocą tej opcji można przeglądać i wybierać do 10 kontenerów do przywrócenia. Użytkownik musi mieć wystarczające uprawnienia do wyświetlania kontenerów na koncie magazynu lub może nie być w stanie zobaczyć zawartości konta magazynu.

    - **Wybierz obiekty blob do przywrócenia przy użyciu dopasowania prefiksu**: Ta opcja umożliwia przywrócenie podzestawu obiektów BLOB przy użyciu dopasowania prefiksu. Można określić maksymalnie 10 lexicographical zakresów obiektów BLOB w jednym kontenerze lub w wielu kontenerach, aby zwrócić te obiekty blob do ich poprzedniego stanu w danym momencie. Oto kilka rzeczy, o których należy pamiętać:

        - Możesz użyć ukośnika (/), aby odróżnić nazwę kontenera z prefiksu obiektu BLOB
        - Określony zakres jest włączny włącznie, jednak określony zakres jest na wyłączność.

    Więcej informacji o używaniu prefiksów do przywracania zakresów obiektów BLOB znajduje się w [tej sekcji](#use-prefix-match-for-restoring-blobs).

1. Po zakończeniu określania obiektów BLOB do przywrócenia przejdź do karty **Recenzja + przywracanie** , a następnie wybierz pozycję **Przywróć** , aby zainicjować przywracanie.

1. **Śledzenie przywracania**: Użyj widoku **zadania tworzenia kopii zapasowej** , aby śledzić szczegóły i stan przywracania. W tym celu przejdź do   >  **zadania tworzenia kopii zapasowej** centrum kopii zapasowych. Stan zostanie wyświetlony **w trakcie** wykonywania operacji przywracania.

    ![Karta zadania tworzenia kopii zapasowej](./media/blob-restore/backup-jobs.png)

    Po pomyślnym zakończeniu operacji przywracania stan zmieni się na **ukończono**. Po pomyślnym zakończeniu przywracania będzie można ponownie odczytywać i zapisywać obiekty blob na koncie magazynu.

## <a name="additional-topics"></a>Tematy dodatkowe

### <a name="use-prefix-match-for-restoring-blobs"></a>Użyj dopasowania prefiksu do przywracania obiektów BLOB

Rozpatrzmy następujący przykład:

![Przywróć z dopasowaniem do prefiksu](./media/blob-restore/prefix-match.png)

Operacja przywracania pokazana w obrazie wykonuje następujące czynności:

- Przywraca kompletną zawartość *container1*.
- Przywraca obiekty blob w lexicographical zakresie *blob1* przez *blob5* w *container2*. Ten zakres przywraca obiekty blob z nazwami, takimi jak *blob1*, *blob11*, *blob100*, *blob2* i tak dalej. Ponieważ koniec zakresu jest na wyłączność, przywraca obiekty blob, których nazwy zaczynają się od *blob4*, ale nie przywraca obiektów blob, których nazwy zaczynają się od *blob5*.
- Przywraca wszystkie obiekty blob w *container3* i *container4*. Ponieważ koniec zakresu jest na wyłączność, ten zakres nie powoduje przywrócenia *container5*.

## <a name="next-steps"></a>Następne kroki

- [Przegląd operacyjnej kopii zapasowej dla obiektów blob platformy Azure (w wersji zapoznawczej)](blob-backup-overview.md)
