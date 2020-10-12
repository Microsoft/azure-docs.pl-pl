---
title: Symulowanie wykrywania ryzyka w Azure AD Identity Protection
description: Dowiedz się, jak symulować wykrywanie zagrożeń w usłudze Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbcc2351b7ead3714cfd21aa30b031cec75074a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463760"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Symulowanie wykrywania ryzyka w usłudze Identity Protection

Administratorzy mogą chcieć symulować ryzyko w swoich środowiskach, aby osiągnąć następujące elementy:

- Wypełnianie danych w środowisku ochrony tożsamości poprzez symulowanie wykrywania ryzyka i luk w zabezpieczeniach.
- Skonfiguruj zasady dostępu warunkowego oparte na ryzyku i przetestuj wpływ tych zasad.

W tym artykule przedstawiono kroki umożliwiające symulowanie następujących typów wykrywania ryzyka:

- Anonimowy adres IP (prosty)
- Nieznane właściwości logowania (umiarkowane)
- Nietypowe podróże (trudne)

Inne wykrycia ryzyka nie mogą być symulowane w sposób bezpieczny.

Więcej informacji o każdym wykrywaniu ryzyka można znaleźć w artykule, [co jest ryzykowne](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonimowy adres IP

Wykonanie poniższej procedury wymaga zastosowania:

- [Przeglądarka sieci Tor](https://www.torproject.org/projects/torbrowser.html.en) do symulowania anonimowych adresów IP. Może być konieczne użycie maszyny wirtualnej, jeśli Twoja organizacja ogranicza użycie przeglądarki sieci Tor.
- Konto testowe, które nie zostało jeszcze zarejestrowane w usłudze Azure Multi-Factor Authentication.

**Aby zasymulować logowanie z anonimowego adresu IP, wykonaj następujące czynności**:

1. Korzystając z [przeglądarki tor](https://www.torproject.org/projects/torbrowser.html.en), przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Wprowadź poświadczenia konta, które mają być wyświetlane w raporcie **logowania z anonimowych adresów IP** .

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10-15 minut. 

## <a name="unfamiliar-sign-in-properties"></a>Nieznane właściwości logowania

Aby symulować nieznane lokalizacje, musisz zalogować się z lokalizacji i urządzenia, z którego konto testowe nie zalogował się przed.

Poniższa procedura używa nowo utworzonego:

- Połączenie sieci VPN, aby symulować nową lokalizację.
- Maszyna wirtualna, która symuluje nowe urządzenie.

Wykonanie poniższej procedury wymaga skorzystania z konta użytkownika, które ma:

- Co najmniej 30-dniowa historia logowania.
- Włączono usługę Azure Multi-Factor Authentication.

**Aby zasymulować logowanie z nieznanej lokalizacji, wykonaj następujące czynności**:

1. Podczas logowania się przy użyciu konta testowego niepowodzenie żądania uwierzytelniania wieloskładnikowego (MFA) przez nieprzekazywanie żądania MFA.
2. Korzystając z nowej sieci VPN, przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com) i wprowadź poświadczenia konta testowego.

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10-15 minut.

## <a name="atypical-travel"></a>Nietypowa podróż

Symulowanie nietypowego stanu podróży jest trudne, ponieważ algorytm używa uczenia maszynowego do oddzielenia fałszywych wartości false, takich jak nietypowe podróże ze znanych urządzeń lub logowania z sieci VPN używanych przez innych użytkowników w katalogu. Ponadto algorytm wymaga od 14 dni i 10 logowań użytkownika przed rozpoczęciem generowania wykrycia ryzyka. Ze względu na złożone modele uczenia maszynowego i powyższe reguły istnieje prawdopodobieństwo, że następujące kroki nie spowodują wykrycia ryzyka. W celu zasymulowania tego wykrywania można wykonać replikację tych kroków dla wielu kont usługi Azure AD.

**Aby symulować nietypowe wykrywanie ryzyka podróży, wykonaj następujące czynności**:

1. Korzystając z standardowej przeglądarki, przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Wprowadź poświadczenia konta, dla którego chcesz wygenerować nietypowe wykrywanie ryzyka podróży.
3. Zmień agenta użytkownika. Możesz zmienić agenta użytkownika w przeglądarce Microsoft Edge z Narzędzia deweloperskie (F12).
4. Zmień adres IP. Adres IP można zmienić przy użyciu sieci VPN, dodatku sieci Tor lub tworzenia nowej maszyny wirtualnej na platformie Azure w innym centrum danych.
5. Zaloguj się, aby [https://myapps.microsoft.com](https://myapps.microsoft.com) korzystać z tych samych poświadczeń co poprzednio i w ciągu kilku minut od momentu wcześniejszego zalogowania.

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 2-4 godzin.

## <a name="testing-risk-policies"></a>Zasady ryzyka dla testowania

Ta sekcja zawiera instrukcje dotyczące testowania użytkownika i zasad dotyczących ryzyka związanego z logowaniem utworzonych w artykule, [jak: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Zasady ryzyka związanego z użytkownikiem

Aby przetestować zasady zabezpieczeń dotyczące ryzyka użytkownika, wykonaj następujące czynności:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do omówienia usługi **Azure Active Directory**  >  **Security**  >  **Overview**.
1. Wybierz pozycję **Konfiguruj zasady ryzyka użytkownika**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1. **Warunki**  -  **Ryzyko użytkownika** Zalecenie firmy Microsoft polega na ustawieniu tej opcji na **wysoki**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają **na celu umożliwienie dostępu** i **wymaganie zmiany hasła**.
   1. **Wymuś zasady**  -  **Wyłączone**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd** .
1. Podnieś poziom ryzyka dla użytkownika przy użyciu konta testowego, na przykład symulacja jednego z wykrycia ryzyka kilka razy.
1. Poczekaj kilka minut, a następnie sprawdź, czy ryzyko zostało podwyższone do użytkownika. Jeśli nie, Symuluj więcej wykryć ryzyka dla użytkownika.
1. Wróć do zasad ryzyka i ustaw ustawienie **Wymuszaj zasady** na **włączone** i **Zapisz** zmiany zasad.
1. Teraz można testować dostęp warunkowy oparty na ryzyku, logując się przy użyciu użytkownika z podwyższonym poziomem ryzyka.

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń dotyczące zagrożeń związanych z logowaniem

Aby przetestować zasady dotyczące ryzyka związanego z logowaniem, wykonaj następujące czynności:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do omówienia usługi **Azure Active Directory**  >  **Security**  >  **Overview**.
1. Wybierz pozycję **Konfiguruj zasady dotyczące ryzyka związanego z logowaniem**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1. **Warunki**  -  **Ryzyko związane z logowaniem** Zalecenie firmy Microsoft polega na ustawieniu tej opcji na **średni i wyższy**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają na celu **umożliwienie dostępu** i **wymaganie uwierzytelniania wieloskładnikowego**.
   1. **Wymuś zasady**  -  **Na**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd** .
1. Teraz można testować dostęp warunkowy oparty na ryzyku, logując się przy użyciu ryzykownej sesji (na przykład przy użyciu przeglądarki tor). 

## <a name="next-steps"></a>Następne kroki

- [Co to jest ryzyko?](concept-identity-protection-risks.md)

- [Instrukcje: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md)

- [Ochrona tożsamości w usłudze Azure Active Directory](overview-identity-protection.md)
