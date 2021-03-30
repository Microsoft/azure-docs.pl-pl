---
title: Azure AD Connect — AD FS zarządzania i dostosowywania | Microsoft Docs
description: AD FS zarządzanie przy użyciu Azure AD Connect i dostosowania środowiska logowania AD FS użytkowników przy użyciu Azure AD Connect i programu PowerShell.
keywords: AD FS, ADFS, zarządzanie AD FS, łączenie z usługą AAD, nawiązywanie połączenia, logowanie, AD FS dostosowanie, naprawa zaufania, M365, Federacja, Jednostka uzależniona
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91274116"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Zarządzanie Active Directory Federation Servicesami i dostosowywanie ich przy użyciu Azure AD Connect
W tym artykule opisano sposób zarządzania i dostosowywania Active Directory Federation Services (AD FS) przy użyciu programu Azure Active Directory Connect (Azure AD). Zawiera również inne typowe zadania AD FS, które mogą być konieczne do pełnej konfiguracji farmy AD FSowej.

| Temat | Co obejmuje |
|:--- |:--- |
| **Zarządzaj AD FS** | |
| [Napraw zaufanie](#repairthetrust) |Jak naprawić relację zaufania federacji z Microsoft 365. |
| [Sfederować z usługą Azure AD przy użyciu alternatywnego identyfikatora logowania](#alternateid) | Konfigurowanie Federacji przy użyciu alternatywnego identyfikatora logowania  |
| [Dodaj serwer AD FS](#addadfsserver) |Jak rozwinąć farmę AD FS z dodatkowym serwerem AD FS. |
| [Dodawanie serwera proxy aplikacji sieci Web AD FS](#addwapserver) |Jak rozwinąć farmę AD FS przy użyciu dodatkowego serwera proxy aplikacji sieci Web (WAP). |
| [Dodawanie domeny federacyjnej](#addfeddomain) |Jak dodać domenę federacyjną. |
| [Aktualizowanie certyfikatu TLS/SSL](how-to-connect-fed-ssl-update.md)| Jak zaktualizować certyfikat TLS/SSL dla farmy AD FS. |
| **Dostosowywanie AD FS** | |
| [Dodawanie niestandardowego logo firmy lub ilustracji](#customlogo) |Jak dostosować stronę logowania AD FS przy użyciu logo firmy i ilustracji. |
| [Dodawanie opisu logowania](#addsignindescription) |Jak dodać opis strony logowania. |
| [Modyfikuj reguły AD FSch roszczeń](#modclaims) |Jak zmodyfikować oświadczenia AD FS w różnych scenariuszach federacyjnych. |

## <a name="manage-ad-fs"></a>Zarządzaj AD FS
Za pomocą Kreatora Azure AD Connect można wykonywać różne zadania związane z AD FS w Azure AD Connect z minimalną interwencją użytkownika. Po zakończeniu instalacji Azure AD Connect przez uruchomienie kreatora można uruchomić kreatora ponownie, aby wykonać dodatkowe zadania.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Napraw zaufanie 
Za pomocą Azure AD Connect można sprawdzić bieżącą kondycję AD FS i zaufania usługi Azure AD i podjąć odpowiednie działania w celu naprawienia zaufania. Wykonaj następujące kroki, aby naprawić usługę Azure AD i AD FS zaufaniem.

1. Wybierz pozycję **napraw usługi AAD i zaufanie ADFS** z listy dodatkowych zadań.
   ![Naprawa usługi AAD i relacji zaufania usług ADFS](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na stronie **Połącz z usługą Azure AD** podaj poświadczenia administratora globalnego usługi Azure AD, a następnie kliknij przycisk **dalej**.
   ![Zrzut ekranu przedstawiający stronę "łączenie się z usługą Azure AD" z przykładowymi poświadczeniami.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na stronie **poświadczenia dostępu zdalnego** wprowadź poświadczenia administratora domeny.

   ![Zrzut ekranu przedstawiający stronę "poświadczenia dostępu zdalnego" z przykładowymi poświadczeniami.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po kliknięciu **przycisku dalej** Azure AD Connect sprawdza kondycję certyfikatu i pokazuje wszelkie problemy.

    ![Stan certyfikatów](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Strona **gotowy do konfiguracji** zawiera listę akcji, które zostaną wykonane w celu naprawy zaufania.

    ![Zrzut ekranu przedstawiający stronę "gotowe do skonfigurowania" z listą akcji.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Kliknij przycisk **Instaluj** , aby naprawić zaufanie.

> [!NOTE]
> Azure AD Connect można naprawić tylko w przypadku certyfikatów z podpisem własnym. Azure AD Connect nie może naprawić certyfikatów innych firm.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Sfederować z usługą Azure AD za pomocą AlternateID 
Zaleca się, aby lokalna główna nazwa użytkownika (UPN) i główna nazwa użytkownika w chmurze były takie same. Jeśli korzysta z lokalnej nazwy UPN domeny bez obsługi routingu (np. Contoso. local) lub nie można zmienić z powodu zależności aplikacji lokalnych, zalecamy skonfigurowanie alternatywnego identyfikatora logowania. Alternatywny identyfikator logowania umożliwia skonfigurowanie środowiska logowania, w którym użytkownicy mogą logować się przy użyciu atrybutu innego niż nazwy UPN, takie jak poczta. Wybór nazwy głównej użytkownika w Azure AD Connect domyślnie do atrybutu userPrincipalName w Active Directory. W przypadku wybrania dowolnego innego atrybutu dla głównej nazwy użytkownika i federowanie przy użyciu AD FS, Azure AD Connect skonfiguruje AD FS dla alternatywnego identyfikatora logowania. Poniżej przedstawiono przykład wybierania innego atrybutu dla nazwy głównej użytkownika:

![Alternatywny atrybut identyfikatora](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurowanie alternatywnego identyfikatora logowania dla AD FS składa się z dwóch głównych kroków:
1. **Skonfiguruj odpowiedni zestaw oświadczeń wystawiania**: reguły oświadczeń wystawiania w relacji zaufania jednostki uzależnionej usługi Azure AD są modyfikowane tak, aby używały wybranego atrybutu userPrincipalName jako alternatywnego identyfikatora użytkownika.
2. **Włącz alternatywny identyfikator logowania w konfiguracji AD FS**: Konfiguracja AD FS jest aktualizowana, aby AD FS można wyszukać użytkowników w odpowiednich lasach przy użyciu alternatywnego identyfikatora. Ta konfiguracja jest obsługiwana dla AD FS w systemie Windows Server 2012 R2 (z KB2919355) lub nowszym. Jeśli serwery AD FS są 2012 R2, Azure AD Connect sprawdza obecność wymaganej bazy wiedzy. Jeśli KB nie zostanie wykryta, po zakończeniu konfiguracji zostanie wyświetlone ostrzeżenie, jak pokazano poniżej:

    ![Ostrzeżenie o brakującym KB w 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Aby skorygować konfigurację w przypadku braku bazy wiedzy, Zainstaluj wymagane [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) , a następnie napraw zaufanie przy użyciu polecenia [napraw AAD i AD FS zaufania](#repairthetrust).

> [!NOTE]
> Aby uzyskać więcej informacji na temat alternateID i kroków ręcznego konfigurowania, Przeczytaj [Konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Dodaj serwer AD FS 

> [!NOTE]
> Aby dodać serwer AD FS, Azure AD Connect wymaga certyfikatu PFX. W związku z tym można wykonać tę operację tylko w przypadku skonfigurowania farmy AD FS przy użyciu Azure AD Connect.

1. Wybierz pozycję **Wdróż dodatkowy serwer federacyjny**, a następnie kliknij przycisk **dalej**.

   ![Dodatkowy serwer federacyjny](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla usługi Azure AD, a następnie kliknij przycisk **dalej**.

   ![Zrzut ekranu przedstawiający stronę "łączenie się z usługą Azure AD" z wprowadzonymi przykładowymi poświadczeniami.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Podaj poświadczenia administratora domeny.

   ![Poświadczenia administratora domeny](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect pyta o hasło pliku PFX, który został podany podczas konfigurowania nowej farmy AD FS przy użyciu Azure AD Connect. Kliknij przycisk **Wprowadź hasło** , aby podać hasło dla pliku PFX.

   ![Zrzut ekranu przedstawiający stronę "Określ certyfikat SSL" z otwartym oknem "hasło certyfikatu".](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Zrzut ekranu przedstawiający stronę "Określ certyfikat SSL" po wprowadzeniu hasła dla pliku PFX.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na stronie **serwery AD FS** wprowadź nazwę serwera lub adres IP, który ma zostać dodany do farmy AD FS.

   ![Serwery AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Kliknij przycisk **dalej** i przejdź przez ostateczną stronę **konfigurowania** . Po zakończeniu dodawania serwerów do farmy AD FS Azure AD Connect zostanie nadana opcja sprawdzenia łączności.

   ![Zrzut ekranu przedstawiający stronę "gotowe do skonfigurowania" z listą akcji do wykonania po kliknięciu pozycji "Zainstaluj".](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Zrzut ekranu przedstawiający stronę "zakończenie instalacji" z wyświetlonym komunikatem "Konfiguracja sieci intranet została pomyślnie zweryfikowana". ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Dodawanie AD FS serwera WAP 

> [!NOTE]
> Aby dodać serwer WAP, Azure AD Connect wymaga certyfikatu PFX. W związku z tym tę operację można wykonać tylko w przypadku skonfigurowania farmy AD FS przy użyciu Azure AD Connect.

1. Wybierz pozycję **Wdróż serwer proxy aplikacji sieci Web** z listy dostępnych zadań.

   ![Wdróż serwer proxy aplikacji sieci Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Podaj poświadczenia administratora globalnego platformy Azure.

   ![Zrzut ekranu przedstawiający stronę "Połącz z usługą Azure AD" z wprowadzonym przykładową nazwą użytkownika i hasłem.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na stronie **Określanie certyfikatu SSL** Podaj hasło dla pliku PFX, który został podany podczas konfigurowania farmy AD FS przy użyciu Azure AD Connect.
   ![Hasło certyfikatu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Określanie certyfikatu TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Dodaj serwer, który ma zostać dodany jako serwer WAP. Ponieważ serwer WAP może nie być przyłączony do domeny, Kreator monituje o poświadczenia administracyjne do dodawanego serwera.

   ![Poświadczenia serwera administracyjnego](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na stronie **poświadczenia zaufania serwera proxy** podaj poświadczenia administracyjne, aby skonfigurować relację zaufania serwera proxy i uzyskać dostęp do serwera podstawowego w farmie AD FS.

   ![Poświadczenia zaufania serwera proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na stronie **gotowy do konfiguracji** Kreator wyświetla listę akcji, które zostaną wykonane.

   ![Zrzut ekranu przedstawiający stronę "gotowe do skonfigurowania" z listą akcji do wykonania.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Kliknij przycisk **Instaluj** , aby zakończyć konfigurację. Po zakończeniu konfiguracji Kreator umożliwia zweryfikowanie łączności z serwerami. Kliknij przycisk **Weryfikuj** , aby sprawdzić łączność.

   ![Instalacja zakończona](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Dodawanie domeny federacyjnej 

Można łatwo dodać domenę do Federacji z usługą Azure AD przy użyciu Azure AD Connect. Azure AD Connect dodaje domenę dla Federacji i modyfikuje reguły dotyczące zasad w celu poprawnego odzwierciedlenia wystawcy, gdy istnieje wiele domen federacyjnych z usługą Azure AD.

1. Aby dodać domenę federacyjną, wybierz zadanie **Dodaj dodatkową domenę usługi Azure AD**.

   ![Dodatkowa domena usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na następnej stronie kreatora podaj poświadczenia administratora globalnego usługi Azure AD.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na stronie **poświadczenia dostępu zdalnego** podaj poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na następnej stronie Kreator zawiera listę domen usługi Azure AD, które można sfederować w katalogu lokalnym za pomocą usługi. Wybierz domenę z listy.

   ![Domena usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Po wybraniu domeny Kreator udostępnia odpowiednie informacje o dalszych akcjach podejmowanych przez kreatora oraz o wpływie konfiguracji. W niektórych przypadkach w przypadku wybrania domeny, która nie została jeszcze zweryfikowana w usłudze Azure AD, Kreator zawiera informacje ułatwiające zweryfikowanie domeny. Aby uzyskać więcej informacji [, zobacz Dodawanie niestandardowej nazwy domeny do Azure Active Directory](../fundamentals/add-custom-domain.md) .

5. Kliknij przycisk **Dalej**. Na stronie **gotowy do konfiguracji** zostanie wyświetlona lista akcji, które zostaną wykonane Azure AD Connect. Kliknij przycisk **Instaluj** , aby zakończyć konfigurację.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Aby można było zalogować się do usługi Azure AD, użytkownicy z dodanej domeny federacyjnej muszą zostać zsynchronizowani.

## <a name="ad-fs-customization"></a>Usługi AD FS dostosowywania
Poniższe sekcje zawierają szczegółowe informacje o niektórych typowych zadaniach, które mogą być wykonywane po dostosowaniu AD FS stronie logowania.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Dodawanie niestandardowego logo firmy lub ilustracji 
Aby zmienić logo firmy, która jest wyświetlana na stronie **logowania** , należy użyć następującego polecenia cmdlet programu Windows PowerShell i składni.

> [!NOTE]
> Zalecane wymiary logo to 260 x 35 \@ 96 dpi z rozmiarem pliku nie większym niż 10 KB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> Parametr *TargetName* jest wymagany. Motyw domyślny wydawany za pomocą AD FS ma nazwę default.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Dodawanie opisu logowania 
Aby dodać opis strony logowania do **strony logowania**, należy użyć następującego polecenia cmdlet programu Windows PowerShell i składni.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Modyfikuj reguły AD FSch roszczeń 
AD FS obsługuje bogaty język, którego można użyć do tworzenia niestandardowych reguł dla roszczeń. Aby uzyskać więcej informacji, zobacz [Rola języka reguł dotyczących roszczeń](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11)).

W poniższych sekcjach opisano, jak można napisać niestandardowe reguły dla niektórych scenariuszy, które odnoszą się do usługi Azure AD i Federacji AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Niezmienny identyfikator uzależniony od wartości, która jest obecna w atrybucie
Azure AD Connect pozwala określić atrybut, który ma być używany jako zakotwiczenie źródłowe, gdy obiekty są synchronizowane z usługą Azure AD. Jeśli wartość w atrybucie niestandardowym nie jest pusta, może być konieczne wystawienie niezmiennego żądania ID.

Na przykład można wybrać opcję **MS-ds-consistencyguid** jako atrybut dla kotwicy źródłowej i wydać **ImmutableID** jako **MS-ds-consistencyguid** w przypadku, gdy atrybut ma wartość. Jeśli nie ma wartości dla atrybutu, należy wydać **objectGUID** jako niezmienny identyfikator. Zestaw niestandardowych reguł roszczeń można skonstruować zgodnie z opisem w następnej sekcji.

**Reguła 1: atrybuty zapytania**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

W tej regule są używane zapytania dotyczące wartości **MS-ds-consistencyguid** i **objectGUID** dla użytkownika z Active Directory. Zmień nazwę magazynu na odpowiednią nazwę magazynu we wdrożeniu AD FS. Zmień również typ oświadczenia na odpowiedni typ oświadczenia dla Federacji, zgodnie z definicją dla **objectGUID** i **MS-ds-consistencyguid**.

Ponadto przy użyciu opcji **Dodaj** **i nie należy** unikać dodawania wychodzącego problemu dla jednostki i można używać wartości jako wartości pośrednich. Po ustaleniu wartości, która ma być używana jako niezmienny identyfikator, zostanie wystawione w późniejszej regule.

**Reguła 2. Sprawdź, czy dla użytkownika istnieje usługa MS-ds-consistencyguid**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Ta zasada definiuje tymczasową flagę o nazwie **idflag** , która jest ustawiona na **useguid** , jeśli dla użytkownika nie ma konta **MS-ds-consistencyguid** . Logika w tym przypadku jest faktem, że AD FS nie zezwala na puste oświadczenia. Dlatego po dodaniu oświadczeń `http://contoso.com/ws/2016/02/identity/claims/objectguid` i `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` w regule 1, można wykonać oświadczenie **msdsconsistencyguid** tylko wtedy, gdy wartość zostanie wypełniona dla użytkownika. Jeśli nie zostanie wypełnione, AD FS widzi, że będzie miał wartość pustą i natychmiast porzuca. Wszystkie obiekty będą mieć **objectGUID**, w związku z czym po wykonaniu reguły 1 będzie ona zawsze istnieć.

**Reguła 3: wystawiaj MS-ds-consistencyguid jako niezmienny identyfikator, jeśli jest obecny**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Jest to **niejawne** sprawdzenie. Jeśli wartość dla tego zgłoszenia istnieje, należy ją wystawić jako niezmienny identyfikator. W poprzednim przykładzie jest stosowane **NameIdentifier** . Należy zmienić to na odpowiedni typ zgłoszenia dla niezmiennego identyfikatora w środowisku.

**Reguła 4. wydawanie identyfikatora objectGuid jako niezmiennego ID, jeśli nie istnieje usługa MS-ds-consistencyGuid**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

W tej regule po prostu sprawdzasz tymczasową flagę **idflag**. Podjęto decyzję o tym, czy wystawić zgłoszenie na podstawie jego wartości.

> [!NOTE]
> Kolejność tych reguł jest ważna.

### <a name="sso-with-a-subdomain-upn"></a>Logowanie jednokrotne za pomocą nazwy UPN domeny podrzędnej

Można dodać więcej niż jedną domenę, która będzie mogła być federacyjną przy użyciu Azure AD Connect, zgodnie z opisem w temacie [Dodawanie nowej domeny federacyjnej](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect wersja 1.1.553.0 i Najnowsza tworzy poprawną regułę dla usługi issuerID. Jeśli nie można użyć Azure AD Connect w wersji 1.1.553.0 lub najnowszej, zaleca się używanie narzędzia [reguł usługi Azure AD RPT](https://aka.ms/aadrptclaimrules) do generowania i ustawiania prawidłowych reguł dotyczących relacji zaufania jednostki uzależnionej usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [opcjach logowania użytkownika](plan-connect-user-signin.md).