---
title: Nie można uzyskać dostępu do tego błędu aplikacji firmowej przy użyciu aplikacji serwera proxy aplikacji
description: Jak rozwiązać typowe problemy z dostępem do aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0339e9f7688555b4d99c2d3255461b5675f642ff
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649555"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Błąd "nie można uzyskać dostępu do tej aplikacji firmowej" podczas korzystania z aplikacji serwera proxy aplikacji

Ten artykuł pomaga rozwiązywać typowe problemy dotyczące błędu "nie można uzyskać dostępu do tej aplikacji firmowej" w aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure.

## <a name="overview"></a>Omówienie

Gdy widzisz ten błąd, Znajdź kod stanu na stronie błędu. Ten kod jest prawdopodobnie jednym z następujących kodów stanu:

- **Limit czasu bramy**: usługa serwera proxy aplikacji nie może nawiązać połączenia z łącznikiem. Ten błąd zwykle wskazuje na problem z przypisaniem łącznika, samym łącznikiem lub regułami sieci wokół łącznika.
- **Zła Brama**: Łącznik nie może nawiązać połączenia z aplikacją zaplecza. Ten błąd może wskazywać na błędną konfigurację aplikacji.
- **Zabronione**: użytkownik nie ma uprawnień dostępu do aplikacji. Ten błąd może wystąpić, gdy użytkownik nie jest przypisany do aplikacji w Azure Active Directory lub jeśli zaplecze użytkownik nie ma uprawnień dostępu do aplikacji.

Aby znaleźć kod, spójrz na tekst w lewym dolnym rogu komunikatu o błędzie dla pola "kod stanu". Poszukaj również dodatkowych porad w dolnej części strony.

