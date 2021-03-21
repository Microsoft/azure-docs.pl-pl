---
title: Utwórz & usunąć Azure AD B2C konta użytkowników w Azure Portal
description: Dowiedz się, jak używać Azure Portal do tworzenia i usuwania użytkowników w katalogu Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033682"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Użyj Azure Portal, aby tworzyć i usuwać użytkowników w programie Azure AD B2C

Mogą istnieć scenariusze, w których chcesz ręcznie utworzyć konta konsumentów w katalogu Azure Active Directory B2C (Azure AD B2C). Mimo że konta konsumentów w katalogu Azure AD B2C są najczęściej tworzone, gdy użytkownicy logują się w celu korzystania z jednej z aplikacji, można je tworzyć programowo i przy użyciu Azure Portal. Ten artykuł koncentruje się na Azure Portal metodzie tworzenia i usuwania użytkowników.

Aby dodać lub usunąć użytkowników, Twoje konto musi mieć przypisaną rolę *administrator użytkownika* lub *administrator globalny* .

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Zgodnie z opisem w temacie [Omówienie kont użytkowników w Azure AD B2C](user-overview.md), istnieją trzy typy kont użytkowników, które można utworzyć w katalogu Azure AD B2C:

* Praca
* Gość
* Produkty konsumenckie

Ten artykuł koncentruje się na pracy z **kontami konsumentów** w Azure Portal. Aby uzyskać informacje na temat tworzenia i usuwania kont służbowych i gościa, zobacz [Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Tworzenie użytkownika odbiorcy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik**.
1. Wybierz pozycję **utwórz Azure AD B2C użytkownika**.
1. Wybierz **metodę logowania** , a następnie wprowadź adres **E-mail** lub **nazwę użytkownika** dla nowego użytkownika. Wybrana tutaj metoda logowania musi pasować do ustawienia określonego dla dostawcy tożsamości *konta lokalnego* Azure AD B2C dzierżawy (zobacz **Zarządzanie**  >  **dostawcami tożsamości** w dzierżawie Azure AD B2C).
1. Wprowadź **nazwę** użytkownika. Jest to zazwyczaj pełna nazwa (imię i nazwisko) użytkownika.
1. Obowiązkowe Możesz **zablokować logowanie** , jeśli chcesz opóźnić możliwość logowania się użytkownika. Logowanie można włączyć później, edytując **profil** użytkownika w Azure Portal.
1. Wybierz opcję **Automatyczne generowanie hasła** lub **pozwól mi tworzyć hasło**.
1. Określ **imię** **i nazwisko** użytkownika.
1. Wybierz przycisk **Utwórz**.

Jeśli nie wybrano opcji **Zablokuj logowanie**, użytkownik może teraz zalogować się przy użyciu podanej metody logowania (adresu e-mail lub nazwy użytkownika).

## <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Jako administrator możesz zresetować hasło użytkownika, jeśli użytkownik zapomni hasła. Po zresetowaniu hasła użytkownika zostanie automatycznie wygenerowane hasło tymczasowe dla użytkownika. Tymczasowe hasło nigdy nie wygasa. Po następnym zalogowaniu się użytkownika hasło będzie nadal działało, niezależnie od tego, jak dużo czasu upłynął od momentu wygenerowania hasła tymczasowego. Następnie użytkownik musi zresetować hasło do stałego. 

> [!IMPORTANT]
> Przed zresetowaniem hasła użytkownika [Skonfiguruj przepływ wymuszania resetowania hasła w Azure Active Directory B2C](force-password-reset.md), w przeciwnym razie użytkownik nie będzie mógł się zalogować.

Aby zresetować hasło użytkownika:

1. W katalogu Azure AD B2C wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika, dla którego chcesz zresetować hasło.
1. Wyszukaj i wybierz użytkownika, który wymaga resetowania, a następnie wybierz pozycję **Resetuj hasło**.

    Zostanie wyświetlona strona **Alain Charon-profile** z opcją **resetowania hasła** .

    ![Strona profil użytkownika z wyróżnioną opcją resetowania hasła](media/manage-users-portal/user-profile-reset-password-link.png)

1. Na stronie **Resetowanie hasła** wybierz pozycję **Resetuj hasło**.
1. Skopiuj hasło i nadaj użytkownikowi. Użytkownik będzie musiał zmienić hasło podczas następnego procesu logowania.


## <a name="delete-a-consumer-user"></a>Usuwanie użytkownika odbiorcy

1. W katalogu Azure AD B2C wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika, który chcesz usunąć.
1. Wybierz pozycję **Usuń**, a następnie przycisk **tak** , aby potwierdzić usunięcie.

Aby uzyskać szczegółowe informacje na temat przywracania użytkownika w ciągu pierwszych 30 dni od usunięcia lub trwałego usunięcia użytkownika, zobacz [przywracanie lub usuwanie ostatnio usuniętego użytkownika przy użyciu Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z automatycznymi scenariuszami zarządzania użytkownikami, na przykład Migrowanie użytkowników z innego dostawcy tożsamości do katalogu Azure AD B2C, zobacz [Azure AD B2C: migracja użytkownika](user-migration.md).
