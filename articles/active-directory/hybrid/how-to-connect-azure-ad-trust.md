---
title: Azure AD Connect — Zarządzanie zaufaniem AD FS za pomocą usługi Azure AD przy użyciu Azure AD Connect | Microsoft Docs
description: Szczegóły operacyjne obsługi zaufania usługi Azure AD za pomocą programu Azure AD Connect.
keywords: AD FS, ADFS, zarządzanie AD FS, usługi AAD Connect, Connect, Azure AD, Trust, AAD, Claim, Claim, reguły dotyczące roszczeń, wystawianie, przekształcanie, reguły, tworzenie kopii zapasowej, przywracanie
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85360099"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Zarządzania relacjami zaufania usługi AD FS dla usługi Azure AD za pomocą usługi Azure AD Connect

## <a name="overview"></a>Omówienie

Azure AD Connect może zarządzać Federacją między lokalnymi Active Directory usługa federacyjna (AD FS) i usługą Azure AD. Ten artykuł zawiera omówienie:

* Różne ustawienia skonfigurowane w ramach relacji zaufania przez Azure AD Connect
* Reguły przekształcania wystawiania (reguły dotyczące roszczeń) ustawione przez Azure AD Connect
* Jak utworzyć kopię zapasową i przywrócić reguły dotyczące roszczeń między uaktualnieniami a aktualizacjami konfiguracji. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Ustawienia kontrolowane przez Azure AD Connect

Azure AD Connect zarządza **tylko** ustawieniami związanymi z relacją zaufania usługi Azure AD. Azure AD Connect nie modyfikuje żadnych ustawień innych relacji zaufania jednostki uzależnionej w AD FS. Poniższa tabela zawiera ustawienia, które są kontrolowane przez Azure AD Connect.

| Ustawienie | Opis |
| :--- | :--- |
| Certyfikat podpisywania tokenu | Azure AD Connect może służyć do resetowania i ponownego tworzenia relacji zaufania z usługą Azure AD. Azure AD Connect wykonuje jednorazowe natychmiastowe Przerzucanie certyfikatów podpisywania tokenów dla AD FS i aktualizuje ustawienia Federacji domeny usługi Azure AD.|
| Algorytm podpisywania tokenu | Firma Microsoft zaleca korzystanie z algorytmu SHA-256 jako algorytm podpisywania tokenu. Azure AD Connect może wykryć, czy algorytm podpisywania tokenu jest ustawiony na wartość mniej bezpieczną niż algorytm SHA-256. Spowoduje to zaktualizowanie ustawienia do algorytmu SHA-256 w następnej możliwej operacji konfiguracji. Inne zaufanie jednostki uzależnionej musi zostać zaktualizowane, aby można było używać nowego tokenu certyfikatu podpisywania. |
| Identyfikator zaufania usługi Azure AD | Azure AD Connect ustawia poprawną wartość identyfikatora dla zaufania usługi Azure AD. AD FS jednoznacznie identyfikuje zaufanie usługi Azure AD przy użyciu wartości identyfikatora. |
| Punkty końcowe usługi Azure AD | Azure AD Connect upewnij się, że punkty końcowe skonfigurowane dla zaufania usługi Azure AD są zawsze zgodnie z najnowszymi zalecanymi wartościami dotyczącymi odporności i wydajności. |
| Reguły przekształcania wystawiania | Istnieją pewne reguły dotyczące reguł, które są zbędne w celu uzyskania optymalnej wydajności funkcji usługi Azure AD w ustawieniu federacyjnym. Azure AD Connect upewnij się, że relacja zaufania usługi Azure AD jest zawsze konfigurowana z właściwym zestawem zalecanych reguł dotyczących roszczeń. |
| Identyfikator alternatywny | Jeśli synchronizacja jest skonfigurowana do używania identyfikatora alternatywnego, Azure AD Connect konfiguruje AD FS do uwierzytelniania przy użyciu identyfikatora alternatywnego. |
| Automatyczna aktualizacja metadanych | Relacja zaufania z usługą Azure AD jest konfigurowana do automatycznej aktualizacji metadanych. AD FS okresowo sprawdza metadane zaufania usługi Azure AD i zachowuje aktualność w przypadku zmiany na stronie usługi Azure AD. |
| Zintegrowane uwierzytelnianie systemu Windows (IWA) | Podczas operacji hybrydowego sprzężenia usługi Azure AD IWA jest włączona na potrzeby rejestracji urządzeń w celu usprawnienia hybrydowego przyłączania do usługi Azure AD dla urządzeń niskiego poziomu |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Przepływy wykonywania i ustawienia federacyjne skonfigurowane przez Azure AD Connect

