---
title: Jak skonfigurować przypisanie aplikacji samoobsługi | Microsoft Docs
description: Włącz dostęp do aplikacji samoobsługi, aby umożliwić użytkownikom znajdowanie własnych aplikacji
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260218"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Jak skonfigurować przypisanie aplikacji samoobsługi

Zanim użytkownicy będą mogli samodzielnie wykrywać aplikacje ze swoich aplikacji, musisz włączyć **samoobsługowy dostęp do aplikacji** do wszystkich aplikacji, które mają pozwolić użytkownikom na samodzielne odnajdowanie i zażądać dostępu do programu. Ta funkcja jest dostępna dla aplikacji, które zostały dodane z [galerii usługi Azure AD](./add-application-portal.md), [platformy Azure serwer proxy aplikacji usługi Azure AD](./application-proxy.md) lub zostały dodane za pośrednictwem [zgody użytkownika lub administratora](../develop/application-consent-experience.md). 

Ta funkcja to świetny sposób oszczędzania czasu i pieniędzy jako grupy IT i jest wysoce zalecany w ramach wdrożenia nowoczesnych aplikacji z Azure Active Directory.

Przy użyciu tej funkcji można:

-   Zezwól użytkownikom na samoobsługowe odnajdywanie aplikacji z [moich aplikacji](https://myapps.microsoft.com/) bez BOTHERING grupy IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi do nich rolami.

-   Opcjonalnie można zezwolić podmiotowi gospodarczemu na zatwierdzanie żądań dostępu do aplikacji, aby grupa IT nie miała do niej uprawnień.

-   Opcjonalnie można skonfigurować maksymalnie 10 osób, które mogą zatwierdzać dostęp do tej aplikacji.

-   Opcjonalnie Zezwól podmiotowi gospodarczemu na ustawienie haseł, których mogą używać użytkownicy do logowania się do aplikacji, bezpośrednio z [aplikacji](https://myapps.microsoft.com/)osoby zatwierdzające w firmie.

-   Opcjonalnie można automatycznie przypisywać użytkownikom samoobsługi bezpośrednio przypisane do roli aplikacji.

> [!NOTE]
> Licencja na Azure Active Directory — wersja Premium (P1 lub P2) jest wymagana dla użytkowników, którzy zażądają dołączenia do aplikacji samoobsługowej i dla właścicieli, aby zatwierdzić lub odrzucić żądania. Bez licencji Azure Active Directory — wersja Premium użytkownicy nie mogą dodawać aplikacji samoobsługi.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji samoobsługi, aby umożliwić użytkownikom znajdowanie własnych aplikacji

Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji i opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. W przypadku logowania jednokrotnego hasła do aplikacji można również zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników z własnych paneli dostępu moje aplikacje.

Aby włączyć samoobsługowy dostęp do aplikacji, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny.

2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw**.

3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij pisać jej nazwę w polu wyszukiwania. Lub Użyj kontrolek filtr, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybierz pozycję **Zastosuj**.

4. W menu nawigacji po lewej stronie wybierz pozycję **samoobsługowe.**

5. Aby włączyć samoobsługowy dostęp do aplikacji dla tej aplikacji, Włącz opcję **zezwól użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz na **wartość tak.**

6. Obok **grupy, do której należy dodać użytkowników**, kliknij pozycję **Wybierz grupę**. Wybierz grupę, a następnie kliknij pozycję **Wybierz**. Gdy żądanie użytkownika zostanie zatwierdzone, zostaną dodane do tej grupy. Podczas przeglądania członkostwa w tej grupie będzie można zobaczyć, kto uzyskał dostęp do aplikacji za pomocą samoobsługowego dostępu.
  
    > [!NOTE]
    > To ustawienie nie obsługuje grup zsynchronizowanych z lokalnego.

7. **Opcjonalne:** Aby wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mogli uzyskać dostęp, ustaw opcję **Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?** Przełącz na **wartość tak**.

8. **Opcjonalne: w przypadku aplikacji korzystających tylko z logowania** jednokrotnego, aby zezwolić podmiotom gospodarczym na określanie haseł wysyłanych do tej aplikacji dla zatwierdzonych użytkowników, ustaw opcję **Zezwalaj na osoby zatwierdzające na ustawienie haseł użytkownika dla tej aplikacji?** Przełącz na wartość **tak**.

9. **Opcjonalne:** Aby określić osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji, obok **osoby, która może zatwierdzić dostęp do tej aplikacji?**, kliknij przycisk **Wybierz osoby zatwierdzające**, a następnie wybierz maksymalnie 10 osób zatwierdzających w biznesie. Następnie kliknij pozycję **Wybierz**.

    >[!NOTE]
    >Grupy nie są obsługiwane. Można wybrać maksymalnie 10 osób zatwierdzających w biznesie. Jeśli określisz wiele osób zatwierdzających, każda osoba zatwierdzająca może zatwierdzić żądanie dostępu.

10. **Opcjonalne:** **w przypadku aplikacji, które uwidaczniają role**, do przypisywania użytkowników z zatwierdzaniem samoobsługi do roli, obok roli, **do której mają być przypisani użytkownicy w tej aplikacji?**, kliknij pozycję **Wybierz rolę**, a następnie wybierz rolę, do której mają być przypisani użytkownicy. Następnie kliknij pozycję **Wybierz**.

11. Kliknij przycisk **Zapisz** w górnej części okienka, aby zakończyć.

Po zakończeniu konfigurowania aplikacji samoobsługi użytkownicy mogą przechodzić do swoich [aplikacji](https://myapps.microsoft.com/) , a następnie kliknąć przycisk **Dodaj aplikacje** samoobsługowe, aby znaleźć aplikacje, które są włączone z dostępem samoobsługowym. Osoby zatwierdzające firmy widzą również powiadomienie w swoich [aplikacjach](https://myapps.microsoft.com/). Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażądał dostępu do aplikacji, która wymaga zatwierdzenia.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../enterprise-users/groups-self-service-management.md)