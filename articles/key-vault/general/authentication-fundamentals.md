---
title: Azure Key Vault podstawowe uwierzytelniania
description: Dowiedz się, jak działa model uwierzytelniania magazynu kluczy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/15/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f0cd2fb341dd790a7628cc3cf6a5bdd87d7c3687
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753420"
---
# <a name="key-vault-authentication-fundamentals"></a>Podstawy uwierzytelniania przy użyciu usługi Key Vault

Azure Key Vault umożliwia bezpieczne przechowywanie poświadczeń aplikacji, takich jak wpisy tajne, klucze i certyfikaty, oraz zarządzanie nimi w centralnym i bezpiecznym repozytorium w chmurze. Key Vault eliminuje konieczność przechowywania poświadczeń w aplikacjach. Aplikacje mogą uwierzytelniać się Key Vault w czasie rzeczywistym w celu pobrania poświadczeń.

Jako administrator możesz ściśle kontrolować, którzy użytkownicy i aplikacje mogą uzyskać dostęp do magazynu kluczy, oraz ograniczyć i przeprowadzić inspekcję wykonywanych przez nich operacji. W tym dokumencie wyjaśniono podstawowe pojęcia dotyczące modelu dostępu do magazynu kluczy. Zapewnia ona poziom wprowadzający wiedzy i pokazuje, jak można uwierzytelniać użytkownika lub aplikację w magazynie kluczy od początku do końca.

## <a name="required-knowledge"></a>Wymagana wiedza

W tym dokumencie założono, że znasz następujące pojęcia. Jeśli nie znasz żadnego z tych pojęć, przed przystąpieniem postępuj zgodnie z linkami pomocy.

