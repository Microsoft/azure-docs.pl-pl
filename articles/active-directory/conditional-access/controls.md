---
title: Niestandardowe kontrolki dostępu warunkowego usługi Azure AD
description: Dowiedz się, jak kontrolki niestandardowe w Azure Active Directory dostęp warunkowy.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837587"
---
# <a name="custom-controls-preview"></a>Formanty niestandardowe (wersja zapoznawcza)

Kontrolki niestandardowe są funkcją w wersji zapoznawczej Azure Active Directory. W przypadku korzystania z kontrolek niestandardowych użytkownicy są przekierowywani do zgodnej usługi, aby spełnić wymagania dotyczące uwierzytelniania poza Azure Active Directory. W celu zapewnienia zgodności z tą kontrolą przeglądarka użytkownika zostanie przekierowana do usługi zewnętrznej, wykonuje wymagane uwierzytelnianie i zostanie przekierowana z powrotem do Azure Active Directory. Azure Active Directory weryfikuje odpowiedź i, jeśli użytkownik został pomyślnie uwierzytelniony lub zweryfikowany, użytkownik kontynuuje w przepływie dostępu warunkowego.

> [!NOTE]
> Aby uzyskać więcej informacji o zmianach, które są planowane dla niestandardowej kontroli, zapoznaj się z archiwum luty 2020, [Aby](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)poznać nowości.

## <a name="creating-custom-controls"></a>Tworzenie niestandardowych formantów

Niestandardowe kontrolki działają z ograniczonym zestawem zatwierdzonych dostawców uwierzytelniania. Aby utworzyć kontrolkę niestandardową, należy najpierw skontaktować się z dostawcą, którego chcesz użyć. Każdy dostawca firmy innej niż Microsoft ma własny proces i wymagania, aby zarejestrować się, subskrybować lub w inny sposób stać się częścią usługi i wskazać, że chcesz zintegrować się z dostępem warunkowym. W tym momencie dostawca dostarczy blok danych w formacie JSON. Te dane umożliwiają dostawcy i dostęp warunkowy współdziałanie dla dzierżawy, tworzy nową kontrolkę i definiuje, jak dostęp warunkowy może stwierdzić, czy użytkownicy pomyślnie przeprowadzili weryfikację u dostawcy.

Skopiuj dane JSON, a następnie wklej je do powiązanego pola tekstowego. Nie wprowadzaj żadnych zmian w pliku JSON, chyba że jawnie zrozumiesz wprowadzaną zmianę. Wprowadzenie jakichkolwiek zmian może spowodować przerwanie połączenia między dostawcą i firmą Microsoft oraz zablokowanie użytkownika i użytkowników poza kontami.

Opcja tworzenia kontrolki niestandardowej znajduje się w sekcji **Zarządzanie** na stronie **dostęp warunkowy** .

![Interfejs formantów niestandardowych w dostępie warunkowym](./media/controls/custom-controls-conditional-access.png)

Kliknięcie przycisku **Nowy formant niestandardowy** powoduje otwarcie bloku z polem tekstowym dla danych JSON formantu.  

![Nowa kontrolka niestandardowa](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Usuwanie formantów niestandardowych

Aby usunąć kontrolkę niestandardową, należy najpierw upewnić się, że nie jest ona używana w żadnym z zasad dostępu warunkowego. Po ukończeniu:

1. Przejdź do listy formantów niestandardowych
1. Kliknij przycisk...  
1. Wybierz pozycję **Usuń**.

## <a name="editing-custom-controls"></a>Edytowanie formantów niestandardowych

Aby edytować kontrolkę niestandardową, należy usunąć bieżącą kontrolkę i utworzyć nową kontrolkę z zaktualizowanymi informacjami.

## <a name="known-limitations"></a>Znane ograniczenia

Kontrolki niestandardowe nie mogą być używane z automatyzacją usługi Identity Protection wymagającą usługi Azure AD Multi-Factor Authentication, samoobsługowego resetowania hasła usługi Azure AD (SSPR), spełniając wymagania dotyczące uwierzytelniania wieloskładnikowego w celu podniesienia poziomu ról w programie Privileged Identity Manager (PIM) w ramach rejestracji urządzeń w usłudze Intune lub dołączania urządzeń do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)

- [Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
