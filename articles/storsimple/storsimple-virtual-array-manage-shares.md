---
title: Zarządzanie udziałami macierzy wirtualnych StorSimple | Microsoft Docs
description: W tym artykule opisano Menedżer urządzeń StorSimple i wyjaśniono, jak używać go do zarządzania udziałami w macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994948"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Use StorSimple Device Manager service to manage shares on the StorSimple Virtual Array (Zarządzanie wirtualną tablicą udziałów za pomocą usługi StorSimple DeviceManager)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak za pomocą usługi StorSimple Menedżer urządzeń utworzyć udziały w wirtualnej macierzy StorSimple i zarządzać nimi.

Usługa StorSimple Menedżer urządzeń jest rozszerzeniem Azure Portal, które umożliwia zarządzanie rozwiązaniem StorSimple z poziomu jednego interfejsu internetowego. Oprócz zarządzania udziałami i woluminami, można użyć usługi StorSimple Menedżer urządzeń do wyświetlania i zarządzania urządzeniami, wyświetlania alertów, zarządzania zasadami tworzenia kopii zapasowych i zarządzania wykazem kopii zapasowych.

## <a name="share-types"></a>Typy udziałów

Udziały StorSimple:

* **Przypięty lokalnie**: dane w tych udziałach pozostają na tablicy przez cały czas i nie zostaną rozlane do chmury.
* **Warstwowe**: dane w tych udziałach mogą zostać rozlane do chmury. Podczas tworzenia udziału warstwowego zostanie zainicjowana około 10% miejsca w warstwie lokalnej, a 90% miejsca jest udostępniane w chmurze. Na przykład, jeśli udostępnisz udział 1 TB, 100 GB będzie znajdować się w miejscu lokalnym, a w chmurze zostanie użyta 900 GB. To z kolei oznacza, że w przypadku braku całego lokalnego miejsca na urządzeniu nie można zainicjować obsługi administracyjnej udziałów warstwowych (ponieważ 10% wymagane w warstwie lokalnej nie będzie dostępne).

### <a name="provisioned-capacity"></a>Przyobsługiwana pojemność

Zapoznaj się z poniższą tabelą dotyczącą maksymalnej alokowanej pojemności dla każdego typu udziału.

| **Identyfikator limitu** | **Limit** |
| --- | --- |
| Minimalny rozmiar udziału warstwowego |500 GB |
| Maksymalny rozmiar udziału warstwowego |20 TB |
| Minimalny rozmiar udziału przypiętego lokalnie |50 GB |
| Maksymalny rozmiar udziału przypiętego lokalnie |2 TB |

## <a name="the-shares-blade"></a>Blok udziałów

Menu **udziały** w bloku podsumowania usługi StorSimple wyświetla listę udziałów magazynu w danej macierzy StorSimple i umożliwia zarządzanie nimi.

