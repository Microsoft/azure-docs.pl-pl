---
title: Azure AD Connect wiele domen
description: W tym dokumencie opisano konfigurowanie i Konfigurowanie wielu domen najwyższego poziomu za pomocą Microsoft 365 i usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306365"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Obsługa wielu domen do federowania w usłudze Azure AD
Poniższa dokumentacja zawiera wskazówki dotyczące korzystania z wielu domen najwyższego poziomu i poddomen w przypadku federowanie Microsoft 365 z domenami usługi Azure AD.

## <a name="multiple-top-level-domain-support"></a>Obsługa wielu domen najwyższego poziomu
Federowanie wiele domen najwyższego poziomu z usługą Azure AD wymaga dodatkowej konfiguracji, która nie jest wymagana w przypadku federowanie z jedną domeną najwyższego poziomu.

W przypadku federacyjnego domeny z usługą Azure AD w domenie na platformie Azure są ustawiane kilka właściwości.  Jednym z nich jest IssuerUri.  Ta właściwość jest identyfikatorem URI używanym przez usługę Azure AD do identyfikowania domeny, z którą skojarzony jest token.  Identyfikator URI nie musi być rozpoznawany jako jakikolwiek element, ale musi być prawidłowym identyfikatorem URI.  Domyślnie usługa Azure AD ustawia identyfikator URI na wartość identyfikatora usługi federacyjnej w konfiguracji AD FS lokalnej.

> [!NOTE]
> Identyfikator usługi federacyjnej jest identyfikatorem URI, który jednoznacznie identyfikuje usługę federacyjną.  Usługa federacyjna to wystąpienie AD FS, które działa jako usługa tokenu zabezpieczającego.
>
>

IssuerUri można wyświetlić za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Zrzut ekranu pokazujący wyniki po wprowadzeniu polecenia "Get-MsolDomainFederationSettings" w programie PowerShell.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Występuje problem podczas dodawania więcej niż jednej domeny najwyższego poziomu.  Załóżmy na przykład, że skonfigurowano federacji między usługą Azure AD a środowiskiem lokalnym.  W przypadku tego dokumentu używana jest domena, bmcontoso.com.  Teraz została dodana druga domena najwyższego poziomu, bmfabrikam.com.

![Zrzut ekranu przedstawiający wiele domen najwyższego poziomu](./media/how-to-connect-install-multiple-domains/domains.png)

Podczas próby przekonwertowania domeny bmfabrikam.com na federacyjny, wystąpi błąd.  Przyczyną jest to, że usługa Azure AD ma ograniczenie, które nie zezwala, aby Właściwość IssuerUri miała taką samą wartość dla więcej niż jednej domeny.  

