---
title: Jak skonfigurować przypisanie aplikacji samoobsługi | Dokumenty firmy Microsoft
description: Umożliwianie dostępu do aplikacji samoobsługowych w celu umożliwienia użytkownikom znajdowania własnych aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e09fd63ee6121ac9bf7f3c2be00f0ac22f752f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731710"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Jak skonfigurować przypisanie aplikacji samoobsługowej

Zanim użytkownicy będą mogli samodzielnie wykrywać aplikacje z panelu dostępu Moje aplikacje, należy włączyć **samoobsługowy dostęp aplikacji** do wszystkich aplikacji, do których chcesz zezwolić użytkownikom na samodzielne wykrywanie i żądanie dostępu. Ta funkcja jest dostępna dla aplikacji, które zostały dodane z [usługi Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), usługi Azure AD Application [Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) lub zostały dodane za zgodą użytkownika [lub administratora.](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) 

Ta funkcja to doskonały sposób na zaoszczędzenie czasu i pieniędzy jako grupa IT i jest wysoce zalecana w ramach wdrażania nowoczesnych aplikacji za pomocą usługi Azure Active Directory.

Przy użyciu tej funkcji można:

-   Pozwól użytkownikom samodzielnie wykrywać aplikacje z [panelu dostępu Moje aplikacje](https://myapps.microsoft.com/) bez przeszkadzania grupie IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowaną grupę, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi im rolami.

-   Opcjonalnie zezwalaj osobom zatwierdzające firmy na zatwierdzanie żądań dostępu do aplikacji, aby grupa IT nie musiała tego robić.

-   Opcjonalnie skonfiguruj do 10 osób, które mogą zatwierdzić dostęp do tej aplikacji.

-   Opcjonalnie zezwól osobom zatwierdzacym na ustawianie haseł, których użytkownicy mogą używać do logowania się do aplikacji, bezpośrednio z [panelu dostępu do aplikacji](https://myapps.microsoft.com/)osoby zatwierdzającej.

-   Opcjonalnie automatycznie przypisz bezpośrednio przypisanych użytkowników samoobsługowych do roli aplikacji.

> [!NOTE]
> Licencja usługi Azure Active Directory Premium (P1 lub P2) jest wymagana dla użytkowników, aby zażądać dołączenia do aplikacji samoobsługowej i dla właścicieli, aby zatwierdzić lub odrzucić żądania. Bez licencji usługi Azure Active Directory Premium użytkownicy nie mogą dodawać aplikacji samoobsługowych.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Umożliwianie dostępu do aplikacji samoobsługowych w celu umożliwienia użytkownikom znajdowania własnych aplikacji

Dostęp do samoobsługi aplikacji to świetny sposób, aby umożliwić użytkownikom samodzielne wykrywanie aplikacji i opcjonalnie zezwolić grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. W przypadku logowania jednokrotnego hasła w aplikacjach można również zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników z ich własnych paneli dostępu Moje aplikacje.

Aby włączyć samoobsługowy dostęp aplikacji do aplikacji, wykonaj poniższe czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny.

2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **Aplikacje przedsiębiorstwa**.

3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij wpisywać jej nazwę w polu wyszukiwania. Możesz też użyć kontrolek filtru, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybrać **pozycję Zastosuj**.

4. W menu nawigacji po lewej stronie wybierz pozycję **Samoobsługa**.

5. Aby włączyć dostęp do aplikacji samoobsługi dla tej aplikacji, należy włączyć **Yes.** **zezwalaj użytkownikom na żądanie dostępu do tej aplikacji?**

6. Obok **do grupy, do której należy dodać przypisanych użytkowników?**, kliknij pozycję **Wybierz grupę**. Wybierz grupę, a następnie kliknij przycisk **Wybierz**. Po zatwierdzeniu żądania użytkownika zostanie on dodany do tej grupy. Podczas przeglądania członkostwa tej grupy, będziesz mógł zobaczyć, kto otrzymał dostęp do aplikacji za pośrednictwem dostępu samoobsługowego.
  
    > [!NOTE]
    > To ustawienie nie obsługuje grup zsynchronizowanych z lokalnego środowiska.

7. **Opcjonalnie:** Aby wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mogli uzyskać dostęp, ustaw **Yes** **opcję Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?**

8. **Opcjonalnie: W przypadku aplikacji używających tylko logowania jednokrotnego hasła,** aby umożliwić osobom zatwierdzających działalność określanie haseł wysyłanych do tej aplikacji dla **Yes**zatwierdzonych użytkowników, należy ustawić opcję Zezwalaj osobom zatwierdzające na **ustawianie haseł użytkownika dla tej aplikacji?**

9. **Opcjonalnie:** Aby określić osoby zatwierdzające firmy, które mogą zatwierdzać dostęp do tej aplikacji, obok pozycji **Select approvers** **Kto może zatwierdzić dostęp do tej aplikacji?** Następnie kliknij pozycję **Wybierz**.

    >[!NOTE]
    >Grupy nie są obsługiwane. Możesz wybrać maksymalnie 10 osób zatwierdzających firmy. Jeśli określisz wiele osób zatwierdzających, każda pojedyncza osoba zatwierdzająca może zatwierdzić żądanie dostępu.

10. **Opcjonalnie:** **W przypadku aplikacji, które udostępniają role**, aby przypisać do roli zatwierdzonych użytkowników samoobsługowych, obok roli Do której **użytkownicy powinni być przypisani w tej aplikacji?** **Select Role** Następnie kliknij pozycję **Wybierz**.

11. Kliknij przycisk **Zapisz** u góry okienka, aby zakończyć.

Po zakończeniu konfiguracji aplikacji samoobsługowej użytkownicy mogą przejść do [panelu dostępu Moje aplikacje](https://myapps.microsoft.com/) i kliknąć przycisk **Dodaj aplikacje samoobsługowe,** aby znaleźć aplikacje, które są włączane z dostępem samoobsługowym. Osoby zatwierdzające firmy widzą również powiadomienie w [panelu dostępu Moje aplikacje](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażąda dostępu do aplikacji, która wymaga ich zatwierdzenia.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../users-groups-roles/groups-self-service-management.md)
