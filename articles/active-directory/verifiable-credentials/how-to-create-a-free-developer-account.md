---
title: Jak utworzyć bezpłatną dzierżawę Azure Active Directory dewelopera
description: W tym artykule pokazano, jak utworzyć konto dewelopera
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587269"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Jak utworzyć bezpłatną dzierżawę Azure Active Directory dewelopera

> [!IMPORTANT]
> Azure Active Directory Poświadczenia weryfikowalne są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> W wersji zapoznawczej wymagana jest licencja P2. 

Istnieją dwa proste sposoby tworzenia bezpłatnej licencji Azure Active Directory z licencją próbną P2, dzięki czemu można zainstalować usługę Weryfikowalny wystawca poświadczeń i przetestować tworzenie i weryfikowanie weryfikowalnych poświadczeń:

- [Dołącz](https://aka.ms/o365devprogram) do bezpłatnego Microsoft 365 Developer Program i uzyskaj bezpłatną piaskownicę, narzędzia i inne zasoby, takie jak Azure Active Directory z licencjami P2. Skonfigurowani użytkownicy, grupy, skrzynki pocztowe itp.
- Utwórz nową [dzierżawę i](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) [aktywuj](https://azure.microsoft.com/trial/get-started-active-directory/) bezpłatną wersję próbną usługi Azure AD — wersja Premium P1 lub P2 w nowej dzierżawie.

Jeśli zdecydujesz się zarejestrować się w celu bezpłatnego Microsoft 365 dewelopera, musisz wykonać kilka prostych kroków:

1. Kliknij przycisk **Dołącz teraz** na ekranie.

2. Zaloguj się przy użyciu nowego konta Microsoft lub użyj istniejącego (służbowego) konta, które już masz.

3. Na stronie rejestracji wybierz swój region, wprowadź nazwę firmy i zaakceptuj warunki i postanowienia programu przed kliknięciem przycisku **Dalej.**

4. Kliknij pozycję **Skonfiguruj subskrypcję**. Określ region, w którym chcesz utworzyć nową dzierżawę, utwórz nazwę użytkownika, domenę i wprowadź hasło. Spowoduje to utworzenie nowej dzierżawy i pierwszego administratora dzierżawy.

5. Wprowadź informacje o zabezpieczeniach potrzebne do ochrony konta administratora nowej dzierżawy. Spowoduje to konfigurację uwierzytelniania MFA dla konta.


W tym momencie utworzono dzierżawę z 25 licencjami użytkowników E5. Licencje E5 obejmują licencje P2 usługi Azure AD. Opcjonalnie możesz dodać przykładowe pakiety danych z użytkownikami, grupami, pocztą e-mail i programem SharePoint, aby ułatwić testowanie w środowisku dewelopera. W przypadku usługi wystawiania poświadczeń weryfikowalnych nie są one wymagane.

Dla wygody możesz dodać własne konto [](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) służbowe jako gościa w nowo utworzonej dzierżawie i użyć tego konta do administrowania dzierżawą. Jeśli chcesz, aby konto gościa było w stanie zarządzać usługą poświadczeń weryfikowalnych, musisz przypisać do tego użytkownika rolę "Administrator globalny".

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz konto dewelopera, możesz wypróbować nasz [pierwszy](get-started-verifiable-credentials.md) samouczek, aby dowiedzieć się więcej na temat weryfikowalnych poświadczeń.
