---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
description: Dowiedz się, jak usługa Azure Key Vault chroni klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 14eda137d386146d96b6b9aa54e1ed57021db19d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432140"
---
# <a name="azure-key-vault-basic-concepts"></a>Podstawowe pojęcia usługi Azure Key Vault

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty. Przechowalnia jest logiczną grupą wpisów tajnych.

Oto inne ważne terminy:

- **Dzierżawa** — dzierżawa to organizacja, która jest właścicielem konkretnego wystąpienia usług firmy Microsoft w chmurze i zarządza nim. Najczęściej jest używany do odwoływania się do zestawu usług platformy Azure i usługi Office 365 dla organizacji.

- **Właściciel magazynu** — właściciel magazynu może utworzyć magazyn kluczy, ma do niego pełny dostęp i kontrolę nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.

- **Użytkownik magazynu** — użytkownik magazynu może wykonywać akcje na zasobach wewnątrz magazynu kluczy, jeśli właściciel magazynu udzieli mu dostępu. Dostępne akcje zależą od przyznanych uprawnień.

- **Zasób** — zasób to dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Typowymi przykładami są maszyny wirtualnej, konta magazynu, aplikacji sieci web, bazy danych i sieci wirtualnej. Jest ich o wiele więcej.

- **Grupa zasobów** — grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

- **Jednostki usługi:** jednostki usługi platformy Azure jest tożsamości zabezpieczeń, które utworzone przez użytkownika aplikacje, usługi i narzędzia automatyzacji używać do uzyskiwania dostępu do określonych zasobów platformy Azure. Potraktuj to jako "tożsamość użytkownika" (nazwa użytkownika i hasło lub certyfikat) z określoną rolą i ściśle kontrolowanymi uprawnieniami. W odróżnieniu od ogólnej tożsamości użytkownika, jednostka usługi powinna wykonywać tylko określone czynności. Poprawia bezpieczeństwo, jeśli przyznasz mu tylko minimalny poziom uprawnień, który jest wymagany do wykonywania swoich zadań zarządzania.

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md): Azure AD to usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę.

- **Identyfikator dzierżawy Azure** — identyfikator dzierżawy to unikatowy sposób identyfikacji wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.

- **Tożsamości zarządzane:** Usługa Azure Key Vault umożliwia bezpieczne przechowywanie poświadczeń oraz innych kluczy i wpisów tajnych, ale kod musi być uwierzytelniony w magazynie kluczy, aby je pobrać. Użycie tożsamości zarządzanej ułatwia rozwiązanie tego problemu, nadając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższą ilustrację i [omówienie tożsamości zarządzanych dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

    ![Diagram działania tożsamości zarządzanych dla zasobów platformy Azure](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Aby wykonać operacje za pomocą usługi Key Vault, należy najpierw uwierzytelnić się do niego. Istnieją trzy sposoby uwierzytelniania w magazynie kluczy:

- [Tożsamości zarządzane dla zasobów platformy Azure:](../../active-directory/managed-identities-azure-resources/overview.md)Podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure, można przypisać tożsamość do maszyny wirtualnej, która ma dostęp do usługi Key Vault. Można również przypisać tożsamości do [innych zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zaletą tego podejścia jest to, że aplikacja lub usługa nie zarządza rotacji pierwszego klucza tajnego. Platforma Azure automatycznie obraca tożsamość. Zaleca się takie podejście jako najlepszą praktykę. 
- **Jednostki usługi i certyfikat:** Można użyć jednostki usługi i skojarzonego certyfikatu, który ma dostęp do usługi Key Vault. Nie zaleca się tego podejścia, ponieważ właściciel aplikacji lub deweloper musi obrócić certyfikat.
- **Jednostki usługi i klucz tajny:** Chociaż można użyć jednostki usługi i klucza tajnego do uwierzytelniania w magazynie kluczy, nie zalecamy go. Trudno jest automatycznie obrócić klucz tajny bootstrap, który jest używany do uwierzytelniania w magazynie kluczy.


## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |"Chcę napisać aplikację dla platformy Azure, która używa kluczy do podpisywania i szyfrowania. Chcę jednak, aby te klucze były zewnętrzne z mojej aplikacji, dzięki czemu rozwiązanie jest odpowiednie dla aplikacji, która jest geograficznie rozproszona. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Chcę również, aby te klucze i sekrety były dla mnie łatwe w użyciu z moich aplikacji, z optymalną wydajnością." |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |"Nie chcę odpowiedzialności ani potencjalnej odpowiedzialności za klucze i tajemnice najemcy moich klientów. <br/><br/>Chcę, aby klienci byli właścicielami i zarządzali swoimi kluczami, abym mógł skupić się na robieniu tego, co robię najlepiej, czyli dostarczaniu podstawowych funkcji oprogramowania." |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonywać operacje kryptograficzne przy użyciu kluczy klientów, Usługa Key Vault wykonuje te operacje w imieniu aplikacji. Aplikacja nie widzi kluczy klientów. |
| Chief Security Officer (CSO) |"Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM FIPS 140-2 Poziomu 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę zarządzać kluczami z jednej lokalizacji na platformie Azure." |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Mimo że usługa Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, może być zaimplementowana i zarządzana przez administratora organizacji, który zarządza innymi usługami platformy Azure. Na przykład ten administrator może zalogować się za pomocą subskrypcji platformy Azure, utworzyć magazyn dla organizacji, w którym mają być przechowywane klucze, a następnie być odpowiedzialny za zadania operacyjne, takie jak te:

- Tworzenie lub importowanie klucza lub klucza tajnego
- Odwoływanie lub usuwanie klucza lub klucza tajnego
- Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
- Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
- Monitorowanie użycia klucza

Ten administrator następnie daje deweloperom identyfikatory URI do wywołania z ich aplikacji. Ten administrator udostępnia również administratorowi zabezpieczeń informacje dotyczące rejestrowania użycia klucza. 

![Omówienie działania usługi Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](developers-guide.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zabezpieczyć skarbiec).](secure-your-key-vault.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
