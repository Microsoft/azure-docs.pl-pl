---
title: Żądanie pakietu dostępu — Zarządzanie prawami usługi Azure AD
description: Dowiedz się, jak za pomocą portalu dostępu uzyskiwać żądania dostępu do pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996648"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Zażądaj dostępu do pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania

Dzięki zarządzaniu prawami usługi Azure AD pakiet dostępu umożliwia jednorazowe skonfigurowanie zasobów i zasad, które automatycznie zarządzają dostępem w okresie istnienia pakietu dostępu. 

Menedżer pakietów dostępu może skonfigurować zasady, aby wymagać zatwierdzenia użytkownikom dostępu do pakietów dostępu. Użytkownik wymagający dostępu do pakietu dostępu może przesłać żądanie uzyskania dostępu. W tym artykule opisano, jak przesłać żądanie dostępu.

## <a name="sign-in-to-the-my-access-portal"></a>Logowanie się do portalu My Access

Pierwszym krokiem jest zalogowanie się w portalu do mojego dostępu, w którym można zażądać dostępu do pakietu dostępu.

**Rola wymagana wstępnie:** Obiektu żądającego

1. Poszukaj wiadomości e-mail lub wiadomości z programu Project lub Business Manager, z którym pracujesz. Wiadomość e-mail powinna zawierać link do pakietu dostępu, do którego będzie potrzebny dostęp. Łącze rozpoczyna się od `myaccess` , zawiera wskazówkę katalogu i kończą się identyfikatorem pakietu dostępu.  (W przypadku instytucji rządowych USA domena może być w `https://myaccess.microsoft.us` zamian).
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otwórz link.

1. Zaloguj się do portalu My Access.

    Upewnij się, że używasz konta organizacji (służbowego). Jeśli nie masz pewności, skontaktuj się z projektem lub menedżerem biznesowym.

## <a name="request-an-access-package"></a>Żądaj pakietu dostępu

Po znalezieniu pakietu dostępu w portalu dostępu można przesłać żądanie.

**Rola wymagana wstępnie:** Obiektu żądającego

1. Znajdź na liście pakiet dostępu.  W razie potrzeby można wyszukać, wpisując ciąg wyszukiwania, a następnie wybierając pozycję **Nazwa**, **wykaz** lub filtr **zasobów** .

    ![Portal dostępu — Wyszukiwanie zasobów](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

1. Kliknij pozycję **Zażądaj dostępu** , aby otworzyć okienko żądania dostępu.

    ![Portal dostępu — dostęp — pakiety](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Jeśli zostanie wyświetlone pole **uzasadnienie biznesowe** , wpisz uzasadnienie dostępu.

1. Jeśli jest włączone **żądanie dla określonego okresu?** , wybierz opcję **tak** lub **nie**.

1. W razie potrzeby określ datę początkową i datę końcową.

    ![Portal dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Po zakończeniu kliknij pozycję **Prześlij** , aby przesłać żądanie.

1. Kliknij pozycję **historia żądań** , aby wyświetlić listę żądań i stan.

    Jeśli pakiet dostępu wymaga zatwierdzenia, żądanie jest teraz w stanie oczekiwania na zatwierdzenie.

### <a name="select-a-policy"></a>Wybieranie zasad

Jeśli zażądano dostępu do pakietu dostępu z wieloma zasadami, które mają zastosowanie, może zostać wyświetlony monit o wybranie zasad. Na przykład Menedżer pakietów dostępu może skonfigurować pakiet dostępu z dwiema zasadami dla dwóch grup pracowników wewnętrznych. Pierwsze zasady mogą zezwalać na dostęp przez 60 dni i wymagać zatwierdzenia. Drugie zasady mogą zezwalać na dostęp przez 2 dni i nie wymagać zatwierdzania. Jeśli wystąpi ten scenariusz, należy wybrać zasady, które mają być używane.

![Portal dostępu — dostęp do żądania — wiele zasad](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Wypełnij informacje o żądającym

Użytkownik może zażądać dostępu do pakietu dostępu, który wymaga uzasadnienia biznesowego i dodatkowych informacji o żądającym, zanim udzieli dostępu do pakietu dostępu. Wypełnij wszystkie informacje o żądającym wymagane do uzyskania dostępu do pakietu dostępu.

![Portal dostępu — żądanie dostępu — wprowadź informacje o żądaniu](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Prześlij ponownie żądanie

Żądanie dostępu do pakietu dostępu może być odrzucone lub żądanie może wygasnąć, jeśli osoby zatwierdzające nie odpowiadają na czas. Jeśli potrzebujesz dostępu, możesz ponowić próbę i ponownie przesłać żądanie. Poniższa procedura wyjaśnia, jak ponownie przesłać żądanie dostępu:

**Rola wymagana wstępnie:** Obiektu żądającego

1. Zaloguj się do portalu **My Access** .

1. Kliknij pozycję **historia żądań** w menu nawigacji po lewej stronie.

1. Znajdź pakiet dostępu, dla którego chcesz ponownie przesłać żądanie.

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

1. Kliknij link Blue **View** z prawej strony wybranego pakietu dostępu.
    
    ![Wybierz pakiet dostępu i link do widoku](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Zostanie otwarte okienko po prawej stronie z historią żądań dla pakietu dostępu.
    
    ![Wybierz przycisk Prześlij ponownie](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Kliknij przycisk **Prześlij ponownie** w dolnej części okienka.

## <a name="cancel-a-request"></a>Anuluj żądanie

W przypadku przesyłania żądania dostępu, gdy żądanie jest nadal w stanie **oczekiwania na zatwierdzenie** , można anulować żądanie.

**Rola wymagana wstępnie:** Obiektu żądającego

1. W portalu mój dostęp po lewej stronie kliknij pozycję **historia żądań** , aby wyświetlić listę żądań i stan.

1. Kliknij link **Wyświetl** dla żądania, które chcesz anulować.

1. Jeśli żądanie jest nadal w stanie **oczekiwania na zatwierdzenie** , możesz kliknąć przycisk **Anuluj żądanie** , aby anulować żądanie.

    ![Portal dostępu — Anuluj żądanie](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknij pozycję **historia żądań** , aby potwierdzić, że żądanie zostało anulowane.

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
