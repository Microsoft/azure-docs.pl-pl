---
title: Tworzenie bezpłatnej dzierżawy Azure Active Directory Developer
description: W tym artykule opisano sposób tworzenia konta dewelopera
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1e8bb59c09622a39dad680940ff34e643ee0cc3b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222745"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Tworzenie bezpłatnej dzierżawy Azure Active Directory Developer

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> W wersji zapoznawczej jest wymagana licencja P2. 

Istnieją dwa proste sposoby tworzenia bezpłatnej Azure Active Directory z licencją na wersję próbną P2, aby można było zainstalować zweryfikowaną usługę wystawcy poświadczeń i można testować tworzenie i weryfikowanie zweryfikowanych poświadczeń:

- [Dołącz](https://aka.ms/o365devprogram) do bezpłatnego programu Microsoft 365 dla deweloperów i uzyskaj bezpłatną piaskownicę, narzędzia i inne zasoby, takie jak Azure Active Directory z licencjami P2. Skonfigurowani użytkownicy, grupy, skrzynki pocztowe itp.
- Utwórz nową [dzierżawę](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) i aktywuj [bezpłatną wersję próbną](https://azure.microsoft.com/trial/get-started-active-directory/) Azure AD — wersja Premium P1 lub P2 w nowej dzierżawie.

Jeśli zdecydujesz się na zarejestrowanie się w celu uzyskania bezpłatnego programu Microsoft 365 dla deweloperów, musisz wykonać kilka prostych czynności:

1. Kliknij przycisk **Dołącz teraz** na ekranie.

2. Zaloguj się przy użyciu nowego konta Microsoft lub Użyj istniejącego konta (służbowego), które już istnieje.

3. Na stronie rejestracji Wybierz swój region, wprowadź nazwę firmy i zaakceptuj warunki i postanowienia programu przed kliknięciem **przycisku Dalej**.

4. Kliknij pozycję **Skonfiguruj subskrypcję**. Określ region, w którym chcesz utworzyć nową dzierżawę, Utwórz nazwę użytkownika, domenę i wprowadź hasło. Spowoduje to utworzenie nowej dzierżawy i pierwszego administratora dzierżawy.

5. Wprowadź informacje o zabezpieczeniach, które są konieczne do ochrony konta administratora nowej dzierżawy. Spowoduje to skonfigurowanie uwierzytelniania MFA dla konta.


W tym momencie utworzono dzierżawcę z 25 licencjami użytkownika E5. Licencje E5 obejmują licencje usługi Azure AD P2. Opcjonalnie można dodać przykładowe pakiety danych do użytkowników, grup, poczty i programu SharePoint, aby ułatwić testowanie w środowisku deweloperskim. W przypadku usługi wydawania poświadczeń do zweryfikowania nie są one wymagane.

Dla wygody użytkownika możesz dodać własne konto służbowe jako [gość](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) w nowo utworzonej dzierżawie i użyć tego konta do administrowania dzierżawcą. Jeśli chcesz, aby konto gościa mogło zarządzać zweryfikowaną usługą poświadczeń, musisz przypisać rolę "Administrator globalny" do tego użytkownika.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz konto dewelopera, możesz skorzystać z [pierwszego samouczka](get-started-verifiable-credentials.md) , aby dowiedzieć się więcej na temat zweryfikowanych poświadczeń.