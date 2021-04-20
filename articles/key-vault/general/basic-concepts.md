---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
description: Dowiedz się, Azure Key Vault chroni klucze kryptograficzne i wpisy tajne, z których korzystają aplikacje i usługi w chmurze.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 46c8845b4f01db09bf5f96eb1e67078b4e361f9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728324"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault podstawowych pojęć

Azure Key Vault to usługa w chmurze do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Klucz tajny to wszystko, do którego chcesz ściśle kontrolować dostęp, na przykład klucze interfejsu API, hasła, certyfikaty lub klucze kryptograficzne. Key Vault obsługuje dwa typy kontenerów: magazyny i zarządzane pule sprzętowych modułów zabezpieczeń (HSM). Magazyny obsługują przechowywanie oprogramowania i kluczy, wpisów tajnych i certyfikatów wspieranych przez moduł HSM. Zarządzane pule modułów HSM obsługują tylko klucze z obsługą modułu HSM. Zobacz [Azure Key Vault REST API overview (Omówienie interfejsu API REST),](about-keys-secrets-certificates.md) aby uzyskać szczegółowe informacje.

Poniżej znajdują się inne ważne terminy:

- **Dzierżawa** — dzierżawa to organizacja, która jest właścicielem konkretnego wystąpienia usług firmy Microsoft w chmurze i zarządza nim. Najczęściej używa się go do odwoływać się do zestawu usług platformy Azure i Microsoft 365 usług dla organizacji.

- **Właściciel magazynu** — właściciel magazynu może utworzyć magazyn kluczy, ma do niego pełny dostęp i kontrolę nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.

- **Użytkownik magazynu** — użytkownik magazynu może wykonywać akcje na zasobach wewnątrz magazynu kluczy, jeśli właściciel magazynu udzieli mu dostępu. Dostępne akcje zależą od przyznanych uprawnień.

- **Administratorzy zarządzanego modułu HSM:** użytkownicy, którym przypisano rolę Administrator, mają pełną kontrolę nad zarządzaną pulą modułów HSM. Mogą oni tworzyć więcej przypisań ról w celu delegowania kontrolowanego dostępu do innych użytkowników.

- **Zarządzany użytkownik/kryptograficzny moduł HSM:** role wbudowane, które są zwykle przypisywane do użytkowników lub podmiotów zabezpieczeń usługi, które będą wykonywać operacje kryptograficzne przy użyciu kluczy w zarządzanym modułze HSM. Użytkownik kryptograficzny może tworzyć nowe klucze, ale nie może usuwać kluczy.

- Szyfrowanie usługi kryptograficznych zarządzanego modułu **HSM:** wbudowana rola, która jest zwykle przypisywana do tożsamości usługi zarządzanej kont usług (np. konta magazynu) na potrzeby szyfrowania danych magazynowanych przy użyciu klucza zarządzanego przez klienta.

- **Zasób** — zasób to dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Typowe przykłady to maszyna wirtualna, konto magazynu, aplikacja internetowa, baza danych i sieć wirtualna. Jest ich o wiele więcej.

- **Grupa zasobów** — grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

- **Podmiot zabezpieczeń:** podmiot zabezpieczeń platformy Azure to tożsamość zabezpieczeń, która jest przez użytkowników tworzona przez aplikacje, usługi i narzędzia automatyzacji do uzyskiwania dostępu do określonych zasobów platformy Azure. Można ją nazwać jako "tożsamość użytkownika" (nazwa użytkownika i hasło lub certyfikat) z określoną rolą i ściśle kontrolowanymi uprawnieniami. Podmiot zabezpieczeń powinien tylko wykonać określone czynności, w przeciwieństwie do ogólnej tożsamości użytkownika. Zwiększa to bezpieczeństwo, jeśli przyznasz jej tylko minimalny poziom uprawnień wymagany do wykonywania zadań zarządzania. Podmiot zabezpieczeń używany z aplikacją lub usługą jest w szczególności nazywany **jednostką usługi**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD to usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę.

- **Identyfikator dzierżawy Azure** — identyfikator dzierżawy to unikatowy sposób identyfikacji wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.

