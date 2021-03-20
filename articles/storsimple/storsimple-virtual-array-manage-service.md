---
title: Wdrażanie usługi StorSimple Device Manager | Microsoft Docs
description: Wyjaśnia, jak utworzyć i usunąć usługę StorSimple Device Manager w Azure Portal, a także opis sposobu zarządzania kluczem rejestracji usługi.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995021"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Wdróż usługę StorSimple Device Manager dla macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Usługa StorSimple Device Manager działa w Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi można za jej pomocą zarządzać urządzeniami z poziomu Microsoft Azure Portal działającego w przeglądarce. Pozwala to monitorować wszystkie urządzenia, które są połączone z usługą StorSimple Device Manager z jednej lokalizacji centralnej, co minimalizuje obciążenie administracyjne.

Typowe zadania związane z usługą StorSimple Device Manager są następujące:

* Tworzenie usługi
* Usuwanie usługi
* Pobieranie klucza rejestracji usługi
* Wygeneruj ponownie klucz rejestracji usługi

W tym samouczku opisano sposób wykonywania każdego z powyższych zadań. Informacje zawarte w tym artykule dotyczą tylko tablic wirtualnych StorSimple. Aby uzyskać więcej informacji na temat serii StorSimple 8000, przejdź do [wdrożenia usługi StorSimple Manager](./storsimple-8000-manage-service.md).

## <a name="create-a-service"></a>Tworzenie usługi

Aby utworzyć usługę, należy dysponować:

* Subskrypcja z Enterprise Agreement
* Konto magazynu Active Microsoft Azure
* Informacje dotyczące rozliczeń, które są używane do zarządzania dostępem

Możesz również wybrać opcję wygenerowania konta magazynu podczas tworzenia usługi.

Pojedyncza usługa może zarządzać wieloma urządzeniami. Jednak urządzenie nie może obejmować wielu usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacjami lub nawet lokalizacjami wdrażania.

> [!NOTE]
> Potrzebujesz oddzielnych wystąpień usługi StorSimple Device Manager, aby zarządzać urządzeniami z serii StorSimple 8000 i macierzami wirtualnymi StorSimple.


Wykonaj następujące kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi upewnij się, że żadne podłączone urządzenia nie korzystają z niej. Jeśli usługa jest używana, Dezaktywuj połączone urządzenia. Operacja Dezaktywuj spowoduje nawiązanie połączenia między urządzeniem i usługą, ale zachowa dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć tej operacji. Wszystkie urządzenia, które używały usługi, muszą zostać zresetowane do ustawień fabrycznych, zanim będą mogły być używane z inną usługą. W tym scenariuszu dane lokalne na urządzeniu oraz konfiguracja zostaną utracone.
 

Wykonaj następujące kroki, aby usunąć usługę.

#### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Przejdź do sekcji **Wszystkie zasoby**. Wyszukaj usługę StorSimple Device Manager. Wybierz usługę, którą chcesz usunąć.
   
    ![Wybierz usługę do usunięcia](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Przejdź do pulpitu nawigacyjnego usługi, aby upewnić się, że nie ma żadnych urządzeń podłączonych do usługi. Jeśli nie ma żadnych urządzeń zarejestrowanych w usłudze, zobaczysz również komunikat transparentu. Kliknij polecenie **Usuń**.
   
    ![Usuń usługę](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** w powiadomieniu o potwierdzeniu. 
   
    ![Potwierdzenie usunięcia usługi](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Usunięcie usługi może potrwać kilka minut. Po pomyślnym usunięciu usługi użytkownik zostanie powiadomiony.
   
    ![Pomyślne usunięcie usługi](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Zostanie odświeżona lista usług.

 ![Zaktualizowana lista usług](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi
Po pomyślnym utworzeniu usługi konieczne będzie zarejestrowanie urządzenia z systemem StorSimple w usłudze. Aby zarejestrować swoje pierwsze urządzenie StorSimple, będzie potrzebny klucz rejestracji usługi. Do zarejestrowania dodatkowych urządzeń za pomocą istniejącej usługi StorSimple wymagany jest zarówno klucz rejestracji, jak i klucz szyfrowania danych usługi (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia StorSimple](./storsimple-8000-security.md). Klucz rejestracji można uzyskać, uzyskując dostęp do bloku **klucze** usługi.

Wykonaj następujące kroki, aby uzyskać klucz rejestracji usługi.

#### <a name="to-get-the-service-registration-key"></a>Aby uzyskać klucz rejestracji usługi
1. W bloku **StorSimple Device Manager** przejdź do pozycji **klucze** **zarządzania &gt;** .
   
   ![Blok Klucze](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W bloku **klucze** zostanie wyświetlony klucz rejestracji usługi. Skopiuj klucz rejestracji za pomocą ikony kopiowania. 

Przechowuj klucz rejestracji usługi w bezpiecznym miejscu. Ten klucz będzie potrzebny, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowe urządzenia w tej usłudze. Po uzyskaniu klucza rejestracji usługi należy skonfigurować urządzenie za pomocą interfejsu program Windows PowerShell dla usługi StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Należy ponownie wygenerować klucz rejestracji usługi, jeśli wymagane jest przeprowadzenie wymiany kluczy lub zmiana listy administratorów usługi. W przypadku ponownego wygenerowania klucza nowy klucz jest używany tylko do rejestrowania kolejnych urządzeń. Ten proces nie ma na nie wpływ na urządzenia, które zostały już zarejestrowane.

Wykonaj następujące kroki, aby ponownie wygenerować klucz rejestracji usługi.

#### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W bloku **StorSimple Device Manager** przejdź do pozycji **klucze** **zarządzania &gt;** .
   
   ![Ponownie blok kluczy](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. W bloku **klucze** kliknij polecenie **Generuj ponownie**.
   
   ![Kliknij pozycję Wygeneruj ponownie](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. W bloku ponowne **generowanie klucza rejestracji usługi** zapoznaj się z akcją wymaganą, gdy klucze zostaną ponownie wygenerowane. Wszystkie kolejne urządzenia zarejestrowane w ramach tej usługi będą korzystały z nowego klucza rejestracji. Kliknij przycisk **Wygeneruj** ponownie, aby potwierdzić. Po zakończeniu rejestracji otrzymasz powiadomienie.
   
   ![Potwierdź ponowne wygenerowanie klucza](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Zostanie wyświetlony nowy klucz rejestracji usługi.
   
    ![Dodatkowy klucz potwierdzenia ponownego wygenerowania](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Skopiuj ten klucz i Zapisz go, aby zarejestrować nowe urządzenia w tej usłudze.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak rozpocząć [pracę](storsimple-virtual-array-deploy1-portal-prep.md) z wirtualną tablicą StorSimple.
* Dowiedz się, jak [administrować urządzeniem StorSimple](storsimple-ova-web-ui-admin.md).