---
title: Zarządzanie udziałami w usłudze Azure Data Box Gateway | Microsoft Docs
description: W tym artykule opisano sposób zarządzania udziałami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 847c8a07bfa245d0c9859bb3cf6f6ed233934250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743883"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-gateway"></a>Zarządzanie udziałami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal 

W tym artykule opisano sposób zarządzania udziałami w usłudze Azure Data Box Gateway. Usługą Azure Data Box Gateway można zarządzać w witrynie Azure Portal lub za pomocą lokalnego internetowego interfejsu użytkownika. Witryna Azure Portal umożliwia dodawanie, usuwanie i odświeżanie udziałów oraz synchronizowanie klucza magazynu dla konta magazynu skojarzonego z udziałami.

## <a name="about-shares"></a>Udziały — informacje

Aby przesłać dane na platformę Azure, należy utworzyć udziały w usłudze Azure Data Box Gateway. Udziały dodawane na urządzeniu Data Box Gateway są udziałami chmurowymi. Dane pochodzące z tych udziałów są automatycznie przekazywane do chmury. Wszystkie funkcje w chmurze, takie jak odświeżanie i synchronizowanie kluczy magazynu, mogą mieć zastosowanie do tych udziałów. Z udziałów chmurowych można korzystać, gdy dane urządzenia mają zostać automatycznie przesłane na konto magazynu w chmurze.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie udziału
> * Usuwanie udziału
> * Odświeżanie udziałów
> * Synchronizowanie klucza magazynu


## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby utworzyć udział.

1. W witrynie Azure Portal przejdź do zasobu usługi Data Box Gateway, a następnie do sekcji **Przegląd**. Na pasku poleceń kliknij przycisk **+ Dodaj udział**.
2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału. 

    ![Klikanie przycisku Dodaj udział](media/data-box-gateway-manage-shares/add-share-1.png)

    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** dla udziału. Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Podaj **konto magazynu**, w którym znajduje się udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener.  

5. W obszarze **Usługa magazynu** wybierz blokowy obiekt blob, stronicowy obiekt blob lub pliki. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym wypadku chcemy, aby dane zostały umieszczone na platformie Azure jako blokowe obiekty blob, dlatego wybierzemy opcję **Blokowy obiekt blob**. Jeśli wybierzesz opcję **Stronicowy obiekt blob**, upewnij się, że dane są wyrównane o 512 bajtów. Na przykład dysk VHDX jest zawsze wyrównany o 512 bajtów.

   > [!IMPORTANT]
   > Upewnij się, że konto usługi Azure Storage, którego używasz, nie ma ustawionych zasad niezmiennościymi, jeśli są używane z urządzeniem Azure Stack EDGE Pro lub Data Box Gateway. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS.
    - **W przypadku tworzenia udziału SMB** — w polu **Użytkownik lokalny ze wszystkimi uprawnieniami** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego podaj **nazwę użytkownika**, **hasło**, a następnie potwierdź hasło. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.

        ![Dodawanie udziału SMB](media/data-box-gateway-manage-shares/add-share-2.png)

        Jeśli zaznaczysz opcję Zezwalaj tylko na operacje odczytu dla tego udziału danych, możesz określić użytkowników z uprawnieniami tylko do odczytu.
    - **W przypadku tworzenia udziału NFS** — należy podać **adresy IP dozwolonych klientów**, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](media/data-box-gateway-manage-shares/add-share-3.png)

7. Kliknij pozycję **Utwórz**, aby utworzyć udział. Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział.
 
## <a name="delete-a-share"></a>Usuwanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć udział.

1. Na liście udziałów kliknij udział, który chcesz usunąć.

    ![Wybieranie udziału](media/data-box-gateway-manage-shares/delete-1.png)

2. Kliknij polecenie **Usuń**. 

    ![Klikanie polecenia Usuń](media/data-box-gateway-manage-shares/delete-2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/data-box-gateway-manage-shares/delete-3.png)

Lista udziałów zostanie zaktualizowana w celu uwzględnienia usuniętego udziału.


## <a name="refresh-shares"></a>Odświeżanie udziałów

Funkcja odświeżania umożliwia odświeżenie zawartości udziału lokalnego. Podczas odświeżania udziału inicjowane jest wyszukiwanie w celu odnalezienia wszystkich obiektów platformy Azure, w tym obiektów blob i plików, które zostały dodane do chmury po ostatnio przeprowadzonym odświeżaniu. Te dodatkowe pliki są następnie używane do odświeżenia zawartości udziału lokalnego na urządzeniu. 

> [!NOTE]
> Uprawnienia i listy kontroli dostępu (ACL) nie są zachowywane w trakcie operacji odświeżania. 

Wykonaj następujące czynności w witrynie Azure Portal, aby odświeżyć udział.

1. W witrynie Azure Portal przejdź do sekcji **Udziały**. Kliknij udział, który chcesz odświeżyć.

   ![Wybierz udział 2](media/data-box-gateway-manage-shares/refresh-1.png)

2. Kliknij przycisk **Odśwież**. 

   ![Klikanie przycisku Odśwież](media/data-box-gateway-manage-shares/refresh-2.png)
 
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Zadanie rozpoczyna odświeżanie zawartości udziału lokalnego. 

   ![Potwierdzanie odświeżania](media/data-box-gateway-manage-shares/refresh-3.png)
 
4.   W trakcie odświeżania opcja odświeżania jest wyszarzona w menu kontekstowym. Kliknij powiadomienie o zadaniu, aby wyświetlić stan zadania odświeżania.

5. Czas odświeżania zależy od liczby plików w kontenerze platformy Azure, a także od liczby plików znajdujących się na urządzeniu. Po pomyślnym zakończeniu odświeżania znacznik czasu udziału zostanie zaktualizowany. Nawet w przypadku częściowego niepowodzenia odświeżania operacja zostanie uznana za zakończoną, a znacznik czasu zostanie zaktualizowany. 

   ![Zaktualizowany znacznik czasu](media/data-box-gateway-manage-shares/refresh-4.png)
 
W przypadku awarii zostanie zgłoszony alert. Alert zawiera szczegółowe informacje o przyczynie awarii oraz zalecenia dotyczące rozwiązania problemu. Alert wskazuje również plik, który zawiera pełne podsumowanie awarii — w tym pliki, których nie udało się zaktualizować lub usunąć.

>[!IMPORTANT]
> W tej wersji nie odświeżaj więcej niż jednego udziału jednocześnie.

## <a name="sync-storage-keys"></a>Synchronizowanie kluczy magazynu

Jeśli klucze konta magazynu zostały wymienione, należy zsynchronizować klucze dostępu do magazynu. Przeprowadzenie synchronizacji pozwala urządzeniu uzyskać najnowsze klucze do konta magazynu.

Wykonaj następujące czynności w witrynie Azure Portal, aby zsynchronizować klucz dostępu do magazynu.

1. W zasobie przejdź do sekcji **Przegląd**. 
2. Z listy udziałów wybierz i kliknij udział skojarzony z kontem magazynu, które ma być synchronizowane. Kliknij pozycję **Synchronizuj klucz magazynu**. 

     ![Synchronizowanie klucza magazynu](media/data-box-gateway-manage-shares/sync-storage-key-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Po zakończeniu synchronizacji zamknij okno dialogowe.

     ![Synchronizuj klucz magazynu 2](media/data-box-gateway-manage-shares/sync-storage-key-2.png)

>[!NOTE]
> Wystarczy zrobić to raz dla danego konta magazynu. Nie trzeba powtarzać tej czynności dla wszystkich udziałów powiązanych z tym samym kontem magazynu.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-gateway-manage-users.md).
