---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564289"
---
#### <a name="to-create-a-cloud-appliance"></a>Aby utworzyć urządzenie w chmurze

1. W witrynie Azure Portal przejdź do usługi **Menedżer urządzeń StorSimple**.
2. Przejdź do bloku **Urządzenia**. Na pasku poleceń w bloku podsumowania usługi kliknij pozycję **Utwórz urządzenie w chmurze**.
    ![Tworzenie urządzenia StorSimple w chmurze](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. W bloku **Tworzenie urządzenia w chmurze** podaj poniższe informacje.
   
    ![StorSimple Utwórz urządzenie w chmurze 2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Nazwa** — unikatowa nazwa urządzenia w chmurze.
   2. **Model** — wybierz model urządzenia w chmurze. Urządzenie 8010 oferuje 30 TB magazynu Standard Storage, a 8020 ma 64 TB magazynu w usłudze Premium Storage. Wybierz model 8010, aby wdrożyć scenariusze pobierania z kopii zapasowych na poziomie elementów. Wybierz model 8020, aby wdrożyć obciążenia o wysokiej wydajności i małych opóźnieniach lub używać go jako urządzenia dodatkowego do odzyskiwania po awarii.
   3. **Wersja** — wybierz wersję urządzenia w chmurze. Wersja odpowiada wersji obrazu dysku wirtualnego używanego do utworzenia urządzenia w chmurze. Biorąc pod uwagę, że wersja urządzenia w chmurze decyduje o tym, które fizyczne urządzenie zostanie przeniesione do tryb failover lub z którego urządzenia będzie można klonować, ważne jest, aby utworzyć odpowiednią wersję urządzenia w chmurze.
   4. **Sieć wirtualna** — określ sieć wirtualną, której chcesz użyć dla tego urządzenia w chmurze. W przypadku korzystania z usługi Premium Storage musisz wybrać sieć wirtualną obsługiwaną za pomocą konta w usłudze Premium Storage. Nieobsługiwane sieci wirtualne są wyszarzone na liście rozwijanej. W przypadku wybrania nieobsługiwanej sieci wirtualnej jest wyświetlane ostrzeżenie.
   5. **Podsieć** — w oparciu o wybraną sieć wirtualną na liście rozwijanej są wyświetlane skojarzone podsieci. Przypisz podsieci do urządzenia w chmurze.
   6. **Konto magazynu** — wybierz konto magazynu do przechowywania obrazu urządzenia w chmurze podczas aprowizowania. Musi ono należeć do tego samego regionu co urządzenie w chmurze i sieć wirtualna. Nie powinno być używane do przechowywania danych przez urządzenie fizyczne ani urządzenie w chmurze. Domyślnie do tego celu jest tworzone nowe konto magazynu. Jednak jeśli wiadomo, że masz już konto magazynu odpowiednie do tego celu, możesz wybrać je z listy. W przypadku tworzenia urządzenia w chmurze w warstwie Premium lista rozwijana będzie zawierać tylko konta usługi Premium Storage.
      
      > [!NOTE]
      > Urządzenie w chmurze może pracować tylko z kontami usługi Azure Storage.
    
   7. Zaznacz pole wyboru, aby wskazać, że wiesz, że dane przechowywane w urządzeniu w chmurze są obsługiwane w centrum danych firmy Microsoft.
       * Kiedy używasz tylko urządzenia fizycznego, klucz szyfrowania jest przechowywany z urządzeniem, więc firma Microsoft nie może go odszyfrować.

       * Kiedy używasz urządzenia w chmurze, zarówno klucz szyfrowania, jak i klucz odszyfrowywania są przechowywane na platformie Microsoft Azure. Aby uzyskać więcej informacji, zapoznaj się z [zagadnieniami dotyczącymi zabezpieczeń podczas używania urządzenia w chmurze](../articles/storsimple/storsimple-8000-security.md).
   8. Kliknij przycisk **Utwórz**, aby aprowizować urządzenie w chmurze. Zainicjowanie obsługi urządzenia może zająć około 30 minut. Otrzymasz powiadomienie o pomyślnym utworzeniu urządzenia w chmurze. Przejdź do bloku Urządzenia. Lista urządzeń zostanie odświeżona w celu wyświetlenia urządzenia w chmurze. Stan urządzenia to **Gotowe do skonfigurowania**.
      
      ![Urządzenie StorSimple w chmurze gotowe do skonfigurowania](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)