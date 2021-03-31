---
title: Dołączanie nowego urządzenia z systemem Windows 10 przy użyciu usługi Azure AD podczas pierwszego uruchomienia | Microsoft Docs
description: Jak użytkownicy mogą konfigurować usługę Azure AD JOIN w trakcie korzystania z funkcji.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: da37316724bf6ef166f08faa7208ad196000bb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85253107"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Samouczek: dołączanie nowego urządzenia z systemem Windows 10 przy użyciu usługi Azure AD podczas pierwszego uruchomienia

Zarządzanie urządzeniami w usłudze Azure Active Directory (Azure AD) pozwala zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

W systemie Windows 10 możesz dołączyć nowe urządzenie do usługi Azure AD podczas pierwszego uruchomienia.  
Dzięki temu możesz dostarczać zapakowane w folię urządzenia do swoich pracowników lub uczniów.

Jeśli masz system Windows 10 Professional lub Windows 10 Enterprise zainstalowany na urządzeniu, środowisko użytkownika domyślnie rozpocznie proces konfiguracji urządzeń firmowych.

W przypadku *trybu OOBE* w systemie Windows dołączanie do lokalnej domeny usługi Active Directory (AD) nie jest obsługiwane. Jeśli planujesz dołączyć komputer do domeny usługi AD, podczas konfiguracji należy wybrać link **Skonfiguruj system Windows za pomocą konta lokalnego**. Następnie możesz dołączyć domenę z poziomu ustawień na komputerze.
 
W tym samouczku dowiesz się, jak dołączyć urządzenie do usługi Azure AD podczas pierwszego uruchomienia:
 > [!div class="checklist"]
> * Wymagania wstępne
> * Dołączanie urządzenia
> * Weryfikacja

## <a name="prerequisites"></a>Wymagania wstępne

Aby dołączyć urządzenie z systemem Windows 10, należy skonfigurować usługę rejestracji urządzeń, aby umożliwić rejestrowanie urządzeń. Oprócz posiadania uprawnień do dołączania urządzeń w dzierżawie usługi Azure AD musisz mieć mniej zarejestrowanych urządzeń niż skonfigurowane maksimum. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień urządzenia](device-management-azure-portal.md#configure-device-settings).

Ponadto, jeśli dzierżawa jest federacyjna, dostawca tożsamości MUSI obsługiwać punkt końcowy nazwy użytkownika/hasła WS-Fed i WS-Trust. Może to być wersja 1.3 lub 2005. Ta obsługa protokołu jest wymagana w celu dołączenia urządzenia do usługi Azure AD i zalogowania się na urządzeniu przy użyciu hasła.

## <a name="joining-a-device"></a>Dołączanie urządzenia

**Aby dołączyć urządzenie z systemem Windows 10 do usługi Azure AD podczas pierwszego uruchomienia:**

1. Po włączeniu nowego urządzenia i uruchomieniu procesu konfiguracji powinien zostać wyświetlony komunikat **Przygotowanie**. Postępuj zgodnie z monitami, aby skonfigurować urządzenie.
1. Rozpocznij od dostosowania regionu i języka. Następnie zaakceptuj postanowienia licencyjne oprogramowania firmy Microsoft.
 
    ![Dostosowywanie regionu](./media/azuread-joined-devices-frx/01.png)

1. Wybierz sieć, której chcesz użyć, aby połączyć się z Internetem.
1. Kliknij opcję **To urządzenie należy do mojej organizacji**. 

    ![Ekran Kto jest właścicielem tego komputera](./media/azuread-joined-devices-frx/02.png)

1. Wprowadź poświadczenia podane przez organizację, a następnie kliknij opcję **Zaloguj**.

    ![Ekran logowania](./media/azuread-joined-devices-frx/03.png)

1. Urządzenie lokalizuje pasującą dzierżawę w usłudze Azure AD. Jeśli znajdujesz się w domenie federacyjnej, system przekieruje Cię do lokalnego serwera usługi Secure Token Service (STS), np. usługi Active Directory Federation Services (AD FS).
1. Jeśli jesteś użytkownikiem, który nie korzysta z domeny federacyjnej, wprowadź poświadczenia bezpośrednio na stronie hostowanej w usłudze Azure AD. 
1. Zostanie wyświetlony monit o zastosowanie uwierzytelniania wieloskładnikowego. 
1. Usługa Azure AD sprawdzi, czy rejestracja w usłudze zarządzania urządzeniami przenośnymi jest wymagana.
1. System Windows rejestruje urządzenie w katalogu organizacji w usłudze Azure AD oraz rejestruje je w usłudze zarządzania urządzeniami przenośnymi, jeśli ma to zastosowanie.
1. Podjęta akcja zależy od typu użytkownika:
   - Użytkownik zarządzany — system Windows przeniesie Cię do pulpitu za pośrednictwem automatycznego procesu logowania.
   - Użytkownik federacyjny — nastąpi przejście do ekranu logowania systemu Windows w celu wprowadzenia poświadczeń.

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie dołączyło do usługi Azure AD, zapoznaj się z oknem dialogowym **Dostęp do zasobów służbowych** na urządzeniu z systemem Windows. Okno dialogowe powinno wskazywać, że masz połączenie z katalogiem usługi Azure AD.

![Dostęp do zasobów służbowych](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).
- Aby uzyskać więcej informacji o zarządzaniu urządzeniami w portalu usługi Azure AD, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md).
