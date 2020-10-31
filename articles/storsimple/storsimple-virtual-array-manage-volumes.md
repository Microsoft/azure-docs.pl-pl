---
title: Zarządzanie woluminami w macierzy wirtualnej StorSimple | Microsoft Docs
description: W tym artykule opisano Menedżer urządzeń StorSimple i wyjaśniono, jak używać go do zarządzania woluminami w macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129953"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Use StorSimple Device Manager service to manage volumes on the StorSimple Virtual Array (Zarządzanie wirtualną tablicą woluminów za pomocą usługi StorSimple Device Manager)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak za pomocą usługi StorSimple Menedżer urządzeń tworzyć woluminy i zarządzać nimi w macierzy wirtualnej StorSimple.

Usługa StorSimple Menedżer urządzeń jest rozszerzeniem Azure Portal, które umożliwia zarządzanie rozwiązaniem StorSimple z poziomu jednego interfejsu internetowego. Oprócz zarządzania udziałami i woluminami, można użyć usługi StorSimple Menedżer urządzeń do wyświetlania i zarządzania urządzeniami, wyświetlania alertów oraz wyświetlania zasad tworzenia kopii zapasowych i wykazów kopii zapasowych oraz zarządzania nimi.

## <a name="volume-types"></a>Typy woluminów

StorSimple woluminy mogą być następujące:

* **Przypięty lokalnie** : dane w tych woluminach pozostają w całej macierzy i nie zostaną rozlane do chmury.
* **Warstwowe** : dane w tych woluminach mogą zostać rozlane do chmury. Podczas tworzenia woluminu warstwowego zostanie zainicjowana około 10% miejsca w warstwie lokalnej i zostanie zainicjowana 90% miejsca w chmurze. Na przykład jeśli Zainicjowano obsługę woluminu o pojemności 1 TB, 100 GB będzie znajdować się w miejscu lokalnym, a w chmurze zostanie użyta 900 GB. To z kolei oznacza, że w przypadku braku całego lokalnego miejsca na urządzeniu nie można zainicjować obsługi administracyjnej woluminu warstwowego (ponieważ 10% wymagane w warstwie lokalnej nie będzie dostępne).

### <a name="provisioned-capacity"></a>Przyobsługiwana pojemność
Zapoznaj się z poniższą tabelą dotyczącą maksymalnej alokowanej pojemności dla każdego typu woluminu.

| **Identyfikator limitu**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimalny rozmiar woluminu warstwowego                            | 500 GB        |
| Maksymalny rozmiar woluminu warstwowego                            | 5 TB          |
| Minimalny rozmiar woluminu przypiętego lokalnie                    | 50 GB         |
| Maksymalny rozmiar woluminu przypiętego lokalnie                    | 200 GB        |

## <a name="the-volumes-blade"></a>Blok woluminy
Menu **woluminy** w bloku podsumowania usługi StorSimple wyświetla listę woluminów magazynu w danej macierzy StorSimple i umożliwia zarządzanie nimi.

