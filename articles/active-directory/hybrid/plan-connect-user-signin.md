---
title: 'Azure AD Connect: Logowanie użytkownika | Microsoft Docs'
description: Azure AD Connect Logowanie użytkownika dla ustawień niestandardowych.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997787"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect opcje logowania użytkownika
Program Azure Active Directory (Azure AD) Connect umożliwia użytkownikom logowanie do zasobów w chmurze i lokalnych przy użyciu tych samych haseł. W tym artykule opisano kluczowe koncepcje dotyczące poszczególnych modeli tożsamości, które ułatwiają wybranie tożsamości, która ma być używana do logowania się do usługi Azure AD.

Jeśli znasz już model tożsamości usługi Azure AD i chcesz dowiedzieć się więcej o określonej metodzie, zobacz odpowiednie łącze:

* [Synchronizacja skrótów haseł](#password-hash-synchronization) z [bezproblemowym logowaniem jednokrotnym (SSO)](how-to-connect-sso.md)
* [Uwierzytelnianie przekazywane](how-to-connect-pta.md) za pomocą [bezproblemowego logowania jednokrotnego (SSO)](how-to-connect-sso.md)
* [Federacyjne Logowanie jednokrotne (z Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federacja z serwerem PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Należy pamiętać, że przez skonfigurowanie Federacji dla usługi Azure AD, należy ustanowić relację zaufania między dzierżawą usługi Azure AD i domenami federacyjnymi. W przypadku tej zaufanej domeny federacyjnej użytkownicy będą mieli dostęp do zasobów w chmurze usługi Azure AD w ramach dzierżawy.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Wybieranie metody logowania użytkownika dla organizacji
Pierwszą decyzją dotyczącą wdrażania Azure AD Connect jest wybór metody uwierzytelniania, która będzie używana przez użytkowników do logowania. Ważne jest, aby upewnić się, że wybrano odpowiednią metodę, która spełnia wymagania w zakresie zabezpieczeń i zaawansowane w organizacji. Uwierzytelnianie ma krytyczne znaczenie, ponieważ umożliwia zweryfikowanie tożsamości użytkownika w celu uzyskania dostępu do aplikacji i danych w chmurze. Aby wybrać właściwą metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt implementacji własnego wyboru. Te czynniki są różne dla każdej organizacji i mogą ulec zmianie w czasie.

Usługa Azure AD obsługuje następujące metody uwierzytelniania: 

* **Uwierzytelnianie w chmurze** — w przypadku wybrania tej metody uwierzytelniania usługa Azure AD obsługuje proces uwierzytelniania logowania użytkownika. Korzystając z uwierzytelniania w chmurze, możesz wybrać jedną z dwóch opcji: 
   * **Synchronizacja skrótów haseł (PHS)** — synchronizacja skrótów haseł umożliwia użytkownikom korzystanie z tej samej nazwy użytkownika i hasła, z których korzystają lokalnie bez konieczności wdrażania żadnej dodatkowej infrastruktury oprócz Azure AD Connect. 
   * **Uwierzytelnianie przekazywane (PTA)** — ta opcja jest podobna do synchronizacji skrótów haseł, ale zapewnia prostą weryfikację hasła przy użyciu lokalnych agentów oprogramowania dla organizacji z silnymi zasadami zabezpieczeń i zgodności.
* **Uwierzytelnianie federacyjne** — w przypadku wybrania tej metody uwierzytelniania usługa Azure AD będzie przełączać proces uwierzytelniania do oddzielnego zaufanego systemu uwierzytelniania, takiego jak AD FS lub system federacyjny innej firmy, aby sprawdzić poprawność logowania użytkownika. 

W przypadku większości organizacji, które po prostu chcą umożliwić użytkownikom logowanie się do Microsoft 365, aplikacji SaaS i innych zasobów opartych na usłudze Azure AD, zaleca się użycie domyślnej opcji synchronizacji skrótów haseł.
 
Aby uzyskać szczegółowe informacje na temat wybierania metody uwierzytelniania, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł
W przypadku synchronizacji skrótów haseł skróty użytkowników są synchronizowane z Active Directory lokalnych do usługi Azure AD. Gdy hasła są zmieniane lub resetowane lokalnie, nowe skróty haseł są natychmiast synchronizowane z usługą Azure AD, dzięki czemu użytkownicy będą mogli zawsze używać tego samego hasła dla zasobów w chmurze i zasobów lokalnych. Hasła nigdy nie są wysyłane do usługi Azure AD ani przechowywane w usłudze Azure AD w postaci zwykłego tekstu. Możesz użyć synchronizacji skrótów haseł razem z funkcją zapisywania zwrotnego haseł w celu włączenia samoobsługowego resetowania hasła w usłudze Azure AD.

Ponadto można włączyć [bezproblemową rejestrację jednokrotną](how-to-connect-sso.md) dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. W przypadku logowania jednokrotnego użytkownicy muszą wprowadzić nazwę użytkownika, aby pomóc im bezpiecznie uzyskać dostęp do zasobów w chmurze.

![Synchronizacja skrótów haseł](./media/plan-connect-user-signin/passwordhash.png)

Aby uzyskać więcej informacji, zobacz artykuł [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md) .

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane
W przypadku uwierzytelniania przekazywanego hasło użytkownika jest sprawdzane pod kątem lokalnego kontrolera Active Directory. Hasło nie musi znajdować się w usłudze Azure AD w żadnej postaci. Pozwala to na ocenę zasad lokalnych, takich jak ograniczenia godzin logowania, podczas uwierzytelniania do usług w chmurze.

Uwierzytelnianie przekazywane korzysta z prostego agenta na komputerze przyłączonym do domeny systemu Windows Server 2012 R2 w środowisku lokalnym. Ten agent nasłuchuje żądań weryfikacji haseł. Nie wymaga otwierania żadnych portów przychodzących w Internecie.

Ponadto można włączyć logowanie jednokrotne dla użytkowników na komputerach przyłączonych do domeny, które znajdują się w sieci firmowej. W przypadku logowania jednokrotnego użytkownicy muszą wprowadzić nazwę użytkownika, aby pomóc im bezpiecznie uzyskać dostęp do zasobów w chmurze.
![Uwierzytelnianie przekazywane](./media/plan-connect-user-signin/pta.png)

Aby uzyskać więcej informacji, zobacz:
- [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
- [Rejestracja jednokrotna](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federacja używająca nowej lub istniejącej farmy z AD FS w systemie Windows Server 2012 R2
Logowanie federacyjne pozwala użytkownikom na logowanie się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych. Chociaż znajdują się one w sieci firmowej, nie muszą oni nawet wprowadzać swoich haseł. Za pomocą opcji federacyjnej z AD FS można wdrożyć nową lub istniejącą farmę z AD FS w systemie Windows Server 2012 R2. Jeśli zdecydujesz się na określenie istniejącej farmy, Azure AD Connect skonfiguruje relację zaufania między farmą i usługą Azure AD, aby umożliwić użytkownikom logowanie się.

<center>

![Federacja z AD FS w systemie Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Wdrażanie Federacji z AD FS w systemie Windows Server 2012 R2

Jeśli wdrażasz nową farmę, potrzebujesz:

* Serwer z systemem Windows Server 2012 R2 dla serwera federacyjnego.
* Serwer z systemem Windows Server 2012 R2 dla serwera proxy aplikacji sieci Web.
* Plik PFX z jednym certyfikatem TLS/SSL dla zamierzonej nazwy usługi federacyjnej. Na przykład: fs.contoso.com.

Jeśli wdrażasz nową farmę lub korzystasz z istniejącej farmy, potrzebujesz:

* Poświadczenia administratora lokalnego na serwerach federacyjnych.
* Poświadczenia administratora lokalnego na wszystkich serwerach grupy roboczej (nie przyłączonych do domeny), dla których zamierzasz wdrożyć rolę serwera proxy aplikacji sieci Web.
* Komputer, na którym uruchamiasz kreatora, ma możliwość nawiązywania połączenia z innymi maszynami, które mają zostać zainstalowane AD FS lub serwera proxy aplikacji sieci Web przy użyciu Windows Remote Management.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego za pomocą AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federacja z serwerem PingFederate
Logowanie federacyjne pozwala użytkownikom na logowanie się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych. Chociaż znajdują się one w sieci firmowej, nie muszą oni nawet wprowadzać swoich haseł.

Aby uzyskać więcej informacji na temat konfigurowania serwera pingfederate do użycia z Azure Active Directory, zobacz [serwera pingfederate Integration with Azure Active Directory i Office 365](https://www.pingidentity.com/AzureADConnect)

Aby uzyskać informacje na temat konfigurowania Azure AD Connect przy użyciu serwera pingfederate, zobacz [Azure AD Connect instalacji niestandardowej](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Zaloguj się przy użyciu wcześniejszej wersji AD FS lub rozwiązania innej firmy
Jeśli już skonfigurowano logowanie w chmurze przy użyciu wcześniejszej wersji AD FS (na przykład AD FS 2,0) lub dostawcy Federacji innej firmy, można pominąć konfigurację logowania użytkownika za pośrednictwem Azure AD Connect. Dzięki temu można uzyskać najnowszą synchronizację i inne możliwości Azure AD Connect podczas korzystania z istniejącego rozwiązania do logowania.

Aby uzyskać więcej informacji, zobacz [listę zgodności Federacji usługi Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Logowanie użytkownika i główna nazwa użytkownika
### <a name="understanding-user-principal-name"></a>Informacje o głównej nazwie użytkownika
W Active Directory domyślny sufiks głównej nazwy użytkownika (UPN) jest nazwą DNS domeny, w której utworzono konto użytkownika. W większości przypadków jest to nazwa domeny, która jest zarejestrowana jako domena przedsiębiorstwa w Internecie. Można jednak dodać więcej sufiksów nazw UPN przy użyciu domen Active Directory i relacji zaufania.

Nazwa UPN użytkownika ma format username@domain . Na przykład dla domeny Active Directory o nazwie "contoso.com" użytkownik o nazwie Jan może mieć nazwę UPN " john@contoso.com ". Nazwa UPN użytkownika jest oparta na dokumencie RFC 822. Mimo że nazwa UPN i adres e-mail mają ten sam format, wartość nazwy UPN dla użytkownika może być taka sama jak adres e-mail użytkownika.

### <a name="user-principal-name-in-azure-ad"></a>Główna nazwa użytkownika w usłudze Azure AD
Kreator Azure AD Connect używa atrybutu userPrincipalName lub umożliwia określenie atrybutu (w instalacji niestandardowej), który ma być używany z lokalnego jako głównej nazwy użytkownika w usłudze Azure AD. Jest to wartość, która jest używana do logowania się do usługi Azure AD. Jeśli wartość atrybutu userPrincipalName nie jest zgodna z zweryfikowaną domeną w usłudze Azure AD, usługa Azure AD zastępuje ją wartością default. onmicrosoft.com.

Każdy katalog w Azure Active Directory ma wbudowaną nazwę domeny o formacie contoso.onmicrosoft.com, która pozwala rozpocząć korzystanie z platformy Azure lub innych usług firmy Microsoft. Środowisko logowania można usprawnić i uprościć, korzystając z domen niestandardowych. Aby uzyskać informacje dotyczące niestandardowych nazw domen w usłudze Azure AD oraz sposobu weryfikowania domeny, zobacz [Dodawanie niestandardowej nazwy domeny do Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfiguracja logowania się w usłudze Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfiguracja logowania do usługi Azure AD za pomocą Azure AD Connect
Środowisko logowania usługi Azure AD zależy od tego, czy usługa Azure AD może być zgodna z sufiksem głównej nazwy użytkownika, który jest synchronizowany z jedną z domen niestandardowych, które są weryfikowane w katalogu usługi Azure AD. Azure AD Connect zapewnia pomoc podczas konfigurowania ustawień logowania usługi Azure AD, dzięki czemu środowisko logowania użytkowników w chmurze będzie podobne do środowiska lokalnego.

Azure AD Connect wyświetla listę sufiksów nazw UPN, które są zdefiniowane dla domen i próbuje dopasować je do domeny niestandardowej w usłudze Azure AD. Następnie pomaga wykonać odpowiednie działania, które należy podjąć.
Na stronie logowania do usługi Azure AD są wyświetlane sufiksy nazw UPN, które są zdefiniowane dla Active Directory lokalnego i wyświetlany jest odpowiedni stan dla każdego sufiksu. Wartości stanu mogą być następujące:

| Stan | Opis | Wymagana akcja |
|:--- |:--- |:--- |
| Sprawdzić |Azure AD Connect znaleziono pasującą zweryfikowaną domenę w usłudze Azure AD. Wszyscy użytkownicy tej domeny mogą logować się przy użyciu poświadczeń lokalnych. |Nie jest wymagana żadna akcja. |
| Niezweryfikowane |Azure AD Connect znaleziono pasującą domenę niestandardową w usłudze Azure AD, ale nie została zweryfikowana. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślny sufiks. onmicrosoft.com po synchronizacji, jeśli domena nie została zweryfikowana. | [Sprawdź domenę niestandardową w usłudze Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nie dodano |Azure AD Connect nie znalazł domeny niestandardowej, która odpowiada sufiksowi nazwy UPN. Sufiks nazwy UPN użytkowników tej domeny zostanie zmieniony na domyślny sufiks. onmicrosoft.com, jeśli domena nie zostanie dodana i zweryfikowana na platformie Azure. | [Dodaj i sprawdź domenę niestandardową, która odnosi się do sufiksu nazwy UPN.](../fundamentals/add-custom-domain.md) |

Na stronie logowania do usługi Azure AD są wyświetlane sufiksy nazw UPN, które są zdefiniowane dla Active Directory lokalnego i odpowiedniej domeny niestandardowej w usłudze Azure AD z bieżącym stanem weryfikacji. W przypadku instalacji niestandardowej można teraz wybrać atrybut nazwy głównej użytkownika na stronie **logowania usługi Azure AD** .

![Strona logowania usługi Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Możesz kliknąć przycisk Odśwież, aby ponownie pobrać najnowszy stan domen niestandardowych z usługi Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Wybieranie atrybutu nazwy głównej użytkownika w usłudze Azure AD
Atrybut userPrincipalName jest atrybutem używanym przez użytkowników podczas logowania się do usługi Azure AD i Microsoft 365. Przed zsynchronizowaniem użytkowników należy zweryfikować domeny (znane także jako sufiksy nazw UPN), które są używane w usłudze Azure AD.

Zdecydowanie zalecamy zachowanie domyślnego atrybutu userPrincipalName. Jeśli ten atrybut jest nonroutable i nie można go zweryfikować, można wybrać inny atrybut (na przykład adres e-mail) jako atrybut, który zawiera identyfikator logowania. Jest to tzw. alternatywny identyfikator. Wartość atrybutu alternatywny identyfikator musi być zgodna ze standardem RFC 822. Możesz użyć alternatywnego identyfikatora z logowaniem jednokrotnym i logowaniem jednokrotnym w systemie jako rozwiązania do logowania.

> [!NOTE]
> Używanie alternatywnego identyfikatora nie jest zgodne ze wszystkimi obciążeniami Microsoft 365. Aby uzyskać więcej informacji, zobacz [artykuł opisujący konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) (strona może zostać wyświetlona w języku angielskim).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Różne niestandardowe Stany domeny i ich wpływ na środowisko logowania platformy Azure
Bardzo ważne jest zrozumienie relacji między Stanami domeny niestandardowej w katalogu usługi Azure AD a sufiksami nazw UPN zdefiniowanymi lokalnie. Przyjrzyjmy się różnym możliwościom logowania platformy Azure podczas konfigurowania synchronizacji przy użyciu Azure AD Connect.

Aby uzyskać poniższe informacje, Załóżmy, że będziemy zainteresowani contoso.com sufiksu UPN, który jest używany w katalogu lokalnym jako część nazwy UPN — na przykład user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Ustawienia ekspresowe/synchronizacja skrótów haseł

| Stan | Wpływ na środowisko logowania użytkownika platformy Azure |
|:---:|:--- |
| Nie dodano |W takim przypadku żadna domena niestandardowa dla contoso.com nie została dodana do katalogu usługi Azure AD. Użytkownicy posiadający nazwę UPN lokalną z sufiksem @contoso.com nie będą mogli korzystać z lokalnej nazwy UPN do logowania się do platformy Azure. Zamiast tego będą musieli używać nowej nazwy UPN udostępnionej przez usługę Azure AD, dodając sufiks dla domyślnego katalogu usługi Azure AD. Na przykład Jeśli synchronizujesz użytkowników z katalogiem usługi Azure AD azurecontoso.onmicrosoft.com, wówczas użytkownik lokalny user@contoso.com otrzyma nazwę UPN user@azurecontoso.onmicrosoft.com . |
| Niezweryfikowane |W tym przypadku mamy niestandardową contoso.com domeny, która jest dodawana do katalogu usługi Azure AD. Nie jest on jednak jeszcze zweryfikowany. Jeśli przejdziesz do synchronizacji użytkowników bez weryfikowania domeny, użytkownicy otrzymają nową nazwę UPN przez usługę Azure AD, podobnie jak w przypadku scenariusza "nie dodano". |
| Sprawdzić |W tym przypadku mamy niestandardową contoso.com domeny, która została już dodana i zweryfikowana w usłudze Azure AD dla sufiksu nazwy UPN. Użytkownicy będą mogli korzystać z lokalnej głównej nazwy użytkownika, na przykład w user@contoso.com celu zalogowania się do platformy Azure po synchronizacji z usługą Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS Federacja
Nie można utworzyć Federacji z domyślną domeną. onmicrosoft.com w usłudze Azure AD lub niezweryfikowaną domeną niestandardową w usłudze Azure AD. Gdy uruchamiasz kreatora Azure AD Connect, w przypadku wybrania niezweryfikowanej domeny w celu utworzenia Federacji z programem Azure AD Connect wyświetli monit o wprowadzenie wymaganych rekordów, w których serwer DNS jest hostowany dla domeny. Aby uzyskać więcej informacji, zobacz temat [Weryfikowanie domeny usługi Azure AD wybranej dla Federacji](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

W przypadku wybrania Federacji opcji logowania użytkownika **z AD FS** należy mieć domenę niestandardową, aby kontynuować tworzenie federacji w usłudze Azure AD. W naszej dyskusji oznacza to, że w katalogu usługi Azure AD powinna zostać dodana niestandardowa contoso.com domeny.

| Stan | Wpływ na środowisko logowania użytkownika platformy Azure |
|:---:|:--- |
| Nie dodano |W takim przypadku Azure AD Connect nie znalazł pasującej domeny niestandardowej dla sufiksu UPN contoso.com w katalogu usługi Azure AD. Aby użytkownicy mogli się zalogować przy użyciu AD FS z lokalną nazwą UPN (na przykład), należy dodać niestandardową domenę contoso.com user@contoso.com . |
| Niezweryfikowane |W takim przypadku Azure AD Connect wyświetli odpowiednie informacje o tym, jak można zweryfikować domenę na późniejszym etapie. |
| Sprawdzić |W takim przypadku można przejść do konfiguracji bez żadnych dalszych akcji. |

## <a name="changing-the-user-sign-in-method"></a>Zmiana metody logowania użytkownika
Można zmienić metodę logowania użytkownika z Federacji, synchronizacji skrótów haseł lub uwierzytelniania przekazywanego za pomocą zadań dostępnych w Azure AD Connect po początkowej konfiguracji Azure AD Connect za pomocą kreatora. Uruchom ponownie Kreatora Azure AD Connect i zobaczysz listę zadań, które można wykonać. Wybierz pozycję **Zmień Logowanie użytkownika** z listy zadań.

![Zmień Logowanie użytkownika](./media/plan-connect-user-signin/changeusersignin.png)

Na następnej stronie jest wyświetlany monit o podanie poświadczeń dla usługi Azure AD.

![Zrzut ekranu pokazujący, gdzie należy wpisać poświadczenia dla usługi Azure AD.](./media/plan-connect-user-signin/changeusersignin2.png)

Na stronie **logowania użytkownika** wybierz żądane Logowanie użytkownika.

![Łączenie z usługą Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Jeśli przeprowadzasz tylko tymczasowy przełącznik do synchronizacji skrótów haseł, zaznacz pole wyboru nie **Konwertuj kont użytkowników** . Nie zaznaczenie opcji spowoduje konwersję każdego użytkownika do federacyjnego i może potrwać kilka godzin.
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat integrowania tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
- Dowiedz się więcej na temat [pojęć dotyczących projektowania Azure AD Connect](plan-connect-design-concepts.md).