![Blok udziałów](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Udział składa się z serii atrybutów:

* **Nazwa udziału** — nazwa opisowa, która musi być unikatowa i pomaga identyfikować udział.
* **Stan** — może być w trybie online lub offline. Jeśli udział jest w trybie offline, użytkownicy udziału nie będą mogli uzyskać do niego dostępu.
* **Typ** — wskazuje, czy udział jest **warstwowy** (wartość domyślna) czy **przypięty lokalnie**.
* **Pojemność** — określa ilość danych używanych w porównaniu do łącznej ilości danych, które mogą być przechowywane w udziale.
* **Opis** — opcjonalne ustawienie, które ułatwia opisywanie udziału.
* **Uprawnienia** — uprawnienia systemu plików NTFS do udziału, którym można zarządzać za pomocą Eksploratora Windows.
* **Kopia zapasowa** — w przypadku macierzy wirtualnej StorSimple wszystkie udziały są automatycznie włączane dla kopii zapasowej.

![Szczegóły udziałów](./media/storsimple-virtual-array-manage-shares/share-details.png)

Skorzystaj z instrukcji przedstawionych w tym samouczku, aby wykonać następujące zadania:

* Dodawanie udziału
* Modyfikowanie udziału
* Przełącz udział w tryb offline
* Usuwanie udziału

## <a name="add-a-share"></a>Dodawanie udziału

1. W bloku podsumowania usługi StorSimple kliknij pozycję **+ Dodaj udział** na pasku poleceń. Spowoduje to otwarcie bloku **Dodawanie udziału** .

    ![Dodaj udział](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. W bloku **Dodaj udział** wykonaj następujące czynności:
   
   1. W polu **Nazwa udziału** wprowadź unikatową nazwę udziału. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.

   2. Opcjonalny **Opis** udziału. Opis pomoże zidentyfikować właścicieli udziałów.

   3. Z listy rozwijanej **Typ** wybierz, czy chcesz utworzyć udział **przypięty** **warstwowy** lub lokalnie. W przypadku obciążeń wymagających lokalnych gwarancji, małych opóźnień i większej wydajności Wybierz **udział przypięty lokalnie**. Dla wszystkich innych danych wybierz opcję udział **warstwowy** .

   4. W polu **pojemność** Określ rozmiar udziału. Udział warstwowy musi mieć wartość z przedziału od 500 GB do 20 TB, a udział przypięty lokalnie musi mieścić się w przedziale od 50 GB do 2 TB.

   5. W polu **Ustaw domyślne pełne uprawnienia do** , przypisz uprawnienia użytkownikowi lub grupie, która uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w _john@contoso.com_ formacie. Zalecamy użycie grupy użytkowników (zamiast pojedynczego użytkownika) w celu umożliwienia administratorom dostępu do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
3. Po zakończeniu konfigurowania udziału kliknij przycisk **Utwórz**. Zostanie utworzony udział z określonymi ustawieniami i zostanie wyświetlone powiadomienie. Domyślnie kopia zapasowa zostanie włączona dla udziału.
4. Aby upewnić się, że udział został pomyślnie utworzony, przejdź do bloku **udziały** . Powinien zostać wyświetlony wymieniony udział.
   
    ![Powodzenie tworzenia udziału](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modyfikowanie udziału

Zmodyfikuj udział, jeśli chcesz zmienić opis udziału. Po utworzeniu udziału nie można modyfikować innych właściwości udostępniania.

#### <a name="to-modify-a-share"></a>Aby zmodyfikować udział

1. **W bloku** podsumowania usługi StorSimple wybierz wirtualną tablicę, w której znajduje się udział, który chcesz zmodyfikować.
2. **Wybierz** udział, aby wyświetlić bieżący opis i zmodyfikować go.
3. Zapisz zmiany, klikając pasek poleceń **Zapisz** . Określone ustawienia zostaną zastosowane i zostanie wyświetlone powiadomienie.
   
    ![ Edytuj udział](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Przełącz udział w tryb offline

Jeśli planujesz ją zmodyfikować lub usunąć, może być konieczne przełączenie udziału w tryb offline. Gdy udział jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy przełączyć udział w tryb offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-share-offline"></a>Aby przełączyć udział w tryb offline

1. Przed przełączeniem do trybu offline upewnij się, że dany udział nie jest używany.
2. Przełącz udział w macierzy do trybu offline, wykonując następujące czynności:
   
    1. **W bloku** podsumowania usługi StorSimple wybierz wirtualną tablicę, w której znajduje się udział, który chcesz przełączyć do trybu offline.

    2. **Wybierz** udział i kliknij przycisk **...** (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Przełącz do trybu offline**.
     
        ![Udział w trybie offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Przejrzyj informacje w bloku **Przełącz do trybu offline** i Potwierdź akceptację operacji. Kliknij polecenie **Przełącz do trybu offline** , aby przełączyć udział w tryb offline. Zobaczysz powiadomienie o trwającej operacji.

    4. Aby upewnić się, że udział został pomyślnie przełączony w tryb offline, przejdź do bloku **udziały** . Stan udziału powinien zostać wyświetlony jako offline.

## <a name="delete-a-share"></a>Usuwanie udziału

> [!IMPORTANT]
> Udział można usunąć tylko wtedy, gdy jest on w trybie offline.


Aby usunąć udział, wykonaj następujące czynności.

#### <a name="to-delete-a-share"></a>Aby usunąć udział

1. **W bloku** podsumowania usługi StorSimple wybierz macierz wirtualną, w której znajduje się udział, który chcesz usunąć.
2. **Wybierz** udział i kliknij przycisk **...** (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz pozycję **Usuń**.
   
    ![Usuń udział](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Sprawdź stan udziału, który chcesz usunąć. Jeśli udział, który chcesz usunąć, nie jest w trybie offline, najpierw Przełącz go do trybu offline. Postępuj zgodnie z instrukcjami w sekcji [Przełącz udział w tryb offline](#take-a-share-offline).
4. Po wyświetleniu monitu o potwierdzenie w bloku **Usuń** Zaakceptuj potwierdzenie i kliknij przycisk **Usuń**. Udział zostanie usunięty, a blok **udziałs** wyświetli zaktualizowaną listę udziałów w ramach macierzy wirtualnej.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [sklonować udział StorSimple](storsimple-virtual-array-clone.md).

