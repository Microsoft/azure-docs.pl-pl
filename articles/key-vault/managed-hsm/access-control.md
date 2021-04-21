---
title: Kontrola dostępu zarządzanego modułu HSM platformy Azure
description: Zarządzanie uprawnieniami dostępu do zarządzanego modułu HSM i kluczy platformy Azure. Obejmuje model uwierzytelniania i autoryzacji dla zarządzanego modułu HSM oraz sposób zabezpieczania modułów HSM.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750288"
---
# <a name="managed-hsm-access-control"></a>Kontrola dostępu zarządzanego modułu HSM

> [!NOTE]
> Key Vault zasobów obsługuje dwa typy zasobów: **magazyny** i **zarządzane moduły HSM.** Kontrola dostępu opisana w tym artykule ma zastosowanie tylko do **zarządzanych modułów HSM.** Aby dowiedzieć się więcej na temat kontroli dostępu do zarządzanego modułu HSM, zobacz Zapewnianie dostępu do kluczy Key Vault, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach [platformy Azure.](../general/rbac-guide.md)

Azure Key Vault HSM to usługa w chmurze, która chroni klucze szyfrowania. Ponieważ te dane są poufne i krytyczne dla działalności firmy, należy zabezpieczyć dostęp do zarządzanych modułów HSM, zezwalając na dostęp do nich tylko autoryzowanym aplikacjom i użytkownikom. Ten artykuł zawiera omówienie modelu kontroli dostępu zarządzanego modułu HSM. Wyjaśniono w nim uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do zarządzanych modułów HSM.

## <a name="access-control-model"></a>Model kontroli dostępu

Dostęp do zarządzanego modułu HSM jest kontrolowany za pośrednictwem dwóch interfejsów: **płaszczyzny** zarządzania i **płaszczyzny danych**. Płaszczyzna zarządzania to miejsce, w którym zarządzasz samym modułem HSM. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie zarządzanych modułów HSM oraz pobieranie właściwości zarządzanego modułu HSM. Płaszczyzna danych to miejsce, w którym pracujesz z danymi przechowywanymi w zarządzanym hsm — czyli kluczach szyfrowania z modułem HSM. Klucze można dodawać, usuwać, modyfikować i używać do wykonywania operacji kryptograficznych, zarządzania przypisaniami ról w celu kontrolowania dostępu do kluczy, tworzenia pełnej kopii zapasowej modułu HSM, przywracania pełnej kopii zapasowej i zarządzania domeną zabezpieczeń z poziomu interfejsu płaszczyzny danych.

