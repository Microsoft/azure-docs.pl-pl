---
title: Uwierzytelnianie w usłudze Azure Key Vault
description: Dowiedz się, jak uwierzytelniać się w Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8a8fe4ed0c24d2ccda5fb844005a33a93e85a169
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365522"
---
# <a name="authenticate-to-azure-key-vault"></a>Uwierzytelnianie w usłudze Azure Key Vault

Azure Key Vault umożliwia przechowywanie wpisów tajnych i kontrolowanie ich dystrybucji w scentralizowanym, bezpiecznym repozytorium w chmurze, co eliminuje konieczność przechowywania poświadczeń w aplikacjach. Aby uzyskać dostęp do tych wpisów tajnych, Key Vault w czasie rzeczywistym aplikacje muszą uwierzytelniać się tylko za pomocą usługi .

## <a name="app-identity-and-security-principals"></a>Tożsamość aplikacji i podmioty zabezpieczeń

Uwierzytelnianie za Key Vault działa w połączeniu z [usługą Azure Active Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)która jest odpowiedzialna za uwierzytelnianie tożsamości dowolnego **podmiotu zabezpieczeń.**

Podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, usługę lub aplikację żądające dostępu do zasobów platformy Azure. Platforma Azure przypisuje unikatowy identyfikator **obiektu do** każdego podmiotu zabezpieczeń.

* Podmiot **zabezpieczeń** użytkownika identyfikuje osobę, która ma profil w Azure Active Directory.

* Podmiot **zabezpieczeń** grupy identyfikuje zestaw użytkowników utworzonych w Azure Active Directory. Wszystkie role lub uprawnienia przypisane do grupy są przyznawane wszystkim użytkownikom w grupie.

* Nazwa **główna usługi** to typ podmiotu zabezpieczeń, który tożsamościuje aplikację lub usługę, czyli fragment kodu, a nie użytkownika lub grupę. Identyfikator obiektu jednostki usługi jest  nazywany identyfikatorem klienta i działa jak nazwa użytkownika. Klucz tajny klienta jednostki **usługi działa** jak jego hasło.

W przypadku aplikacji istnieją dwa sposoby uzyskiwania jednostki usługi:

* Zalecane: włącz tożsamość zarządzaną **przypisaną** przez system dla aplikacji.

    Dzięki tożsamości zarządzanej platforma Azure wewnętrznie zarządza jednostką usługi aplikacji i automatycznie uwierzytelnia aplikację w innych usługach platformy Azure. Tożsamość zarządzana jest dostępna dla aplikacji wdrożonych w różnych usługach.

    Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](../../active-directory/managed-identities-azure-resources/overview.md) Zobacz również [usługi platformy Azure,](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)które obsługują tożsamość zarządzaną . Linki do artykułów opisujących sposób włączania tożsamości zarządzanej dla określonych usług (takich jak App Service, Azure Functions, Virtual Machines itp.).

* Jeśli nie możesz użyć tożsamości  zarządzanej, zamiast tego zarejestruj aplikację w dzierżawie usługi Azure AD, zgodnie z opisem w przewodniku Szybki start: rejestrowanie aplikacji za pomocą platformy [tożsamości platformy Azure.](../../active-directory/develop/quickstart-register-app.md) Rejestracja tworzy również drugi obiekt aplikacji, który identyfikuje aplikację we wszystkich dzierżawach.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autoryzowanie podmiotu zabezpieczeń do uzyskiwania dostępu do Key Vault

Key Vault działa z dwoma oddzielnymi poziomami autoryzacji:

