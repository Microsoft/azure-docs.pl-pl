---
title: Wysoka dostępność dla serwera usługi Azure MFA — Azure Active Directory
description: Wdróż wiele wystąpień Serwer Multi-Factor Authentication platformy Azure w konfiguracjach, które zapewniają wysoką dostępność.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23cf7abeb0aaf8bc3939296a307edb07ce5fcf0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742480"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurowanie usługi Azure Serwer Multi-Factor Authentication w celu zapewnienia wysokiej dostępności

Aby uzyskać wysoką dostępność przy użyciu wdrożenia usługi Azure Server MFA, należy wdrożyć wiele serwerów MFA. Ta sekcja zawiera informacje na temat układu o zrównoważonym obciążeniu w celu osiągnięcia celów wysokiej dostępności w ramach wdrożenia serwera Azure MFS.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać Multi-Factor Authentication usługi Azure AD opartych na chmurze.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="mfa-server-overview"></a>Omówienie serwera usługi MFA

Architektura usługi serwera usługi Azure MFA obejmuje kilka składników, jak pokazano na poniższym diagramie:

 ![Składniki architektury serwera MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Serwer MFA to serwer z systemem Windows, na którym jest zainstalowane oprogramowanie Multi-Factor Authentication platformy Azure. Wystąpienie serwera usługi MFA musi być aktywowane przez usługę MFA na platformie Azure do działania. W środowisku lokalnym można zainstalować więcej niż jeden serwer usługi MFA.

Pierwszy zainstalowany serwer usługi MFA jest serwerem głównym usługi MFA z domyślną aktywacją przez usługę Azure MFA. Serwer główny MFA ma zapisywalną kopię bazy danych PhoneFactor. pfdata. Kolejne instalacje wystąpień serwera usługi MFA są znane jako elementy podrzędne. Podwładny usługi MFA mają zreplikowaną kopię bazy danych PhoneFactor. pfdata, która jest tylko do odczytu. Serwery MFA replikujeją informacje przy użyciu zdalnego wywołania procedury (RPC). Wszystkie serwery usługi MFA muszą być przyłączone do domeny lub autonomiczne w celu replikowania informacji.

Serwery główne i podrzędne usługi MFA komunikują się z usługą MFA, gdy wymagane jest uwierzytelnianie dwuskładnikowe. Na przykład gdy użytkownik próbuje uzyskać dostęp do aplikacji, która wymaga uwierzytelniania dwuskładnikowego, użytkownik zostanie najpierw uwierzytelniony przez dostawcę tożsamości, na przykład Active Directory (AD).

Po pomyślnym uwierzytelnieniu w usłudze AD serwer usługi MFA komunikuje się z usługą MFA. Serwer usługi MFA czeka na powiadomienie z usługi MFA, aby zezwalać na dostęp użytkownika do aplikacji lub go odmawiać.

Jeśli serwer główny usługi MFA przejdzie w tryb offline, uwierzytelnienia nadal mogą być przetwarzane, ale nie można przetworzyć operacji wymagających zmian w bazie danych MFA. (Przykłady: dodanie użytkowników, samoobsługowe zmiany kodu PIN, zmiana informacji o użytkowniku lub dostęp do portalu użytkowników)

## <a name="deployment"></a>Wdrożenie

Należy wziąć pod uwagę następujące ważne kwestie dotyczące równoważenia obciążenia serwera usługi Azure MFA i powiązanych składników.

* **Użycie usługi RADIUS standard w celu zapewnienia wysokiej dostępności**. Jeśli serwery usługi Azure MFA są używane jako serwery RADIUS, można skonfigurować jeden serwer MFA jako cel uwierzytelniania podstawowego usługi RADIUS i inne serwery usługi Azure MFA jako cele uwierzytelniania pomocniczego. Jednak ta metoda zapewniania wysokiej dostępności może nie być praktyczna, ponieważ należy poczekać na przekroczenie limitu czasu, gdy uwierzytelnianie nie powiedzie się w podstawowym celu uwierzytelniania, zanim będzie można uwierzytelnić się na serwerze docelowym uwierzytelniania pomocniczego. Równoważenie obciążenia ruchu usługi RADIUS między klientem RADIUS a serwerami RADIUS (w tym przypadku serwerów usługi Azure MFA działających jako serwery RADIUS) jest bardziej wydajne, aby można było skonfigurować klientów usługi RADIUS za pomocą pojedynczego adresu URL, na który mogą oni wskazywać.
* **Należy ręcznie promować podwładnych usługi MFA**. Jeśli główny serwer usługi Azure MFA przejdzie w tryb offline, pomocnicze serwery usługi Azure MFA kontynuują przetwarzanie żądań MFA. Jednak do momentu udostępnienia serwera głównego MFA Administratorzy nie mogą dodawać użytkowników ani modyfikować ustawień usługi MFA, a użytkownicy nie mogą wprowadzać zmian przy użyciu portalu użytkowników. Podwyższanie poziomu usługi MFA do roli głównej zawsze jest procesem ręcznym.
* **Separability składników**. Serwer usługi Azure MFA zawiera kilka składników, które można zainstalować w tym samym wystąpieniu systemu Windows Server lub w różnych wystąpieniach. Te składniki obejmują Portal użytkowników, usługę sieci Web aplikacji mobilnej oraz adapter usług ADFS (Agent). Ta separability umożliwia użycie serwera proxy aplikacji sieci Web w celu opublikowania portalu użytkowników i serwera sieci Web aplikacji mobilnej z sieci obwodowej. Taka konfiguracja zwiększa bezpieczeństwo projektu, jak pokazano na poniższym diagramie. Portal użytkowników usługi MFA i serwer sieci Web aplikacji mobilnej można również wdrożyć w konfiguracjach o zrównoważonym obciążeniu.

   ![Serwer usługi MFA z siecią obwodową](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Hasło jednorazowe (OTP) za pośrednictwem wiadomości SMS (jeden-Way SMS) wymaga użycia sesji programu Sticky, jeśli ruch jest zrównoważony obciążenia**. Jednokierunkowa wiadomość SMS to opcja uwierzytelniania, która powoduje, że serwer MFA wysyła użytkownikom wiadomość tekstową zawierającą uwierzytelnianie OTP. Użytkownik wprowadza uwierzytelnianie OTP w oknie monitu, aby zakończyć wyzwanie usługi MFA. W przypadku równoważenia obciążenia serwerów usługi Azure MFA ten sam serwer, który obsłużył początkowe żądanie uwierzytelniania, musi być serwerem, który odbiera komunikat OTP od użytkownika. Jeśli inny serwer MFA odbiera odpowiedź OTP, żądanie uwierzytelnienia nie powiedzie się. Aby uzyskać więcej informacji, zobacz [hasło jednorazowe za pośrednictwem wiadomości SMS dodanej do serwera usługi Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Wdrożenia ze zrównoważonym obciążeniem portalu użytkowników i usługi sieci Web aplikacji mobilnej wymagają sesji programu Sticky Notes**. W przypadku równoważenia obciążenia w portalu użytkowników usługi MFA i usłudze sieci Web aplikacji mobilnej każda sesja musi pozostać na tym samym serwerze.

## <a name="high-availability-deployment"></a>Wdrożenie wysokiej dostępności

Na poniższym diagramie przedstawiono kompletną implementację usługi Azure MFA, a także jej składniki oraz spis usług AD FS.

 ![Implementacja wysokiej dostępności serwera usługi Azure MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Zwróć uwagę na następujące elementy dla odpowiadającego im obszaru podanego powyżej.

1. Dwa serwery usługi Azure MFA (MFA1 i MFA2) mają zrównoważone obciążenie (mfaapp.contoso.com) i są skonfigurowane do używania portu statycznego (4443) do replikowania bazy danych PhoneFactor. pfdata. Zestaw SDK usługi sieci Web jest instalowany na każdym serwerze MFA, aby umożliwić komunikację przez port TCP 443 z serwerami usług AD FS. Serwery usługi MFA są wdrażane w konfiguracji bezstanowej równoważenia obciążenia. Jeśli jednak chcesz używać uwierzytelniania OTP za pośrednictwem programu SMS, musisz użyć równoważenia obciążenia stanowego.
   ![Serwer Azure MFA — App Server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Ponieważ usługa RPC korzysta z portów dynamicznych, nie zaleca się otwierania zapór do zakresu portów dynamicznych, które mogą być używane przez usługę RPC. W przypadku zapory **między** serwerami aplikacji MFA należy skonfigurować serwer usługi MFA do komunikowania się z portem statycznym dla ruchu związanego z replikacją między serwerami podrzędnymi i głównymi, a następnie otworzyć ten port w zaporze. Aby wymusić port statyczny, można utworzyć wartość rejestru DWORD o ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` nazwie ```Pfsvc_ncan_ip_tcp_port``` i ustawić wartość dostępnego portu statycznego. Połączenia są zawsze inicjowane przez podrzędne serwery usługi MFA do serwera głównego. port statyczny jest wymagany tylko w przypadku serwera głównego, ale ponieważ w dowolnym momencie można podwyższyć poziom podwładnego jako główny, należy ustawić port statyczny na wszystkich serwerach usługi MFA.

2. Dwa serwery aplikacji mobilnej Portal użytkowników/MFA (MFA-UP-MAS1 i MFA-UP-MAS2) są zrównoważone obciążenie w konfiguracji **stanowej** (MFA.contoso.com). Odwołaj te sesje, które są wymagane do równoważenia obciążenia aplikacji Portal użytkowników usługi MFA i App Service Mobile.
   ![Serwer usługi Azure MFA — Portal użytkowników i urządzenia przenośne App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Farma serwerów usług ADFS jest równoważna i publikowana w Internecie za pomocą serwerów proxy usług AD równoważenia obciążenia w sieci obwodowej. Każdy serwer usług ADFS korzysta z agenta usług AD FS do komunikowania się z serwerami usługi Azure MFA przy użyciu jednego adresu URL ze zrównoważonym obciążeniem (mfaapp.contoso.com) za pośrednictwem portu TCP 443.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie i Konfigurowanie serwera usługi Azure MFA](howto-mfaserver-deploy.md)
