---
title: Zarządzanie poświadczeniami konta magazynu, urządzeniem z serii StorSimple 8000
description: Wyjaśnia, w jaki sposób można użyć strony StorSimple Menedżer urządzeń configure, aby dodać, edytować, usunąć lub obrócić klucze zabezpieczeń dla konta magazynu.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951744"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Zarządzanie poświadczeniami konta magazynu za pomocą usługi StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie

Sekcja **konfiguracji** w bloku usługi StorSimple Menedżer urządzeń przedstawia wszystkie globalne parametry usługi, które można utworzyć w usłudze StorSimple Menedżer urządzeń. Te parametry mogą być stosowane do wszystkich urządzeń podłączonych do usługi i obejmują:

* Poświadczenia konta magazynu
* Szablony przepustowości 
* Rekordy kontroli dostępu 

W tym samouczku wyjaśniono, jak dodać, edytować lub usunąć poświadczenia konta magazynu, lub obrócić klucze zabezpieczeń dla konta magazynu.

 ![Lista poświadczeń konta magazynu 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Konta magazynu zawierają poświadczenia używane przez urządzenie StorSimple do uzyskiwania dostępu do konta magazynu za pomocą dostawcy usług w chmurze. W przypadku kont usługi Microsoft Azure Storage są to poświadczenia, takie jak nazwa konta i podstawowy klucz dostępu. 

W bloku **poświadczenia konta magazynu** wszystkie konta magazynu utworzone dla subskrypcji rozliczeń są wyświetlane w formacie tabelarycznym zawierającym następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do konta podczas jego tworzenia.
* **Włączony protokół SSL** — czy protokół TLS jest włączony, a komunikacja między urządzeniami i chmurą odbywa się za pośrednictwem bezpiecznego kanału.
* **Używane przez** — liczba woluminów korzystających z konta magazynu.

Najbardziej typowe zadania związane z kontami magazynu, które można wykonać, to:

* Dodawanie konta magazynu 
* Edytowanie konta magazynu 
* Usuwanie konta magazynu 
* Kluczowe rotacja kont magazynu 

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Istnieją trzy typy kont magazynu, które mogą być używane z urządzeniem StorSimple.

* **Automatycznie generowane konta magazynu** — w takiej sytuacji nazwa jest generowana automatycznie podczas pierwszego tworzenia usługi. Aby dowiedzieć się więcej na temat sposobu tworzenia tego konta magazynu, zobacz [krok 1. Tworzenie nowej usługi](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) w ramach [wdrażania lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Konta magazynu w ramach subskrypcji usługi** — są to konta usługi Azure Storage, które są skojarzone z tą samą subskrypcją, co w przypadku usługi. Aby dowiedzieć się więcej o sposobie tworzenia tych kont magazynu, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-account-create.md). 
* **Konta magazynu poza subskrypcją usługi** — są to konta usługi Azure Storage, które nie są skojarzone z Twoją usługą i mogą istnieć przed utworzeniem usługi.

## <a name="add-a-storage-account"></a>Dodawanie konta magazynu

Konto magazynu można dodać, podając unikatową przyjazną nazwę i poświadczenia dostępu, które są połączone z kontem magazynu (z określonym dostawcą usług w chmurze). Istnieje również możliwość włączenia trybu Transport Layer Security (TLS), wcześniej znanego jako tryb SSL (SSL), aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między urządzeniem i chmurą.

Można utworzyć wiele kont dla danego dostawcy usług w chmurze. Należy jednak pamiętać, że po utworzeniu konta magazynu nie można zmienić dostawcy usługi w chmurze.

Podczas zapisywania konta magazynu usługa próbuje skomunikować się z dostawcą usług w chmurze. Podane poświadczenia i materiał dostępu zostaną uwierzytelnione w tej chwili. Konto magazynu jest tworzone tylko w przypadku pomyślnego uwierzytelnienia. Jeśli uwierzytelnianie nie powiedzie się, zostanie wyświetlony odpowiedni komunikat o błędzie.

Aby dodać poświadczenia konta usługi Azure Storage, wykonaj następujące procedury:

* Aby dodać poświadczenia konta magazynu, które ma tę samą subskrypcję platformy Azure co usługa Menedżer urządzeń
* Aby dodać poświadczenia konta usługi Azure Storage, które jest poza subskrypcją usługi Menedżer urządzeń

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Aby dodać poświadczenia konta usługi Azure Storage poza subskrypcją usługi StorSimple Menedżer urządzeń

1. Przejdź do usługi StorSimple Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** . Ta lista zawiera wszystkie istniejące poświadczenia konta magazynu skojarzone z usługą StorSimple Menedżer urządzeń.
3. Kliknij pozycję **Dodaj**.
4. W bloku **Dodawanie poświadczeń konta magazynu** wykonaj następujące czynności:
   
    1. W obszarze **subskrypcja** wybierz pozycję **inne**.
   
    2. Podaj nazwę poświadczenia konta usługi Azure Storage.
   
    3. W polu tekstowym **klucz dostępu do konta magazynu** Podaj podstawowy klucz dostępu dla poświadczeń konta usługi Azure Storage. Aby uzyskać ten klucz, przejdź do usługi Azure Storage, wybierz swoje poświadczenia konta magazynu, a następnie kliknij przycisk **Zarządzaj kluczami konta**. Teraz można skopiować podstawowy klucz dostępu.
   
    4. Aby włączyć protokół TLS, kliknij przycisk **Włącz** , aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między usługą StorSimple Menedżer urządzeń i chmurą. Kliknij przycisk **Wyłącz** tylko wtedy, gdy Pracujesz w chmurze prywatnej.
   
    5. Kliknij pozycję **Dodaj**. Po pomyślnym utworzeniu poświadczenia konta magazynu otrzymasz powiadomienie.

5. Nowo utworzone poświadczenia konta magazynu są wyświetlane w bloku StorSimple Configure Menedżer urządzeń Service w obszarze **poświadczenia konta magazynu**.
   


## <a name="edit-a-storage-account"></a>Edytowanie konta magazynu

Można edytować konto magazynu, które jest używane przez kontener woluminów. Jeśli edytujesz konto magazynu, które jest aktualnie używane, jedynym polem dostępnym do modyfikacji jest klucz dostępu dla konta magazynu. Możesz podać nowy klucz dostępu do magazynu i zapisać zaktualizowane ustawienia.

#### <a name="to-edit-a-storage-account"></a>Aby edytować konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

    ![Poświadczenia konta magazynu 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. W bloku **poświadczenia konta magazynu** z listy poświadczenia konta magazynu wybierz i kliknij tę, którą chcesz edytować. 

3. Możesz zmodyfikować wybór opcji **Włącz protokół SSL** . Możesz również kliknąć pozycję **więcej...** , a następnie wybrać pozycję **Synchronizuj klucz dostępu, aby obrócić** klucze dostępu do konta magazynu. Aby uzyskać więcej informacji na temat tworzenia rotacji kluczy, przejdź do [kluczowego rotacji kont magazynu](#key-rotation-of-storage-accounts) . Po zmodyfikowaniu ustawień kliknij przycisk **Zapisz**. 

    ![Zapisz edytowane poświadczenia konta magazynu](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Potwierdź modyfikacje](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Ustawienia zostaną zaktualizowane i zapisane dla konta magazynu. 

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

> [!IMPORTANT]
> Konto magazynu można usunąć tylko wtedy, gdy nie jest używane przez kontener woluminów. Jeśli konto magazynu jest używane przez kontener woluminów, najpierw usuń kontener woluminów, a następnie usuń skojarzone konto magazynu.

#### <a name="to-delete-a-storage-account"></a>Aby usunąć konto magazynu

1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.

2. Na tabelarycznej liście kont magazynu Umieść wskaźnik myszy na koncie, które chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie kliknij przycisk **Usuń**.

    ![Usuwanie poświadczeń konta magazynu](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby kontynuować usuwanie. Lista tabelaryczna zostanie zaktualizowana w celu odzwierciedlenia zmian.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Kluczowe rotacja kont magazynu

Ze względów bezpieczeństwa kluczowe rotacja jest często wymagane w centrach danych. Każda subskrypcja Microsoft Azure może mieć jedno lub więcej skojarzonych kont magazynu. Dostęp do tych kont jest kontrolowany przez klucze subskrypcji i dostępu dla każdego konta magazynu. 

Podczas tworzenia konta magazynu Microsoft Azure generuje 2 512-bitowe klucze dostępu do magazynu, które są używane do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. Posiadanie dwóch kluczy dostępu do magazynu umożliwia ponowne wygenerowanie kluczy bez przerw w świadczeniu usługi magazynu ani dostęp do tej usługi. Używanym kluczem jest klucz *podstawowy* , a klucz kopii zapasowej jest określany jako klucz *pomocniczy* . Jeden z tych dwóch kluczy należy podać, gdy urządzenie Microsoft Azure StorSimple uzyskuje dostęp do dostawcy usługi magazynu w chmurze.

## <a name="what-is-key-rotation"></a>Co to jest rotacja kluczy?

Zazwyczaj aplikacje używają tylko jednego z kluczy, aby uzyskać dostęp do danych. Po upływie pewnego czasu Twoje aplikacje mogą przełączać się do korzystania z drugiego klucza. Po przełączeniu aplikacji do klucza pomocniczego można wycofać pierwszy klucz, a następnie wygenerować nowy klucz. Użycie dwóch kluczy w ten sposób umożliwia aplikacjom dostęp do danych bez powodowania przestojów.

Klucze konta magazynu są zawsze przechowywane w usłudze w postaci zaszyfrowanej. Można je jednak zresetować za pomocą usługi StorSimple Menedżer urządzeń. Usługa może uzyskać klucz podstawowy i klucz pomocniczy dla wszystkich kont magazynu w ramach tej samej subskrypcji, w tym kont utworzonych w usłudze Storage, a także domyślnych kont magazynu wygenerowanych podczas pierwszego tworzenia usługi StorSimple Menedżer urządzeń. Usługa StorSimple Menedżer urządzeń zawsze będzie pobierać te klucze z klasycznego portalu Azure, a następnie przechowywać je w sposób zaszyfrowany.

## <a name="rotation-workflow"></a>Przepływ pracy rotacji

Administrator Microsoft Azure może ponownie wygenerować lub zmienić klucz podstawowy lub pomocniczy bezpośrednio przez uzyskanie dostępu do konta magazynu (za pośrednictwem usługi Microsoft Azure Storage). Ta zmiana nie jest automatycznie widoczna dla usługi StorSimple Menedżer urządzeń.

Aby poinformować usługę StorSimple Menedżer urządzeń o zmianie, musisz uzyskać dostęp do usługi StorSimple Menedżer urządzeń, uzyskać dostęp do konta magazynu, a następnie zsynchronizować klucz podstawowy lub pomocniczy (w zależności od tego, który został zmieniony). Następnie usługa pobiera najnowszy klucz, szyfruje klucze i wysyła zaszyfrowany klucz do urządzenia.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Aby synchronizować klucze dla kont magazynu w ramach tej samej subskrypcji co usługa 
1. Przejdź do usługi Menedżer urządzeń StorSimple. W sekcji **Konfiguracja** kliknij pozycję **Poświadczenia konta magazynu**.
2. Na tabelarycznej liście kont magazynu kliknij tę, którą chcesz zmodyfikować. 

    ![Synchronizuj klucze](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Kliknij przycisk **... Więcej** , a następnie wybierz pozycję **Synchronizuj klucz dostępu do obrotu**.   

    ![Synchronizuj klucze 2](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. W usłudze StorSimple Menedżer urządzeń należy zaktualizować klucz, który został wcześniej zmieniony w usłudze Microsoft Azure Storage. Jeśli podstawowy klucz dostępu został zmieniony (ponownie wygenerowany), wybierz pozycję klucz **podstawowy** . Jeśli klucz pomocniczy został zmieniony, wybierz pozycję klucz **pomocniczy** . Kliknij przycisk **Synchronizuj klucz**.
      
      ![Synchronizuj klucze 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po pomyślnym zsynchronizowaniu klucza użytkownik zostanie powiadomiony.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Aby synchronizować klucze kont magazynu poza subskrypcją usługi
1. Na stronie **usługi** kliknij kartę **Konfiguracja** .
2. Kliknij przycisk **Dodaj/Edytuj konta magazynu**.
3. W oknie dialogowym wykonaj następujące czynności:
   
   1. Wybierz konto magazynu z kluczem dostępu, który chcesz zaktualizować.
   2. Należy zaktualizować klucz dostępu do magazynu w ramach usługi StorSimple Menedżer urządzeń. W takim przypadku można zobaczyć klucz dostępu do magazynu. Wprowadź nowy klucz w polu **klucz dostępu do konta magazynu** . 
   3. Zapisz zmiany. Klucz dostępu do konta magazynu powinien zostać zaktualizowany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).