![Przykład: błąd limitu czasu bramy](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Aby uzyskać szczegółowe informacje na temat rozwiązywania problemów głównych przyczyn tych błędów oraz więcej szczegółowych informacji na temat sugerowanych poprawek, zobacz odpowiednią sekcję poniżej.

## <a name="gateway-timeout-errors"></a>Błędy przekroczenia limitu czasu bramy

Limit czasu bramy występuje, gdy usługa próbuje skontaktować się z łącznikiem i nie może w przedziale czasu. Ten błąd jest zwykle spowodowany przez aplikację przypisaną do grupy łączników bez łączników roboczych lub niektóre porty wymagane przez łącznik nie są otwarte.

## <a name="bad-gateway-errors"></a>Błędne błędy bramy

Nieprawidłowy błąd bramy wskazuje, że łącznik nie może nawiązać połączenia z aplikacją zaplecza. Upewnij się, że aplikacja została opublikowana. Typowe błędy, które powodują wystąpienie tego błędu:

- Literówka lub błąd w wewnętrznym adresie URL
- Nie publikuje katalogu głównego aplikacji. Na przykład publikowanie, `http://expenses/reimbursement` ale próba uzyskania dostępu `http://expenses`
- Problemy z konfiguracją ograniczonego delegowania protokołu Kerberos (KCD)
- Problemy z aplikacją zaplecza

## <a name="forbidden-errors"></a>Błędy zabronione

Jeśli widzisz błąd zabroniony, użytkownik nie został przypisany do aplikacji. Ten błąd może być w Azure Active Directory lub w aplikacji zaplecza.

Aby dowiedzieć się, jak przypisać użytkowników do aplikacji na platformie Azure, zapoznaj się z [dokumentacją konfiguracyjną](application-proxy-add-on-premises-application.md#test-the-application).

Jeśli potwierdzasz, że użytkownik jest przypisany do aplikacji na platformie Azure, sprawdź konfigurację użytkownika w aplikacji zaplecza. Jeśli używasz ograniczonego delegowania protokołu Kerberos/zintegrowanego uwierzytelniania systemu Windows, zobacz stronę Rozwiązywanie problemów z usługą KCD, aby uzyskać wskazówki.

## <a name="check-the-applications-internal-url"></a>Sprawdź wewnętrzny adres URL aplikacji

Pierwszym krokiem jest podwójne sprawdzenie i poprawienie wewnętrznego adresu URL przez otworzenie aplikacji za pomocą **aplikacji dla przedsiębiorstw**, a następnie wybranie menu **serwera proxy aplikacji** . Upewnij się, że wewnętrzny adres URL jest używany w sieci lokalnej w celu uzyskania dostępu do aplikacji.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Sprawdź, czy aplikacja jest przypisana do grupy łączników roboczych

Aby sprawdzić, czy aplikacja jest przypisana do grupy łączników roboczych:

1. Otwórz aplikację w portalu, przechodząc do **Azure Active Directory**, klikając pozycję **aplikacje dla przedsiębiorstw**, a następnie **wszystkie aplikacje.** Otwórz aplikację, a następnie wybierz pozycję **serwer proxy aplikacji** w menu po lewej stronie.
1. Sprawdź pole Grupa łączników. Jeśli w grupie nie ma aktywnych łączników, zostanie wyświetlone ostrzeżenie. Jeśli nie widzisz żadnych ostrzeżeń, przejdź do, aby sprawdzić, czy wszystkie [wymagane porty](application-proxy-add-on-premises-application.md) są dozwolone.
1. Jeśli pokazywana jest niewłaściwa Grupa łączników, Użyj listy rozwijanej, aby wybrać poprawną grupę i potwierdzić, że nie są już wyświetlane żadne ostrzeżenia. Jeśli zostanie wyświetlona zamierzona Grupa łączników, kliknij komunikat ostrzegawczy, aby otworzyć stronę z zarządzaniem łącznikiem.
1. W tym miejscu istnieje kilka sposobów szczegółowego przechodzenia do szczegółów:

   - Przenieś aktywny Łącznik do grupy: Jeśli masz aktywny łącznik, który powinien należeć do tej grupy i ma linię wglądu w docelową aplikację zaplecza, możesz przenieść Łącznik do przypisanej grupy. Aby to zrobić, kliknij łącznik. W polu "Grupa łączników" Użyj listy rozwijanej, aby wybrać poprawną grupę, a następnie kliknij przycisk Zapisz.
   - Pobierz nowy łącznik dla tej grupy: na tej stronie można pobrać link umożliwiający [pobranie nowego łącznika](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Zainstaluj łącznik na komputerze z bezpośrednią linią wglądu w aplikację zaplecza. Zwykle łącznik jest instalowany na tym samym serwerze co aplikacja. Użyj linku Pobierz łącznik, aby pobrać łącznik na maszynę docelową. Następnie kliknij łącznik i Użyj listy rozwijanej "Grupa łączników", aby upewnić się, że należy ona do odpowiedniej grupy.
   - Zbadaj nieaktywny łącznik: Jeśli łącznik jest wyświetlany jako nieaktywny, nie może nawiązać połączenia z usługą. Ten błąd jest zwykle spowodowany blokadą niektórych wymaganych portów. Aby rozwiązać ten problem, należy przejść do, aby sprawdzić, czy wszystkie wymagane porty są dozwolone.

Po zastosowaniu tych kroków, aby upewnić się, że aplikacja jest przypisana do grupy z łącznikami roboczymi, Przetestuj aplikację ponownie. Jeśli nadal nie działa, przejdź do następnej sekcji.

## <a name="check-all-required-ports-are-open"></a>Sprawdź, czy wszystkie wymagane porty są otwarte

Sprawdź, czy wszystkie wymagane porty są otwarte. W przypadku wymaganych portów Zobacz sekcję otwieranie portów [w samouczku: Dodawanie aplikacji lokalnej dla dostępu zdalnego za pomocą serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md). Jeśli wszystkie wymagane porty są otwarte, przejdź do następnej sekcji.

## <a name="check-for-other-connector-errors"></a>Sprawdź inne błędy łącznika

Jeśli żaden z powyższych elementów nie rozwiąże problemu, następnym krokiem jest wyszukanie problemów lub błędów za pomocą łącznika. Niektóre typowe błędy można zobaczyć w [dokumencie Rozwiązywanie problemów](./application-proxy-troubleshoot.md#connector-errors).

Możesz również przejrzeć dzienniki łączników, aby zidentyfikować wszelkie błędy. Wiele komunikatów o błędach udostępnia konkretne zalecenia dotyczące poprawek. Aby wyświetlić dzienniki, zobacz [dokumentację łączników](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Dodatkowe rozwiązania

Jeśli powyższe nie rozwiąże problemu, istnieje kilka różnych możliwych przyczyn. Aby zidentyfikować problem:

Jeśli aplikacja jest skonfigurowana do korzystania ze zintegrowanego uwierzytelniania systemu Windows (IWA), Przetestuj aplikację bez rejestracji jednokrotnej. Jeśli nie, przejdź do następnego akapitu. Aby sprawdzić aplikację bez rejestracji jednokrotnej, Otwórz aplikację za pomocą **aplikacji dla przedsiębiorstw** i przejdź do menu **Logowanie** jednokrotne. Zmień listę rozwijaną z "zintegrowane uwierzytelnianie systemu Windows" na "Logowanie jednokrotne w usłudze Azure AD".

Teraz otwórz przeglądarkę i spróbuj ponownie uzyskać dostęp do aplikacji. Powinien zostać wyświetlony monit o uwierzytelnienie i zapoznaj się z aplikacją. Jeśli użytkownik może się uwierzytelnić, ten problem dotyczy konfiguracji ograniczonego delegowania protokołu Kerberos (KCD), która umożliwia logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz stronę Rozwiązywanie problemów z KCD.

Jeśli ten błąd będzie nadal występować, przejdź do komputera, na którym jest zainstalowany łącznik, Otwórz przeglądarkę i spróbuj uzyskać dostęp do wewnętrznego adresu URL używanego dla aplikacji. Łącznik działa jak inny klient z tego samego komputera. Jeśli nie możesz skontaktować się z aplikacją, sprawdź, dlaczego komputer nie może nawiązać połączenia z aplikacją, lub użyj łącznika na serwerze, który jest w stanie uzyskać dostęp do aplikacji.

Jeśli możesz uzyskać dostęp do aplikacji z tej maszyny, aby wyszukać problemy lub błędy za pomocą łącznika. Niektóre typowe błędy można zobaczyć w [dokumencie Rozwiązywanie problemów](application-proxy-troubleshoot.md#connector-errors). Możesz również przejrzeć dzienniki łączników, aby zidentyfikować wszelkie błędy. Wiele z naszych komunikatów o błędach może udostępnić bardziej szczegółowe zalecenia dotyczące poprawek. Aby dowiedzieć się, jak wyświetlić dzienniki, zapoznaj się z [dokumentacją dotyczącą łączników](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Następne kroki

[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)