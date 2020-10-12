---
title: Konfigurowanie serwera usługi MFA — Azure Active Directory
description: Dowiedz się, jak skonfigurować ustawienia dla serwera usługi Azure MFA w Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69733071c5b43ee9c8e6450e3a9924bc656d5c84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485609"
---
# <a name="configure-mfa-server-settings"></a>Konfigurowanie ustawień serwera usługi MFA

Ten artykuł ułatwia zarządzanie ustawieniami serwera usługi Azure MFA w Azure Portal.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego od użytkowników, powinni korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i generować poświadczenia aktywacji w zwykły sposób.

Dostępne są następujące ustawienia serwera MFA:

| Cecha | Opis |
| ------- | ----------- |
| Ustawienia serwera | Pobierz serwer usługi MFA i Wygeneruj poświadczenia aktywacji w celu zainicjowania środowiska |
| [Jednorazowe obejście](#one-time-bypass) | Zezwalaj użytkownikowi na uwierzytelnianie bez uwierzytelniania wieloskładnikowego przez ograniczony czas. |
| [Reguły buforowania](#caching-rules) |  Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku. |
| Stan serwera | Zobacz stan lokalnych serwerów usługi MFA, w tym wersji, stanu, adresu IP i daty ostatniej komunikacji. |

## <a name="one-time-bypass"></a>Jednorazowe obejście

Funkcja jednorazowe obejście umożliwia użytkownikowi uwierzytelnianie pojedynczego czasu bez przeprowadzania uwierzytelniania wieloskładnikowego. Obejście jest tymczasowe i wygasa po określonej liczbie sekund. W sytuacjach, gdy aplikacja mobilna lub telefon nie otrzymuje powiadomienia lub połączenia telefonicznego, można zezwolić na jednorazowe obejście, aby użytkownik mógł uzyskać dostęp do żądanego zasobu.

Aby utworzyć jednorazowe obejście, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie przejdź do usługi **Security**  >  **MFA**  >  **jednorazowe obejście**.
1. Wybierz pozycję **Dodaj**.
1. W razie potrzeby wybierz grupę replikacji dla obejścia.
1. Wprowadź nazwę użytkownika jako `username\@domain.com` . Wprowadź liczbę sekund, przez jaką obejście ma być ostatni i powód obejścia.
1. Wybierz pozycję **Dodaj**. Limit czasu zaczyna obowiązywać natychmiast. Użytkownik musi się zalogować przed upływem jednorazowego obejścia.

Możesz również wyświetlić raport jednorazowe obejście z tego samego okna.

## <a name="caching-rules"></a>Reguły buforowania

Można ustawić okres, aby zezwolić na próby uwierzytelniania po uwierzytelnieniu użytkownika przy użyciu funkcji _buforowania_ . Kolejne próby uwierzytelniania dla użytkownika w określonym przedziale czasu powiodło się automatycznie.

Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku.

>[!NOTE]
> Funkcja buforowania nie jest przeznaczona do użycia podczas logowania do Azure Active Directory (Azure AD).

Aby skonfigurować buforowanie, wykonaj następujące czynności:

1. Przejdź do **Azure Active Directory**  >  **Security**  >  **MFA**  >  **reguły buforowania**usługi MFA.
1. Wybierz pozycję **Dodaj**.
1. Z listy rozwijanej wybierz **Typ pamięci podręcznej** . Wprowadź maksymalną liczbę sekund w **pamięci podręcznej**.
1. W razie potrzeby wybierz typ uwierzytelniania i Określ aplikację.
1. Wybierz pozycję **Dodaj**.

## <a name="next-steps"></a>Następne kroki

Dodatkowe opcje konfiguracji serwera usługi MFA są dostępne z poziomu konsoli sieci Web serwera usługi MFA. [Serwer usługi Azure MFA można również skonfigurować pod kątem wysokiej dostępności](howto-mfaserver-deploy-ha.md).