Program Azure AD Connect nie aktualizuje wszystkich ustawień zaufania usługi Azure AD podczas przepływów konfiguracji. Zmodyfikowane ustawienia zależą od tego, który przepływ zadania lub wykonywania jest wykonywany. Poniższa tabela zawiera listę ustawień, na które mają wpływ różne przepływy wykonania.

| Przepływ wykonywania | Ustawienia, których dotyczy problem |
| :--- | :--- |
| Instalacja pierwszego przebiegu (Express) | Brak |
| Instalacja pierwszego przebiegu (Nowa Farma AD FS) | Zostanie utworzona nowa Farma AD FS i zostanie utworzona relacja zaufania z usługą Azure AD od podstaw. |
| Instalacja pierwszego przebiegu (istniejąca Farma AD FS, istniejąca relacja zaufania usługi Azure AD) | Identyfikator zaufania usługi Azure AD, reguły przekształcania wystawiania, punkty końcowe usługi Azure AD, alternatywny identyfikator (w razie potrzeby), automatyczna aktualizacja metadanych |
| Zresetuj relację zaufania usługi Azure AD | Certyfikat podpisywania tokenu, algorytm podpisywania tokenu, identyfikator zaufania usługi Azure AD, reguły przekształcania wystawiania, punkty końcowe usługi Azure AD, alternatywny identyfikator (w razie potrzeby), automatyczna aktualizacja metadanych |
| Dodaj serwer federacyjny | Brak |
| Dodawanie serwera WAP | Brak |
| Opcje urządzenia | Reguły przekształcania wystawiania, IWA na potrzeby rejestracji urządzeń |
| Dodawanie domeny federacyjnej | Jeśli domena jest dodawana po raz pierwszy, oznacza to, że Instalator zmienia się z Federacji jednodomenowej na Federacji wielodomenowej — Azure AD Connect spowoduje odtworzenie zaufania od podstaw. Jeśli relacja zaufania z usługą Azure AD jest już skonfigurowana dla wielu domen, modyfikowane są tylko reguły przekształcania wystawiania |
| Aktualizowanie protokołu TLS | Brak |

We wszystkich operacjach, w których wszystkie ustawienia są modyfikowane, Azure AD Connect wykonuje kopię zapasową bieżących ustawień zaufania w **%ProgramData%\AADConnect\ADFS**