* Azure Active Directory [link](../../active-directory/fundamentals/active-directory-whatis.md)
* Link podmiotów [zabezpieczeń](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault konfiguracji systemu Key Vault podsumowanie

1. Zarejestruj użytkownika lub aplikację w u Azure Active Directory jako podmiot zabezpieczeń.
1. Skonfiguruj przypisanie roli dla podmiotu zabezpieczeń w Azure Active Directory.
1. Skonfiguruj zasady dostępu magazynu kluczy dla podmiotu zabezpieczeń.
1. Skonfiguruj Key Vault dostępu zapory do magazynu kluczy (opcjonalnie).
1. Przetestuj zdolność podmiotu zabezpieczeń do uzyskiwania dostępu do magazynu kluczy.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Rejestrowanie użytkownika lub aplikacji w u Azure Active Directory jako podmiot zabezpieczeń

Gdy użytkownik lub aplikacja wykonuje żądanie do magazynu kluczy, żądanie musi zostać najpierw uwierzytelnione przez Azure Active Directory. Aby to działało, użytkownik lub aplikacja musi zostać zarejestrowana w Azure Active Directory jako podmiot zabezpieczeń.

Skorzystaj z poniższych linków do dokumentacji, aby dowiedzieć się, jak zarejestrować użytkownika lub aplikację w Azure Active Directory.
**Upewnij się, że tworzysz hasło do rejestracji użytkownika i klucz tajny klienta lub poświadczenia certyfikatu klienta dla aplikacji.**

* Rejestrowanie użytkownika za Azure Active Directory [linku](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Rejestrowanie aplikacji w linku Azure Active Directory [aplikacji](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Przypisywanie roli podmiotowi zabezpieczeń

Możesz użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby przypisać uprawnienia do podmiotów zabezpieczeń. Te uprawnienia są nazywane przypisaniami ról.

W kontekście magazynu kluczy te przypisania ról określają poziom dostępu podmiotu zabezpieczeń do płaszczyzny zarządzania (znanej także jako płaszczyzna sterowania) magazynu kluczy. Te przypisania ról nie zapewniają bezpośredniego dostępu do wpisów tajnych płaszczyzny danych, ale zapewniają dostęp do zarządzania właściwościami magazynu kluczy. Na przykład użytkownik lub aplikacja  z przypisaną rolą Czytelnik nie będzie mieć uprawnienia do zmiany ustawień zapory  magazynu kluczy, natomiast użytkownik lub aplikacja z przypisaną rolą Współautor może wprowadzać zmiany. Żadna z ról nie będzie mieć bezpośredniego dostępu do wykonywania operacji na wpisach tajnych, kluczach i certyfikatach, takich jak tworzenie lub pobieranie ich wartości, dopóki nie zostanie im przypisany dostęp do płaszczyzny danych magazynu kluczy. Zostanie to uwzględnione w następnym kroku.

>[!IMPORTANT]
> Mimo że użytkownicy z rolą Współautor lub Właściciel nie mają domyślnie dostępu do wykonywania operacji na wpisach tajnych przechowywanych w magazynie kluczy, role Współautor i Właściciel zapewniają uprawnienia do dodawania lub usuwania zasad dostępu do wpisów tajnych przechowywanych w magazynie kluczy. W związku z tym użytkownik z tymi przypisaniami ról może udzielić sobie dostępu do wpisów tajnych w magazynie kluczy. Z tego powodu zaleca się, aby tylko administratorzy mieli dostęp do ról Współautor lub Właściciel. Użytkownikom i aplikacjom, które muszą tylko pobierać wpisy tajne z magazynu kluczy, należy przyznać rolę Czytelnik. **Więcej szczegółów można znaleźć w następnej sekcji.**

>[!NOTE]
> Po przypisaniu przypisania roli do użytkownika na poziomie dzierżawy usługi Azure Active Directory ten zestaw uprawnień będzie ujmować wszystkie subskrypcje, grupy zasobów i zasoby w zakresie przypisania. Aby postępować zgodnie z podmiotem zabezpieczeń o najmniejszych uprawnieniach, możesz określić to przypisanie roli w bardziej szczegółowym zakresie. Możesz na przykład przypisać użytkownikowi rolę Czytelnik na poziomie subskrypcji i rolę Właściciel dla pojedynczego magazynu kluczy. Przejdź do ustawień zarządzania dostępem do tożsamości (IAM) subskrypcji, grupy zasobów lub magazynu kluczy, aby wykonać przypisanie roli w bardziej szczegółowym zakresie.

* Aby dowiedzieć się więcej o linku do ról platformy [Azure](../../role-based-access-control/built-in-roles.md)
* Aby dowiedzieć się więcej na temat przypisywania lub usuwania przypisań [ról, link](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurowanie zasad dostępu magazynu kluczy dla podmiotu zabezpieczeń

Przed udzieleniem użytkownikom i aplikacjom dostępu do magazynu kluczy należy poznać różne typy operacji, które mogą być wykonywane w magazynie kluczy. Istnieją dwa główne typy operacji magazynu kluczy: operacje płaszczyzny zarządzania (nazywanej również płaszczyzną sterowania) i operacje płaszczyzny danych.

W tej tabeli przedstawiono kilka przykładów różnych operacji, które są kontrolowane przez płaszczyznę zarządzania i płaszczyznę danych. Operacje, które zmieniają właściwości magazynu kluczy, to operacje płaszczyzny zarządzania. Operacje, które zmieniają lub pobierają wartość wpisów tajnych przechowywanych w magazynie kluczy, są operacjami płaszczyzny danych.

|Operacje płaszczyzny zarządzania (przykłady)|Operacje płaszczyzny danych (przykłady)|
| --- | --- |
| Tworzenie Key Vault | Tworzenie klucza, klucza tajnego, certyfikatu
| Usuwanie Key Vault | Usuwanie klucza, klucza tajnego, certyfikatu
| Dodawanie lub usuwanie Key Vault przypisań ról | Lista i uzyskiwanie wartości kluczy, wpisów tajnych, certyfikatów
| Dodawanie lub usuwanie Key Vault dostępu | Tworzenie kopii zapasowej i przywracanie kluczy, wpisów tajnych, certyfikatów
| Modyfikowanie Key Vault ustawień zapory | Odnawianie kluczy, wpisów tajnych, certyfikatów
| Modyfikowanie Key Vault odzyskiwania | Przeczyszczanie lub odzyskiwanie nieu programowo usuniętych kluczy, wpisów tajnych, certyfikatów
| Modyfikowanie Key Vault ustawień dzienników diagnostycznych

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Przypisania ról dostępu & Azure Active Directory zarządzania

Azure Active Directory przypisań ról przyznają dostęp do wykonywania operacji płaszczyzny zarządzania w magazynie kluczy. Ten dostęp jest zwykle udzielany użytkownikom, a nie aplikacjom. Można ograniczyć operacje płaszczyzny zarządzania, które użytkownik może wykonywać, zmieniając przypisanie roli użytkownika.

Na przykład przypisanie użytkownikowi roli czytelnika Key Vault umożliwi mu wyświetlanie właściwości magazynu kluczy, takich jak zasady dostępu, ale nie umożliwi mu zmiany. Przypisanie użytkownika do roli właściciela umożliwi mu pełny dostęp do zmiany ustawień płaszczyzny zarządzania magazynu kluczy.

Przypisania ról są kontrolowane w bloku magazynu kluczy Access Control (IAM). Jeśli chcesz, aby określony użytkownik miał dostęp jako czytelnik lub administrator wielu zasobów magazynu kluczy, możesz utworzyć przypisanie roli na poziomie magazynu, grupy zasobów lub subskrypcji, a przypisanie roli zostanie dodane do wszystkich zasobów w zakresie przypisania.

Dostęp do płaszczyzny danych lub dostęp do wykonywania operacji na kluczach, wpisach tajnych i certyfikatach przechowywanych w magazynie kluczy można dodać na jeden z dwóch sposobów.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opcja dostępu do płaszczyzny danych 1: zasady Key Vault dostępu do płaszczyzny danych

Zasady dostępu magazynu kluczy przyznają użytkownikom i aplikacjom dostęp do wykonywania operacji płaszczyzny danych w magazynie kluczy.

> [!NOTE]
> Ten model dostępu nie jest zgodny z funkcją RBAC platformy Azure dla magazynu kluczy (opcja 2) udokumentowaną poniżej. Musisz wybrać jedną z nich. Będzie można to zrobić po kliknięciu karty Zasady dostępu w magazynie kluczy.

Klasyczne zasady dostępu są szczegółowe, co oznacza, że można zezwolić lub odmówić poszczególnym użytkownikom lub aplikacjom możliwości wykonywania poszczególnych operacji w magazynie kluczy. Oto kilka przykładów:

* Podmiot zabezpieczeń 1 może wykonać dowolną operację klucza, ale nie może wykonywać żadnych operacji na kluczu tajnym lub certyfikacie.
* Podmiot zabezpieczeń 2 może wyświetlić i odczytać wszystkie klucze, wpisy tajne i certyfikaty, ale nie może wykonywać żadnych operacji tworzenia, usuwania ani odnawiania.
* Podmiot zabezpieczeń 3 może tworzyć kopie zapasowe i przywracać wszystkie wpisy tajne, ale nie może odczytywać wartości samych wpisów tajnych.

Jednak klasyczne zasady dostępu nie zezwalają na uprawnienia na poziomie obiektu, a przypisane uprawnienia są stosowane do zakresu pojedynczego magazynu kluczy. Jeśli na przykład udzielisz uprawnienia zasad dostępu "Secret Get" do podmiotu zabezpieczeń w określonym magazynie kluczy, podmiot zabezpieczeń będzie mieć możliwość uzyskania wszystkich wpisów tajnych w tym konkretnym magazynie kluczy. Jednak to uprawnienie "Pobierz klucz tajny" nie będzie automatycznie rozszerzane na inne magazyny kluczy i musi być przypisane jawnie.

> [!IMPORTANT]
> Klasyczne zasady dostępu magazynu kluczy i Azure Active Directory ról są od siebie niezależne. Przypisanie podmiotu zabezpieczeń roli "Współautor" na poziomie subskrypcji nie umożliwi automatycznie podmiotowi zabezpieczeń wykonywania operacji na płaszczyźnie danych w każdym magazynie kluczy w zakresie subskrypcji. Podmiot zabezpieczeń nadal musi zostać udzielony lub przyznać sobie uprawnienia zasad dostępu do wykonywania operacji płaszczyzny danych.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>Opcja 2 dostępu do płaszczyzny danych: RBAC platformy Azure dla Key Vault

Nowym sposobem udzielania dostępu do płaszczyzny danych magazynu kluczy jest korzystanie z kontroli dostępu na podstawie ról (RBAC) platformy Azure dla magazynu kluczy.

> [!NOTE]
> Ten model dostępu nie jest zgodny z przedstawionymi powyżej klasycznymi zasadami dostępu magazynu kluczy. Musisz wybrać jedną z nich. Ten wybór będzie można dokonać po kliknięciu karty Zasady dostępu w magazynie kluczy.

Key Vault są zestawem wbudowanych przypisań ról platformy Azure, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do kluczy, wpisów tajnych i certyfikatów. Ten model uprawnień udostępnia również dodatkowe możliwości, które nie są dostępne w klasycznym modelu zasad dostępu magazynu kluczy.

* Uprawnieniami RBAC platformy Azure można zarządzać na dużą skalę, umożliwiając użytkownikom przypisywanie tych ról na poziomie subskrypcji, grupy zasobów lub indywidualnego magazynu kluczy. Użytkownik będzie miał uprawnienia do płaszczyzny danych do wszystkich magazynów kluczy w zakresie przypisania RBAC platformy Azure. Eliminuje to konieczność przypisywania indywidualnych uprawnień zasad dostępu do poszczególnych użytkowników/aplikacji na magazyn kluczy.

* Uprawnienia RBAC platformy Azure są zgodne z Privileged Identity Management lub PIM. Dzięki temu można skonfigurować kontrolę dostępu just in time dla ról uprzywilejowanych, takich jak Key Vault administrator. Jest to najlepsze rozwiązanie w zakresie zabezpieczeń i jest zgodna z zasadą najmniejszych uprawnień przez wyeliminowanie stałego dostępu do magazynów kluczy.

Aby dowiedzieć się więcej na temat kontroli dostępu na Key Vault Azure dla klientów, zobacz następujące dokumenty:

* Azure RBAC for Key Vault [link](rbac-guide.md)
* Link do kontroli RBAC platformy Azure Key Vault ról [platformy Azure](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Konfigurowanie Key Vault zapory

Domyślnie magazyn kluczy zezwala na wysyłanie ruchu z publicznego Internetu do magazynu kluczy za pośrednictwem publicznego punktu końcowego. Aby uzyskać dodatkową warstwę zabezpieczeń, można skonfigurować zaporę Azure Key Vault, aby ograniczyć dostęp do publicznego punktu końcowego magazynu kluczy.

Aby włączyć zaporę magazynu kluczy, kliknij kartę Sieć w portalu magazynu kluczy i wybierz przycisk radiowy Zezwalaj na dostęp z: "Prywatny punkt końcowy i wybrane sieci". Jeśli włączysz zaporę magazynu kluczy, możesz zezwolić na ruch przez zaporę magazynu kluczy.

* Dodaj adresy IPv4 do listy zezwalań zapory magazynu kluczy. Ta opcja sprawdza się najlepiej w przypadku aplikacji, które mają statyczne adresy IP.

* Dodaj sieć wirtualną do zapory magazynu kluczy. Ta opcja sprawdza się najlepiej w przypadku zasobów platformy Azure, które mają dynamiczne adresy IP, takich Virtual Machines. Możesz dodać zasoby platformy Azure do sieci wirtualnej i dodać sieć wirtualną do listy zezwalań zapory magazynu kluczy. Ta opcja używa punktu końcowego usługi, który jest prywatnym adresem IP w sieci wirtualnej. Zapewnia to dodatkową warstwę ochrony, dzięki czemu żaden ruch między magazynem kluczy a siecią wirtualną nie jest przekierowyny przez publiczny Internet. Aby dowiedzieć się więcej na temat punktu końcowego usługi, zapoznaj się z następującą dokumentacją. [Link](./network-security.md)

* Dodaj połączenie łącza prywatnego do magazynu kluczy. Ta opcja łączy sieć wirtualną bezpośrednio z konkretnym wystąpieniem magazynu kluczy, efektywnie przenosząc magazyn kluczy do sieci wirtualnej. Aby dowiedzieć się więcej na temat konfigurowania połączenia prywatnego punktu końcowego z magazynem kluczy, zobacz następujący [link](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testowanie możliwości uzyskiwania dostępu do magazynu kluczy przez jednostkę usługi

Po zakończeniu wszystkich powyższych kroków będziesz mieć możliwość ustawienia i pobrania wpisów tajnych z magazynu kluczy.

### <a name="authentication-process-for-users-examples"></a>Proces uwierzytelniania użytkowników (przykłady)

* Użytkownicy mogą zalogować się do Azure Portal, aby korzystać z magazynu kluczy. [Key Vault Portal Szybki start](./quick-create-portal.md)

* Użytkownik może używać interfejsu wiersza polecenia platformy Azure do korzystania z magazynu kluczy. [Key Vault szybki start dla interfejsu wiersza polecenia platformy Azure](./quick-create-cli.md)

* Użytkownik może używać Azure PowerShell magazynu kluczy. [Key Vault Azure PowerShell Szybki start](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory uwierzytelniania dla aplikacji lub usług (przykłady)

* Aplikacja udostępnia klucz tajny klienta i identyfikator klienta w funkcji w celu Azure Active Directory tokenu. 

* Aplikacja udostępnia certyfikat w celu uzyskania tokenu Azure Active Directory tokenu. 

* Zasób platformy Azure używa uwierzytelniania MSI w celu uzyskania Azure Active Directory tokenu. 

* Dowiedz się więcej o linku uwierzytelniania tożsamości usługi [msi](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>Proces uwierzytelniania dla aplikacji (przykład w języku Python)

Użyj następującego przykładowego kodu, aby sprawdzić, czy aplikacja może pobrać klucz tajny z magazynu kluczy przy użyciu skonfigurowanej jednostki usługi.

>[!NOTE]
>Ten przykład jest tylko w celach demonstracyjnych i testowych. **NIE UŻYWAJ UWIERZYTELNIANIA ZA POMOCĄ KLUCZ TAJNY KLIENTA W ŚRODOWISKU PRODUKCYJNYM** Nie jest to bezpieczna praktyka projektowania. Najlepszym rozwiązaniem jest użycie certyfikatu klienta lub uwierzytelniania MSI.

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

Aby dowiedzieć się więcej na temat uwierzytelniania magazynu kluczy, zobacz następujący dokument. [Uwierzytelnianie usługi Key Vault](./authentication.md)
