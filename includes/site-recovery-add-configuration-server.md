---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001568"
---
1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W obszarze **przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

    ![Zrzut ekranu przedstawiający ekran przed rozpoczęciem w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.

    ![Zrzut ekranu przedstawiający ekran licencji Third Party Software w ramach ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. W obszarze **Rejestracja** wybierz klucz rejestracji pobrany z magazynu.

    ![Zrzut ekranu przedstawiający ekran rejestracji w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. W obszarze **Ustawienia internetowe** Określ, jak dostawca działający na serwerze konfiguracji łączy się z Azure Site Recovery przez Internet. Upewnij się, że adresy URL są dozwolone.

    - Jeśli chcesz nawiązać połączenie z serwerem proxy, który jest aktualnie skonfigurowany na komputerze, wybierz pozycję **Połącz z Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio z Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelniania lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**, a następnie określ adres, port i poświadczenia.
     ![Zrzut ekranu przedstawiający ekran ustawień internetowych w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Zrzut ekranu przedstawiający ekran sprawdzanie wymagań wstępnych w ramach ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![Zrzut ekranu przedstawiający ekran konfiguracji programu MySQL w ramach ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. W obszarze **szczegóły środowiska** wybierz pozycję nie, jeśli replikujesz Azure Stack maszyny wirtualne lub serwery fizyczne. 
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Zrzut ekranu przedstawiający ekran lokalizacja instalacji w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. W **obszarze Wybór sieci** najpierw wybierz kartę interfejsu sieciowego używaną przez serwer przetwarzania na potrzeby odnajdywania i instalacji wypychanej usługi mobilności na maszynach źródłowych, a następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania lub otrzymywania ruchu związanego z replikacją.

    ![Zrzut ekranu przedstawiający ekran wybór sieci w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.

    ![Zrzut ekranu przedstawiający ekran podsumowanie w ujednoliconej konfiguracji.](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia**  >  **serwery** w magazynie.
