---
title: Uwierzytelnianie LDAP i serwer usługi Azure MFA — Azure Active Directory
description: Wdrażanie uwierzytelniania LDAP i Serwer Multi-Factor Authentication platformy Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742225"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Uwierzytelnianie LDAP i Serwer Multi-Factor Authentication platformy Azure

Domyślnie usługa Serwer Azure Multi-Factor Authentication jest skonfigurowana pod kątem importowania lub synchronizowania użytkowników z usługi Active Directory. Można ją jednak skonfigurować pod kątem powiązań z różnymi katalogami LDAP, takimi jak katalog ADAM, lub z określonym kontrolerem domeny usługi Active Directory. Po nawiązaniu połączenia z katalogiem za pośrednictwem protokołu LDAP Serwer Multi-Factor Authentication platformy Azure może działać jako serwer proxy LDAP na potrzeby uwierzytelniania. Umożliwia również korzystanie z powiązania LDAP jako elementu docelowego protokołu RADIUS do wstępnego uwierzytelniania użytkowników przy użyciu uwierzytelniania w usługach IIS lub do uwierzytelniania podstawowego w portalu użytkowników usługi Azure MFA.

Aby użyć usługi Azure Multi-Factor Authentication jako serwera proxy LDAP, Wstaw Serwer Multi-Factor Authentication platformy Azure między klientem LDAP (na przykład urządzenie sieci VPN, aplikacja) i serwer katalogu LDAP. Serwer usługi Azure Multi-Factor Authentication musi zostać skonfigurowany pod kątem komunikacji zarówno z serwerami klienta, jak i z katalogiem LDAP. W tej konfiguracji serwer usługi Azure Multi-Factor Authentication akceptuje żądania LDAP od serwerów i aplikacji klientów i przekazuje je do docelowego serwera katalogu LDAP w celu sprawdzania poprawności podstawowych poświadczeń. Jeśli katalog LDAP sprawdza poprawność poświadczeń głównych, usługa Azure Multi-Factor Authentication wykonuje drugą weryfikację tożsamości i wysyła odpowiedź z powrotem do klienta LDAP. Cały proces uwierzytelniania powiedzie się tylko wtedy, gdy zarówno uwierzytelnianie serwera LDAP, jak i drugi etap weryfikacji przebiegną prawidłowo.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać Multi-Factor Authentication usługi Azure AD opartych na chmurze.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="configure-ldap-authentication"></a>Konfigurowanie uwierzytelniania LDAP

Aby skonfigurować uwierzytelnianie LDAP, zainstaluj usługę Serwer Azure Multi-Factor Authentication na serwerze z systemem Windows. Postępuj zgodnie z następującą procedurą:

### <a name="add-an-ldap-client"></a>Dodawanie klienta LDAP