- **Tożsamości zarządzane:** Azure Key Vault umożliwia bezpieczne przechowywanie poświadczeń oraz innych kluczy i wpisów tajnych, ale kod musi uwierzytelniać się w celu Key Vault ich pobrania. Korzystanie z tożsamości zarządzanej ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższy obraz i omówienie tożsamości zarządzanych [dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Authentication
Aby wykonać wszystkie operacje Key Vault, najpierw musisz się w nim uwierzytelnić. Istnieją trzy sposoby uwierzytelniania w celu Key Vault:

- [Tożsamości zarządzane dla zasobów platformy Azure:](../../active-directory/managed-identities-azure-resources/overview.md)podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure można przypisać tożsamość do maszyny wirtualnej, która ma dostęp do Key Vault. Tożsamości można również przypisywać do [innych zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Zaletą tego podejścia jest to, że aplikacja lub usługa nie zarządza rotacją pierwszego tajnego. Platforma Azure automatycznie obraca tożsamość. Zalecamy takie podejście jako najlepsze rozwiązanie. 
- **Jednostki usługi i certyfikatu:** można użyć jednostki usługi i skojarzonego certyfikatu, który ma dostęp do Key Vault. Nie zalecamy tego podejścia, ponieważ właściciel lub deweloper aplikacji musi obrócić certyfikat.
- **Jednostkę usługi i** klucz tajny: mimo że do uwierzytelniania w usłudze Key Vault można użyć jednostki usługi i tajnego, nie jest to zalecane. Trudno jest automatycznie obrócić klucz tajny ładowania początkowego, który jest używany do uwierzytelniania w Key Vault.


## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |"Chcę napisać aplikację dla platformy Azure, która używa kluczy do podpisywania i szyfrowania. Chcę jednak, aby te klucze były zewnętrzne względem mojej aplikacji, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Chcę również, aby te klucze i wpisy tajne były łatwe w użyciu z poziomu moich aplikacji z optymalną wydajnością". |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |"Nie chcę ponosić odpowiedzialności ani ponosić potencjalnej odpowiedzialności za klucze dzierżawy i wpisy tajne moich klientów. <br/><br/>Chcę, aby klienci posiadali klucze i zarządzali nimi, abym w stanie skoncentrować się na tym, co robię najlepiej, czyli zapewnianiu podstawowych funkcji oprogramowania". |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonywać operacje kryptograficzne przy użyciu kluczy klientów, Key Vault wykonuje te operacje w imieniu aplikacji. Aplikacja nie widzi kluczy klientów. |
| Chief Security Officer (CSO) |"Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM FIPS 140-2 poziom 2 lub FIPS 140-2 poziom 3 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>Mimo że korzystamy z wielu usług i zasobów platformy Azure, chcę zarządzać kluczami z jednej lokalizacji na platformie Azure". |√ **magazyny dla** modułów HSM zweryfikowanych w programie FIPS 140-2 poziom 2.<br/>√ zarządzane **pule modułów HSM** dla modułów HSM zweryfikowanych w programie FIPS 140-2 poziom 3.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Chociaż Key Vault korzyści dla deweloperów i administratorów zabezpieczeń, może być wdrażana i zarządzana przez administratora organizacji, który zarządza innymi usługami platformy Azure. Na przykład ten administrator może zalogować się przy użyciu subskrypcji platformy Azure, utworzyć magazyn dla organizacji, w której będą przechowywane klucze, a następnie być odpowiedzialny za takie zadania operacyjne:

- Tworzenie lub importowanie klucza lub klucza tajnego
- Odwoływanie lub usuwanie klucza lub klucza tajnego
- Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
- Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
- Monitorowanie użycia klucza

Następnie ten administrator przekazuje deweloperom URI do wywołania z aplikacji. Ten administrator przekazuje również administratorowi zabezpieczeń informacje dotyczące rejestrowania użycia klucza. 

![Omówienie sposobu działania Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](developers-guide.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zabezpieczyć magazyn.](secure-your-key-vault.md)
- Dowiedz się, jak [zabezpieczyć zarządzane pule modułów HSM](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