![Blok woluminów](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Wolumin składa się z serii atrybutów:

* **Nazwa woluminu** — nazwa opisowa, która musi być unikatowa i pomaga identyfikować wolumin.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest on widoczny dla inicjatorów (serwerów), które mają dostęp do korzystania z woluminu.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (domyślnie) czy **przypięty lokalnie** .
* **Pojemność** — określa ilość danych używanych w porównaniu do całkowitej ilości danych, które mogą być przechowywane przez inicjatora (serwer).
* **Kopia zapasowa** — w przypadku macierzy wirtualnej StorSimple wszystkie woluminy są automatycznie włączane na potrzeby tworzenia kopii zapasowych.
* **Połączone hosty** — określa inicjatory (serwery), które mają dostęp do tego woluminu.

![Szczegóły woluminów](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Skorzystaj z instrukcji przedstawionych w tym samouczku, aby wykonać następujące zadania:

* Dodaj wolumin
* Modyfikowanie woluminu
* Przełącz wolumin w tryb offline
* Usuwanie woluminu

## <a name="add-a-volume"></a>Dodaj wolumin

1. W bloku podsumowania usługi StorSimple kliknij pozycję **+ Dodaj wolumin** na pasku poleceń. Spowoduje to otwarcie bloku **Dodawanie woluminu** .
   
    ![Zrzut ekranu przedstawia przycisk Dodaj wolumin i okienko Dodaj wolumin.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. W bloku **Dodaj wolumin** wykonaj następujące czynności:
   
   * W polu **Nazwa woluminu** wprowadź unikatową nazwę woluminu. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
   * Z listy rozwijanej **Typ** wybierz, czy chcesz utworzyć wolumin **przypięty** **warstwowy** lub lokalnie. W przypadku obciążeń wymagających lokalnych gwarancji, małych opóźnień i większej wydajności Wybierz **wolumin przypięty lokalnie** . Dla wszystkich innych danych wybierz opcję wolumin **warstwowy** .
   * W polu **pojemność** Określ rozmiar woluminu. Wolumin warstwowy musi zawierać się w przedziale od 500 GB do 5 TB, a wolumin przypięty lokalnie musi mieć wartość z zakresu od 50 GB do 500 GB.
   * * Kliknij pozycję **połączone hosty** , wybierz rekord kontroli dostępu (ACR) odpowiadający inicjatorowi iSCSI, który ma zostać połączony z tym woluminem, a następnie kliknij pozycję **Wybierz** .
3. Aby dodać nowy połączony host, kliknij przycisk **Dodaj nowy** , wprowadź nazwę hosta i jego kwalifikowaną nazwę iSCSI (IQN), a następnie kliknij przycisk **Dodaj** .
   
    ![Zrzut ekranu przedstawia okienko połączone hosty, w którym można dodać nowy.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po zakończeniu konfigurowania woluminu kliknij przycisk **Utwórz** . Wolumin zostanie utworzony przy użyciu określonych ustawień i zostanie wyświetlone powiadomienie dotyczące pomyślnego utworzenia tego samego. Domyślnie kopia zapasowa zostanie włączona dla woluminu.
5. Aby upewnić się, że wolumin został pomyślnie utworzony, przejdź do bloku **woluminy** . Powinien zostać wyświetlony wymieniony wolumin.
   
    ![Powodzenie tworzenia woluminu](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Należy zmodyfikować wolumin, gdy trzeba zmienić hosty, które uzyskują dostęp do woluminu. Nie można zmodyfikować innych atrybutów woluminu po utworzeniu woluminu.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować wolumin

1. **W bloku** podsumowania usługi StorSimple wybierz macierz wirtualną, w której znajduje się wolumin, który chcesz zmodyfikować.
2. **Wybierz** wolumin, a następnie kliknij pozycję **połączone hosty** , aby wyświetlić aktualnie podłączony Host i zmodyfikować go na inny serwer.
   
    ![Edytuj wolumin](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Zapisz zmiany, klikając pasek poleceń **Zapisz** . Określone ustawienia zostaną zastosowane i zostanie wyświetlone powiadomienie.

## <a name="take-a-volume-offline"></a>Przełącz wolumin w tryb offline

Może zajść potrzeba przełączenia woluminu w tryb offline, gdy planujesz go zmodyfikować lub usunąć. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy przełączyć wolumin w tryb offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przełączyć wolumin w tryb offline

1. Upewnij się, że dany wolumin nie jest używany przed przełączeniem go w tryb offline.
2. Najpierw Przełącz wolumin do trybu offline na hoście. Eliminuje to potencjalne ryzyko uszkodzenia danych na woluminie. Aby zapoznać się z określonymi krokami, zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego hosta.
3. Gdy wolumin na hoście jest w trybie offline, zrób wolumin w macierzy w trybie offline, wykonując następujące czynności:
   
   * **W bloku** podsumowania usługi StorSimple wybierz macierz wirtualną, w której znajduje się wolumin, który ma zostać przełączony w tryb offline.
   * **Wybierz** wolumin, a następnie kliknij przycisk **...** (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Przełącz do trybu offline** .
     
        ![Wolumin w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Przejrzyj informacje w bloku **Przełącz do trybu offline** i Potwierdź akceptację operacji. Kliknij polecenie **Przełącz do trybu offline** , aby przełączyć wolumin w tryb offline. Zobaczysz powiadomienie o trwającej operacji.
   * Aby upewnić się, że wolumin został pomyślnie przełączony w tryb offline, przejdź do bloku **woluminy** . Stan woluminu powinien być widoczny jako offline.
     
       ![Potwierdzenie woluminu w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.
> 
> 

Wykonaj następujące kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. **W bloku** podsumowania usługi StorSimple wybierz macierz wirtualną, w której znajduje się wolumin, który chcesz usunąć.
2. **Wybierz** wolumin, a następnie kliknij przycisk **...** (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz pozycję **Usuń** .
   
    ![Usuwanie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, najpierw Przełącz go do trybu offline, wykonując czynności opisane w sekcji [Zrób wolumin w trybie offline](#take-a-volume-offline).
4. Po wyświetleniu monitu o potwierdzenie w bloku **Usuń** Zaakceptuj potwierdzenie i kliknij przycisk **Usuń** . Wolumin zostanie usunięty, a blok **woluminy** wyświetli zaktualizowaną listę woluminów w ramach macierzy wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [sklonować wolumin StorSimple](storsimple-virtual-array-clone.md).