- **Zasady dostępu** kontrolują, czy użytkownik, grupa lub nazwa główna usługi  ma autoryzację dostępu do wpisów tajnych, kluczy i certyfikatów w ramach istniejącego zasobu Key Vault (czasami nazywanego operacjami "płaszczyzny danych"). Zasady dostępu są zwykle przyznawane użytkownikom, grupom i aplikacjom.

    Aby przypisać zasady dostępu, zobacz następujące artykuły:

    - [Witryna Azure Portal](assign-access-policy-portal.md)
    - [Interfejs wiersza polecenia platformy Azure](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Uprawnienia roli** kontrolują, czy użytkownik, grupa lub nazwa główna usługi ma autoryzację do tworzenia i usuwania zasobu Key Vault (czasami nazywanego operacjami "płaszczyzny zarządzania"). Takie role są najczęściej przyznawane tylko administratorom.
 
    Aby przypisać role i zarządzać nimi, zobacz następujące artykuły:

    - [Witryna Azure Portal](../../role-based-access-control/role-assignments-portal.md)
    - [Interfejs wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Aby uzyskać ogólne informacje na temat ról, zobacz Co to jest kontrola dostępu na podstawie ról [(RBAC) platformy Azure?.](../../role-based-access-control/overview.md)


> [!IMPORTANT]
> Aby zapewnić największe bezpieczeństwo, zawsze przestrzegaj podmiotu zabezpieczeń najmniejszych uprawnień i przyznaj tylko najbardziej specyficzne zasady dostępu i role, które są niezbędne. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurowanie zapory Key Vault sieciowej

Domyślnie program Key Vault dostęp do zasobów za pośrednictwem publicznych adresów IP. W celu zwiększenia bezpieczeństwa można również ograniczyć dostęp do określonych zakresów adresów IP, punktów końcowych usługi, sieci wirtualnych lub prywatnych punktów końcowych.

Aby uzyskać więcej informacji, zobacz Access Azure Key Vault behind a firewall (Dostęp [za zaporą).](./access-behind-firewall.md)


## <a name="the-key-vault-authentication-flow"></a>Przepływ Key Vault uwierzytelniania

1. Jednostki usługi żądają uwierzytelnienia w usłudze Azure AD, na przykład:
    * Użytkownik loguje się do Azure Portal przy użyciu nazwy użytkownika i hasła.
    * Aplikacja wywołuje interfejs API REST platformy Azure, prezentując identyfikator klienta i klucz tajny lub certyfikat klienta.
    * Zasób platformy Azure, taki jak maszyna wirtualna z tożsamością zarządzaną, kontaktuje się z punktem końcowym REST [usługi Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) w celu uzyskania tokenu dostępu.

1. Jeśli uwierzytelnianie w usłudze Azure AD powiedzie się, jednostki usługi zostanie przyznany token OAuth.

1. Nazwa główna usługi wykonuje wywołanie do interfejsu API REST Key Vault za pośrednictwem Key Vault końcowego (URI) klienta.

1. Key Vault Zapora sprawdza następujące kryteria. Jeśli którekolwiek kryterium zostanie spełnione, wywołanie będzie dozwolone. W przeciwnym razie wywołanie zostanie zablokowane i zostanie zwrócona niedozwolona odpowiedź.

    * Zapora jest wyłączona, a publiczny punkt końcowy Key Vault jest dostępny z publicznego Internetu.
    * Wywołujący jest usługą [Key Vault,](./overview-vnet-service-endpoints.md#trusted-services)dzięki czemu może pominąć zaporę.
    * Wywołujący jest wymieniony w zaporze według adresu IP, sieci wirtualnej lub punktu końcowego usługi.
    * Wywołujący może nawiązać Key Vault za pośrednictwem skonfigurowanego połączenia łącza prywatnego.    

1. Jeśli zapora zezwala na wywołanie, Key Vault usługę Azure AD w celu zweryfikowania tokenu dostępu jednostki usługi.

1. Key Vault sprawdza, czy podmiot zabezpieczeń usługi ma niezbędne zasady dostępu dla żądanej operacji. Jeśli nie, Key Vault zwraca niedozwoloną odpowiedź.

1. Key Vault wykonuje żądaną operację i zwraca wynik.

Na poniższym diagramie przedstawiono proces wywoływania przez aplikację interfejsu API Key Vault "Pobierz klucz tajny":

![Przepływ Azure Key Vault uwierzytelniania](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault SDK dla wpisów tajnych, certyfikatów i kluczy wykonać dodatkowe wywołanie do usługi Key Vault bez tokenu dostępu, co powoduje odpowiedź 401 do pobrania informacji o dzierżawie. Aby uzyskać więcej informacji, [zobacz Uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Przykłady kodu

W poniższej tabeli pokazano linki do różnych artykułów, w których pokazano, jak pracować Key Vault kodem aplikacji przy użyciu bibliotek zestawu Azure SDK dla tego języka. Dla wygody dostępne są inne interfejsy, takie jak interfejs wiersza Azure Portal Azure i interfejs wiersza polecenia.

| Key Vault tajne | Key Vault kluczy | Key Vault certyfikatów |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Witryna Azure Portal](../secrets/quick-create-portal.md) | [Witryna Azure Portal](../keys/quick-create-portal.md) | [Witryna Azure Portal](../certificates/quick-create-portal.md) |
| [Interfejs wiersza polecenia platformy Azure](../secrets/quick-create-cli.md) | [Interfejs wiersza polecenia platformy Azure](../keys/quick-create-cli.md) | [Interfejs wiersza polecenia platformy Azure](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [Szablon usługi ARM](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Następne kroki

- [Key Vault rozwiązywania problemów z zasadami dostępu](troubleshooting-access-issues.md)
- [Key Vault błędów interfejsu API REST](rest-error-codes.md)
- [Key Vault dewelopera](developers-guide.md)
- [Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
