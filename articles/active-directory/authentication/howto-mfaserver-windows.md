---
title: Uwierzytelnianie systemu Windows i serwer usługi Azure MFA — Azure Active Directory
description: Wdrażanie uwierzytelniania systemu Windows i serwera Azure Multi-Factor Authentication.
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
ms.openlocfilehash: 962f022e5ed38e5c60f327fa4f85a5edc872b938
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742052"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Uwierzytelnianie systemu Windows i serwer usługi Azure Multi-Factor Authentication

Sekcja Uwierzytelnianie systemu Windows w ramach serwera Azure Multi-Factor Authentication umożliwia włączenie i skonfigurowanie uwierzytelniania systemu Windows dla aplikacji. Przed skonfigurowaniem uwierzytelniania systemu Windows weź pod uwagę kwestie wymienione na poniższej liście:

* Aby usługi terminalowe zaczęły działać, po konfiguracji należy ponownie uruchomić usługę Azure Multi-Factor Authentication.
* Jeśli zaznaczono opcję "Wymagaj dopasowania użytkownika w systemie Azure Multi-Factor Authentication" i nie jesteś na liście użytkowników, nie będzie można zalogować się do maszyny po ponownym uruchomieniu.
* To, czy adresy IP są zaufane, zależy od tego, czy aplikacja może uwierzytelnić adres IP klienta. Obecnie są obsługiwane wyłącznie usługi terminalowe.  

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać Multi-Factor Authentication usługi Azure AD opartych na chmurze.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

> [!NOTE]
> Ta funkcja nie jest obsługiwana w kontekście zabezpieczania sesji usług terminalowych w systemie Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Aby zabezpieczyć aplikację z uwierzytelnianiem systemu Windows, wykonaj czynności opisane w poniższej procedurze

1. Kliknij ikonę uwierzytelniania systemu Windows na serwerze usługi Azure Multi-Factor Authentication.
   ![Uwierzytelnianie systemu Windows na serwerze usługi MFA](./media/howto-mfaserver-windows/windowsauth.png)
2. Zaznacz pole wyboru **Włącz uwierzytelnianie systemu Windows**. Domyślnie to pole nie jest zaznaczone.
3. Na karcie Aplikacje administrator może skonfigurować co najmniej jedną aplikację na potrzeby uwierzytelniania systemu Windows.
4. Wybierz serwer lub aplikację — określ, czy serwer lub aplikacja są włączone. Kliknij przycisk **OK**.
5. Kliknij przycisk **Dodaj...**
6. Na karcie Zaufane adresy IP można pominąć sesje uwierzytelniania usługi Azure Multi-Factor Authentication dla sesji systemu Windows z określonych adresów IP. Na przykład: jeśli pracownicy korzystają z aplikacji zarówno w biurze, jak i w domu, można wybrać taką konfigurację, aby nie musieli oni odbierać połączeń z usługi Azure Multi-Factor Authentication, przebywając w biurze. W tym celu należy określić podsieć biura jako wpis z zaufanymi adresami IP.
7. Kliknij przycisk **Dodaj...**
8. Wybierz opcję **Pojedynczy adres IP**, jeśli chcesz pominąć pojedynczy adres IP.
9. Wybierz opcję **Zakres adresów IP**, aby pominąć cały zakres adresów IP. Na przykład: 10.63.193.1–10.63.193.100.
10. Wybierz opcję **Podsieć**, aby określić zakres adresów IP, korzystając z notacji podsieci. Wprowadź początkowy adres IP podsieci i wybierz odpowiednią maskę sieci z listy rozwijanej.
11. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie urządzeń VPN innych firm dla serwera Azure MFA](howto-mfaserver-nps-vpn.md)

- [Augment your existing authentication infrastructure with the NPS extension for Azure MFA](howto-mfa-nps-extension.md) (Rozszerzanie istniejącej infrastruktury uwierzytelniania za pomocą rozszerzenia serwera NPS dla usługi Azure MFA)
