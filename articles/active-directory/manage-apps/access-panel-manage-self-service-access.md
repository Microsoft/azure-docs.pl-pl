---
title: Jak używać samoobsługowego dostępu do aplikacji w usłudze Azure AD
description: Włącz samoobsługowe, aby użytkownicy mogli znaleźć aplikacje w usłudze Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 13f91fdd9e2d9501fba426bd6facbf9824a39285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257021"
---
# <a name="how-to-use-self-service-application-access"></a>Jak korzystać z samoobsługowego dostępu do aplikacji

Zanim użytkownicy będą mogli samodzielnie wykrywać aplikacje ze strony Moje aplikacje, musisz włączyć **samoobsługowy dostęp do aplikacji** do wszystkich aplikacji, które mają pozwolić użytkownikom na samodzielne odnajdowanie i zażądać dostępu do programu.

Ta funkcja to świetny sposób oszczędzania czasu i pieniędzy jako grupy IT i jest wysoce zalecany w ramach wdrożenia nowoczesnych aplikacji z Azure Active Directory.

Aby dowiedzieć się więcej o używaniu aplikacji z perspektywy użytkowników końcowych, zobacz [Moje aplikacje — pomoc](../user-help/my-apps-portal-end-user-access.md).

Przy użyciu tej funkcji można:

-   Zezwól użytkownikom na samodzielne odkrywanie aplikacji z [moich aplikacji](https://myapps.microsoft.com/) bez BOTHERING grupy IT.
-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi do nich rolami.
-   Opcjonalnie Zezwól komuś na zatwierdzanie żądań dostępu do aplikacji, aby grupa IT nie miała do niej uprawnień.
-   Opcjonalnie można skonfigurować maksymalnie 10 osób, które mogą zatwierdzać dostęp do tej aplikacji.
-   Opcjonalnie Zezwól komuś na ustawienie haseł, których użytkownicy mogą używać do logowania się do aplikacji.
-   Opcjonalnie można automatycznie przypisywać użytkownikom samoobsługi bezpośrednio przypisane do roli aplikacji.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji samoobsługi, aby umożliwić użytkownikom znajdowanie własnych aplikacji

Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji, opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Możesz zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników, aby hasła Single-Sign w aplikacjach bezpośrednio ze strony Moje aplikacje.

Aby włączyć samoobsługowy dostęp do aplikacji, wykonaj następujące czynności:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4. Wybierz pozycję **Aplikacje dla przedsiębiorstw** z menu nawigacji usługi Azure Active Directory po lewej stronie.
5. Wybierz pozycję **Wszystkie aplikacje**, aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
6. Wybierz aplikację, z której chcesz włączyć dostęp do funkcji samoobsługi z listy.
7. Po załadowaniu aplikacji wybierz pozycję **samoobsługa** z menu nawigacji po lewej stronie aplikacji.
8. Aby włączyć samoobsługowy dostęp do aplikacji dla tej aplikacji, Włącz opcję **zezwól użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz na **wartość tak.**
9. Aby wybrać grupę, do której mają zostać dodane użytkownicy żądający dostępu do tej aplikacji, wybierz selektor obok etykiety, **do której Grupa powinna zostać dodana** , a następnie wybierz grupę.
10. **Opcjonalne:** Jeśli chcesz wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mieć dostęp, ustaw opcję **Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?** Przełącz na **wartość tak**.
11. **Opcjonalne: w przypadku aplikacji korzystających tylko z logowania** jednokrotnego, jeśli chcesz zezwolić tym osobom, aby osoby zatwierdzające firmy określiły hasła wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustaw opcję **Zezwalaj na osoby zatwierdzające, aby ustawić hasła użytkownika dla tej aplikacji?** Przełącz na **wartość tak**.
12. **Opcjonalne:** Określ osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji. Wybrać **, kto może zatwierdzać dostęp do tej aplikacji?** Następnie wybierz maksymalnie 10 osób zatwierdzających w biznesie.
    * Grupy nie są obsługiwane.
13. **Opcjonalne:** **w przypadku aplikacji, które uwidaczniają role**, aby przypisać użytkowników, którzy mają zostać przypisani do roli, wybierz selektor obok elementu, **do którego rola powinna być przypisana do tej aplikacji?** , aby wybrać rolę, do której mają być przypisani użytkownicy.
14. Wybierz przycisk **Zapisz** znajdujący się u góry, aby zakończyć.

Po zakończeniu konfiguracji aplikacji samoobsługi użytkownicy mogą przejść do obszaru [Moje aplikacje](https://myapps.microsoft.com/) i wybrać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono samoobsługowy dostęp. Osoby zatwierdzające firmy widzą również powiadomienie na stronie [Moje aplikacje](https://myapps.microsoft.com/) . Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażądał dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługują tylko jednorazowe przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, każda osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="things-to-check-if-self-service-isnt-working"></a>Elementy do sprawdzenia, czy funkcja samoobsługi nie działa
-   Upewnij się, że użytkownik lub Grupa została włączona, aby zażądać dostępu do aplikacji samoobsługowej.
-   Upewnij się, że użytkownik jest w odpowiednim miejscu do samoobsługowego dostępu do aplikacji. Użytkownicy mogą przechodzić do strony [Moje aplikacje](https://myapps.microsoft.com/) i wybierać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono samoobsługowy dostęp.
-   Jeśli dostęp do aplikacji samoobsługi został niedawno skonfigurowany, spróbuj zalogować się i ponownie w moich aplikacjach użytkownika po kilku minutach, aby sprawdzić, czy pojawiły się zmiany dostępu samoobsługi.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../enterprise-users/groups-self-service-management.md)
