---
title: Wdróż StorSimple wirtualną macierzy dla programu Cloud Solution Provider
description: Dowiedz się, w jaki sposób partner CSP może dodać klienta lub nową subskrypcję do istniejącego klienta, a następnie utworzyć usługę do wdrożenia macierzy wirtualnej StorSimple w dostawcy usług kryptograficznych.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021864"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Wdróż StorSimple wirtualną macierzy dla programu Cloud Solution Provider

## <a name="overview"></a>Omówienie

StorSimple wirtualną macierzy można wdrożyć przez partnerów dostawcy rozwiązań w chmurze (CSP) dla swoich klientów. Partner programu CSP może utworzyć usługę StorSimple Device Manager. Tej usługi można następnie użyć do wdrożenia i zarządzania macierzą wirtualną StorSimple oraz skojarzonymi udziałami, woluminami i kopiami zapasowymi.

W tym artykule opisano, jak partner programu CSP może dodać klienta lub nową subskrypcję do istniejącego klienta, a następnie utworzyć usługę do wdrożenia macierzy wirtualnej StorSimple w dostawcy usług kryptograficznych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Zarejestrowano Cię w ramach programu CSP.
- Masz prawidłowe poświadczenia logowania [Centrum partnerskiego](https://partnercenter.microsoft.com/) . Poświadczenia umożliwiają zalogowanie się do portalu dla partnerów w celu dodania nowych klientów, wyszukania klientów lub przechodzenia do konta klienta z poziomu pulpitu nawigacyjnego partnera. Dostawca usług kryptograficznych może działać jako administrator StorSimple w imieniu klienta w Azure Portal.
                             
## <a name="add-a-customer"></a>Dodawanie klienta

Dodanie klienta powoduje automatyczne utworzenie subskrypcji. Aby dodać klienta (i automatycznie utworzyć subskrypcję), wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń dostawcy CSP. Kliknij pozycję **pulpit nawigacyjny**.

     ![Pulpit nawigacyjny w centrum partnerskim](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W okienku po lewej stronie kliknij pozycję **Customers**. W okienku po prawej stronie kliknij pozycję **Dodaj klientów**. Wprowadź szczegóły klienta. Kliknij przycisk **Dalej: subskrypcje** , aby utworzyć subskrypcję klienta.

    ![Dodaj klienta](./media/storsimple-partner-csp-deploy/image2.png)

3.  Wybierz ofertę **Microsoft Azure** . Przewiń w dół strony i kliknij przycisk **Przegląd**.

    ![Przejrzyj informacje o subskrypcji](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Przejrzyj informacje, a następnie kliknij pozycję **Prześlij**.

    ![Prześlij subskrypcję](./media/storsimple-partner-csp-deploy/image4.png)

5. Zapisz informacje o potwierdzeniu do użytku w przyszłości.

    ![Potwierdzenie zapisu](./media/storsimple-partner-csp-deploy/image5.png)

6. Znajdź lub przejdź do właśnie dodanego klienta. Kliknij **nazwę firmy** , aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W okienku po lewej stronie wybierz pozycję **Zarządzanie usługami**. W okienku po prawej stronie w obszarze **Administruj usługami** kliknij pozycję **Portal zarządzania Microsoft Azure** , aby zalogować się jako administrator platformy Azure dla klienta.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć StorSimple Device Manager, kliknij przycisk **+ Nowy** i Wyszukaj lub przejdź do **serii urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrożenia usługi StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Tworzenie usługi StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Dodawanie subskrypcji

W niektórych przypadkach użytkownik może mieć istniejącego klienta i musi dodać subskrypcję. Aby dodać subskrypcję do istniejącego klienta, wykonaj następujące kroki w portalu dla partnerów.

1. Przejdź do [Centrum partnerskiego](https://partnercenter.microsoft.com/) i zaloguj się przy użyciu poświadczeń dostawcy CSP. Kliknij pozycję **pulpit nawigacyjny**.

     ![Pulpit nawigacyjny w centrum partnerskim](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. W okienku po lewej stronie kliknij pozycję **Customers**. Znajdź lub przejdź do klienta, do którego chcesz dodać subskrypcję. Kliknij ![ ikonę rozwiń ikonę, ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) Aby rozwinąć wiersz nazwy firmy dla klienta. W obszarze Szczegóły kliknij pozycję **Dodaj subskrypcje**.

    ![Klienci](./media/storsimple-partner-csp-deploy/image10.png)

3. Sprawdź **Microsoft Azure** **najważniejszych ofert** w subskrypcji i kliknij pozycję **Prześlij**. Spowoduje to utworzenie nowej subskrypcji.

    ![Dodaj nową subskrypcję](./media/storsimple-partner-csp-deploy/image11.png)

6. Po utworzeniu nowej subskrypcji kliknij pozycję **< — klienci** w okienku po lewej stronie, aby powrócić do strony **klienci** . Wyszukaj klienta, dla którego właśnie utworzono subskrypcję. Kliknij **nazwę firmy** , aby przejść do szczegółów.

    ![Wyszukaj klienta](./media/storsimple-partner-csp-deploy/image6.png)  

7. W okienku po lewej stronie wybierz pozycję **Zarządzanie usługami**. W okienku po prawej stronie w obszarze **Administruj usługami** kliknij pozycję **Portal zarządzania Microsoft Azure** , aby zalogować się jako administrator platformy Azure dla klienta.

    ![Logowanie do witryny Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Aby utworzyć StorSimple Device Manager, kliknij przycisk **+ Nowy** i Wyszukaj lub przejdź do **serii urządzeń wirtualnych StorSimple**. Aby uzyskać więcej informacji, przejdź do [wdrożenia usługi StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Tworzenie usługi StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli masz więcej pytań dotyczących StorSimple w dostawcy CSP, przejdź do [StorSimple w programie CSP: często zadawane pytania](storsimple-partner-csp-faq.md).
- Jeśli wszystko jest gotowe do wdrożenia StorSimple, przejdź do [wdrożenia StorSimple w dostawcy usług kryptograficznych](storsimple-partner-csp-deploy.md).
