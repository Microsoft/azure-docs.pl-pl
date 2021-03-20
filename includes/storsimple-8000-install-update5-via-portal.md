---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9832a961562930e61e94b28ed78976fd9f8c6f78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376007"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie.

    ![Wybierz urządzenie](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Przejdź do **ustawień urządzenia ustawienia**  >  **aktualizacje urządzeń**.

    ![Kliknij pozycję Aktualizacje urządzeń](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Zostanie wyświetlone powiadomienie, jeśli są dostępne nowe aktualizacje. Alternatywnie w bloku **aktualizacje urządzeń** kliknij pozycję **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.

    ![Kliknij pozycję Aktualizacje urządzeń 2](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji. Aby zastosować aktualizacje, kliknij przycisk **instaluj aktualizacje**. Przed zastosowaniem aktualizacji w bloku **Potwierdź regularne aktualizacje** zapoznaj się z wymaganiami wstępnymi. Zaznacz pole wyboru, aby wskazać, że wszystko jest gotowe do zaktualizowania urządzenia, a następnie kliknij przycisk **Instaluj**.

    ![Kliknij pozycję Aktualizacje urządzeń 3](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.

     Aktualizacja jest pobierana i instalowana tylko wtedy, gdy wszystkie testy zostały zakończone pomyślnie. Zobaczysz powiadomienie o trwających testach. Jeśli wyewidencjonowanie nie powiedzie się, otrzymasz przyczyny niepowodzenia. Rozwiąż te problemy, a następnie spróbuj ponownie wykonać operację. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.

7. Po pomyślnym zakończeniu sprawdzenia zostanie utworzone zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.

9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.

    ![Aktualizowanie tworzenia zadania 2](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Możesz również monitorować postęp zadania aktualizacji z **ustawień urządzenia > zadania**. W bloku **zadania** można zobaczyć postęp aktualizacji.

     ![Aktualizowanie tworzenia zadania 3](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Po zakończeniu zadania przejdź do **ustawień urządzenia, > aktualizacje urządzeń**. Wersja oprogramowania powinna zostać zaktualizowana.

