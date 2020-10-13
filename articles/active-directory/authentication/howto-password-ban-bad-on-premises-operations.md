---
title: Włącz lokalną ochronę hasłem usługi Azure AD
description: Dowiedz się, jak włączyć ochronę hasłem w usłudze Azure AD dla środowiska lokalnego Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d72aa65f18896ebbecf651da32558059ba782cc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968399"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Włącz ochronę hasłem Azure Active Directory lokalnych

Użytkownicy często tworzą hasła używające zwykłych słów lokalnych, takich jak szkoła, zespół sportowy lub osoba sławę. Hasła te są łatwe do odgadnięcia i są słabe przed atakami opartymi na słownikach. Aby wymusić silne hasła w organizacji, usługa Ochrona hasłem w usłudze Azure Active Directory (Azure AD) zapewnia globalną i niestandardową listę wykluczonych haseł. Żądanie zmiany hasła nie powiedzie się, jeśli na liście wykluczone hasła znajduje się dopasowanie.

Aby chronić lokalne środowisko Active Directory Domain Services (AD DS), możesz zainstalować i skonfigurować ochronę hasłem usługi Azure AD, aby współpracowały z Premium kontrolerem domeny. W tym artykule pokazano, jak włączyć ochronę hasłem w usłudze Azure AD dla środowiska lokalnego.

Aby uzyskać więcej informacji na temat sposobu działania ochrony haseł usługi Azure AD w środowisku lokalnym, zobacz [Jak wymusić ochronę hasłem usługi Azure AD dla systemu Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule pokazano, jak włączyć ochronę hasłem w usłudze Azure AD dla środowiska lokalnego. Przed ukończeniem tego artykułu [Zainstaluj i Zarejestruj usługę serwera proxy ochrony hasłem usługi Azure AD i agentów kontrolerów domeny](howto-password-ban-bad-on-premises-deploy.md) w lokalnym środowisku AD DS.

## <a name="enable-on-premises-password-protection"></a>Włącz ochronę hasłem lokalnym

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Azure Active Directory**  >  **Security**  >  **metod uwierzytelniania**zabezpieczeń  >  **Ochrona hasłem**.
1. Ustaw opcję **Włącz ochronę hasłem w systemie Windows Server Active Directory** na *wartość tak*.

    Jeśli to ustawienie ma wartość *nie*, wszystkie wdrożone Agenty usługi Azure AD Password Protection są w trybie quiescent, w którym wszystkie hasła są akceptowane jako-is. Nie są wykonywane żadne działania weryfikacyjne, a zdarzenia inspekcji nie są generowane.

1. Zaleca się wstępne ustawienie **trybu** *inspekcji*. Po zalogowaniu się do funkcji i wpływu na użytkowników w organizacji możesz przełączyć **tryb** na *wymuszone*. Aby uzyskać więcej informacji, zobacz następującą sekcję w obszarze [tryby działania](#modes-of-operation).
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

    [![Włącz lokalną ochronę hasłem w metodach uwierzytelniania w Azure Portal](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Tryby operacyjne

Po włączeniu ochrony lokalnego hasła usługi Azure AD można użyć trybu *inspekcji* lub trybu *wymuszania* . Zalecamy, aby początkowe wdrożenie i testy były zawsze uruchamiane w trybie inspekcji. Wpisy w dzienniku zdarzeń powinny być następnie monitorowane w celu przewidywania, czy jakiekolwiek istniejące procesy operacyjne byłyby zakłócone po włączeniu trybu *wymuszania* .

### <a name="audit-mode"></a>Tryb inspekcji

Tryb *inspekcji* jest przeznaczony do uruchamiania oprogramowania w trybie "co jeśli". Każda usługa agenta DC ochrony haseł usługi Azure AD szacuje hasło przychodzące zgodnie z aktualnie aktywnymi zasadami.

Jeśli bieżące zasady zostały skonfigurowane tak, aby były w trybie inspekcji, komunikaty o nieprawidłowym haśle powodują wyświetlenie komunikatów dziennika zdarzeń, ale są przetwarzane i aktualizowane. To zachowanie jest jedyną różnicą między trybem inspekcji i wymuszania. Wszystkie inne operacje działają tak samo.

### <a name="enforced-mode"></a>Tryb wymuszony

Tryb *wymuszony* jest zamierzony jako Konfiguracja końcowa. Podobnie jak w przypadku trybu inspekcji każda usługa agenta DC ochrony haseł usługi Azure AD oblicza hasła przychodzące zgodnie z aktualnie aktywnymi zasadami. Gdy tryb wymuszony jest włączony, hasło uznawane za niezabezpieczone zgodnie z zasadami jest odrzucane.

Jeśli hasło zostanie odrzucone w trybie wymuszonym przez agenta DC ochrony hasłem usługi Azure AD, użytkownik końcowy zobaczy podobny błąd, na przykład jeśli jego hasło zostało odrzucone przez tradycyjne lokalne wymuszanie złożoności hasła. Na przykład użytkownik może zobaczyć następujący tradycyjny komunikat o błędzie na ekranie logowania lub zmiany hasła systemu Windows:

*"Nie można zaktualizować hasła. Wartość podana dla nowego hasła nie spełnia wymagań dotyczących długości, złożoności lub historii domeny ".*

Ten komunikat to tylko jeden przykład z kilku możliwych wyników. Konkretny komunikat o błędzie może się różnić w zależności od rzeczywistego oprogramowania lub scenariusza, który podejmuje próbę ustawienia niezabezpieczonego hasła.

Użytkownicy końcowi mający znaczenie mogą potrzebować współpracy z pracownikami IT w celu zrozumienia nowych wymagań i wybrania bezpiecznych haseł.

> [!NOTE]
> Ochrona hasłem w usłudze Azure AD nie kontroluje określonego komunikatu o błędzie wyświetlanego przez komputer kliencki w przypadku odrzucenia słabego hasła.

## <a name="next-steps"></a>Następne kroki

Aby dostosować listę wykluczonych haseł dla swojej organizacji, zobacz sekcję [Konfigurowanie niestandardowej niedozwolonego hasła usługi Azure AD Password Protection](tutorial-configure-custom-password-protection.md).

Aby monitorować zdarzenia Premium, zobacz [monitorowanie Premium usługi Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
