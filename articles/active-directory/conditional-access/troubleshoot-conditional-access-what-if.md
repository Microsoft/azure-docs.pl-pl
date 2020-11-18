---
title: Rozwiązywanie problemów z dostępem warunkowym za pomocą narzędzia What If — Azure Active Directory
description: Gdzie można znaleźć zasady dostępu warunkowego, które zostały zastosowane i dlaczego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837250"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Rozwiązywanie problemów z dostępem warunkowym przy użyciu narzędzia What If

[Narzędzie What If](what-if-tool.md) w dostępie warunkowym jest zaawansowane podczas próby zrozumienia, dlaczego zasady zostały lub nie zostały zastosowane do użytkownika w konkretnym przypadku lub jeśli zasady byłyby stosowane w znanym stanie.

Narzędzie What If znajduje się w **Azure Portal**  >  **Azure Active Directory**  >  **dostęp warunkowy**  >  **What If**.

![Narzędzie What If dostępu warunkowego w stanie domyślnym](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Narzędzie What If obecnie nie szacuje zasad w trybie tylko do raportowania.

## <a name="gathering-information"></a>Zbieranie informacji

Narzędzie What If wymaga tylko **użytkownika** , aby rozpocząć pracę. 

Następujące dodatkowe informacje są opcjonalne, ale pomogą w zawężaniu zakresu dla określonych przypadków.

* Aplikacje w chmurze lub akcje
* Adres IP 
* Kraj/region
* Platforma urządzeń
* Aplikacje klienckie (wersja zapoznawcza)
* Stan urządzenia (wersja zapoznawcza) 
* Ryzyko związane z logowaniem

Te informacje można zbierać od użytkownika, urządzenia lub dziennika logowania usługi Azure AD.

## <a name="generating-results"></a>Generowanie wyników

Wprowadź kryteria zebrane w poprzedniej sekcji i wybierz **What If** , aby wygenerować listę wyników. 

W dowolnym momencie możesz wybrać opcję **Zresetuj** , aby wyczyścić wszystkie dane wejściowe kryteriów i powrócić do stanu domyślnego.

## <a name="evaluating-results"></a>Ocenianie wyników

### <a name="policies-that-will-apply"></a>Zasady, które będą stosowane

Na tej liście zostaną wyświetlone zasady dostępu warunkowego, które mają zastosowanie w danym warunku. Lista będzie zawierać zarówno kontrolki Grant, jak i Session, które mają zastosowanie. Przykłady obejmują wymaganie uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do określonej aplikacji.

### <a name="policies-that-will-not-apply"></a>Zasady, które nie będą stosowane

Zostanie wyświetlona lista zasad dostępu warunkowego, które nie będą miały zastosowania w przypadku zastosowania warunków. Lista będzie zawierać wszelkie zasady i powód, dla których nie mają zastosowania. Przykłady obejmują użytkowników i grupy, które mogą być wykluczone z zasad.

## <a name="use-case"></a>Przypadek użycia

Wiele organizacji tworzy zasady w oparciu o lokalizacje sieciowe, umożliwiając dostęp do zaufanych lokalizacji i lokalizacji blokowania, w których nie powinien się pojawić.

Aby sprawdzić, czy konfiguracja została wprowadzona prawidłowo, administrator może użyć narzędzia What If, aby naśladować dostęp, z lokalizacji, która powinna być dozwolona, i z lokalizacji, która powinna zostać odrzucona.

[![Narzędzie What If pokazujące wyniki z dostępem do bloku](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

W takim przypadku użytkownikowi zablokowano dostęp do dowolnej aplikacji w chmurze w podróży do Korei Północnej, ponieważ firma Contoso zablokował dostęp z tej lokalizacji.

Ten test można rozszerzyć, aby uwzględnić inne punkty danych w celu zawężenia zakresu.

## <a name="next-steps"></a>Następne kroki

* [Co to jest dostęp warunkowy?](overview.md)
* [Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [Co to jest tożsamość urządzenia?](../devices/overview.md)
* [Jak to działa: usługa Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)