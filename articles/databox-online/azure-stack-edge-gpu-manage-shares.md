---
title: Azure Stack Edge Zarządzanie udziałami GPU w programie | Microsoft Docs
description: Opisuje, jak używać Azure Portal do zarządzania udziałami na urządzeniach z procesorem GPU Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b79cb1b195d35c1e25dd750476c0dacb296f8010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102638312"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Użyj Azure Portal, aby zarządzać udziałami na Azure Stack brzegowej Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób zarządzania udziałami na Azure Stack Edge. Możesz zarządzać Azure Stack Edge przy użyciu Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby dodać, usunąć, odświeżyć udziały lub zsynchronizować klucz magazynu dla konta magazynu skojarzonego z udziałami. Ten artykuł ma zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.

## <a name="about-shares"></a>Udziały — informacje

Aby przesłać dane na platformę Azure, musisz utworzyć udziały na Azure Stack Edge. Udziały dodawane na urządzeniu Azure Stack EDGE Pro mogą być udziałami lokalnymi lub udziałami, które wypychanie danych do chmury.

 - **Udziały lokalne**: Użyj tych udziałów, jeśli chcesz, aby dane były przetwarzane lokalnie na urządzeniu.
 - **Udziały**: te udziały mają być automatycznie wypychane na konto magazynu w chmurze. Wszystkie funkcje w chmurze, takie jak **odświeżanie** i **Synchronizacja kluczy magazynu** , mają zastosowanie do udziałów.


## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby utworzyć udział.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **> udziały w usłudze Cloud Storage Gateway**. Wybierz pozycję **+ Dodaj udział** na pasku poleceń.

    ![Wybierz pozycję Dodaj udział](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** udziału. Typ może być typu **SMB** lub **NFS** z domyślnym protokołem SMB. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Podaj **konto magazynu**, w którym znajduje się udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener.

5. Z listy rozwijanej wybierz **usługę magazynu** z blokowych obiektów blob, stronicowego obiektu BLOB lub plików. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym wystąpieniu dane mają być przechowywane jako blokowe obiekty blob na platformie Azure, dlatego wybieramy **blokowy obiekt BLOB**. Jeśli wybierzesz **stronicowy obiekt BLOB**, musisz upewnić się, że dane są wyrównane do 512 bajtów. Użyj **stronicowego obiektu BLOB** dla dysków VHD lub VHDX, które są zawsze 512 bajty.

6. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS.
    - **W przypadku tworzenia udziału SMB** — w polu **Użytkownik lokalny ze wszystkimi uprawnieniami** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego podaj **nazwę użytkownika**, **hasło**, a następnie potwierdź hasło. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.

        ![Dodawanie udziału SMB](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Jeśli zaznaczysz opcję Zezwalaj tylko na operacje odczytu dla tego udziału danych, możesz określić użytkowników z uprawnieniami tylko do odczytu.
    - **W przypadku tworzenia udziału NFS** — należy podać **adresy IP dozwolonych klientów**, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Aby łatwo uzyskać dostęp do udziałów z modułów obliczeniowych Edge, Użyj lokalnego punktu instalacji. Wybierz pozycję **Użyj udziału przy użyciu obliczeń brzegowych** , aby udział został automatycznie zainstalowany po utworzeniu. Po wybraniu tej opcji moduł Edge może również używać obliczeń z lokalnym punktem instalacji.

8. Kliknij przycisk **Utwórz** , aby utworzyć udział. Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału przy użyciu określonych ustawień blok **udziały** jest aktualizowany w celu odzwierciedlenia nowego udziału.

## <a name="add-a-local-share"></a>Dodawanie udziału lokalnego

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **> udziały w usłudze Cloud Storage Gateway**. Wybierz pozycję **+ Dodaj udział** na pasku poleceń.

    ![Wybierz pozycję Dodaj udział 2](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i wielkie liter oraz łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** udziału. Typ może być typu **SMB** lub **NFS** z domyślnym protokołem SMB. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

   > [!IMPORTANT]
   > Upewnij się, że konto usługi Azure Storage, którego używasz, nie ma ustawionych zasad niezmiennościymi, jeśli są używane z urządzeniem Azure Stack EDGE Pro lub Data Box Gateway. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. Aby łatwo uzyskać dostęp do udziałów z modułów obliczeniowych Edge, Użyj lokalnego punktu instalacji. Wybierz pozycję **Użyj udziału przy użyciu obliczeń brzegowych** , aby moduł Edge mógł korzystać z obliczeń w lokalnym punkcie instalacji.

5. Wybierz opcję **Skonfiguruj jako udziały lokalne krawędzi**. Dane w udziałach lokalnych będą przechowywane lokalnie na urządzeniu. Te dane można przetwarzać lokalnie.

6. W polu **użytkownik lokalny wszystkie uprawnienia** wybierz opcję **Utwórz nowe** lub **Użyj istniejącej**.

7. Wybierz przycisk **Utwórz**. 

    ![Utwórz udział lokalny](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    Zobaczysz powiadomienie o tym, że tworzenie udziału jest w toku. Po utworzeniu udziału przy użyciu określonych ustawień blok **udziały** jest aktualizowany w celu odzwierciedlenia nowego udziału.

    ![Widok widoku udziałów aktualizacji](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Wybierz udział, aby wyświetlić mountpoint lokalny dla modułów obliczeniowych Edge dla tego udziału.

    ![Wyświetl szczegóły udziału lokalnego](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Instalowanie udziału

Jeśli udział został utworzony przed skonfigurowaniem obliczeń na urządzeniu Azure Stack EDGE Pro, należy zainstalować udział. Wykonaj następujące kroki, aby zainstalować udział.


1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **> udziały w usłudze Cloud Storage Gateway**. Z listy udziałów wybierz udział, który chcesz zainstalować. Kolumna **użyta do obliczenia** będzie wyświetlać stan jako **wyłączony** dla wybranego udziału.

    ![Wybieranie udziału](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. Wybierz pozycję **Zainstaluj**.

    ![Wybierz pozycję Zainstaluj](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**. Spowoduje to zainstalowanie udziału.

    ![Potwierdź instalację](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. Po zainstalowaniu udziału przejdź do listy udziałów. Zobaczysz, że wartość w kolumnie **używane dla obliczeń** wskazuje stan udziału jako **włączony**.

    ![Udział zainstalowany](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. Wybierz ponownie udział, aby wyświetlić lokalny mountpoint dla udziału. Moduł obliczeń brzegowych używa tego lokalnego mountpoint dla udziału.

    ![Mountpoint lokalny dla udziału](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Odinstalowywanie udziału

Aby odinstalować udział, wykonaj następujące czynności w Azure Portal.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **> udziały w usłudze Cloud Storage Gateway**. Z listy udziałów wybierz udział, który chcesz odinstalować. Należy upewnić się, że odinstalowany udział nie jest używany przez żadne moduły. Jeśli udział jest używany przez moduł, zobaczysz problemy z odpowiednim modułem.

    ![Wybierz udział 2](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Wybierz pozycję **Odinstaluj**.

    ![Wybierz pozycję Odinstaluj](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**. Spowoduje to odinstalowanie udziału.

    ![Potwierdź odinstalowanie](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. Po odinstalowaniu tego udziału przejdź do listy udziałów. Zobaczysz, że w kolumnie **obliczenia** jest wyświetlany stan udziału jako **wyłączony**.

    ![Udostępnij dezinstalację](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Usuwanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć udział.

1. Na liście udziałów kliknij udział, który chcesz usunąć.

    ![Wybierz pozycję Udostępnij 3](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. Kliknij polecenie **Usuń**.

    ![Klikanie polecenia Usuń](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

Lista udziałów aktualizacji w celu odzwierciedlenia usunięcia.

## <a name="refresh-shares"></a>Odświeżanie udziałów

Funkcja odświeżania umożliwia odświeżenie zawartości udziału. Podczas odświeżania udziału inicjowane jest wyszukiwanie w celu odnalezienia wszystkich obiektów platformy Azure, w tym obiektów blob i plików, które zostały dodane do chmury po ostatnio przeprowadzonym odświeżaniu. Te dodatkowe pliki są następnie pobierane w celu odświeżenia zawartości udziału na urządzeniu.

> [!IMPORTANT]
> - Nie można odświeżyć udziałów lokalnych.
> - Uprawnienia i listy kontroli dostępu (ACL) nie są zachowywane w trakcie operacji odświeżania. 

Wykonaj następujące czynności w witrynie Azure Portal, aby odświeżyć udział.

1.  W witrynie Azure Portal przejdź do sekcji **Udziały**. Kliknij udział, który chcesz odświeżyć.

    ![Wybierz pozycję Udostępnij 4](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  Kliknij przycisk **Odśwież**. 

    ![Klikanie przycisku Odśwież](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Zadanie rozpoczyna odświeżanie zawartości udziału lokalnego.

    ![Potwierdzanie odświeżania](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   W trakcie odświeżania opcja odświeżania jest wyszarzona w menu kontekstowym. Kliknij powiadomienie o zadaniu, aby wyświetlić stan zadania odświeżania.

5.   Czas odświeżania zależy od liczby plików w kontenerze platformy Azure, a także od liczby plików znajdujących się na urządzeniu. Po pomyślnym zakończeniu odświeżania znacznik czasu udziału zostanie zaktualizowany. Nawet w przypadku częściowego niepowodzenia odświeżania operacja zostanie uznana za zakończoną, a znacznik czasu zostanie zaktualizowany. Dzienniki błędów odświeżania również są aktualizowane.

![Zaktualizowany znacznik czasu](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
W przypadku awarii zostanie zgłoszony alert. Alert zawiera szczegółowe informacje o przyczynie awarii oraz zalecenia dotyczące rozwiązania problemu. Alert wskazuje również plik, który zawiera pełne podsumowanie awarii — w tym pliki, których nie udało się zaktualizować lub usunąć.

## <a name="sync-pinned-files"></a>Synchronizuj przypięte pliki 

Aby automatycznie synchronizować przypięte pliki, wykonaj następujące czynności w Azure Portal:
 
1. Wybierz istniejące konto usługi Azure Storage. 

2. Przejdź do pozycji **kontenery** i wybierz pozycję **+ kontener** , aby utworzyć kontener. Nazwij ten kontener jako *newcontainer*. Ustaw **poziom dostępu publicznego** na kontener.

    ![Automatyczna synchronizacja dla przypiętych plików 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Wybierz nazwę kontenera i ustaw następujące metadane:  

    - Name = "przypięty" 
    - Wartość = "true" 

    ![Automatyczna synchronizacja dla przypiętych plików 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Utwórz nowy udział na urządzeniu. Mapuj ją do przypiętego kontenera, wybierając opcję istniejącej kontenera. Oznacz udział jako tylko do odczytu. Utwórz nowego użytkownika i podaj nazwę użytkownika oraz odpowiednie hasło dla tego udziału.  

    ![Automatyczna synchronizacja dla przypiętych plików 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. W Azure Portal przejdź do utworzonego kontenera. Przekaż plik, który ma zostać przypięty do newcontainer, który ma metadane ustawione na przypięty. 

6. Wybierz pozycję **Odśwież dane** w Azure Portal, aby urządzenie pobrało zasady przypinania dla danego kontenera usługi Azure Storage.  

    ![Automatyczna synchronizacja dla przypiętych plików 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Uzyskaj dostęp do nowego udziału, który został utworzony na urządzeniu. Plik przekazany do konta magazynu jest teraz pobierany do udziału lokalnego. 

    Gdy urządzenie zostanie odłączone lub ponownie połączone, wyzwala odświeżanie. Odświeżenie spowoduje wyświetlenie tylko tych plików, które uległy zmianie. 


## <a name="sync-storage-keys"></a>Synchronizowanie kluczy magazynu

Jeśli klucze konta magazynu zostały wymienione, należy zsynchronizować klucze dostępu do magazynu. Przeprowadzenie synchronizacji pozwala urządzeniu uzyskać najnowsze klucze do konta magazynu.

Wykonaj następujące czynności w witrynie Azure Portal, aby zsynchronizować klucz dostępu do magazynu.

1. W zasobie przejdź do sekcji **Przegląd**. Na liście udziałów kliknij udział skojarzony z kontem magazynu, które ma zostać zsynchronizowane.

    ![Wybierz pozycję Udostępnij przy użyciu odpowiedniego konta magazynu](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. Kliknij przycisk **Synchronizuj klucz magazynu**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Wybierz pozycję Synchronizuj klucz magazynu](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

>[!NOTE]
> Wystarczy zrobić to raz dla danego konta magazynu. Nie trzeba powtarzać tej czynności dla wszystkich udziałów powiązanych z tym samym kontem magazynu.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](azure-stack-edge-gpu-manage-users.md).