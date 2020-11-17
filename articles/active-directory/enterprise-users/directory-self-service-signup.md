---
title: Samoobsługowe rejestrowanie się w przypadku użytkowników zweryfikowanych pocztą e-mail — Azure AD | Microsoft Docs
description: Korzystanie z samoobsługowego rejestrowania w organizacji usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0edd85ea4478ac95ea854b73717056d52a9098b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647566"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Co to jest samoobsługowe rejestrowanie się w Azure Active Directory?

W tym artykule wyjaśniono, jak za pomocą samoobsługowego tworzenia konta zapełnić organizację w usłudze Azure Active Directory (Azure AD). Jeśli chcesz przejąć nazwę domeny z niezarządzanej organizacji usługi Azure AD, zobacz sekcję [przejmowanie niezarządzanego katalogu jako administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Dlaczego warto korzystać z samoobsługowego rejestrowania?

* Uzyskaj klientom szybsze usługi
* Tworzenie ofert opartych na wiadomościach e-mail dla usługi
* Tworzenie przepływów rejestrowania na podstawie poczty e-mail, które szybko umożliwiają użytkownikom tworzenie tożsamości przy użyciu łatwych do zapamiętania aliasów poczty e-mail
* Samoobsługowy katalog usługi Azure AD można włączyć w katalogu zarządzanym, który może być używany przez inne usługi.

## <a name="terms-and-definitions"></a>Warunki i definicje

* **Rejestracja samoobsługowa**: jest to metoda, za pomocą której użytkownik loguje się do usługi w chmurze i ma tożsamość automatycznie utworzona dla nich w usłudze Azure AD w oparciu o swoją domenę poczty e-mail.
* **Niezarządzany katalog usługi Azure AD**: jest to katalog, w którym jest tworzona tożsamość. Katalog niezarządzany jest katalogiem, który nie ma administratora globalnego.
* **Zweryfikowany adres e-mail użytkownika**: to jest typ konta użytkownika w usłudze Azure AD. Użytkownik, który ma tożsamość utworzoną automatycznie po zarejestrowaniu się w celu samoobsługowego oferowania, jest znany jako użytkownik zweryfikowany pocztą e-mail. Użytkownik zweryfikowany przez e-mail jest regularnym członkiem katalogu oznaczonego za pomocą creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak mogę kontrolować samoobsługowe ustawienia?

Administratorzy mają dwie obecnie dostępne kontrolki samoobsługowe. Mogą kontrolować, czy:

* Użytkownicy mogą dołączać do katalogu za pośrednictwem poczty e-mail
* Użytkownicy mogą sama licencjonować za aplikacje i usługi

### <a name="how-can-i-control-these-capabilities"></a>Jak można kontrolować te możliwości?

Administrator może skonfigurować te funkcje przy użyciu następującego polecenia cmdlet usługi Azure AD Set-MsolCompanySettings parametry:

* **AllowEmailVerifiedUsers** kontroluje, czy użytkownik może utworzyć katalog lub dołączyć do niego. Jeśli ustawisz ten parametr na $false, żaden użytkownik zweryfikowany przez e-mail nie będzie mógł dołączyć do katalogu.
* **AllowAdHocSubscriptions** umożliwia użytkownikom wykonywanie rejestracji samoobsługowej. Jeśli ustawisz ten parametr na $false, żaden użytkownik nie będzie mógł wykonać rejestracji samoobsługowej.
  
AllowEmailVerifiedUsers i AllowAdHocSubscriptions to ustawienia dotyczące całego katalogu, które mogą być stosowane do katalogu zarządzanego lub niezarządzanego. Oto przykład, w którym:

* Katalog można administrować za pomocą zweryfikowanej domeny, takiej jak contoso.com
* Aby zaprosić użytkownika, który jeszcze nie istnieje ( userdoesnotexist@contoso.com ) w katalogu głównym contoso.com, używasz współpracy B2B z innego katalogu.
* Katalog macierzysty ma AllowEmailVerifiedUsers włączony

Jeśli powyższe warunki są spełnione, wówczas użytkownik będący członkiem jest tworzony w katalogu macierzystym, a w katalogu zapraszania jest tworzony użytkownik Gość B2B.

Aby uzyskać więcej informacji na temat usługi Flow i logowania w wersji próbnej usługi PowerApps, zobacz następujące artykuły:

* [Jak uniemożliwić istniejącym użytkownikom rozpoczynanie korzystania z usługi Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Usługa Flow w organizacji — pytania i odpowiedzi](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak formanty współpracują ze sobą?
Te dwa parametry mogą być używane w połączeniu do definiowania bardziej precyzyjnej kontroli nad rejestracją samoobsługową. Na przykład następujące polecenie umożliwi użytkownikom wykonywanie rejestracji samoobsługowej, ale tylko wtedy, gdy Ci użytkownicy mają już konto w usłudze Azure AD (innymi słowy, użytkownicy, którzy chcą utworzyć konto zweryfikowane pocztą e-mail do utworzenia, nie mogą wykonać rejestracji samoobsługowej):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Poniższy schemat blokowy wyjaśnia różne kombinacje tych parametrów oraz wyniki związane z katalogiem i samoobsługowym rejestracją.

![Schemat blokowy formantów rejestracji samoobsługowej](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Aby uzyskać więcej informacji i przykłady korzystania z tych parametrów, zobacz [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Następne kroki

* [Dodawanie nazwy domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Zamknij konto służbowe w niezarządzanym katalogu](users-close-account.md)