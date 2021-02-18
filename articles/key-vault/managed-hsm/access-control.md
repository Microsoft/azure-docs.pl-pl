---
title: Kontrola dostępu modułu HSM zarządzanego przez platformę Azure
description: Zarządzaj uprawnieniami dostępu do modułu HSM i kluczy zarządzanych przez platformę Azure. Obejmuje model uwierzytelniania i autoryzacji dla zarządzanego modułu HSM oraz sposób zabezpieczania sprzętowych modułów zabezpieczeń.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 0c0a0c5f62f92aaf195e207dfd505ffb017d924e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653904"
---
# <a name="managed-hsm-access-control"></a>Kontrola dostępu zarządzanego modułu HSM

> [!NOTE]
> Dostawca zasobów Key Vault obsługuje dwa typy zasobów: **magazyny** i **zarządzane sprzętowych modułów zabezpieczeń**. Kontrola dostępu opisana w tym artykule dotyczy tylko **zarządzanych sprzętowych modułów zabezpieczeń**. Aby dowiedzieć się więcej na temat kontroli dostępu do zarządzanego modułu HSM, zobacz [zapewnianie dostępu do Key Vault kluczy, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach platformy Azure](../general/rbac-guide.md).

Azure Key Vault zarządzany moduł HSM to usługa w chmurze, która chroni klucze szyfrowania. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do zarządzanego sprzętowych modułów zabezpieczeń, zezwalając na dostęp do niego tylko autoryzowanym aplikacjom i użytkownikom. Ten artykuł zawiera omówienie modelu kontroli dostępu zarządzanego modułu HSM. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do zarządzanego sprzętowych modułów zabezpieczeń.

## <a name="access-control-model"></a>Model kontroli dostępu

Dostęp do zarządzanego modułu HSM jest kontrolowany przez dwa interfejsy: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzna zarządzania to miejsce, w którym można zarządzać modułem HSM. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie zarządzanych sprzętowych modułów zabezpieczeń oraz pobieranie właściwości zarządzanych modułów HSM. Płaszczyzna danych to miejsce, w którym pracujesz z danymi przechowywanymi w zarządzanym module HSM — czyli kluczami szyfrowania opartymi na module HSM. Można dodawać, usuwać, modyfikować i używać kluczy do wykonywania operacji kryptograficznych, zarządzania przypisaniami ról w celu kontrolowania dostępu do kluczy, tworzenia pełnej kopii zapasowej HSM, przywracania pełnej kopii zapasowej i zarządzania domeną zabezpieczeń z poziomu interfejsu płaszczyzny danych.

