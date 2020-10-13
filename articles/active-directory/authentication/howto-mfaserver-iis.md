---
title: Uwierzytelnianie usług IIS i serwer usługi Azure MFA — Azure Active Directory
description: Wdrażanie uwierzytelniania usług IIS i serwera Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c072298bf5cce4e22fe50d2474c7abe6b915f74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919509"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Konfigurowanie serwera usługi Azure Multi-Factor Authentication na potrzeby aplikacji internetowych usług IIS

Sekcja Uwierzytelnianie usług IIS dostępna w ramach serwera usługi Azure Multi-Factor Authentication (MFA) pozwala włączyć i skonfigurować uwierzytelnianie usług IIS na potrzeby integracji z aplikacjami internetowymi usług Microsoft IIS. Serwer usługi Azure MFA instaluje wtyczkę, która umożliwia filtrowanie żądań wysyłanych do serwera sieci Web usług IIS w celu dodania usługi Azure Multi-Factor Authentication. Wtyczka IIS obsługuje uwierzytelnianie oparte na formularzach i zintegrowane uwierzytelnianie HTTP systemu Windows. Ponadto skonfigurowanie zaufanych adresów IP umożliwia wykluczenie wewnętrznych adresów IP z uwierzytelniania dwuskładnikowego.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać opartych na chmurze Multi-Factor Authentication platformy Azure.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.
>
> W przypadku korzystania z usługi Azure Multi-Factor Authentication opartej na chmurze nie istnieje alternatywa dla wtyczki usług IIS udostępnianej przez serwer usługi Azure Multi-Factor Authentication (MFA). Zamiast tego należy użyć serwera proxy aplikacji sieci Web (WAP) z Active Directory Federation Services (AD FS) lub serwera proxy aplikacji Azure Active Directory.

