---
title: Jak używać samoobsługowego dostępu do aplikacji w usłudze Azure AD
description: Włączanie samoobsługi w celu umożliwienia użytkownikom znalezienia aplikacji w usłudze Azure AD
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377619"
---
# <a name="how-to-use-self-service-application-access"></a>Jak używać samoobsługowego dostępu do aplikacji

Zanim użytkownicy będą w stanie samodzielnie odnajdywać aplikacje ze  strony usługi Moje aplikacje, należy włączyć dostęp samoobsługowej aplikacji do wszystkich aplikacji, do których chcesz zezwolić użytkownikom na samodzielne odnajdywanie i żądanie dostępu.

Ta funkcja to doskonały sposób na zaoszczędowanie czasu i pieniędzy jako grupa IT. Jest ona zdecydowanie zalecana w ramach nowoczesnego wdrażania aplikacji z Azure Active Directory.

Aby dowiedzieć się więcej Moje aplikacje z perspektywy użytkownika końcowego, zobacz [Moje aplikacje pomocy portalu.](../user-help/my-apps-portal-end-user-access.md)

Przy użyciu tej funkcji można:

-   Pozwól użytkownikom samodzielnie odnajdywać aplikacje [Moje aplikacje](https://myapps.microsoft.com/) bez przeszkadzania grupie IT.
-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi do nich rolami.
-   Opcjonalnie możesz zezwolić innej osobie na zatwierdzanie żądań dostępu do aplikacji, aby grupa IT nie wymagała tego.
-   Opcjonalnie skonfiguruj maksymalnie 10 osób, które mogą zatwierdzić dostęp do tej aplikacji.
-   Opcjonalnie możesz zezwolić innej osobie na ustawienie haseł, których użytkownicy mogą używać do logowania się do aplikacji.
-   Opcjonalnie możesz automatycznie przypisywać użytkowników przypisanych do samoobsługi bezpośrednio do roli aplikacji.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włączanie dostępu do aplikacji samoobsługowej w celu umożliwienia użytkownikom znalezienia własnych aplikacji

Dostęp do aplikacji samoobsługowej to doskonały sposób, aby umożliwić użytkownikom samodzielne odnajdywanie aplikacji. Opcjonalnie można zezwolić grupie biznesowej na zatwierdzenie dostępu do tych aplikacji. Możesz zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników na potrzeby zarządzania hasłami Single-Sign w aplikacjach bezpośrednio ze Moje aplikacje aplikacji.

Aby włączyć dostęp aplikacji samoobsługowej do aplikacji, wykonaj poniższe kroki:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2. Otwórz rozszerzenie **Azure Active Directory,** wybierając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.
3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.
4. Wybierz pozycję **Aplikacje dla przedsiębiorstw** z menu nawigacji usługi Azure Active Directory po lewej stronie.
5. Wybierz pozycję **Wszystkie aplikacje**, aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz tutaj aplikacji, którą chcesz  wyświetlić, użyj kontrolki  Filtr w górnej części listy Wszystkie aplikacje i ustaw opcję Pokaż na wartość **Wszystkie aplikacje.** 
6. Wybierz z listy aplikację, do której chcesz włączyć dostęp samoobsługowy.
7. Po zakończeniu ładowania aplikacji wybierz **pozycję Samoobsługa** z menu nawigacji po lewej stronie aplikacji.
8. Aby włączyć dostęp do aplikacji samoobsługowej dla tej aplikacji, przełącz przełącznik Zezwalaj użytkownikom na żądanie dostępu do tej **aplikacji?** na **tak.**
9. Następnie, aby wybrać grupę, do której mają zostać dodani użytkownicy, którzy żądają dostępu do tej aplikacji, wybierz selektor obok etykiety Do której grupy powinni zostać dodani **użytkownicy?** i wybierz grupę.
10. **Opcjonalnie:** Jeśli chcesz wymagać zatwierdzenia biznesowego przed zezwoleniem użytkownikom na dostęp, ustaw przełącznik Wymagaj zatwierdzenia przed udzieleniem dostępu do **tej aplikacji?** na **wartość Tak.**
11. **Opcjonalnie:** jeśli chcesz zezwolić tym osobom zatwierdzającym na określenie haseł wysyłanych do tej aplikacji dla zatwierdzonych użytkowników, ustaw przełącznik Zezwalaj osobom zatwierdzającym na ustawianie haseł użytkowników dla tej **aplikacji?** na wartość **Tak.**
12. **Opcjonalnie:** Określ osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji. Wybierz **pozycję Kto może zatwierdzać dostęp do tej aplikacji?** Następnie wybierz maksymalnie 10 osób zatwierdzających w firmie.
    * Grupy nie są obsługiwane.
13. **Opcjonalnie:** w przypadku aplikacji, które uwidoczniają **role,** jeśli chcesz przypisać użytkowników zatwierdzonych do samoobsługi do roli, wybierz selektor obok roli Do której roli powinni być przypisani użytkownicy w tej **aplikacji?,** aby wybrać rolę, do której ci użytkownicy powinni być przypisani.
14. Wybierz przycisk **Zapisz** u góry, aby zakończyć.

Po ukończeniu konfiguracji aplikacji samoobsługowej użytkownicy mogą przejść do usługi [Moje aplikacje](https://myapps.microsoft.com/) i wybrać przycisk **+Dodaj,** aby znaleźć aplikacje, dla których włączono dostęp samoobsługowy. Osoby zatwierdzające w firmie widzą również powiadomienie na [Moje aplikacje](https://myapps.microsoft.com/) stronie. Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażąda dostępu do aplikacji wymagającej zatwierdzenia. 

Te zatwierdzenia obsługują tylko przepływy pracy zatwierdzania pojedynczego, co oznacza, że w przypadku określenia wielu osób zatwierdzających każda jedna osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="things-to-check-if-self-service-isnt-working"></a>Co należy sprawdzić, jeśli samoobsługa nie działa
-   Upewnij się, że użytkownik lub grupa została włączona, aby zażądać dostępu do aplikacji samoobsługowej.
-   Upewnij się, że użytkownik odwiedza odpowiednie miejsce w celu uzyskania dostępu do aplikacji samoobsługowej. Użytkownicy mogą przejść do swojej [Moje aplikacje](https://myapps.microsoft.com/) i wybrać przycisk **+Dodaj,** aby znaleźć aplikacje, dla których włączono dostęp samoobsługowy.
-   Jeśli dostęp do aplikacji samoobsługowej został niedawno skonfigurowany, spróbuj zalogować się i wylogować ponownie w chmurze użytkownika Moje aplikacje kilka minut, aby sprawdzić, czy pojawiły się zmiany dostępu samoobsługowego.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../enterprise-users/groups-self-service-management.md)
