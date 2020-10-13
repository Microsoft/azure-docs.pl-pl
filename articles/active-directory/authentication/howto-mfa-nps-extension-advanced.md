---
title: Konfigurowanie rozszerzenia serwera NPS usługi Azure MFA — Azure Active Directory
description: Po zainstalowaniu rozszerzenia serwera NPS wykonaj następujące kroki, aby uzyskać konfigurację zaawansowaną, taką jak dozwolone listy adresów IP i zamiana nazwy UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9156f84e5189b38a2c15f257bd6a47ac3db130
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964404"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Zaawansowane opcje konfiguracji rozszerzenia serwera NPS dla uwierzytelniania wieloskładnikowego

Rozszerzenie serwera zasad sieciowych (NPS) rozszerza funkcje Multi-Factor Authentication platformy Azure oparte na chmurze do infrastruktury lokalnej. W tym artykule przyjęto założenie, że już masz zainstalowane rozszerzenie, a teraz chcesz dowiedzieć się, jak dostosować rozszerzenie dla potrzeb. 

## <a name="alternate-login-id"></a>Alternatywny identyfikator logowania

Ponieważ rozszerzenie zasad sieciowych nawiązuje połączenie zarówno z katalogami lokalnymi, jak i w chmurze, może wystąpić problem polegający na tym, że lokalne nazwy głównych użytkowników (UPN) nie są zgodne z nazwami w chmurze. Aby rozwiązać ten problem, użyj alternatywnych identyfikatorów logowania. 

W ramach rozszerzenia serwera NPS można wyznaczyć atrybut Active Directory, który będzie używany zamiast nazwy UPN dla Multi-Factor Authentication platformy Azure. Pozwala to chronić zasoby lokalne przy użyciu weryfikacji dwuetapowej bez modyfikowania lokalnych nazw UPN. 

Aby skonfigurować Alternatywne identyfikatory logowania, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i edytuj następujące wartości rejestru:

| Nazwa | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | ciąg | Pusty | Określ nazwę atrybutu Active Directory, który ma być używany zamiast nazwy UPN. Ten atrybut jest używany jako atrybut AlternateLoginId. Jeśli dla tej wartości rejestru jest ustawiony [prawidłowy atrybut Active Directory](/windows/win32/adschema/attributes-all) (na przykład mail lub DisplayName), wartość atrybutu jest używana zamiast nazwy UPN użytkownika w celu uwierzytelnienia. Jeśli ta wartość rejestru jest pusta lub nie została skonfigurowana, AlternateLoginId jest wyłączona, a nazwa UPN użytkownika jest używana do uwierzytelniania. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | Fałsz | Użyj tej flagi, aby wymusić użycie wykazu globalnego na potrzeby wyszukiwania LDAP podczas wyszukiwania AlternateLoginId. Skonfiguruj kontroler domeny jako wykaz globalny, Dodaj atrybut AlternateLoginId do wykazu globalnego, a następnie Włącz tę flagę. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowany (Niepuste), **Ta flaga jest wymuszana jako true**, niezależnie od wartości ustawienia rejestru. W takim przypadku rozszerzenie serwera NPS wymaga skonfigurowania wykazu globalnego z atrybutem AlternateLoginId dla każdego lasu. |
| LDAP_LOOKUP_FORESTS | ciąg | Pusty | Podaj rozdzieloną średnikami listę lasów do przeszukania. Na przykład *contoso. com; Foobar. com*. W przypadku skonfigurowania tej wartości rejestru rozszerzenie serwera zasad sieciowych iteracyjnie przeszukuje wszystkie lasy w kolejności, w której zostały wymienione, i zwraca pierwszą pomyślną wartość AlternateLoginId. Jeśli ta wartość rejestru nie jest skonfigurowana, wyszukiwanie AlternateLoginId jest ograniczone do bieżącej domeny.|

Aby rozwiązać problemy z alternatywnymi identyfikatorami logowania, należy użyć zalecanych kroków dla [alternatywnych błędów identyfikatorów logowania](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Wyjątki adresów IP

Jeśli potrzebujesz monitorować dostępność serwera, na przykład jeśli usługi równoważenia obciążenia weryfikują, które serwery działają przed wysłaniem obciążeń, nie chcesz, aby te testy były blokowane przez żądania weryfikacji. Zamiast tego należy utworzyć listę adresów IP, które są używane przez konta usług, i wyłączyć Multi-Factor Authentication wymagania dla tej listy.

Aby skonfigurować listę dozwolonych adresów IP, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i skonfiguruj następującą wartość rejestru:

| Nazwa | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | ciąg | Pusty | Podaj rozdzieloną średnikami listę adresów IP. Uwzględnij adresy IP maszyn, na których pochodzą żądania obsługi, takie jak serwer NAS/VPN. Zakresy adresów IP i podsieci nie są obsługiwane. <br><br> Na przykład *: 10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> Ten klucz rejestru nie jest tworzony domyślnie przez Instalatora i w dzienniku AuthZOptCh pojawia się błąd, gdy usługa zostanie ponownie uruchomiona. Ten błąd może zostać zignorowany, ale jeśli ten klucz rejestru zostanie utworzony i pozostawiony pusty, jeśli nie jest wymagany, komunikat o błędzie nie zostanie zwrócony.

Gdy żądanie pochodzi z adresu IP, który istnieje w `IP_WHITELIST` , weryfikacja dwuetapowa jest pomijana. Lista adresów IP jest porównywana z adresem IP podanym w atrybucie *ratNASIPAddress* żądania RADIUS. Jeśli żądanie usługi RADIUS występuje bez atrybutu ratNASIPAddress, rejestrowane jest następujące ostrzeżenie: "P_WHITE_LIST_WARNING:: IP dozwolonych jest ignorowane jako źródłowy adres IP w żądaniu usługi RADIUS w atrybucie NasIpAddress".

## <a name="next-steps"></a>Następne kroki

[Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)