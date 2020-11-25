---
title: Zarządzanie poświadczeniami konta magazynu macierzy wirtualnych StorSimple | Microsoft Docs
description: Wyjaśnia, w jaki sposób można użyć strony StorSimple Menedżer urządzeń configure, aby dodać, edytować, usunąć lub obrócić klucze zabezpieczeń dla poświadczeń konta magazynu skojarzonych z wirtualną tablicą StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023895"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Użyj StorSimple Menedżer urządzeń do zarządzania poświadczeniami konta magazynu dla macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie
Sekcja **konfiguracji** bloku StorSimple Menedżer urządzeń w macierzy wirtualnej StorSimple przedstawia globalne parametry usługi, które można utworzyć w usłudze StorSimple Manager. Te parametry mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Rekordy kontroli dostępu
  
  ![Pulpit nawigacyjny usługi Menedżer urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

W tym samouczku wyjaśniono, jak można dodawać, edytować lub usuwać poświadczenia konta magazynu dla macierzy wirtualnej StorSimple. Informacje przedstawione w tym samouczku dotyczą tylko macierzy wirtualnej StorSimple. Aby uzyskać informacje na temat zarządzania kontami magazynu w serii 8000, zobacz [Korzystanie z usługi StorSimple Manager do zarządzania kontem magazynu](./storsimple-8000-manage-storage-accounts.md).

Poświadczenia konta magazynu zawierają poświadczenia używane przez urządzenie do uzyskiwania dostępu do konta magazynu za pomocą dostawcy usług w chmurze. W przypadku kont usługi Microsoft Azure Storage są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu.

W bloku **poświadczenia konta magazynu** wszystkie poświadczenia konta magazynu utworzone dla subskrypcji rozliczeń są wyświetlane w formacie tabelarycznym zawierającym następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta podczas jego tworzenia.
* **Włączony protokół SSL** — czy protokół TLS jest włączony, a komunikacja między urządzeniami i chmurą odbywa się za pośrednictwem bezpiecznego kanału.
  
  ![Sekcja konfiguracji](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Najczęstsze zadania związane z poświadczeniami konta magazynu, które można wykonać w bloku **poświadczenia konta magazynu** , są następujące:

* Dodawanie poświadczeń konta magazynu
* Edytuj poświadczenia konta magazynu
* Usuwanie poświadczeń konta magazynu

## <a name="types-of-storage-account-credentials"></a>Typy poświadczeń konta magazynu
Istnieją trzy typy poświadczeń konta magazynu, które mogą być używane z urządzeniem StorSimple.

* **Automatycznie generowane poświadczenia konta magazynu** — w miarę jak nazwa sugeruje, że ten typ poświadczenia konta magazynu jest generowany automatycznie podczas pierwszego tworzenia usługi. Aby dowiedzieć się więcej o sposobie tworzenia tego poświadczenia konta magazynu, zobacz [Tworzenie nowej usługi](storsimple-virtual-array-manage-service.md#create-a-service).
* **poświadczenia konta magazynu w ramach subskrypcji usługi** — są to poświadczenia konta usługi Azure Storage, które są skojarzone z tą samą subskrypcją co usługa. Aby dowiedzieć się więcej o tym, jak są tworzone poświadczenia konta magazynu, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-account-create.md).
* **poświadczenia konta magazynu poza subskrypcją usługi** — są to poświadczenia konta usługi Azure Storage, które nie są skojarzone z usługą i mogą być dostępne przed utworzeniem usługi.

## <a name="add-a-storage-account-credential"></a>Dodawanie poświadczeń konta magazynu
Poświadczenia konta magazynu można dodać do konfiguracji usługi StorSimple Menedżer urządzeń, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu. Istnieje również możliwość włączenia trybu Transport Layer Security (TLS), wcześniej znanego jako tryb SSL (SSL), aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między urządzeniem i chmurą.

Można utworzyć wiele kont dla danego dostawcy usług w chmurze. Podczas zapisywania poświadczenia konta magazynu usługa próbuje skomunikować się z dostawcą usług w chmurze. Poświadczenia i materiały dostępu podane przez użytkownika są uwierzytelniane w tym momencie. Poświadczenia konta magazynu są tworzone tylko w przypadku pomyślnego uwierzytelnienia. Jeśli uwierzytelnianie nie powiedzie się, zostanie wyświetlony odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta usługi Azure Storage, wykonaj następujące procedury:

* Aby dodać poświadczenia konta magazynu, które ma tę samą subskrypcję platformy Azure co usługa Menedżer urządzeń
* Aby dodać poświadczenia konta usługi Azure Storage, które jest poza subskrypcją usługi Menedżer urządzeń

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczenia konta magazynu, które ma tę samą subskrypcję platformy Azure co usługa Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** .
3. Kliknij pozycję **Dodaj**.
4. W bloku **Dodawanie konta magazynu** wykonaj następujące czynności:
   
    1. W obszarze **subskrypcja** wybierz pozycję **bieżące**.
    2. Podaj nazwę konta usługi Azure Storage.
    3. Wybierz pozycję **Włącz** , aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między urządzeniem StorSimple a chmurą. Wybierz opcję **Wyłącz** tylko wtedy, gdy Pracujesz w chmurze prywatnej.
    4. Kliknij pozycję **Dodaj**. Po pomyślnym utworzeniu konta magazynu otrzymasz powiadomienie.<br></br>
   
        ![Dodawanie istniejącego poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Aby dodać poświadczenia konta usługi Azure Storage, które jest poza subskrypcją usługi Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** . Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą StorSimple Menedżer urządzeń.
3. Kliknij pozycję **Dodaj**.
4. W bloku **Dodawanie konta magazynu** wykonaj następujące czynności:
   
    1. W obszarze **subskrypcja** wybierz pozycję **inne**.
   
    2. Podaj nazwę poświadczenia konta usługi Azure Storage.
   
    3. W polu tekstowym **klucz dostępu do konta magazynu** Podaj podstawowy klucz dostępu dla poświadczeń konta usługi Azure Storage. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz swoje poświadczenia konta magazynu, a następnie kliknij przycisk **Zarządzaj kluczami konta**. Teraz można skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć protokół TLS, kliknij przycisk **Włącz** , aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między usługą StorSimple Menedżer urządzeń i chmurą. Kliknij przycisk **Wyłącz** tylko wtedy, gdy Pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Dodaj**. Po pomyślnym utworzeniu poświadczenia konta magazynu otrzymasz powiadomienie.

5. Nowo utworzone poświadczenia konta magazynu są wyświetlane w bloku StorSimple Configure Menedżer urządzeń Service w obszarze **poświadczenia konta magazynu**.
   
    ![Dodawanie poświadczeń konta magazynu poza subskrypcją usługi Menedżer urządzeń](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Edytuj poświadczenia konta magazynu
Można edytować poświadczenia konta magazynu używanego przez urządzenie. Jeśli edytujesz poświadczenia konta magazynu, które jest aktualnie używane, pola dostępne do modyfikacji to klucz dostępu i tryb TLS dla poświadczeń konta magazynu. Możesz podać nowy klucz dostępu do magazynu lub zmodyfikować ustawienie **Włącz tryb SSL** i zapisać zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account-credential"></a>Aby edytować poświadczenia konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** . Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą StorSimple Menedżer urządzeń.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz i kliknij dwukrotnie konto, które chcesz zmodyfikować.
4. W bloku **Właściwości** poświadczeń konta magazynu wykonaj następujące czynności:
   
   1. W razie potrzeby można zmodyfikować wybór trybu **Włącz protokół SSL** .
   2. Możesz wybrać ponowne wygenerowanie kluczy dostępu poświadczeń konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). Podaj nowy klucz poświadczeń konta magazynu. W przypadku konta usługi Azure Storage jest to podstawowy klucz dostępu.
   3. Kliknij pozycję **Zapisz** w górnej części bloku **Właściwości** , aby zapisać ustawienia. Ustawienia są aktualizowane w bloku **poświadczenia konta magazynu** .
      
      ![Edytuj poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Usuwanie poświadczeń konta magazynu
> [!IMPORTANT]
> Poświadczenia konta magazynu można usunąć tylko wtedy, gdy nie jest ono używane. Jeśli poświadczenia konta magazynu są używane, zostanie wyświetlone powiadomienie.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Aby usunąć poświadczenia konta magazynu
1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** . Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą StorSimple Menedżer urządzeń.
3. Na tabelarycznej liście poświadczeń konta magazynu wybierz i kliknij dwukrotnie konto, które chcesz usunąć.
4. W bloku **Właściwości** poświadczeń konta magazynu wykonaj następujące czynności:
   
   1. Kliknij przycisk **Usuń** , aby usunąć poświadczenia.
   2. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby kontynuować usuwanie. Lista tabelaryczna została zaktualizowana w celu odzwierciedlenia zmian.
      
      ![Usuwanie poświadczeń konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizowanie kluczy poświadczeń konta magazynu
Ze względów bezpieczeństwa kluczowe rotacja jest często wymagane w centrach danych. Administrator Microsoft Azure może ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy przez bezpośredni dostęp do poświadczeń konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Ta zmiana nie jest automatycznie widoczna dla usługi StorSimple Menedżer urządzeń.

Aby poinformować usługę StorSimple Menedżer urządzeń o zmianie, musisz uzyskać dostęp do usługi StorSimple Menedżer urządzeń, uzyskać dostęp do poświadczenia konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, który został zmieniony). Następnie usługa pobiera najnowszy klucz, szyfruje klucze i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Aby synchronizować klucze dla poświadczeń konta magazynu w tej samej subskrypcji co usługa (tylko platforma Azure)
1. W bloku spocznik usługi wybierz swoją usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **poświadczenia konta magazynu**.
2. W bloku **poświadczenia konta magazynu** na liście poświadczenia konta magazynu wybierz poświadczenia konta magazynu, którego klucze chcesz synchronizować.
3. W bloku **Właściwości** dla wybranego poświadczenia konta magazynu wykonaj następujące czynności:
   
    1. Kliknij przycisk **więcej**, a następnie kliknij przycisk **Synchronizuj klucz dostępu**.
   
    2. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Synchronizuj klucz** , aby zakończyć synchronizację.
    
4. W usłudze StorSimple Menedżer urządzeń należy zaktualizować klucz, który został wcześniej zmieniony w usłudze Microsoft Azure Storage. W bloku **Synchronizuj klucz konta magazynu** , jeśli podstawowy klucz dostępu został zmieniony (ponownie wygenerowany), kliknij przycisk podstawowy, a następnie kliknij przycisk **Synchronizuj klucz**. Jeśli klucz pomocniczy został zmieniony, kliknij przycisk **pomocniczy**, a następnie kliknij przycisk **Synchronizuj klucz**.
   
    ![Synchronizuj klucz dostępu](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [administrować wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).