Aby uzyskać dostęp do zarządzanego modułu HSM na jednej z płaszczyzn, wszystkie wywołujące muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie określa tożsamość wywołującego. Autoryzacja określa, które operacje może wykonywać wywołujący. Wywołujący może być dowolnym [](../../role-based-access-control/overview.md#security-principal) podmiotem zabezpieczeń zdefiniowanym w Azure Active Directory — użytkownik, grupa, nazwa główna usługi lub tożsamość zarządzana.

Obie płaszczyzny używają Azure Active Directory do uwierzytelniania. W celu autoryzacji używają różnych systemów w następujący sposób
- Płaszczyzna zarządzania korzysta z kontroli dostępu opartej na rolach platformy Azure — Kontroli dostępu opartej na rolach platformy Azure — systemu autoryzacji opartego na usłudze Azure Azure Resource Manager 
- Płaszczyzna danych używa zarządzanej kontroli dostępu na poziomie modułu HSM (RBAC zarządzanego lokalnego modułu HSM) — systemu autoryzacji zaimplementowanego i wymuszonego na poziomie zarządzanego modułu HSM.

Po utworzeniu zarządzanego modułu HSM żąda on również listy administratorów płaszczyzny danych (obsługiwane są wszystkie [podmioty](../../role-based-access-control/overview.md#security-principal) zabezpieczeń). Tylko ci administratorzy mogą uzyskać dostęp do płaszczyzny danych zarządzanego modułu HSM w celu wykonywania kluczowych operacji i zarządzania przypisaniami ról płaszczyzny danych (zarządzana lokalna RBAC modułu HSM).

Model uprawnień dla obu płaszczyzn używa tej samej składni, ale są one wymuszane na różnych poziomach, a przypisania ról używają różnych zakresów. RBAC platformy Azure dla płaszczyzny zarządzania jest wymuszany przez Azure Resource Manager, podczas gdy lokalna RBAC zarządzanego modułu HSM płaszczyzny danych jest wymuszana przez sam zarządzany moduł HSM.

> [!IMPORTANT]
> Przyznanie dostępu do zarządzanego modułu HSM do zarządzanej płaszczyzny zarządzania podmiotem zabezpieczeń nie powoduje przyznania im żadnego dostępu do płaszczyzny danych w celu uzyskania dostępu do kluczy lub przypisań ról płaszczyzny danych Zarządzane lokalne RBAC modułu HSM). Ta izolacja jest zgodnie z projektem, aby zapobiec przypadkowemu rozszerzaniu uprawnień wpływających na dostęp do kluczy przechowywanych w zarządzanym modułze HSM.

Na przykład administrator subskrypcji (ponieważ ma uprawnienie "Współautor" do wszystkich zasobów w subskrypcji) może usunąć zarządzany moduł HSM w swojej subskrypcji, ale jeśli nie ma dostępu do płaszczyzny danych specjalnie przyznanego za pośrednictwem lokalnej kontroli dostępu na poziomie zarządzanych modułów HSM, nie może uzyskać dostępu do kluczy ani zarządzać przypisaniem roli w zarządzanym modułów HSM, aby udzielić sobie lub innym osobom dostępu do płaszczyzny danych.

## <a name="azure-active-directory-authentication"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Gdy tworzysz zarządzany moduł HSM w subskrypcji platformy Azure, jest on automatycznie kojarzony Azure Active Directory dzierżawą subskrypcji. Wszystkie wywołujące w obu płaszczyznach muszą być zarejestrowane w tej dzierżawie i uwierzytelnione w celu uzyskania dostępu do zarządzanego modułu HSM.

Aplikacja uwierzytelnia się za pomocą Azure Active Directory przed wywołaniem jednej z płaszczyzn. Aplikacja może używać dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie w celu uzyskania dostępu. Zasób jest punktem końcowym na płaszczyźnie zarządzania lub danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do zarządzanego punktu końcowego modułu HSM. Aby dowiedzieć się więcej, przejrzyj [cały przepływ uwierzytelniania.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Korzystanie z jednego mechanizmu uwierzytelniania dla obu płaszczyzn ma kilka korzyści:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich zarządzanych modułów HSM w swojej organizacji.
- Jeśli użytkownik opuści program, natychmiast utraci dostęp do wszystkich zarządzanych modułów HSM w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji Azure Active Directory, takich jak włączanie uwierzytelniania wieloskładnikowego w celu dodania zabezpieczeń.

## <a name="resource-endpoints"></a>Punkty końcowe zasobów

Podmioty zabezpieczeń mają dostęp do płaszczyzn za pośrednictwem punktów końcowych. Mechanizmy kontroli dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do używania kluczy w zarządzanym hsm, należy udzielić dostępu do płaszczyzny danych przy użyciu lokalnej kontroli dostępu RBAC zarządzanego modułu HSM. Aby udzielić użytkownikowi dostępu do zasobu zarządzanego modułu HSM w celu tworzenia, odczytywania, usuwania, przenoszenia zarządzanych modułów HSM oraz edytowania innych właściwości i tagów, których używasz kontroli dostępu na platformie Azure.

W poniższej tabeli przedstawiono punkty końcowe płaszczyzn zarządzania i danych.

| Płaszczyzna &nbsp; dostępu | Punkty końcowe dostępu | Operacje | Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne:**<br> management.azure.com:443<br> | Tworzenie, odczytywanie, aktualizowanie, usuwanie i przenoszenie zarządzanych modułów HSM<br>Ustawianie tagów zarządzanego modułu HSM | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne:**<br> &lt;hsm-name &gt; .managedhsm.azure.net:443<br> | **Klucze:** odszyfrowywanie, szyfrowanie,<br> odpakuj, opakuj, zweryfikuj, podpisz, pobierz, lista, zaktualizuj, utwórz, importuj, usuń, utwórz kopię zapasową, przywróć, przeczyść<br/><br/> Zarządzanie rolami płaszczyzny danych (zarządzana kontrola RBAC lokalnego modułu **HSM):** lista definicji ról, przypisywanie ról, usuwanie przypisań _<br/> <br/> ról,_ definiowanie ról niestandardowych *Tworzenie kopii zapasowej/przywracanie : kopia zapasowa, przywracanie, sprawdzanie stanu operacji tworzenia kopii zapasowej/przywracania **<br/> <br/>** Domena zabezpieczeń**: pobieranie i przekazywanie domeny zabezpieczeń | Lokalna RBAC zarządzanego modułu HSM |
|||||

## <a name="management-plane-and-azure-rbac"></a>Płaszczyzna zarządzania i RBAC platformy Azure

Na płaszczyźnie zarządzania do autoryzowania operacji, które może wykonywać wywołujący, używa się kontroli RBAC platformy Azure. W modelu RBAC platformy Azure każda subskrypcja platformy Azure ma wystąpienie Azure Active Directory. Dostęp z tego katalogu można przyznać użytkownikom, grupom i aplikacjom. Dostęp jest udzielany w celu zarządzania zasobami w subskrypcji platformy Azure, które korzystają Azure Resource Manager modelu wdrażania. Aby udzielić dostępu, użyj interfejsu [Azure Portal](https://portal.azure.com/), interfejsu wiersza polecenia platformy [Azure,](/cli/azure/install-classic-cli) [interfejsu Azure PowerShell](/powershell/azureps-cmdlets-docs)lub interfejsów [AZURE RESOURCE MANAGER API REST.](/rest/api/authorization/roleassignments)

Magazyn kluczy można utworzyć w grupie zasobów i zarządzać dostępem przy użyciu Azure Active Directory. Użytkownikom lub grupom można przyznać możliwość zarządzania magazynami kluczy w grupie zasobów. Dostęp można przyznać na określonym poziomie zakresu, przypisując odpowiednie role platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać mu wstępnie zdefiniowaną rolę `key vault Contributor` w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Grupa zarządzania:** Rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich subskrypcji w tej grupie zarządzania.
- **Subskrypcja:** Rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów:** rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób:** rola platformy Azure przypisana do określonego zasobu ma zastosowanie do tego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie pasuje do Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Azure RBAC: Built-in roles (Azure RBAC: role wbudowane).](../../role-based-access-control/built-in-roles.md)

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Lokalna RBAC płaszczyzny danych i zarządzanego modułu HSM

Aby wykonać określone operacje klucza, należy udzielić podmiotowi zabezpieczeń dostępu, przypisując rolę. Dla każdego przypisania roli należy określić rolę i zakres, dla których to przypisanie ma zastosowanie. W przypadku funkcji RBAC zarządzanego modułu HSM dostępne są dwa zakresy.

- **"/" lub "/keys"**: zakres poziomu modułu HSM. Podmioty zabezpieczeń, którym przypisano rolę w tym zakresie, mogą wykonywać operacje zdefiniowane w roli dla wszystkich obiektów (kluczy) w zarządzanym hsm.
- **"/keys/ &lt; key-name &gt; "**: Zakres poziomu klucza. Podmioty zabezpieczeń przypisane do roli w tym zakresie mogą wykonywać operacje zdefiniowane w tej roli tylko dla wszystkich wersji określonego klucza.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać samouczek z wprowadzeniem dla administratora, zobacz [Co to jest zarządzany moduł HSM?](overview.md)
- Aby uzyskać samouczek zarządzania rolami, zobacz [Temat RBAC lokalnego zarządzanego modułu HSM](role-management.md)
- Aby uzyskać więcej informacji na temat rejestrowania użycia dla rejestrowania zarządzanego modułu HSM, zobacz [Rejestrowanie zarządzanego modułu HSM](logging.md)