![Uwierzytelnianie usług IIS na serwerze usługi MFA](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Używanie uwierzytelniania usług IIS opartego na formularzach z serwerem usługi Azure Multi-Factor Authentication

Aby zabezpieczyć aplikację internetową usług IIS, która korzysta z uwierzytelniania opartego na formularzach, zainstaluj serwer usługi Azure Multi-Factor Authentication na serwerze internetowym usług IIS i skonfiguruj go zgodnie z poniższą procedurą:

1. Na serwerze usługi Azure Multi-Factor Authentication kliknij ikonę Uwierzytelnianie usług IIS w menu po lewej stronie.
2. Kliknij kartę **oparte na formularzach** .
3. Kliknij pozycję **Dodaj**.
4. Aby automatycznie wykryć zmienne nazwy użytkownika, hasła i domeny, wprowadź adres URL logowania ( `https://localhost/contoso/auth/login.aspx` na przykład) w oknie dialogowym automatyczna konfiguracja witryny sieci web Form-Based a następnie kliknij przycisk **OK**.
5. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać uwierzytelnianiu wieloskładnikowemu. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub będzie wykluczona z uwierzytelniania wieloskładnikowego, nie zaznaczaj tego pola.
6. Jeśli nie można automatycznie wykryć zmiennych na stronie, kliknij przycisk **Określ ręcznie** w oknie dialogowym Konfigurowana automatycznie witryna sieci Web oparta na formularzach.
7. W oknie dialogowym Dodawanie witryny sieci Web opartej na formularzach wprowadź adres URL strony logowania w polu Adres URL przesyłania, a następnie wprowadź nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
8. Wybierz poprawny format żądania. W przypadku większości aplikacji internetowych należy ustawić format na wartość **POST lub GET**.
9. Wprowadź zmienne nazwy użytkownika, hasła i domeny (jeśli jest wyświetlana na stronie logowania). W celu znalezienia nazw pól wejściowych przejdź do strony logowania w przeglądarce sieci Web, kliknij prawym przyciskiem myszy na stronie i wybierz polecenie **Wyświetl źródło**.
10. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Azure Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać uwierzytelnianiu wieloskładnikowemu. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub będzie wykluczona z uwierzytelniania wieloskładnikowego, nie zaznaczaj tego pola.
11. Kliknij pozycję **Zaawansowane**, aby przejrzeć ustawienia zaawansowane, w tym:

    - Wybór pliku niestandardowej strony odmowy
    - Buforowanie pomyślnych uwierzytelnień do witryny sieci Web przez pewien czas przy użyciu plików cookie
    - Wybór, czy poświadczenia podstawowe mają być uwierzytelniane względem domeny systemu Windows, katalogu LDAP, czy serwera RADIUS.

12. Kliknij przycisk **OK**, aby wrócić do okna dialogowego Dodawanie witryny sieci Web opartej na formularzach.
13. Kliknij przycisk **OK**.
14. Po wykryciu lub wprowadzeniu adresu URL i zmiennych na stronie dane witryny sieci Web wyświetlane są w panelu Oparte na formularzach.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Używanie zintegrowanego uwierzytelniania systemu Windows z serwerem usługi Azure Multi-Factor Authentication

Aby zabezpieczyć aplikację internetową usług IIS, która korzysta ze zintegrowanego uwierzytelniania HTTP systemu Windows, zainstaluj serwer usługi Azure MFA na serwerze internetowym usług IIS i skonfiguruj go zgodnie z poniższą procedurą:

1. Na serwerze usługi Azure Multi-Factor Authentication kliknij ikonę Uwierzytelnianie usług IIS w menu po lewej stronie.
2. Kliknij kartę **http** .
3. Kliknij pozycję **Dodaj**.
4. W oknie dialogowym Dodawanie podstawowego adresu URL wprowadź adres URL witryny sieci Web, w której jest przeprowadzane uwierzytelnianie HTTP (np `http://localhost/owa` .), a następnie podaj nazwę aplikacji (opcjonalnie). Nazwa aplikacji jest widoczna w raportach usługi Azure Multi-Factor Authentication i może być wyświetlana w uwierzytelniających wiadomościach SMS lub wiadomościach aplikacji mobilnej.
5. Dostosuj wartości w polach Limit czasu bezczynności i Maksymalna długość sesji, jeśli wartości domyślne nie są wystarczające.
6. Zaznacz pole **Wymagaj dopasowania użytkownika usługi Multi-Factor Authentication**, jeśli wszyscy użytkownicy zostali lub zostaną zaimportowani na serwer i będą podlegać uwierzytelnianiu wieloskładnikowemu. Jeśli znaczna liczba użytkowników nie została jeszcze zaimportowana na serwer i/lub będzie wykluczona z uwierzytelniania wieloskładnikowego, nie zaznaczaj tego pola.
7. W razie potrzeby zaznacz pole **Pamięć podręczna plików cookie**.
8. Kliknij przycisk **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Włączanie wtyczek IIS dla serwera usługi Azure Multi-Factor Authentication

Po skonfigurowaniu adresów URL i ustawień dla uwierzytelniania opartego na formularzach lub uwierzytelniania HTTP wybierz lokalizacje w usługach IIS, w których mają zostać załadowane i włączone wtyczki IIS usługi Azure Multi-Factor Authentication. Postępuj zgodnie z następującą procedurą:

1. W przypadku korzystania z usług IIS 6 kliknij kartę **ISAPI** . Wybierz witrynę internetową, w ramach której działa aplikacja sieci Web (np. domyślna witryna sieci Web), aby włączyć wtyczkę filtru ISAPI Multi-Factor Authentication platformy Azure dla tej lokacji.
2. W przypadku korzystania z usług IIS 7 lub nowszych kliknij kartę **moduł macierzysty** . Wybierz serwer, witryny sieci Web lub aplikacje, aby włączyć wtyczkę usług IIS na żądanych poziomach.
3. Kliknij pole **Włącz uwierzytelnianie usług IIS** w górnej części ekranu. Po wykonaniu tych czynności wybrana aplikacja usług IIS jest zabezpieczona przez usługę Azure Multi-Factor Authentication. Upewnij się, że zaimportowano użytkowników na serwer.

## <a name="trusted-ips"></a>Zaufane adresy IP

Zaufane adresy IP pozwalają użytkownikom pominąć uwierzytelnianie za pomocą usługi Azure Multi-Factor Authentication w przypadku żądań witryn sieci Web pochodzących z określonych adresów IP lub podsieci. Na przykład można wykluczyć użytkowników z uwierzytelniania za pomocą usługi Azure Multi-Factor Authentication, jeśli logują się oni z komputerów znajdujących się w biurze. W tym celu należy określić podsieć biura jako wpis z zaufanymi adresami IP. Aby skonfigurować zaufane adresy IP, postępuj zgodnie z poniższą procedurą:

1. W sekcji uwierzytelnianie usług IIS kliknij kartę **Zaufane adresy IP** .
2. Kliknij pozycję **Dodaj**.
3. Gdy pojawi się okno dialogowe Dodawanie zaufanych adresów IP, wybierz przycisk radiowy **Pojedynczy adres IP**, **Zakres adresów IP** lub **Podsieć**.
4. Wprowadź adres IP, zakres adresów IP lub podsieć, które powinny być dozwolone. Jeśli wprowadzasz podsieć, wybierz odpowiednią maskę sieci i kliknij przycisk **OK**.