![Zrzut ekranu pokazujący błąd Federacji w programie PowerShell.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain — parametr
Aby obejść to ograniczenie, należy dodać inny IssuerUri, który można wykonać przy użyciu `-SupportMultipleDomain` parametru.  Ten parametr jest używany z następującymi poleceniami cmdlet:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ten parametr sprawia, że usługa Azure AD konfiguruje IssuerUri, tak aby była oparta na nazwie domeny.  IssuerUri będą unikatowe w różnych katalogach w usłudze Azure AD.  Użycie parametru umożliwia pomyślne ukończenie polecenia programu PowerShell.

![Zrzut ekranu pokazujący pomyślne zakończenie polecenia programu PowerShell.](./media/how-to-connect-install-multiple-domains/convert.png)

Przeglądając ustawienia dla domeny bmfabrikam.com, można wyświetlić następujące elementy:

![Zrzut ekranu pokazujący ustawienia dla domeny "bmfabrikam.com".](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` nie zmienia innych punktów końcowych, które są nadal skonfigurowane do wskazywania usługi federacyjnej w adfs.bmcontoso.com.

Inną kwestią `-SupportMultipleDomain` jest to, że system AD FS obejmuje odpowiednią wartość wystawcy w tokenach wystawionych dla usługi Azure AD. Ta wartość jest ustawiana przez pobranie części domeny nazwy UPN użytkowników i ustawienie jej jako domeny w IssuerUri, np. https://{sufiks UPN}/ADFS/Services/Trust.

W tym czasie podczas uwierzytelniania do usługi Azure AD lub Microsoft 365 element IssuerUri w tokenie użytkownika jest używany do lokalizowania domeny w usłudze Azure AD. Jeśli nie można znaleźć dopasowania, uwierzytelnianie zakończy się niepowodzeniem.

Na przykład, jeśli nazwa UPN użytkownika to bsimon@bmcontoso.com , element IssuerUri w tokenie, AD FS problemy, zostanie ustawiony na `http://bmcontoso.com/adfs/services/trust` . Ten element będzie zgodny z konfiguracją usługi Azure AD, a uwierzytelnianie powiedzie się.

Poniżej przedstawiono dostosowaną regułę, która implementuje tę logikę:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Aby można było użyć przełącznika-SupportMultipleDomain podczas próby dodania nowych lub przekonwertowania już istniejących domen, Twoje zaufanie federacyjne musi już być skonfigurowane do ich obsługi.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak zaktualizować zaufanie między AD FS i usługą Azure AD
Jeśli nie skonfigurowano zaufania federacyjnego między AD FS i wystąpieniem usługi Azure AD, może być konieczne ponowne utworzenie tego zaufania.  Przyczyną jest to, że po jego pierwotnym skonfigurowaniu bez `-SupportMultipleDomain` parametru IssuerUri jest ustawiana z wartością domyślną.  Na poniższym zrzucie ekranu można zobaczyć, że IssuerUri jest ustawiony na `https://adfs.bmcontoso.com/adfs/services/trust` .

Jeśli nowa domena została pomyślnie dodana w portalu usługi Azure AD, a następnie podjęta zostanie próba konwersji przy użyciu `Convert-MsolDomaintoFederated -DomainName <your domain>` , zostanie wyświetlony następujący błąd.

![Zrzut ekranu pokazujący błąd Federacji w programie PowerShell po próbie przekonwertowania nowej domeny za pomocą polecenia "Convert-MsolDomaintoFederated".](./media/how-to-connect-install-multiple-domains/trust1.png)

Jeśli spróbujesz dodać `-SupportMultipleDomain` przełącznik, zostanie wyświetlony następujący błąd:

![Zrzut ekranu pokazujący błąd Federacji po dodaniu przełącznika "-SupportMultipleDomain".](./media/how-to-connect-install-multiple-domains/trust2.png)

Po prostu próba uruchomienia `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` w oryginalnej domenie spowoduje również wystąpienie błędu.

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/trust3.png)

Wykonaj poniższe kroki, aby dodać dodatkową domenę najwyższego poziomu.  Jeśli dodano już domenę i nie użyto `-SupportMultipleDomain` parametru, należy zacząć od procedury usuwania i aktualizowania oryginalnej domeny.  Jeśli jeszcze nie dodano domeny najwyższego poziomu, można rozpocząć od procedury dodawania domeny przy użyciu programu PowerShell dla Azure AD Connect.

Wykonaj następujące kroki, aby usunąć zaufanie online firmy Microsoft i zaktualizować oryginalną domenę.

1. Na serwerze federacyjnym AD FS Otwórz **AD FS zarządzania.**
2. Po lewej stronie rozwiń węzeł **relacje zaufania** i **relacje zaufania jednostek uzależnionych**
3. Po prawej stronie Usuń wpis **Microsoft Office 365 Identity platform** .
   ![Usuń firmę Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Na maszynie, na której zainstalowano [moduł Azure Active Directory dla programu Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) , uruchom następujące polecenia: `$cred=Get-Credential` .  
5. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, z którą federowaniesz.
6. W programie PowerShell wprowadź `Connect-MsolService -Credential $cred`
7. W programie PowerShell wprowadź `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Ta aktualizacja dotyczy oryginalnej domeny.  W związku z tym Użyj powyższych domen:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Aby dodać nową domenę najwyższego poziomu przy użyciu programu PowerShell, wykonaj następujące czynności:

1. Na maszynie, na której zainstalowano [moduł Azure Active Directory dla programu Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) , uruchom następujące polecenia: `$cred=Get-Credential` .  
2. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, z którą federowaniesz
3. W programie PowerShell wprowadź `Connect-MsolService -Credential $cred`
4. W programie PowerShell wprowadź `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Wykonaj następujące kroki, aby dodać nową domenę najwyższego poziomu przy użyciu Azure AD Connect.

1. Uruchom Azure AD Connect z pulpitu lub menu Start
2. Wybierz pozycję "Dodaj dodatkową domenę usługi Azure AD" ![ , aby wyświetlić stronę "dodatkowe zadania" z wybraną opcją "Dodaj dodatkową domenę usługi Azure AD".](./media/how-to-connect-install-multiple-domains/add1.png)
3. Wprowadź poświadczenia usługi Azure AD i Active Directory
4. Wybierz drugą domenę, którą chcesz skonfigurować dla Federacji.
   ![Dodaj dodatkową domenę usługi Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknięcie pozycji Zainstaluj

### <a name="verify-the-new-top-level-domain"></a>Weryfikowanie nowej domeny najwyższego poziomu
Za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>` można wyświetlić zaktualizowane IssuerUri.  Zrzut ekranu poniżej przedstawia ustawienia Federacji zostały zaktualizowane w oryginalnej domenie `http://bmcontoso.com/adfs/services/trust`

![Zrzut ekranu pokazujący ustawienia Federacji zaktualizowane w oryginalnej domenie.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

A IssuerUri w nowej domenie został ustawiony na `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Obsługa poddomen
Po dodaniu poddomeny, ze względu na sposób, w jaki usługa Azure AD obsłuży domeny, będzie dziedziczyć ustawienia elementu nadrzędnego.  Tak więc IssuerUri musi być zgodna z elementami nadrzędnymi.

Pozwala to na przykład, że mam bmcontoso.com, a następnie dodać corp.bmcontoso.com.  IssuerUri dla użytkownika z corp.bmcontoso.com będzie musiał być **`http://bmcontoso.com/adfs/services/trust`** .  Jednak standardowa reguła zaimplementowana powyżej dla usługi Azure AD spowoduje wygenerowanie tokenu z wystawcą jako **`http://corp.bmcontoso.com/adfs/services/trust`** . które nie są zgodne z wymaganą wartością domeny, a uwierzytelnianie zakończy się niepowodzeniem.

### <a name="how-to-enable-support-for-subdomains"></a>Jak włączyć obsługę poddomen
Aby obejść ten problem, należy zaktualizować AD FS zaufania jednostki uzależnionej dla usługi Microsoft Online.  W tym celu należy skonfigurować niestandardową regułę oświadczeń, tak aby podczas konstruowania niestandardowej wartości wystawcy przyłączył się wszystkie poddomeny od sufiksu UPN użytkownika.

Następujące zgłoszenie spowoduje to:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
Ostatni numer w wyrażeniu regularnym to liczba domen nadrzędnych w domenie głównej. W tym miejscu bmcontoso.com jest używany, więc wymagane są dwie domeny nadrzędne. Jeśli zostały zachowane trzy domeny nadrzędne (tj.: corp.bmcontoso.com), liczba ta byłaby trzy. Ostatecznie można wskazywać zakres, a dopasowanie będzie zawsze wykonywane w celu dopasowania do maksymalnej liczby domen. " {2,3} " będzie pasować do dwóch do trzech domen (tj.: bmfabrikam.com i Corp.bmcontoso.com).

Wykonaj poniższe kroki, aby dodać niestandardową wartość do obsługi poddomen.

1. Otwórz AD FS zarządzanie
2. Kliknij prawym przyciskiem myszy relację zaufania Microsoft Online RP i wybierz pozycję Edytuj reguły dotyczące roszczeń
3. Wybierz trzecią regułę zgłoszenia i Zastąp wartość ![ Edytuj zgłoszenie](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Zastąp bieżące to:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    with

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Zastąp wierzytelność](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kliknij przycisk Ok.  Kliknij pozycję Zastosuj.  Kliknij przycisk Ok.  Zamknij przystawkę zarządzania usługami AD FS.

## <a name="next-steps"></a>Następne kroki
Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md), [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).