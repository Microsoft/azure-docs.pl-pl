---
title: 'Azure AD Connect: zadania po konfiguracji hybrydowej usługi Azure AD Join | Microsoft Docs'
description: Ten dokument zawiera szczegóły zadań konfiguracyjnych wymaganych do ukończenia sprzężenia hybrydowego usługi Azure AD
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612473"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Zadania pokonfiguracyjne dotyczące dołączania do hybrydowej usługi Azure AD

Po uruchomieniu Azure AD Connect, aby skonfigurować organizację dla sprzężenia hybrydowego usługi Azure AD, istnieje kilka dodatkowych kroków, które należy wykonać, aby zakończyć instalację.  Wykonaj tylko te kroki, które mają zastosowanie do urządzeń.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurowanie kontrolowanego wdrożenia (opcjonalnie)
Wszystkie urządzenia przyłączone do domeny z systemem Windows 10 i Windows Server 2016 są automatycznie rejestrowane w usłudze Azure AD po zakończeniu wszystkich kroków konfiguracji. Jeśli wolisz przeprowadzić kontrolowane wdrożenie, a nie rejestrację automatyczną, możesz użyć zasad grupy, aby selektywnie włączać lub wyłączać Automatyczne wdrażanie.  Te zasady grupy należy ustawić przed rozpoczęciem wykonywania innych czynności konfiguracyjnych:
* Utwórz obiekt zasad grupy w Active Directory.
* Nadaj mu nazwę (np. sprzężenie hybrydowe usługi Azure AD).
* Edytuj i przejdź do: Konfiguracja komputera > zasad > Szablony administracyjne > składników systemu Windows > rejestracji urządzeń.

>[!NOTE]
>W przypadku 2012R2 ustawienia zasad są w **konfiguracji komputera > zasad > Szablony administracyjne > składników systemu Windows > Workplace Join > automatyczne dołączanie komputerów klienckich**

* Włącz to ustawienie: Zarejestruj komputery przyłączone do domeny jako urządzenia.
* Zastosuj i kliknij przycisk OK.
* Połącz obiekt zasad grupy z wybraną lokalizacją (jednostką organizacyjną, grupą zabezpieczeń lub domeną dla wszystkich urządzeń).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurowanie sieci za pomocą punktów końcowych rejestracji urządzeń
Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacyjnej na potrzeby rejestracji w usłudze Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementowanie urządzeń usługi WPAD dla systemu Windows 10
Jeśli Twoja organizacja uzyskuje dostęp do Internetu za pośrednictwem serwera proxy wychodzącego, zaimplementuj funkcję autowykrywania serwera proxy sieci Web (WPAD), aby umożliwić komputerom z systemem Windows 10 rejestrowanie się w usłudze Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Skonfiguruj punkt połączenia usługi w lasach, które nie zostały skonfigurowane przez Azure AD Connect 

Punkt połączenia z usługą (SCP) zawiera informacje o dzierżawie usługi Azure AD, które będą używane przez urządzenia do rejestracji podwójnej.  Uruchom skrypt programu PowerShell, ConfigureSCP.ps1 pobrany z Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Skonfiguruj dowolną usługę federacyjną, która nie została skonfigurowana przez Azure AD Connect

Jeśli organizacja używa usługi federacyjnej do logowania się do usługi Azure AD, reguły dotyczące roszczeń w relacji zaufania jednostki uzależnionej usługi Azure AD muszą zezwalać na uwierzytelnianie urządzeń. Jeśli używasz Federacji z AD FS, przejdź do pozycji [AD FS help](https://aka.ms/aadrptclaimrules) , aby wygenerować reguły dotyczące roszczeń. Jeśli używasz rozwiązania federacyjnego innego niż firmy Microsoft, skontaktuj się z tym dostawcą, aby uzyskać wskazówki.  

>[!NOTE]
>W przypadku urządzeń z systemem Windows niższego poziomu usługa musi obsługiwać wydawanie wiaormultiauthn i oświadczeń w przypadku otrzymywania żądań do zaufania usługi Azure AD. W AD FS należy dodać regułę przekształcania wystawiania przechodzącą przez metodę uwierzytelniania.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Włącz bezproblemową rejestrację jednokrotną usługi Azure AD dla urządzeń niższego poziomu systemu Windows

Jeśli Twoja organizacja używa synchronizacji skrótów haseł lub uwierzytelniania przekazywanego w celu zalogowania się do usługi Azure AD, Włącz bezproblemową rejestrację jednokrotną usługi Azure AD przy użyciu tej metody logowania, aby uwierzytelniać urządzenia niskiego poziomu systemu Windows:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ustawianie zasad usługi Azure AD dla urządzeń niższego poziomu systemu Windows

Aby zarejestrować urządzenia niskiego poziomu systemu Windows, należy się upewnić, że zasady usługi Azure AD umożliwiają użytkownikom rejestrowanie urządzeń. 

* Zaloguj się do swojego konta w Azure Portal.
* Przejdź do: Azure Active Directory > urządzeń > ustawień urządzenia
* Ustaw dla opcji "użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD".
* Klikanie pozycji Zapisz.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Dodawanie punktu końcowego usługi Azure AD do urządzeń niższego poziomu systemu Windows

Dodaj punkt końcowy uwierzytelniania urządzenia usługi Azure AD do lokalnych stref intranetowych na urządzeniach z systemem Windows niższego poziomu, aby uniknąć wyświetlania przez certyfikat podczas uwierzytelniania urządzeń: `https://device.login.microsoftonline.com` 

Jeśli używasz [bezproblemowego logowania jednokrotnego](how-to-connect-sso.md), Włącz opcję "Zezwalaj na aktualizacje paska stanu za pośrednictwem skryptu" w tej strefie i Dodaj następujący punkt końcowy: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Zainstaluj program Microsoft Workplace Join na urządzeniach z systemem Windows niższego poziomu

Ten Instalator tworzy zaplanowane zadanie w systemie urządzenia, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie sprzęga urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu przy użyciu zintegrowanego uwierzytelniania systemu Windows. Centrum pobierania jest o godzinie https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Skonfiguruj zasady grupy tak, aby zezwalały na rejestrację urządzeń

Aby dowiedzieć się, jak zezwolić na sprzężenie hybrydowe usługi Azure AD dla poszczególnych urządzeń, zobacz [kontrolowane sprawdzanie poprawności hybrydowego przyłączania do usługi Azure AD](../devices/hybrid-azuread-join-control.md).

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie zapisywania zwrotnego urządzeń](how-to-connect-device-writeback.md)
