---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 31e61069c95be9bd1c7a684bb83ebcd93bcb14be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019151"
---
1. Uruchom instalatora UnifiedSetup.exe usługi Azure Site Recovery.
2. W obszarze **Przed rozpoczęciem** wybierz pozycję **Dodaj dodatkowe serwery przetwarzania w celu zwiększenia skali wdrożenia**.

   ![Dodawanie serwera przetwarzania](./media/site-recovery-add-process-server/ps-page-1.png)

3. W obszarze **Szczegóły serwera konfiguracji** określ adres IP serwera konfiguracji i hasło.

   ![Dodawanie serwera przetwarzania 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. W obszarze **Ustawienia internetowe** określ, jak dostawca działający na serwerze konfiguracji ma się łączyć z usługą Azure Site Recovery przez Internet.

   ![Dodawanie serwera przetwarzania 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Jeśli chcesz łączyć się z serwerem proxy aktualnie skonfigurowanym na maszynie, wybierz opcję **Połącz przy użyciu istniejących ustawień serwera proxy**.
   * Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio bez serwera proxy**.
   * Jeśli istniejący serwer proxy wymaga uwierzytelniania lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**.

     * Jeśli używasz niestandardowego serwera proxy, musisz określić adres, port i poświadczenia.
     * W przypadku używania serwera proxy już zezwolono na użycie adresów URL usług.

5. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

     ![Dodawanie serwera przetwarzania 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli tak, instalator sprawdzi, czy jest zainstalowany program PowerCLI 6.0.

     ![Dodawanie serwera przetwarzania 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.
     ![Zrzut ekranu pokazujący lokalizację instalacji plików binarnych i magazynu pamięci podręcznej.](./media/site-recovery-add-process-server/ps-page-6.png)

8. W obszarze **Wybór sieci** określ odbiornik (kartę sieciową i port SSL), za pomocą którego serwer konfiguracji będzie wysyłać i odbierać dane replikacji. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania i odbierania ruchu związanego z replikacją.

     ![Dodawanie serwera przetwarzania 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.

     ![Dodawanie serwera przetwarzania 7](./media/site-recovery-add-process-server/ps-page-8.png)
