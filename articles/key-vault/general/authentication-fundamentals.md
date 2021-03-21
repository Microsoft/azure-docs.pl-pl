---
title: Podstawowe informacje dotyczące uwierzytelniania Azure Key Vault
description: Dowiedz się, jak działa model uwierzytelniania magazynu kluczy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103572812"
---
# <a name="key-vault-authentication-fundamentals"></a>Podstawy uwierzytelniania przy użyciu usługi Key Vault

Azure Key Vault pozwala bezpiecznie przechowywać poświadczenia aplikacji, takie jak wpisy tajne, klucze i certyfikaty w centralnym i bezpiecznym repozytorium chmury i zarządzać nimi. Key Vault eliminuje konieczność przechowywania poświadczeń w aplikacjach. Aplikacje mogą uwierzytelniać się w celu Key Vault w czasie wykonywania w celu pobrania poświadczeń.

Jako administrator możesz ściśle kontrolować, którzy użytkownicy i aplikacje mogą uzyskiwać dostęp do magazynu kluczy, a także ograniczać i przeprowadzać inspekcję wykonywanych przez nie operacji. W tym dokumencie opisano podstawowe koncepcje modelu dostępu magazynu kluczy. Zapewni to wstępny poziom wiedzy i pokazuje, jak można uwierzytelnić użytkownika lub aplikację w magazynie kluczy od początku do końca.

## <a name="required-knowledge"></a>Wymagana wiedza

W tym dokumencie założono, że znasz następujące pojęcia. Jeśli nie znasz żadnych z tych koncepcji, przed kontynuowaniem postępuj zgodnie z łączami pomocy.

