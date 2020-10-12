---
title: Błąd "nie można dostać się tam z tego miejsca" — usługa Azure AD
description: Znajdź potencjalne poprawki, dla których jest wyświetlany komunikat o błędzie "nie można uzyskać informacji z tego miejsca".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799421"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Komunikat o błędzie "nie można uzyskać z tego miejsca"

Podczas uzyskiwania dostępu do wewnętrznych aplikacji lub usług sieci Web w organizacji może zostać wyświetlony komunikat o błędzie z informacją o tym, że nie można go **znaleźć w tym miejscu**. Ten komunikat oznacza, że organizacja umieściła zasady, które uniemożliwiają uzyskiwanie dostępu do zasobów organizacji przez urządzenie. Aby rozwiązać ten problem, możesz spróbować skontaktować się z pomocą techniczną, korzystając z kilku rzeczy, które możesz wypróbować.

## <a name="make-sure-youre-using-a-supported-browser"></a>Upewnij się, że używasz obsługiwanej przeglądarki
Jeśli zostanie wyświetlony komunikat z informacją **o tym,** że próbujesz uzyskać dostęp do witryn organizacji z nieobsługiwanej przeglądarki, sprawdź, która przeglądarka jest uruchomiona.

![Komunikat o błędzie związany z obsługą przeglądarki](media/user-help-device-remediation/browser-version.png)

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwaną przeglądarkę w zależności od używanego systemu operacyjnego. W przypadku korzystania z systemu Windows 10 obsługiwane przeglądarki obejmują programy Microsoft Edge, Internet Explorer i Google Chrome. Jeśli używasz innego systemu operacyjnego, możesz zapoznać się z pełną listą [obsługiwanych przeglądarek](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Upewnij się, że korzystasz z obsługiwanego systemu operacyjnego
Upewnij się, że korzystasz z obsługiwanej wersji systemu operacyjnego, w tym:

- **Klient systemu Windows.** System Windows 7 lub nowszy.

- **System Windows Server.** System Windows Server 2008 R2 lub nowszy.

- **macOS.** macOS X lub nowsza

- **Android i iOS.** Najnowsza wersja systemów operacyjnych Android i iOS

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwany system operacyjny.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Upewnij się, że urządzenie jest przyłączone do sieci
Jeśli zostanie wyświetlony komunikat z informacją, że urządzenie **nie** jest zgodne z zasadami dostępu w organizacji, upewnij się, że urządzenie zostało dołączone do sieci organizacji.

![Komunikat o błędzie związany z tym, czy jesteś w sieci](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Aby sprawdzić, czy urządzenie jest przyłączone do sieci
1. Zaloguj się do systemu Windows przy użyciu konta służbowego. Na przykład alain@contoso.com.

2. Połącz się z siecią organizacji za pośrednictwem wirtualnej sieci prywatnej (VPN) lub funkcji DirectAccess.

3. Po nawiązaniu połączenia naciśnij klawisz **logo systemu Windows + L** , aby zablokować urządzenie.

4. Odblokuj urządzenie przy użyciu konta służbowego, a następnie spróbuj ponownie uzyskać dostęp do problematycznej aplikacji lub usługi.

    Jeśli zobaczysz komunikat o błędzie **nie można uzyskać z tego miejsca** , wybierz link **więcej szczegółów** , a następnie skontaktuj się z pomocą techniczną, podając szczegóły.

### <a name="to-join-your-device-to-your-network"></a>Aby dołączyć urządzenie do sieci
Jeśli urządzenie nie jest przyłączone do sieci organizacji, możesz wykonać jedną z dwóch czynności:

- **Dołącz do urządzenia służbowego.** Dołącz do sieci swojej organizacji swoje urządzenie z systemem Windows 10, aby uzyskać dostęp do zasobów, które mogą być ograniczone. Aby uzyskać więcej informacji oraz instrukcje krok po kroku, zobacz sekcję [Przyłącz urządzenie służbowe do sieci organizacji](user-help-join-device-on-network.md).

- **Zarejestruj swoje urządzenie osobiste w celu pracy.** Zarejestrowanie urządzenia osobistego, zazwyczaj telefonu lub tabletu, w sieci organizacji. Po zarejestrowaniu urządzenia będzie można uzyskać dostęp do zasobów ograniczonych w organizacji. Aby uzyskać więcej informacji oraz instrukcje krok po kroku, zobacz temat [Rejestrowanie urządzenia osobistego w sieci organizacji](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Następne kroki
- [Co to jest portal aplikacji?](./my-apps-portal-end-user-access.md)

- [Logowanie się za pomocą telefonu zamiast hasła](user-help-auth-app-sign-in.md)