![Strona Azure AD Connect wyświetlająca komunikat dotyczący istniejącej kopii zapasowej zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> W wersjach wcześniejszych niż 1.1.873.0 kopia zapasowa zawierała tylko reguły przekształcania wystawiania i ich kopia zapasowa została utworzona w pliku dziennika śledzenia kreatora.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Reguły przekształcania wystawiania ustawione przez Azure AD Connect

Azure AD Connect upewnij się, że relacja zaufania usługi Azure AD jest zawsze konfigurowana z właściwym zestawem zalecanych reguł dotyczących roszczeń. Firma Microsoft zaleca używanie programu Azure AD Connect do zarządzania relacją zaufania usługi Azure AD. Ta sekcja zawiera zestaw reguł przekształcania wystawiania i ich opis.

| Nazwa reguły | Opis |
| --- | --- |
| Wystaw nazwę UPN | Ta reguła wysyła zapytanie do wartości userPrincipalName z atrybutu skonfigurowanego w ustawieniach synchronizacji dla elementu userPrincipalName.|
| Zapytanie objectGUID i msdsconsistencyguid dla niestandardowego żądania ImmutableId | Ta reguła dodaje wartość tymczasową w potoku dla wartości objectGUID i msdsconsistencyguid, jeśli istnieje |
| Sprawdź obecność elementu msdsconsistencyguid | W zależności od tego, czy wartość dla msdsconsistencyguid istnieje, czy nie, ustawiamy tymczasową flagę, która ma być używana jako ImmutableId |
| Wydaj msdsconsistencyguid jako niezmienny identyfikator, jeśli istnieje | Wydaj msdsconsistencyguid jako ImmutableId, jeśli wartość istnieje |
| Problem objectGuidRule, jeśli reguła msdsConsistencyGuid nie istnieje | Jeśli wartość parametru msdsconsistencyguid nie istnieje, wartość objectGUID zostanie wystawiona jako ImmutableId |
| Problem z NameIdentifier | Ta reguła wystawia wartość dla żądania NameIdentifier.|
| Wystaw AccountType dla komputerów przyłączonych do domeny | Jeśli uwierzytelniana jednostka jest urządzeniem przyłączonym do domeny, ta reguła wystawia typ konta jako DJ oznacza urządzenie przyłączone do domeny |
| Wydaj element AccountType z wartością użytkownika, jeśli nie jest to konto komputera | Jeśli uwierzytelnianą jednostką jest użytkownik, ta reguła wystawia typ konta jako użytkownik |
| Wystaw issuerid, gdy nie jest to konto komputera | Ta reguła wystawia wartość issuerId, gdy jednostka uwierzytelniana nie jest urządzeniem. Wartość jest tworzona za pomocą wyrażenia regularnego, które jest konfigurowane przez Azure AD Connect. Wyrażenie regularne jest tworzone po uwzględnieniu wszystkich domen federacyjnych przy użyciu Azure AD Connect. |
| Problem z issuerid dla uwierzytelniania komputera z DJ | Ta reguła wystawia wartość issuerId, gdy uwierzytelnianą jednostką jest urządzenie |
| Problem z onpremobjectguid dla komputerów przyłączonych do domeny | Jeśli uwierzytelniana jednostka jest urządzeniem przyłączonym do domeny, ta reguła wystawia lokalny element objectGUID dla urządzenia |
| Przekazuj podstawowy identyfikator SID | Ta reguła wystawia podstawowy identyfikator SID jednostki uwierzytelniającej |
| Przekazywanie roszczeń — insideCorporateNetwork | Ta reguła wystawia wniosek, który pomaga usłudze Azure AD wiedzieć, czy uwierzytelnianie jest przychodzące z sieci firmowej lub zewnętrznie |
| Przekazywanie roszczeń — Psso |   |
| Wygeneruj oświadczenia o wygaśnięciu hasła | Ta reguła wystawia trzy oświadczenia dotyczące czasu wygaśnięcia hasła, liczbę dni, przez które hasło wygasa uwierzytelnianą jednostkę i adres URL, pod którym należy się kierować, aby zmienić hasło.|
| Przekazywanie roszczeń — authnmethodsreferences | Wartość w poświadczeniu wystawionym w ramach tej reguły wskazuje typ uwierzytelniania, który został wykonany dla jednostki |
| Przekazywanie roszczeń — multifactorauthenticationinstant | Wartość tego żądania określa czas w formacie UTC, kiedy użytkownik ostatnio wykonał uwierzytelnianie wieloskładnikowe. |
| Przekazywanie roszczeń — AlternateLoginID | Ta reguła wystawia wyjątek AlternateLoginID, jeśli uwierzytelnianie zostało wykonane przy użyciu alternatywnego identyfikatora logowania. |

> [!NOTE]
> Reguły oświadczeń dla nazw UPN i ImmutableId różnią się w zależności od opcji niedomyślnej podczas konfiguracji Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Przywróć reguły przekształcania wystawiania

Azure AD Connect w wersji 1.1.873.0 lub nowszej wykonuje kopię zapasową ustawień zaufania usługi Azure AD po każdej aktualizacji ustawień zaufania usługi Azure AD. Dla ustawień zaufania usługi Azure AD utworzono kopię zapasową w lokalizacji **%ProgramData%\AADConnect\ADFS**. Nazwa pliku ma następujący format: AadTrust- &lt; Date &gt; - &lt; Time &gt; . txt, na przykład AadTrust-20180710-150216.txt

![Zrzut ekranu przedstawiający przykład tworzenia kopii zapasowej zaufania usługi Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Reguły przekształcania wystawiania można przywrócić przy użyciu sugerowanych kroków poniżej.

1. Otwórz interfejs użytkownika zarządzania AD FS w programie Menedżer serwera
2. Otwórz właściwości zaufania usługi Azure AD, przechodząc do **AD FS jednostki &gt; uzależnionej zaufania &gt; Microsoft Office 365 tożsamość platformy Identity &gt; Edycja zasady wystawiania oświadczeń**
3. Kliknij pozycję **Dodaj regułę**
4. W szablonie reguły oświadczeń wybierz pozycję Wyślij oświadczenia przy użyciu reguły niestandardowej, a następnie kliknij przycisk **dalej** .
5. Skopiuj nazwę reguły dotyczącej roszczeń z pliku kopii zapasowej i wklej ją w polu **Nazwa reguły** dotyczącej roszczeń
6. Skopiuj regułę roszczeń z pliku kopii zapasowej do pola tekstowego dla **reguły niestandardowej** i kliknij przycisk **Zakończ** .

> [!NOTE]
> Upewnij się, że reguły dodatkowe nie powodują konfliktu z regułami skonfigurowanymi przez Azure AD Connect.

## <a name="next-steps"></a>Następne kroki
* [Zarządzanie Active Directory Federation Servicesami i dostosowywanie ich przy użyciu Azure AD Connect](how-to-connect-fed-management.md)