* Azure Active Directory [łącze](../../active-directory/fundamentals/active-directory-whatis.md)
* [Łącze](./authentication.md#app-identity-and-security-principals) podmiotów zabezpieczeń

## <a name="key-vault-configuration-steps-summary"></a>Podsumowanie kroków konfiguracji Key Vault

1. Zarejestruj użytkownika lub aplikację w Azure Active Directory jako podmiot zabezpieczeń.
1. Skonfiguruj przypisanie roli dla podmiotu zabezpieczeń w Azure Active Directory.
1. Skonfiguruj zasady dostępu magazynu kluczy dla podmiotu zabezpieczeń.
1. Skonfiguruj dostęp Key Vault zapory do magazynu kluczy (opcjonalnie).
1. Przetestuj możliwość uzyskiwania dostępu do magazynu kluczy przez podmiot zabezpieczeń.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Rejestrowanie użytkownika lub aplikacji w Azure Active Directory jako podmiot zabezpieczeń

Gdy użytkownik lub aplikacja wysyła żądanie do magazynu kluczy, żądanie musi zostać najpierw uwierzytelnione przez Azure Active Directory. Aby to działało, użytkownik lub aplikacja musi zostać zarejestrowana w Azure Active Directory jako podmiot zabezpieczeń.

Skorzystaj z poniższych linków do dokumentacji, aby dowiedzieć się, jak zarejestrować użytkownika lub aplikację w Azure Active Directory.
**Upewnij się, że utworzono hasło do rejestracji użytkownika i klucz tajny klienta lub poświadczenie certyfikatu klienta dla aplikacji.**

* Rejestrowanie użytkownika w Azure Active Directory [linku](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Rejestrowanie aplikacji w Azure Active Directory [linku](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Przypisywanie podmiotu zabezpieczeń roli

Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można przypisać uprawnienia do podmiotów zabezpieczeń. Te uprawnienia są nazywane przypisaniami ról.

W kontekście magazynu kluczy te przypisania ról określają poziom dostępu podmiotu zabezpieczeń do płaszczyzny zarządzania (zwanej również płaszczyzną kontroli) magazynu kluczy. Te przypisania ról nie zapewniają bezpośredniego dostępu do kluczy tajnych płaszczyzny danych, ale zapewniają dostęp do zarządzania właściwościami magazynu kluczy. Na przykład użytkownik lub aplikacja z przypisaną **rolą czytelnika** nie będzie mogła wprowadzać zmian w ustawieniach zapory magazynu kluczy, podczas gdy użytkownik lub aplikacja przypisana do **roli współautor** może wprowadzać zmiany. Żadna rola nie będzie mieć bezpośredniego dostępu do wykonywania operacji na wpisach tajnych, kluczach i certyfikatach, takich jak tworzenie lub pobieranie ich wartości, dopóki nie zostanie przypisany dostęp do płaszczyzny danych magazynu kluczy. Zostało to omówione w następnym kroku.

>[!IMPORTANT]
> Chociaż użytkownicy z rolą współautor lub owner nie mają dostępu do wykonywania operacji na wpisach tajnych przechowywanych w magazynie kluczy, domyślnie role współautor i właściciela, zapewniają uprawnienia do dodawania lub usuwania zasad dostępu do wpisów tajnych przechowywanych w magazynie kluczy. W związku z tym użytkownik z tymi przypisaniami ról może udzielić sobie dostępu do wpisów tajnych w magazynie kluczy. Z tego powodu zaleca się, aby tylko administratorzy mieli dostęp do ról współautora lub właściciela. Tylko użytkownicy i aplikacje, które muszą pobierać wpisy tajne z magazynu kluczy, powinny mieć przyznane rolę czytelnik. **Więcej szczegółów znajduje się w następnej sekcji.**

>[!NOTE]
> Po przypisaniu roli do użytkownika na poziomie dzierżawy Azure Active Directory ten zestaw uprawnień będzie Trickle w dół do wszystkich subskrypcji, grup zasobów i zasobów w zakresie przypisania. Aby obsłużyć najwyższe uprawnienia, można przypisywać tę rolę bardziej szczegółowym zakresem. Na przykład można przypisać użytkownikowi rolę czytelnik na poziomie subskrypcji i rolę właściciela dla jednego magazynu kluczy. Przejdź do ustawień zarządzania dostępem do tożsamości (IAM) subskrypcji, grupy zasobów lub magazynu kluczy, aby przypisywać rolę z bardziej szczegółowym zakresem.

* Aby dowiedzieć się więcej o usłudze Azure role [link](../../role-based-access-control/built-in-roles.md)
* Aby dowiedzieć się więcej na temat przypisywania lub usuwania [linku](../../role-based-access-control/role-assignments-portal.md) przypisań ról

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurowanie zasad dostępu magazynu kluczy dla podmiotu zabezpieczeń

Przed przyznaniem dostępu dla użytkowników i aplikacji w celu uzyskania dostępu do magazynu kluczy ważne jest, aby zrozumieć różne typy operacji, które mogą być wykonywane w magazynie kluczy. Istnieją dwa główne typy operacji magazynu kluczy, płaszczyzna zarządzania (określana także jako płaszczyzna kontroli) oraz operacje płaszczyzny danych.

W tej tabeli przedstawiono kilka przykładów różnych operacji, które są kontrolowane przez płaszczyznę zarządzania w porównaniu do płaszczyzny danych. Operacje, które zmieniają właściwości magazynu kluczy, są operacje płaszczyzny zarządzania. Operacje, które zmieniają lub pobierają wartość wpisów tajnych przechowywanych w magazynie kluczy, to operacje płaszczyzny danych.

|Operacje płaszczyzny zarządzania (przykłady)|Operacje płaszczyzny danych (przykłady)|
| --- | --- |
| Utwórz Key Vault | Tworzenie klucza, wpisu tajnego, certyfikatu
| Usuń Key Vault | Usuwanie klucza, wpisu tajnego, certyfikatu
| Dodawanie lub usuwanie przypisań ról Key Vault | Wyświetlanie listy i pobieranie wartości kluczy, wpisów tajnych, certyfikatów
| Dodawanie lub usuwanie zasad dostępu Key Vault | Klucze i przywracanie kopii zapasowych, wpisy tajne, certyfikaty
| Modyfikowanie ustawień zapory Key Vault | Odnawianie kluczy, wpisów tajnych, certyfikatów
| Modyfikowanie ustawień odzyskiwania Key Vault | Przeczyszczanie lub odzyskanie usuniętych nietrwałych kluczy, wpisów tajnych, certyfikatów
| Modyfikowanie ustawień dzienników diagnostycznych Key Vault

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Dostęp do płaszczyzny zarządzania & Azure Active Directory przypisania roli

Przypisania ról Azure Active Directory udzielą dostępu do wykonywania operacji płaszczyzny zarządzania w magazynie kluczy. Ten dostęp jest zazwyczaj udzielany użytkownikom, a nie aplikacjom. Można ograniczyć działania płaszczyzny zarządzania, które użytkownik może wykonywać, zmieniając przypisanie roli użytkownika.

Na przykład przypisanie użytkownikowi roli czytnika Key Vault do użytkownika umożliwi użytkownikom wyświetlanie właściwości magazynu kluczy, takich jak zasady dostępu, ale nie pozwala na wprowadzanie żadnych zmian. Przypisanie użytkownika, rola właściciela umożliwi im pełny dostęp do zmiany ustawień płaszczyzny zarządzania magazynem kluczy.

Przypisania ról są kontrolowane w bloku Magazyn kluczy Access Control (IAM). Jeśli chcesz, aby określony użytkownik miał dostęp jako czytelnik lub być administratorem wielu zasobów magazynu kluczy, możesz utworzyć przypisanie roli do magazynu, grupy zasobów lub poziomu subskrypcji, a przypisanie roli zostanie dodane do wszystkich zasobów w zakresie przypisania.

Dostęp do płaszczyzny danych lub dostęp do wykonywania operacji dotyczących kluczy, wpisów tajnych i certyfikatów przechowywanych w magazynie kluczy można dodać na jeden z dwóch sposobów.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opcja dostępu do płaszczyzny danych 1: klasyczne Key Vault zasad dostępu

Zasady dostępu magazynu kluczy umożliwiają użytkownikom i aplikacjom dostęp do wykonywania operacji na płaszczyznach danych w magazynie kluczy.

> [!NOTE]
> Ten model dostępu nie jest zgodny z usługą Azure RBAC dla magazynu kluczy (opcja 2) udokumentowaną poniżej. Musisz wybrać jeden z nich. Możesz wybrać tę opcję po kliknięciu karty zasady dostępu w magazynie kluczy.

Zasady dostępu klasycznego są szczegółowe, co oznacza, że można zezwolić lub zablokować możliwość wykonywania indywidualnych operacji przez poszczególnych użytkowników lub aplikacji w ramach magazynu kluczy. Oto kilka przykładów:

* Podmiot zabezpieczeń 1 może wykonać dowolną operację klucza, ale nie może wykonać żadnych operacji tajnych lub certyfikatów.
* Podmiot zabezpieczeń 2 może wyświetlać i odczytywać wszystkie klucze, wpisy tajne oraz certyfikaty, ale nie może wykonywać żadnych operacji tworzenia, usuwania i odnawiania.
* Podmiot zabezpieczeń 3 może tworzyć kopie zapasowe i przywracać wszystkie wpisy tajne, ale nie może odczytać wartości samych wpisów tajnych.

Jednak klasyczne zasady dostępu nie zezwalają na uprawnienia na poziomie obiektu, a przypisane uprawnienia są stosowane do zakresu poszczególnych magazynów kluczy. Na przykład jeśli przyznano uprawnienia "tajne pobieranie" zasad dostępu do podmiotu zabezpieczeń w określonym magazynie kluczy, podmiot zabezpieczeń ma możliwość pobrania wszystkich wpisów tajnych w tym konkretnym magazynie kluczy. Jednak uprawnienie "Pobierz klucz tajny" nie będzie automatycznie zwiększać do innych magazynów kluczy i muszą zostać przypisane jawnie.

> [!IMPORTANT]
> Zasady dostępu do magazynu kluczy klasycznych i przypisania ról Azure Active Directory są niezależne od siebie nawzajem. Przypisanie podmiotu zabezpieczeń roli "Współautor" na poziomie subskrypcji nie spowoduje automatycznego zezwolenia podmiotowi zabezpieczeń na wykonywanie operacji w obrębie poszczególnych magazynów kluczy w ramach subskrypcji. Podmiot zabezpieczeń musi być nadal musi być udzielony lub udzielić sobie uprawnień dostępu, aby wykonywać operacje płaszczyzny danych.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Opcja dostępu do płaszczyzny danych 2: Azure RBAC dla Key Vault (wersja zapoznawcza)

Nowy sposób udzielenia dostępu do płaszczyzny danych magazynu kluczy polega na użyciu kontroli dostępu opartej na rolach (Azure RBAC) dla magazynu kluczy.

> [!NOTE]
> Ten model dostępu nie jest zgodny z klasycznymi zasadami dostępu magazynu kluczy podanymi powyżej. Musisz wybrać jeden z nich. Możesz wybrać tę opcję po kliknięciu karty zasady dostępu w magazynie kluczy.

Przypisania ról Key Vault są zestawem wbudowanych przypisań ról platformy Azure, które obejmują wspólne zestawy uprawnień używane do uzyskiwania dostępu do kluczy, wpisów tajnych i certyfikatów. Ten model uprawnień umożliwia również dodatkowe możliwości, które nie są dostępne w modelu zasad dostępu do magazynu kluczy klasycznych.

* Uprawnieniami RBAC platformy Azure można zarządzać w odpowiedniej skali, umożliwiając użytkownikom ich przypisanie do subskrypcji, grupy zasobów lub poszczególnych poziomów magazynów kluczy. Użytkownik będzie miał uprawnienia do płaszczyzny danych dla wszystkich magazynów kluczy w zakresie przypisania kontroli RBAC platformy Azure. Eliminuje to konieczność przypisywania uprawnień poszczególnych zasad dostępu dla użytkownika/aplikacji na magazyn kluczy.

* Uprawnienia usługi Azure RBAC są zgodne z programem Privileged Identity Management lub PIM. Pozwala to na skonfigurowanie kontroli dostępu just in Time dla ról uprzywilejowanych, takich jak administrator Key Vault. Jest to najlepsze rozwiązanie w zakresie zabezpieczeń, które jest zgodne z najważniejszym poziomem uprawnień, eliminując stały dostęp do Twoich magazynów kluczy.

Aby dowiedzieć się więcej o usłudze Azure RBAC dla Key Vault, zobacz następujące dokumenty:

* [Link](./secure-your-key-vault.md#management-plane-and-azure-rbac) RBAC platformy Azure dla Key Vault
* [Link](../../role-based-access-control/built-in-roles.md#key-vault-administrator) usługi Azure RBAC dla ról Key Vault

## <a name="configure-key-vault-firewall"></a>Konfigurowanie zapory Key Vault

Domyślnie Magazyn kluczy umożliwia wysyłanie danych z publicznej sieci Internet do magazynu kluczy za pośrednictwem publicznego punktu końcowego. Aby uzyskać dodatkową warstwę zabezpieczeń, można skonfigurować zaporę Azure Key Vault, aby ograniczyć dostęp do publicznego punktu końcowego magazynu kluczy.

Aby włączyć zaporę magazynu kluczy, kliknij kartę Sieć w portalu magazynu kluczy i wybierz przycisk radiowy, aby zezwolić na dostęp z: "prywatny punkt końcowy i wybrane sieci". W przypadku wybrania opcji włączenia zapory magazynu kluczy są dostępne sposoby zezwalania na ruch przez zaporę magazynu kluczy.

* Dodaj adresy IPv4 do listy dozwolonych zapór magazynu kluczy. Ta opcja sprawdza się najlepiej w przypadku aplikacji, które mają statyczne adresy IP.

* Dodaj sieć wirtualną do zapory magazynu kluczy. Ta opcja sprawdza się najlepiej w przypadku zasobów platformy Azure, które mają dynamiczne adresy IP, takie jak Virtual Machines. Możesz dodać zasoby platformy Azure do sieci wirtualnej i dodać sieć wirtualną do listy dozwolonych zapór magazynu kluczy. Ta opcja używa punktu końcowego usługi, który jest prywatnym adresem IP w ramach sieci wirtualnej. Zapewnia to dodatkową warstwę ochrony, dlatego żaden ruch między magazynem kluczy i siecią wirtualną nie jest kierowany przez publiczny Internet. Aby dowiedzieć się więcej o punkcie końcowym usługi, zobacz następującą dokumentację. [powiązań](./network-security.md)

* Dodaj połączenie prywatne z magazynem kluczy. Ta opcja nawiązuje połączenie sieci wirtualnej bezpośrednio z określonym wystąpieniem magazynu kluczy, efektywnie udostępniając swój magazyn kluczy w sieci wirtualnej. Aby dowiedzieć się więcej o konfigurowaniu połączenia prywatnego punktu końcowego z magazynem kluczy, zobacz następujący [link](./private-link-service.md) .

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testowanie możliwości uzyskiwania dostępu do magazynu kluczy przez jednostkę usługi

Po wykonaniu wszystkich powyższych czynności będzie można ustawić i pobrać wpisy tajne z magazynu kluczy.

### <a name="authentication-process-for-users-examples"></a>Proces uwierzytelniania dla użytkowników (przykłady)

* Użytkownicy mogą logować się do Azure Portal, aby korzystać z magazynu kluczy. [Przewodnik Szybki Start dotyczący Key Vault Portal](./quick-create-portal.md)

* Użytkownik może użyć interfejsu wiersza polecenia platformy Azure, aby użyć magazynu kluczy. [Key Vault szybki start interfejsu wiersza polecenia platformy Azure](./quick-create-cli.md)

* Użytkownik może używać Azure PowerShell do korzystania z magazynu kluczy. [Key Vault Azure PowerShell — Szybki Start](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Proces uwierzytelniania Azure Active Directory dla aplikacji lub usług (przykłady)

* Aplikacja udostępnia klucz tajny klienta i identyfikator klienta w funkcji, aby uzyskać token Azure Active Directory. 

* Aplikacja udostępnia certyfikat umożliwiający uzyskanie tokenu Azure Active Directory. 

* Zasób platformy Azure używa uwierzytelniania MSI w celu uzyskania tokenu Azure Active Directory. 

* Dowiedz się więcej o [łączu](../../active-directory/managed-identities-azure-resources/overview.md) uwierzytelniania MSI

### <a name="authentication-process-for-application-python-example"></a>Proces uwierzytelniania dla aplikacji (przykład w języku Python)

Użyj poniższego przykładu kodu, aby sprawdzić, czy aplikacja może pobrać wpis tajny z magazynu kluczy przy użyciu skonfigurowanej jednostki usługi.

>[!NOTE]
>Ten przykład służy tylko do celów demonstracyjnych i testowych. **nie używaj uwierzytelniania tajnego klienta w środowisku produkcyjnym** Nie jest to bezpieczna metoda projektowania. Najlepszym rozwiązaniem jest użycie certyfikatu klienta lub uwierzytelniania MSI.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu magazynu kluczy bardziej szczegółowo, zapoznaj się z następującym dokumentem. [Uwierzytelnianie usługi Key Vault](./authentication.md)
