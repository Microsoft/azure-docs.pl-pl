---
title: Zarządzanie procesami roboczymi teraźniejszości — Azure Active Directory
description: Dowiedz się więcej na temat możliwości zarządzania przez proces roboczy teraźniejszości, które są dostępne w portalu mojego personelu.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969242"
---
# <a name="frontline-worker-management"></a>Zarządzanie procesami roboczymi teraźniejszości

Konto teraźniejszości Worker jest przeznaczone dla ponad 80% pracowników globalnych. Jednak ze względu na wysoką skalowalność, szybkie obroty i procesy pofragmentowane pracownicy teraźniejszości często nie posiadają narzędzi, które ułatwiają nieznacznie łatwiejsze wykonywanie zadań. Zarządzanie procesami roboczymi teraźniejszości przenosi transformację cyfrową na cały teraźniejszości pracowników. Pracownicy mogą obejmować menedżerów, teraźniejszości pracowników, operacje i.

Zarządzanie procesami roboczymi teraźniejszości umożliwia pracownikom teraźniejszości, ułatwiając wykonywanie następujących czynności:
- Usprawnianie wspólnych zadań IT w ramach mojego personelu
- Łatwe dołączanie pracowników teraźniejszości przez uproszczone uwierzytelnianie
- Bezproblemowe udostępnianie urządzeń udostępnionych i bezpieczne wylogowanie z teraźniejszości procesów roboczych

## <a name="delegated-user-management-through-my-staff"></a>Zarządzanie użytkownikami delegowanymi za poorednictwem mojego personelu

Azure Active Directory (Azure AD) zapewnia możliwość delegowania zarządzania użytkownikami do menedżerów teraźniejszości za pomocą [portalu My personel](../roles/my-staff-configure.md), pomagając zaoszczędzić cenny czas i zmniejszyć ryzyko. Przez włączenie uproszczonego resetowania haseł i zarządzania telefonem bezpośrednio ze sklepu lub fabryki fabryk, menedżerowie mogą udzielić dostępu pracownikom bez routingu żądania za pośrednictwem pomocy technicznej, IT lub operacji.

![Zarządzanie użytkownikami delegowanymi w portalu My kadr](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Przyspieszenie dołączania z uproszczonym uwierzytelnianiem

Mój personel umożliwia również menedżerom teraźniejszości zarejestrowanie numerów telefonów członków zespołu na potrzeby [logowania do programu SMS](../authentication/howto-authentication-sms-signin.md). W wielu pionowych teraźniejszości pracownicy utrzymują kombinację lokalnej nazwy użytkownika i hasła, czyli rozwiązanie, które jest często uciążliwe, kosztowne i podatne na błędy. Po umożliwieniu uwierzytelniania przy użyciu logowania za pomocą programu SMS procesy robocze teraźniejszości mogą logować się przy użyciu logowania jednokrotnego [(SSO)](../manage-apps/what-is-single-sign-on.md) dla Microsoft Teams i innych aplikacji, używając tylko numeru telefonu i jednorazowego kodu dostępu (OTP) wysyłanego za pośrednictwem programu SMS. Dzięki temu logowanie się do teraźniejszości procesów roboczych jest proste i bezpieczne, zapewniając szybki dostęp do aplikacji, których potrzebują.

![Logowanie za pomocą programu SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

Menedżerowie teraźniejszości mogą również używać aplikacji zarządzanego ekranu głównego (MHS), aby umożliwić pracownikom dostęp do określonego zestawu aplikacji na urządzeniach z systemem Android, które zostały zarejestrowane w usłudze Intune. Dedykowane urządzenia są zarejestrowane w [trybie udostępnionego urządzenia usługi Azure AD](../develop/msal-shared-devices.md). Po skonfigurowaniu trybu kiosku z wieloma aplikacjami w konsoli programu Microsoft Endpoint Manager (MEM) MHS jest automatycznie uruchamiany jako domyślny ekran główny na urządzeniu i pojawia się jako użytkownik końcowy jako *jedyny* ekran główny. Aby dowiedzieć się więcej, zobacz jak [skonfigurować aplikację ekran macierzysty zarządzanego przez firmę Microsoft dla systemu Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Zabezpieczanie wylogowywania procesów roboczych teraźniejszości z urządzeń udostępnionych

W wielu firmach są używane urządzenia współużytkowane, dzięki czemu pracownicy teraźniejszości mogą wykonywać operacje związane z zarządzaniem zapasami i punktami sprzedaży, bez konieczności aprowizacji i śledzenia poszczególnych urządzeń. Dzięki wylogowaniu się z udostępnionego urządzenia, proces roboczy teraźniejszości może bezpiecznie wylogować się ze wszystkich aplikacji na dowolnym udostępnionym urządzeniu przed przełączeniem go do centrum lub przekazaniem go do zespołu po następnym przejściu. Microsoft teams to jedna z aplikacji, które są obecnie obsługiwane na urządzeniach udostępnionych i umożliwia pracownikom teraźniejszości wyświetlanie przypisanych do nich zadań. Gdy proces roboczy wyloguje się z urządzenia udostępnionego, usługi Intune i Azure AD Wyczyść wszystkie dane firmowe, aby urządzenie było bezpiecznie przekazywane do następnego skojarzenia. Możesz zintegrować tę możliwość ze wszystkimi firmowymi aplikacjami dla [systemów iOS](../develop/msal-ios-shared-devices.md) i [Android](../develop/msal-android-shared-devices.md) przy użyciu [biblioteki uwierzytelniania firmy Microsoft](../develop/msal-overview.md).

![Logowanie do udostępnionego urządzenia](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o zarządzaniu delegowanymi użytkownikami, zobacz [dokumentację dotyczącą pracowników](../user-help/my-staff-team-manager.md).
- Zapoznaj się z samouczkiem dotyczącym [KONFIGUROWANIA oprogramowania SAP SuccessFactors w Active Directory celu](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)zainicjowania obsługi administracyjnej użytkowników w programie SAP SuccessFactors.
- W przypadku przychodzącej aprowizacji użytkowników z produktu Workday zapoznaj się z samouczkiem dotyczącym [konfigurowania usługi Workday dla automatycznego aprowizacji użytkowników](../saas-apps/workday-inbound-tutorial.md).