Aby można było uzyskać dostęp do zarządzanego modułu HSM w jednej z płaszczyzn, wszyscy wywołujący muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie ustanawia tożsamość obiektu wywołującego. Autoryzacja określa, które operacje mogą zostać wykonane przez obiekt wywołujący. Obiekt wywołujący może być dowolnymi [podmiotami zabezpieczeń](../../role-based-access-control/overview.md#security-principal) zdefiniowanymi w Azure Active Directory — użytkownik, Grupa, nazwa główna usługi lub tożsamość zarządzana.

Obie płaszczyzny używają Azure Active Directory do uwierzytelniania. W przypadku autoryzacji korzystają z różnych systemów w następujący sposób
- Płaszczyzna zarządzania używa kontroli dostępu opartej na rolach na platformie Azure--Azure Resource Manager RBAC 
- Płaszczyzna danych używa zarządzanej kontroli RBAC na poziomie modułu HSM (zarządzanej przez moduł HSM Local RBAC) — system autoryzacji zaimplementowany i wymuszany na zarządzanym poziomie modułu HSM.

Po utworzeniu zarządzanego modułu HSM, Obiekt żądający zawiera również listę administratorów płaszczyzny danych (obsługiwane są wszystkie [podmioty zabezpieczeń](../../role-based-access-control/overview.md#security-principal) ). Tylko ci Administratorzy mogą uzyskać dostęp do zarządzanej płaszczyzny danych modułu HSM, aby wykonywać kluczowe operacje i zarządzać przypisaniami ról płaszczyzny danych (zarządzane lokalne RBAC modułu HSM).

Model uprawnień dla obu płaszczyzn używa tej samej składni, ale są one wymuszane na różnych poziomach i przypisaniach ról używają różnych zakresów. Płaszczyzna zarządzania Azure RBAC jest wymuszana przez Azure Resource Manager, gdy lokalna RBAC modułu HSM zarządzanej płaszczyzny danych jest wymuszana za pomocą zarządzanego modułu HSM.

> [!IMPORTANT]
> Przyznanie jednostce zarządzania podmiotom zabezpieczeń dostępu do zarządzanego modułu HSM nie przyznaje im dostępu do płaszczyzny danych, aby uzyskać dostęp do kluczy lub przypisań roli płaszczyzny danych zarządzane lokalne RBAC (HSM). Ta izolacja jest zaprojektowana w celu zapobiegania przypadkowemu rozszerzeniu uprawnień wpływających na dostęp do kluczy przechowywanych w zarządzanym module HSM.

Na przykład administrator subskrypcji (ponieważ ma uprawnienie "Współautor" do wszystkich zasobów w ramach subskrypcji) może usunąć zarządzany moduł HSM w swojej subskrypcji, ale jeśli nie ma dostępu do płaszczyzny danych, który jest przyznany przez zarządzane lokalne RBAC modułu HSM, nie może uzyskać dostępu do kluczy lub zarządzać przypisaniem roli w zarządzanym module HSM, aby przyznać sobie lub innym osobom dostęp do płaszczyzny danych.

## <a name="azure-active-directory-authentication"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Podczas tworzenia zarządzanego modułu HSM w ramach subskrypcji platformy Azure jest on automatycznie kojarzony z dzierżawą Azure Active Directory subskrypcji. Wszystkie obiekty wywołujące w obu płaszczyznach muszą być zarejestrowane w tej dzierżawie i uwierzytelniać się w celu uzyskania dostępu do zarządzanego modułu HSM.

Aplikacja uwierzytelnia się za pomocą Azure Active Directory przed wywołaniem dowolnej płaszczyzny. Aplikacja może korzystać z dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie, aby uzyskać dostęp. Zasób jest punktem końcowym w obszarze zarządzania lub płaszczyzny danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do zarządzanego punktu końcowego modułu HSM. Aby dowiedzieć się więcej, zapoznaj się z [całym przepływem uwierzytelniania](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Korzystanie z jednego mechanizmu uwierzytelniania dla obu płaszczyzn ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich zarządzanych sprzętowych modułów zabezpieczeń w organizacji.
- Jeśli użytkownik opuści użytkownika, natychmiast utraci dostęp do wszystkich zarządzanych sprzętowych modułów zabezpieczeń w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w Azure Active Directory, takich jak włączenie uwierzytelniania wieloskładnikowego w celu zwiększenia bezpieczeństwa.

## <a name="resource-endpoints"></a>Punkty końcowe zasobów

Podmioty zabezpieczeń uzyskują dostęp do płaszczyzn za pomocą punktów końcowych. Kontrole dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do używania kluczy w zarządzanym module HSM, przyznano dostęp do płaszczyzny danych przy użyciu modułu HSM Local RBAC. Aby udzielić użytkownikowi dostępu do zarządzanego zasobu modułu HSM do tworzenia, odczytywania, usuwania, przenoszenia zarządzanego sprzętowych modułów zabezpieczeń i edytowania innych właściwości oraz tagów korzystających z usługi Azure RBAC.

W poniższej tabeli przedstawiono punkty końcowe dla punktów zarządzania i płaszczyzny danych.

| &nbsp;Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne**<br> management.azure.com:443<br> | Tworzenie, odczytywanie, aktualizowanie, usuwanie i przenoszenie zarządzanych sprzętowych modułów zabezpieczeń<br>Ustawianie zarządzanych tagów modułu HSM | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne**<br> &lt;HSM-Name &gt; . managedhsm.Azure.NET:443<br> | **Klucze**: Odszyfruj, Szyfruj,<br> odpakowywanie, zawijanie, sprawdzanie, podpisywanie, pobieranie, wyświetlanie, aktualizowanie, tworzenie, importowanie, usuwanie, tworzenie kopii zapasowej, przywracanie, przeczyszczanie<br/><br/> **Zarządzanie rolami płaszczyzny danych (zarządzana lokalna RBAC modułu HSM)**_: lista definicji ról, przypisywanie ról, usuwanie przypisań ról, <br/> <br/> Definiowanie ról niestandardowych_* kopia zapasowa/przywracanie **: kopia zapasowa, przywracanie <br/> <br/> , sprawdzanie stanu kopii zapasowej/przywracanie**— domena zabezpieczeń * *: pobieranie i przekazywanie domeny zabezpieczeń | Zarządzane lokalne RBAC modułu HSM |
|||||
## <a name="management-plane-and-azure-rbac"></a>Płaszczyzna zarządzania i kontrola RBAC platformy Azure

W płaszczyźnie zarządzania korzystasz z usługi Azure RBAC do autoryzacji operacji, które mogą zostać wykonane przez obiekt wywołujący. W modelu RBAC platformy Azure Każda subskrypcja platformy Azure ma wystąpienie Azure Active Directory. Przyznasz użytkownikom, grupom i aplikacjom dostęp do tego katalogu. Dostęp jest udzielany do zarządzania zasobami w ramach subskrypcji platformy Azure, która używa modelu wdrażania Azure Resource Manager. Aby udzielić dostępu, użyj [Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST Azure Resource Manager](/rest/api/authorization/roleassignments).

Należy utworzyć magazyn kluczy w grupie zasobów i zarządzać dostępem przy użyciu Azure Active Directory. Użytkownicy lub grupy mogą zarządzać magazynami kluczy w grupie zasobów. Przyznanie dostępu na określonym poziomie zakresu przez przypisanie odpowiednich ról platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Grupa zarządzania**: rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich subskrypcji w tej grupie zarządzania.
- **Subskrypcja**: rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób**: dla danego zasobu jest stosowana rola platformy Azure przypisana do określonego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie spełnia Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Azure RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Lokalna kontrola RBAC płaszczyzny danych i zarządzanego modułu HSM

Przyznaje podmiotowi zabezpieczeń dostęp do wykonywania określonych operacji kluczowych, przypisując rolę. Dla każdego przypisania roli należy określić rolę i zakres, w których ma zastosowanie to przypisanie. W przypadku zarządzanych modułów HSM Local RBAC dostępne są dwa zakresy.

- **"/" lub "/Keys"**: zakres poziomu modułu HSM. Podmioty zabezpieczeń przypisane do roli w tym zakresie mogą wykonywać operacje zdefiniowane w roli dla wszystkich obiektów (kluczy) w zarządzanym module HSM.
- **"/Keys/ &lt; Key Name &gt; "**: zakres poziomu klucza. Podmioty zabezpieczeń przypisane do roli w tym zakresie mogą wykonywać operacje zdefiniowane w tej roli tylko dla wszystkich wersji określonego klucza.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [co to jest zarządzany moduł HSM?](overview.md).
- Aby zapoznać się z samouczkiem zarządzania rolami, zobacz [zarządzany moduł HSM Local RBAC](role-management.md)
- Aby uzyskać więcej informacji na temat rejestrowania użycia zarządzanego modułu HSM, zobacz [Rejestrowanie zarządzanego modułu HSM](logging.md).