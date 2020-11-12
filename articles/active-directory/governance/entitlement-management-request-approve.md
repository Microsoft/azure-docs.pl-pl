---
title: Zatwierdzanie lub odrzucanie żądań dostępu — Zarządzanie prawami w usłudze Azure AD
description: Dowiedz się, jak zatwierdzić lub odrzucić żądania do pakietu dostępu przy użyciu portalu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
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
ms.openlocfilehash: dfbd5e6f190e0600c31aa17e9b1a75f08e213df4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537666"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Zatwierdzanie lub odrzucanie żądań dostępu w usłudze Azure AD uprawnienia do zarządzania

Korzystając z usługi Azure AD uprawnia do zarządzania, można skonfigurować zasady, aby wymagały zatwierdzenia pakietów dostępu, i wybrać co najmniej jedną osobę zatwierdzającą. W tym artykule opisano, jak Wyznaczeni osoby zatwierdzające mogą zatwierdzać lub odrzucać żądania dostępu do pakietów.

## <a name="open-request"></a>Otwórz żądanie

Pierwszym krokiem w celu zatwierdzenia lub odmowy żądań dostępu jest znalezienie i otwarcie żądania dostępu oczekujące na zatwierdzenie. Istnieją dwa sposoby otwierania żądania dostępu.

**Rola wymagana wstępnie:** Osoby zatwierdzającej

1. Poszukaj wiadomości e-mail z Microsoft Azure, która prosi o zatwierdzenie lub odrzucenie żądania. Oto przykład wiadomości e-mail:

    ![Zatwierdź żądanie uzyskania dostępu do poczty e-mail pakietu](./media/entitlement-management-shared/approver-request-email.png)

1. Kliknij link **Zatwierdź lub Odmów żądania** , aby otworzyć żądanie dostępu.

1. Zaloguj się do portalu My Access.

Jeśli nie masz wiadomości e-mail, możesz znaleźć żądania dostępu oczekujące na zatwierdzenie, wykonując następujące kroki.

1. Zaloguj się do portalu My Access pod adresem [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (W przypadku instytucji rządowych Stanów Zjednoczonych jest to domena w linku my Access Portal `myaccess.microsoft.us` ).

1. W menu po lewej stronie kliknij pozycję **zatwierdzenia** , aby wyświetlić listę żądań dostępu oczekujących na zatwierdzenie.

1. Na karcie **oczekiwanie** Znajdź żądanie.

## <a name="view-requestors-answers-to-questions-preview"></a>Wyświetl odpowiedzi na pytania żądającego (wersja zapoznawcza)

1. Przejdź do karty **zatwierdzenia** w polu mój dostęp.

1. Przejdź do żądania, które chcesz zatwierdzić, a następnie kliknij przycisk **szczegóły**. Możesz również kliknąć pozycję **Zatwierdź** lub **Odmów** , jeśli wszystko jest gotowe do podjęcia decyzji.

1. Kliknij pozycję **szczegóły żądania**.

     ![Portal dostępu — dostęp — szczegóły żądania](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. Informacje dostarczone przez zleceniodawcę będą znajdować się w dolnej części panelu.

     ![Zrzut ekranu przedstawia szczegóły żądania](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Na podstawie informacji podanej przez zleceniodawcę możesz zatwierdzić lub odrzucić żądanie. Zapoznaj się z instrukcjami zawartymi w temacie zatwierdzanie lub odrzucanie żądań wskazówek.

## <a name="approve-or-deny-request"></a>Zatwierdź lub Odmów żądania

Po otwarciu żądania dostępu oczekującego na zatwierdzenie możesz zobaczyć szczegóły, które ułatwią zatwierdzenie lub odrzucenie decyzji.

**Rola wymagana wstępnie:** Osoby zatwierdzającej

1. Kliknij link **Wyświetl** , aby otworzyć okienko żądanie dostępu.

1. Kliknij przycisk **szczegóły** , aby wyświetlić szczegółowe informacje o żądaniu dostępu.

    Szczegóły obejmują imię i nazwisko użytkownika, organizację, datę rozpoczęcia dostępu i daty zakończenia, jeśli zostały podane, uzasadnienie biznesowe, czas przesyłania żądania oraz czas wygaśnięcia żądania.

1. Kliknij przycisk **Zatwierdź** lub **Odmów**.

1. W razie potrzeby wprowadź przyczynę.

    ![Zrzut ekranu przedstawia stronę, na której jest akceptowane lub odrzucane żądanie.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Kliknij pozycję **Prześlij** , aby przesłać swoją decyzję.

    Jeśli zasady są skonfigurowane z wieloma osobami zatwierdzającymi, tylko jedna osoba zatwierdzająca musi podejmować decyzje dotyczące oczekujących zatwierdzeń. Gdy osoba zatwierdzająca przesłała swoją decyzję do żądania dostępu, żądanie zostanie zakończone i nie jest już dostępne dla innych osób zatwierdzających do zatwierdzenia lub odrzucenia żądania. Inne osoby zatwierdzające mogą zobaczyć decyzję o żądaniu i twórcę decyzji w portalu mojego dostępu. W tej chwili obsługiwane jest tylko zatwierdzanie jednoetapowe.

    Jeśli żadna ze skonfigurowanych osób zatwierdzających nie może zatwierdzić lub odrzucić żądania dostępu, żądanie wygasa po upływie skonfigurowanego czasu trwania żądania. Użytkownik otrzymuje powiadomienie o wygaśnięciu żądania dostępu i konieczności ponownego przesłania żądania dostępu.

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