1. W Serwer Multi-Factor Authentication platformy Azure wybierz ikonę uwierzytelniania LDAP w menu po lewej stronie.
2. Zaznacz pole wyboru **Włącz uwierzytelnianie LDAP** .

   ![Uwierzytelnianie LDAP na serwerze usługi MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na karcie klienci zmień port TCP i SSL (TLS), jeśli usługa Azure Multi-Factor Authentication LDAP powinna być powiązana z portami niestandardowymi w celu nasłuchiwania żądań LDAP.
4. Jeśli planujesz używać LDAPs z klienta do Serwer Multi-Factor Authentication platformy Azure, certyfikat TLS/SSL musi być zainstalowany na tym samym serwerze co serwer usługi MFA. Kliknij przycisk **Przeglądaj** obok pola certyfikat protokołu SSL (TLS) i wybierz certyfikat, który ma być używany do bezpiecznego połączenia.
5. Kliknij pozycję **Dodaj**.
6. W oknie dialogowym Dodawanie klienta LDAP wprowadź adres IP urządzenia, serwera lub aplikacji, które są uwierzytelniane na serwerze i nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
7. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać weryfikacji dwuetapowej. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub jest wykluczona z weryfikacji dwuetapowej, pozostaw pole niezaznaczone. Dodatkowe informacje na temat tej funkcji można znaleźć w pliku pomocy serwera usługi MFA.

Powtórz te kroki, aby dodać kolejnych klientów LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurowanie połączenia katalogu LDAP

Gdy usługa Azure Multi-Factor Authentication jest skonfigurowana pod kątem odbierania uwierzytelnień LDAP, musi ona pełnić rolę serwera proxy przesyłającego te uwierzytelnienia do katalogu LDAP. W związku z tym na karcie Cel wyświetlana jest tylko jedna, wyszarzona opcja użycia obiektu docelowego LDAP.

> [!NOTE]
> Integracja katalogów nie gwarantuje pracy z katalogami innymi niż Active Directory Domain Services.

1. Aby skonfigurować połączenie z katalogiem LDAP, kliknij ikonę **Integracja katalogu** .
2. Na karcie Ustawienia wybierz przycisk radiowy **Użyj określonej konfiguracji LDAP** .
3. Wybierz pozycję **Edytuj...**
4. W oknie dialogowym Edycja konfiguracji LDAP wypełnij pola informacjami wymaganymi do nawiązania połączenia z katalogiem LDAP. Opisy tych pól znajdują się w pliku pomocy serwera usługi Azure Multi-Factor Authentication.

    ![Konfiguracja LDAP integracji katalogu](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Przetestuj połączenie LDAP, klikając przycisk **Testuj** .
6. Jeśli test połączenia LDAP zakończył się pomyślnie, kliknij przycisk **OK** .
7. Kliknij kartę **filtry** . Serwer jest wstępnie skonfigurowany do ładowania kontenerów, grup zabezpieczeń i użytkowników z Active Directory. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP prawdopodobnie będzie konieczna edycja wyświetlanych filtrów. Kliknij link **Pomoc** , aby uzyskać więcej informacji na temat filtrów.
8. Kliknij kartę **atrybuty** . Serwer jest wstępnie skonfigurowany do mapowania atrybutów z Active Directory.
9. W przypadku konieczności utworzenia powiązania z innym katalogiem LDAP lub zmiany wstępnie skonfigurowanego mapowania atrybutów kliknij przycisk **Edytuj…**
10. W oknie dialogowym Edycja atrybutów zmodyfikuj mapowanie atrybutu LDAP katalogu. Nazwy atrybutów można wpisać lub wybrać, klikając przycisk.. **.** znajdującego się obok każdego pola. Kliknij link **Pomoc** , aby uzyskać więcej informacji na temat atrybutów.
11. Kliknij przycisk **OK** .
12. Kliknij ikonę **Ustawienia firmy** i wybierz kartę **rozpoznawanie nazwy użytkownika** .
13. W przypadku nawiązywania połączenia z katalogiem Active Directory z serwera przyłączonego do domeny zostaw zaznaczony przycisk radiowy **Użyj identyfikatorów zabezpieczeń systemu Windows (SID) w celu dopasowania nazw użytkowników**. W przeciwnym razie wybierz przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników** .

Gdy przycisk radiowy **Użyj atrybutu unikatowego identyfikatora LDAP w celu dopasowania nazw użytkowników** jest zaznaczony, serwer usługi Azure Multi-Factor Authentication próbuje rozpoznać unikatowy identyfikator w katalogu LDAP na podstawie każdej z nazw użytkowników. Wyszukiwanie LDAP jest wykonywane na atrybutach nazwy użytkownika zdefiniowanych w obszarze Integracja katalogu — atrybuty >. Po uwierzytelnieniu użytkownika nazwa użytkownika jest rozpoznawana jako unikatowy identyfikator w katalogu LDAP. Unikatowy identyfikator jest używany do dopasowania użytkownika w pliku danych Multi-Factor Authentication platformy Azure. Umożliwia to porównywanie bez uwzględniania wielkości liter i długich i krótkich formatów nazw użytkowników.

Po wykonaniu tych kroków serwer usługi MFA nasłuchuje na skonfigurowanych portach żądań dostępu LDAP ze skonfigurowanych klientów i pełni rolę serwera proxy dla tych żądań do katalogu LDAP w celu uwierzytelnienia.

## <a name="configure-ldap-client"></a>Konfigurowanie klienta LDAP

Aby skonfigurować klienta LDAP, skorzystaj z poniższych wytycznych:

* Skonfiguruj urządzenie, serwer lub aplikację pod kątem uwierzytelniania z użyciem protokołu LDAP na serwerze usługi Azure Multi-Factor Authentication tak, jakby był to katalog LDAP. Użyj tych samych ustawień, które zwykle są używane do bezpośredniego łączenia się z katalogiem LDAP, z wyjątkiem nazwy serwera lub adresu IP, które będą odpowiadać danym serwera usługi Azure Multi-Factor Authentication.
* Skonfiguruj limit czasu protokołu LDAP na 30-60 sekund, aby zapewnić czas na zweryfikowanie poświadczeń użytkownika w katalogu LDAP, przeprowadź weryfikację drugiego kroku, odbierz odpowiedź i odpowiedzieć na żądanie dostępu LDAP.
* W przypadku korzystania z LDAP urządzenie lub serwer wykonujący zapytania LDAP musi mieć zaufanie do certyfikatu TLS/SSL zainstalowanego w Serwer Multi-Factor Authentication platformy Azure.
