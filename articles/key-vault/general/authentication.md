---
title: Uwierzytelnianie w usłudze Azure Key Vault
description: Dowiedz się, jak uwierzytelniać się w usłudze Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: d247e657e93afd0c43ecee1154c542398304d8dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89481379"
---
# <a name="authenticate-to-azure-key-vault"></a>Uwierzytelnianie w usłudze Azure Key Vault

Azure Key Vault pozwala przechowywać wpisy tajne i kontrolować ich dystrybucję w scentralizowanym, bezpiecznym repozytorium w chmurze, co eliminuje konieczność przechowywania poświadczeń w aplikacjach. Aplikacje muszą uwierzytelniać się za pomocą Key Vault w czasie wykonywania, aby uzyskać dostęp do tych kluczy tajnych.

## <a name="app-identity-and-security-principals"></a>Tożsamość aplikacji i podmioty zabezpieczeń

Uwierzytelnianie za pomocą Key Vault działa w połączeniu z usługą [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), która jest odpowiedzialna za uwierzytelnianie tożsamości dowolnego danego **podmiotu zabezpieczeń**.

Podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, usługę lub aplikację, która żąda dostępu do zasobów platformy Azure. Platforma Azure przypisuje unikatowy **Identyfikator obiektu** do każdego podmiotu zabezpieczeń.

* Podmiot zabezpieczeń **użytkownika** identyfikuje osoby, które mają profil w Azure Active Directory.

* Podmiot zabezpieczeń **grupy** identyfikuje zestaw użytkowników utworzonych w Azure Active Directory. Wszystkie role lub uprawnienia przypisane do grupy są udzielane wszystkim użytkownikom należącym do grupy.

* Nazwa **główna usługi** jest typem podmiotu zabezpieczeń, który tożsamościuje aplikację lub usługę, czyli fragment kodu, a nie użytkownika lub grupy. Identyfikator obiektu jednostki usługi jest znany jako jego **Identyfikator klienta** i działa jak jego nazwa użytkownika. **Wpis tajny klienta** jednostki usługi zachowuje się jak jego hasło.

W przypadku aplikacji istnieją dwa sposoby uzyskania nazwy głównej usługi:

* Zalecane: Włącz **tożsamość zarządzaną** przypisaną przez system dla aplikacji.

    Dzięki tożsamości zarządzanej platforma Azure wewnętrznie zarządza główną jednostką usługi aplikacji i automatycznie uwierzytelnia aplikację przy użyciu innych usług platformy Azure. Tożsamość zarządzana jest dostępna dla aplikacji wdrożonych w różnych usługach.

    Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](/azure/active-directory/managed-identities-azure-resources/overview). Zobacz również [usługi platformy Azure obsługujące tożsamość zarządzaną](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), które łączą się z artykułami opisującymi, jak włączyć zarządzaną tożsamość dla określonych usług (takich jak App Service, Azure Functions, Virtual Machines itp.).

* Jeśli nie możesz użyć tożsamości zarządzanej, zamiast tego **zarejestrujsz** aplikację za pomocą dzierżawy usługi Azure AD, zgodnie z opisem w [przewodniku szybki start: Zarejestruj aplikację na platformie tożsamości platformy Azure](/azure/active-directory/develop/quickstart-register-app). Rejestracja powoduje także utworzenie drugiego obiektu aplikacji, który identyfikuje aplikację we wszystkich dzierżawcach.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autoryzuj podmiot zabezpieczeń, aby uzyskać dostęp do Key Vault

Key Vault współpracuje z dwoma odrębnymi poziomami autoryzacji:

