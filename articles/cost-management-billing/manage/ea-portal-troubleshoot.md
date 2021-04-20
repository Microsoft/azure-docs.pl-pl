---
title: Rozwiązywanie problemów z dostępem do portalu Umowy Enterprise platformy Azure
description: W tym artykule opisano najczęstsze problemy z Umową Enterprise (EA) w portalu EA platformy Azure.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726470"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Rozwiązywanie problemów z dostępem do portalu Umowy Enterprise platformy Azure

W tym artykule opisano najczęstsze problemy z Umową Enterprise (EA) w portalu EA platformy Azure. Portal EA platformy Azure służy do zarządzania użytkownikami i kosztami umowy Enterprise. Podczas konfigurowania lub aktualizowania dostępu do portalu EA platformy Azure mogą wystąpić poniższe problemy.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemy z dodawaniem administratora do rejestracji

Istnieją różne typy poziomów uwierzytelniania dla rejestracji przedsiębiorstwa. W razie zastosowania nieprawidłowych poziomów uwierzytelniania mogą wystąpić problemy przy próbie uzyskania dostępu do portalu EA platformy Azure.

Portal EA platformy Azure służy do przyznawania uprawnień użytkownikom z różnymi poziomami uwierzytelniania. Administrator przedsiębiorstwa może zaktualizować poziom uwierzytelniania w celu spełnienia wymagań bezpieczeństwa organizacji.

### <a name="authentication-level-types"></a>Typy poziomów uwierzytelniania

- Tylko konto Microsoft — dla organizacji, które chcą tworzyć użytkowników, używać ich i zarządzać nimi za pomocą kont Microsoft.
- Konto służbowe — dla organizacji, które skonfigurowały usługę Active Directory Federation w chmurze i mają wszystkie konta w jednej dzierżawie.
- Konto służbowe między dzierżawami — dla organizacji, które skonfigurowały usługę Active Directory Federation w chmurze i mają konta w wielu dzierżawach.
- Konto mieszane — umożliwia dodawanie użytkowników przy użyciu konta Microsoft lub konta służbowego.

Pierwsze konto służbowe dodane do rejestracji określa domenę _domyślną_. Aby dodać konto służbowe do innej dzierżawy, należy zmienić poziom uwierzytelniania w obszarze Rejestracja na potrzeby uwierzytelniania między dzierżawami.

Aby zaktualizować poziom uwierzytelniania:

1. Zaloguj się do portalu EA platformy Azure jako administrator przedsiębiorstwa.
2. Kliknij pozycję **Zarządzaj zespołem** na panelu nawigacyjnym po lewej stronie.
3. Kliknij kartę **Rejestracja**.
4. W obszarze **Szczegóły rejestracji** wybierz pozycję **Poziom uwierzytelniania**.
5. Kliknij symbol ołówka.
6. Kliknij pozycję **Zapisz**.

![Przykład pokazujący poziomy uwierzytelniania ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Konta Microsoft muszą mieć skojarzony identyfikator utworzony pod adresem [https://signup.live.com](https://signup.live.com/).

Konta służbowe są dostępne dla organizacji, które skonfigurowały usługę Active Directory Federation i mają wszystkie konta w jednej dzierżawie. Użytkowników można dodawać przy użyciu uwierzytelniania użytkowników federacyjnych w firmie lub szkole, jeśli wewnętrzna usługa Active Directory firmy jest federacyjna.

Jeśli Twoja organizacja nie korzysta z federacji Active Directory, nie możesz użyć służbowego adresu e-mail. Zamiast tego zarejestruj się lub utwórz nowy adres e-mail i zarejestruj go jako konto Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Nie można uzyskać dostępu do portalu EA platformy Azure

Jeśli podczas próby zalogowania się do portalu EA platformy Azure zostanie wyświetlony komunikat o błędzie, wykonaj następujące kroki rozwiązywania problemów:

- Upewnij się, że używasz poprawnego adresu URL portalu EA platformy Azure, czyli https://ea.azure.com.
- Ustal, czy dostęp do portalu EA platformy Azure był dodany jako konto służbowe, czy jako konto Microsoft.
  - Jeśli używasz konta służbowego, wprowadź służbowy adres e-mail i hasło służbowe. Twoje hasło służbowe jest udostępniane przez Twoją organizację. W razie problemów z hasłem możesz skontaktować się z działem IT, aby dowiedzieć się, jak je zresetować.
  - Jeśli korzystasz z konta Microsoft, wprowadź adres e-mail i hasło do konta Microsoft. Jeśli nie pamiętasz hasła do konta Microsoft, możesz je zresetować pod adresem [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Aby się zalogować, użyj sesji przeglądarki w trybie prywatnym lub incognito, tak aby nie były przechowywane żadne pliki cookie ani buforowane informacje z poprzednich lub istniejących sesji. Wyczyść pamięć podręczną przeglądarki i otwórz okno https://ea.azure.com w trybie prywatnym lub incognito.
- Jeśli podczas korzystania z konta Microsoft zostanie wyświetlony komunikat o błędzie _Nieprawidłowy użytkownik_, przyczyną może być posiadanie wielu kont Microsoft. Konto, za pomocą którego próbowano się zalogować, nie jest podstawowym adresem e-mail.
Komunikat _Nieprawidłowy użytkownik_ może być też spowodowany użyciem błędnego typu konta podczas dodawania użytkownika do rejestracji. Na przykład użyto konta służbowego, a nie konta Microsoft. W tym przykładzie inny administrator EA musi dodać prawidłowe konto. W przeciwnym razie należy się skontaktować z [pomocą techniczną](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Jeśli musisz sprawdzić alias podstawowy, przejdź do strony [https://account.live.com](https://account.live.com). Następnie kliknij **swoje informacje**, a potem pozycję **Zarządzaj logowaniem do firmy Microsoft**. Postępuj zgodnie z monitami, aby zweryfikować alternatywny adres e-mail i uzyskać kod umożliwiający dostęp do poufnych informacji. Wprowadź kod zabezpieczeń. Jeśli nie chcesz konfigurować uwierzytelniania dwuskładnikowego, wybierz opcję **Skonfiguruj je później**.
  - Zostanie wyświetlona strona **Zarządzanie logowaniem do firmy Microsoft**, na której możesz zobaczyć swoje aliasy kont. Sprawdź, czy do logowania się do portalu EA platformy Azure jest używany alias podstawowy. Jeśli nie, możesz go ustawić jako alias podstawowy. Możesz też użyć zamiast niego podstawowego aliasu dla portalu EA platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Administratorzy portalu EA platformy Azure powinni przeczytać artykuł [Administracja portalu Azure EA](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
- Przeczytaj często [zadawane Cost Management + Billing odpowiedzi](../cost-management-billing-faq.yml) na pytania i odpowiedzi dotyczące typowych problemów z aktywacją ea platformy Azure.