- **Zasady dostępu** kontrolują, czy użytkownik, Grupa lub jednostka usługi mają autoryzację dostępu do wpisów tajnych, kluczy i certyfikatów *w ramach* istniejącego zasobu Key Vault (czasami określanego w przypadku operacji "płaszczyzny danych"). Zasady dostępu są zwykle przyznawane użytkownikom, grupom i aplikacjom.

    Aby przypisać zasady dostępu, zobacz następujące artykuły:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Interfejs wiersza polecenia platformy Azure](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Uprawnienia roli** określają, czy użytkownik, Grupa lub jednostka usługi mają autoryzację do tworzenia, usuwania i zarządzania zasobami Key Vault (czasami określana jako operacje "płaszczyzny zarządzania"). Takie role są najczęściej udzielane tylko administratorom.
 
    Aby przypisać role i zarządzać nimi, zobacz następujące artykuły:

    - [Azure Portal](/azure/role-based-access-control/role-assignments-portal)
    - [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Key Vault obecnie obsługuje rolę [współautor](/azure/role-based-access-control/built-in-roles#key-vault-contributor) , która umożliwia wykonywanie operacji zarządzania na zasobach Key Vault. Niektóre inne role są obecnie dostępne w wersji zapoznawczej. Można również tworzyć role niestandardowe, zgodnie z opisem w temacie [role niestandardowe platformy Azure](/azure/role-based-access-control/custom-roles).

    Aby uzyskać ogólne informacje na temat ról, zobacz [co to jest usługa Azure Role-Based Access Control (RBAC)?](/azure/role-based-access-control/overview).


> [!IMPORTANT]
> Aby zapewnić najwyższy poziom zabezpieczeń, zawsze należy przestrzegać głównych uprawnień i przydzielić tylko najbardziej szczegółowe zasady dostępu i role, które są niezbędne. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurowanie zapory Key Vault

Domyślnie Key Vault zezwala na dostęp do zasobów za pomocą publicznych adresów IP. Aby zwiększyć bezpieczeństwo, można także ograniczyć dostęp do określonych zakresów adresów IP, punktów końcowych usług, sieci wirtualnych lub prywatnych punktów końcowych.

Aby uzyskać więcej informacji, zobacz [dostęp Azure Key Vault za zaporą](/azure/key-vault/general/access-behind-firewall).


## <a name="the-key-vault-authentication-flow"></a>Przepływ uwierzytelniania Key Vault

1. Jednostki usługi żądają uwierzytelnienia w usłudze Azure AD, na przykład:
    * Użytkownik loguje się do Azure Portal przy użyciu nazwy użytkownika i hasła.
    * Aplikacja wywołuje interfejs API REST platformy Azure, który przedstawia identyfikator klienta i klucz tajny lub certyfikat klienta.
    * Zasób platformy Azure, taki jak maszyna wirtualna z zarządzaną tożsamością, kontaktuje się z punktem końcowym REST [usługi azure instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) w celu uzyskania tokenu dostępu.

1. W przypadku pomyślnego uwierzytelnienia w usłudze Azure AD jednostka usługi uzyskuje token OAuth.

1. Jednostka usługi wykonuje wywołanie do Key Vault interfejsu API REST za pomocą punktu końcowego Key Vault (URI).

1. Key Vault zapory sprawdza następujące kryteria. Jeśli dowolne kryterium jest spełnione, wywołanie jest dozwolone. W przeciwnym razie wywołanie jest zablokowane i zwracana jest niedozwolona odpowiedź.

    * Zapora jest wyłączona, a publiczny punkt końcowy Key Vault jest dostępny z publicznego Internetu.
    * Obiekt wywołujący jest [Key Vault zaufaną](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), umożliwiając mu ominięcie zapory.
    * Obiekt wywołujący jest wymieniony w polu Zapora według adresu IP, sieci wirtualnej lub punktu końcowego usługi.
    * Obiekt wywołujący może dotrzeć do Key Vault za pośrednictwem skonfigurowanego połączenia z linkiem prywatnym.    

1. Jeśli zapora zezwala na wywołanie, Key Vault wywołuje usługę Azure AD w celu weryfikacji tokenu dostępu jednostki usługi.

1. Key Vault sprawdza, czy jednostka usługi ma wymagane zasady dostępu dla wymaganej operacji. Jeśli nie, Key Vault zwraca niedostępną odpowiedź.

1. Key Vault wykonuje żądaną operację i zwraca wynik.

Na poniższym diagramie przedstawiono proces dla aplikacji wywołującej interfejs API Key Vault "Get Secret":

![Przepływ uwierzytelniania Azure Key Vault](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Przykłady kodu

Poniższa tabela zawiera linki do różnych artykułów, które pokazują, jak korzystać z Key Vault w kodzie aplikacji przy użyciu bibliotek zestawu Azure SDK dla danego języka. Inne interfejsy, takie jak interfejs wiersza polecenia platformy Azure i Azure Portal, są dołączane do wygody.

| Key Vault wpisy tajne | Klucze Key Vault | Key Vault certyfikaty |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (zestaw SDK w wersji 4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (zestaw SDK v3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure Portal](/azure/key-vault/secrets/quick-create-portal) | [Azure Portal](/azure/key-vault/keys/quick-create-portal) | [Azure Portal](/azure/key-vault/certificates/quick-create-portal) |
| [Interfejs wiersza polecenia platformy Azure](/azure/key-vault/secrets/quick-create-cli) | [Interfejs wiersza polecenia platformy Azure](/azure/key-vault/keys/quick-create-cli) | [Interfejs wiersza polecenia platformy Azure](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [Szablon usługi ARM](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z Key Vault zasad dostępu](troubleshooting-access-issues.md)
- [Kody błędów interfejsu API REST Key Vault](rest-error-codes.md)
- [Przewodnik dewelopera Key Vault](developers-guide.md)
- [Co to jest usługa Azure Role-Based Access Control (RBAC)?](/azure/role-based-access-control/overview)
