---
title: Dostosowywanie oświadczeń aplikacji dzierżawy usługi Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Ta strona zawiera opis mapowania oświadczeń Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: c300faf33f57518d26f82234bdff94a37235cd66
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275796"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Instrukcje: Dostosowywanie oświadczeń emitowanych w tokenach dla określonej aplikacji w dzierżawie (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja zastępuje i zastępuje [dostosowanie oświadczeń](active-directory-saml-claims-customization.md) oferowane przez portal już dziś. W tej samej aplikacji w przypadku dostosowywania oświadczeń przy użyciu portalu oprócz metody Graf/PowerShell szczegółowej w tym dokumencie tokeny wystawione dla tej aplikacji będą ignorować konfigurację w portalu. Konfiguracje wykonane za pomocą metod opisanych w tym dokumencie nie zostaną odzwierciedlone w portalu.

Ta funkcja jest używana przez administratorów dzierżawy do dostosowywania oświadczeń emitowanych w tokenach dla określonej aplikacji w swojej dzierżawie. Zasad mapowania oświadczeń można użyć do:

- Wybierz oświadczenia, które są zawarte w tokenach.
- Utwórz nieistniejące typy, które jeszcze nie istnieją.
- Wybierz lub Zmień źródło danych emitowanych w określonych oświadczeniach.

> [!NOTE]
> Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej. Przygotuj się na przywracanie lub usuwanie wszelkich zmian. Ta funkcja jest dostępna w każdej subskrypcji usługi Azure Active Directory (Azure AD) w ramach publicznej wersji zapoznawczej. Jeśli jednak funkcja będzie ogólnie dostępna, niektóre aspekty funkcji mogą wymagać subskrypcji usługi Azure AD w wersji Premium. Ta funkcja obsługuje Konfigurowanie zasad mapowania roszczeń dla protokołów protokołu WS-in, SAML, OAuth i OpenID Connect Connect.

## <a name="claims-mapping-policy-type"></a>Typ zasad mapowania oświadczeń

W usłudze Azure AD obiekt **zasad** reprezentuje zestaw reguł wymuszanych w poszczególnych aplikacjach lub we wszystkich aplikacjach w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są następnie stosowane do obiektów, do których są przypisane.

Zasady mapowania oświadczeń to typ obiektu **zasad** , który modyfikuje oświadczenia emitowane w tokenach wystawionych dla określonych aplikacji.

## <a name="claim-sets"></a>Zestawy oświadczeń

Istnieją pewne zestawy oświadczeń, które określają, jak i kiedy są używane w tokenach.

| Zestaw roszczeń | Opis |
|---|---|
| Podstawowy zestaw roszczeń | Są obecne w każdym tokenie, niezależnie od zasad. Te oświadczenia są również uznawane za ograniczone i nie można ich modyfikować. |
| Podstawowy zestaw roszczeń | Obejmuje oświadczenia, które są domyślnie emitowane dla tokenów (oprócz podstawowego zestawu oświadczeń). Możesz pominąć lub zmodyfikować podstawowe oświadczenia przy użyciu zasad mapowania oświadczeń. |
| Zestaw ograniczonych roszczeń | Nie można zmodyfikować przy użyciu zasad. Nie można zmienić źródła danych, a podczas generowania tych oświadczeń nie jest stosowane przekształcenie. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: zestaw ograniczonego tokenu sieci Web JSON (JWT)

| Typ zgłoszenia (nazwa) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| zewnętrzny |
| actortoken |
| AIO |
| altsecid |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| AppID |
| appidacr |
| twierdz |
| at_hash |
| AUD |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| CNF |
| kod |
| funkcje sterowania |
| credential_keys |
| przedstawiciel |
| csr_type |
| Identyfikator |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| poczta e-mail |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| ziół |
| group_sids |
| grupy |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| IAT |
| IdentityProvider |
| dostawcy tożsamości |
| in_corp |
| np |
| ipaddr |
| isbrowserhostedapp |
| ISS |
| JWK |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| NameID |
| NBF |
| netbios_name |
| jednorazow |
| OID |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| hasło |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| Identyfikator |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| zasób |
| role (rola) |
| role |
| scope |
| punkcie |
| SID |
| podpisane |
| signin_state |
| src1 |
| src2 |
| Sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| TID |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| głównej |
| user_setting_sync_url |
| nazwa użytkownika |
| Identyfikator UTI |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: zestaw roszczeń z ograniczeniami SAML

| Typ zgłoszenia (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Właściwości zasad mapowania oświadczeń

Aby kontrolować, jakie oświadczenia są emitowane i skąd pochodzą dane, użyj właściwości zasad mapowania oświadczeń. Jeśli nie ustawiono zasad, System wystawia tokeny, które obejmują podstawowy zestaw oświadczeń, podstawowy zestaw oświadczeń i wszelkie [opcjonalne oświadczenia](active-directory-optional-claims.md) , które aplikacja wybrała do odebrania.

### <a name="include-basic-claim-set"></a>Uwzględnij podstawowy zestaw roszczeń

**Ciąg:** IncludeBasicClaimSet

**Typ danych:** Wartość logiczna (true lub false)

**Podsumowanie:** Ta właściwość określa, czy podstawowy zestaw roszczeń jest uwzględniony w tokenach, których dotyczą te zasady.

- W przypadku ustawienia wartości true wszystkie oświadczenia w podstawowym zestawie oświadczeń są emitowane w tokenach, których dotyczą zasady.
- W przypadku ustawienia wartości false oświadczenia w podstawowym zestawie oświadczeń nie znajdują się w tokenach, chyba że są one indywidualnie dodawane we właściwości schematu oświadczeń tych samych zasad.

> [!NOTE]
> Oświadczenia w podstawowym zestawie oświadczeń są obecne w każdym tokenie, niezależnie od tego, jaka właściwość jest ustawiona na.

### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema

**Typ danych:** Obiekt BLOB JSON z co najmniej jednym wpisem schematu roszczeń

**Podsumowanie:** Ta właściwość określa, które oświadczenia są obecne w tokenach, których dotyczą zasady, oprócz podstawowego zestawu oświadczeń i podstawowego zestawu oświadczeń.
Niektóre informacje są wymagane dla każdego wpisu schematu roszczeń zdefiniowanego w tej właściwości. Określ miejsce, z którego pochodzą dane (para**wartości**, **źródła/identyfikatora**lub **para Source/ExtensionID**), a które są emitowane jako (**Typ zgłoszenia**).

### <a name="claim-schema-entry-elements"></a>Elementy wpisów schematu roszczeń

**Wartość:** Element Value definiuje wartość statyczną jako dane, które mają być emitowane w ramach tego żądania.

**Para Source/ID:** Elementy source i ID definiują lokalizację, z której pochodzą dane.

**Para Source/ExtensionID:** Elementy source i ExtensionID definiują atrybut rozszerzenia schematu katalogu, z którego pochodzą dane z tego żądania. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów rozszerzenia schematu katalogu w oświadczeniach](active-directory-schema-extensions.md).

Ustaw element source na jedną z następujących wartości:

- "użytkownik": dane w ramach tego żądania są właściwością obiektu użytkownika.
- "aplikacja": dane w ramach tego żądania są właściwością w jednostce usługi aplikacji (klienta).
- "zasób": dane w ramach tego żądania są właściwością jednostki usługi zasobu.
- "odbiorcy": dane w ramach tego żądania są właściwością jednostki usługi, która jest odbiorcami tokenu (jednostki usługi klienta lub zasobu).
- "Firma": dane w ramach tego żądania są właściwością obiektu firmy dzierżawcy zasobu.
- "transformacja": dane w potwierdzeniu pochodzą z transformacji oświadczeń (zobacz sekcję "transformacja oświadczeń" w dalszej części tego artykułu).

Jeśli źródło jest przekształcane, element **TransformationID** musi być również uwzględniony w tej definicji tego żądania.

Element ID identyfikuje, która Właściwość źródła udostępnia wartość dla tego żądania. W poniższej tabeli wymieniono wartości identyfikatorów, które są prawidłowe dla każdej wartości źródła.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: prawidłowe wartości identyfikatorów na Źródło

| Element źródłowy | ID (Identyfikator) | Opis |
|-----|-----|-----|
| Użytkownik | surname | Nazwa rodziny |
| Użytkownik | givenname | Imię |
| Użytkownik | displayname | Nazwa wyświetlana |
| Użytkownik | obiektu | ObjectID |
| Użytkownik | mail (poczta) | Adres e-mail |
| Użytkownik | userPrincipalName | Nazwa główna użytkownika |
| Użytkownik | działu,|Dział|
| Użytkownik | nazwy pospolitej onpremisessamaccountname | Nazwa lokalnego konta SAM |
| Użytkownik | NetBiosName| Nazwa NetBios |
| Użytkownik | NazwaDomenyDNS | Nazwa domeny DNS |
| Użytkownik | onpremisesecurityidentifier | Lokalny identyfikator zabezpieczeń |
| Użytkownik | CompanyName| Nazwa organizacji |
| Użytkownik | streetaddress | Ulica i numer |
| Użytkownik | pocztowy | Postal Code |
| Użytkownik | preferredlanguage | Preferowany język |
| Użytkownik | onpremisesuserprincipalname | Lokalna nazwa UPN |*
| Użytkownik | mailNickname | Pseudonim poczty |
| Użytkownik | extensionattribute1 | Atrybut rozszerzenia 1 |
| Użytkownik | extensionattribute2 | Atrybut rozszerzenia 2 |
| Użytkownik | extensionattribute3 | Atrybut rozszerzenia 3 |
| Użytkownik | extensionattribute4 | Atrybut rozszerzenia 4 |
| Użytkownik | extensionattribute5 | Atrybut rozszerzenia 5 |
| Użytkownik | extensionattribute6 | Atrybut rozszerzenia 6 |
| Użytkownik | extensionattribute7 | Atrybut rozszerzenia 7 |
| Użytkownik | extensionattribute8 | Atrybut rozszerzenia 8 |
| Użytkownik | extensionattribute9 | Atrybut rozszerzenia 9 |
| Użytkownik | extensionattribute10 | Atrybut rozszerzenia 10 |
| Użytkownik | extensionattribute11 | Atrybut rozszerzenia 11 |
| Użytkownik | extensionattribute12 | Atrybut rozszerzenia 12 |
| Użytkownik | extensionattribute13 | Atrybut rozszerzenia 13 |
| Użytkownik | extensionattribute14 | Atrybut rozszerzenia 14 |
| Użytkownik | extensionattribute15 | Atrybut rozszerzenia 15 |
| Użytkownik | othermail | Inna poczta |
| Użytkownik | country | Kraj/region |
| Użytkownik | city | City (Miasto) |
| Użytkownik | stan | Stan |
| Użytkownik | stanowiska | Stanowisko |
| Użytkownik | employeeid | Identyfikator pracownika |
| Użytkownik | facsimiletelephonenumber | Numer telefonu faksu |
| Użytkownik | assignedroles | lista ról aplikacji przypisanych do użytkownika|
| aplikacja, zasób, odbiorcy | displayname | Nazwa wyświetlana |
| aplikacja, zasób, odbiorcy | obiektu | ObjectID |
| aplikacja, zasób, odbiorcy | tags | Główny tag usługi |
| Firma | tenantcountry | Kraj/region dzierżawy |

**TransformationID:** Element TransformationID musi być podany tylko wtedy, gdy element source ma wartość "Transformation".

- Ten element musi być zgodny z elementem ID wpisu przekształcenia we właściwości **ClaimsTransformation** , który definiuje sposób generowania danych dla tego żądania.

**Typ zgłoszenia:** Elementy **JwtClaimType** i **SamlClaimType** definiują, do których odnosi się ten wpis schematu tego żądania.

- JwtClaimType musi zawierać nazwę żądania, które ma być emitowane w JWTs.
- SamlClaimType musi zawierać identyfikator URI żądania, które ma być emitowane w tokenach SAML.

* **atrybut onPremisesUserPrincipalName:** W przypadku używania alternatywnego identyfikatora Atrybut userPrincipalName jest synchronizowany z atrybutem usługi Azure AD onPremisesUserPrincipalName. Ten atrybut jest dostępny tylko wtedy, gdy jest skonfigurowany alternatywny identyfikator, ale jest również dostępny za poorednictwem programu MS Graph beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Nazwy i identyfikatory URI oświadczeń w zestawie oświadczeń z ograniczeniami nie mogą być używane dla elementów typu oświadczenia. Aby uzyskać więcej informacji, zobacz sekcję "wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ClaimsTransformation

**Typ danych:** Obiekt BLOB JSON z co najmniej jednym wpisem transformacji

**Podsumowanie:** Użyj tej właściwości, aby zastosować typowe przekształcenia do danych źródłowych w celu wygenerowania danych wyjściowych dla oświadczeń określonych w schemacie oświadczeń.

**Identyfikator:** Użyj elementu ID, aby odwołać się do tego wpisu przekształcenia we wpisie schematu oświadczeń TransformationID. Ta wartość musi być unikatowa dla każdego wpisu transformacji w ramach tych zasad.

**TransformationMethod:** Element TransformationMethod identyfikuje, która operacja jest wykonywana w celu wygenerowania danych dla tego żądania.

W oparciu o wybraną metodę jest oczekiwany zestaw danych wejściowych i wyjściowych. Zdefiniuj dane wejściowe i wyjściowe przy użyciu elementów **InputClaims**, **InputParameters** i **OutputClaims** .

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: metody transformacji i oczekiwane dane wejściowe i wyjściowe

|TransformationMethod|Oczekiwane dane wejściowe|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Join|ciąg1, ciąg2, separator|Oświadczenie outputclaim|Sprzęga ciągi wejściowe przy użyciu separatora między. Na przykład: ciąg1: " foo@bar.com ", ciąg2: "piaskownica", separator: "." powoduje w oświadczenie outputclaim: " foo@bar.com.sandbox "|
|ExtractMailPrefix|Adres e-mail lub nazwa UPN|wyodrębniony ciąg|ExtensionAttributes 1-15 lub wszystkie inne rozszerzenia schematu, które przechowują nazwę UPN lub adres e-mail użytkownika, np. johndoe@contoso.com . Wyodrębnia lokalną część adresu e-mail. Na przykład: mail: " foo@bar.com " powoduje oświadczenie outputclaim: "foo". Jeśli \@ znak nie jest obecny, oryginalny ciąg wejściowy jest zwracany w postaci, w jakiej jest.|

**InputClaims:** Użyj elementu InputClaims, aby przekazać dane ze wpisu schematu roszczeń do transformacji. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest przyłączony do elementu ID wpisu schematu Claims w celu znalezienia odpowiedniego żądania wejściowego.
- **TransformationClaimType** służy do przydzielenia unikatowej nazwy do tej operacji. Ta nazwa musi być zgodna z jednym z oczekiwanych danych wejściowych dla metody transformacji.

**InputParameters:** Użyj elementu InputParameters, aby przekazać stałą wartość do transformacji. Ma dwa atrybuty: **Value** i **ID**.

- **Wartość** to rzeczywista wartość stałej, która ma zostać przeniesiona.
- **Identyfikator** jest używany do nadawania unikatowej nazwy dane wejściowe. Nazwa musi odpowiadać jednemu z oczekiwanych danych wejściowych dla metody transformacji.

**OutputClaims:** Użyj elementu OutputClaims, aby przechowywać dane wygenerowane przez transformację i powiązać je z wpisem schematu roszczenia. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest dołączany z identyfikatorem wpisu schematu roszczeń, aby znaleźć odpowiednie zgłoszenie wyjściowe.
- **TransformationClaimType** jest używana do nadania unikatowej nazwy dane wyjściowe. Nazwa musi odpowiadać jednemu z oczekiwanych danych wyjściowych dla metody transformacji.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**NameID SAML i nazwa UPN:** Atrybuty, z których pochodzą wartości NameID i nazwy UPN, a dozwolone przekształcenia oświadczeń są ograniczone. Zobacz Tabela 5 i tabela 6, aby wyświetlić dozwolone wartości.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: atrybuty dozwolone jako źródło danych dla elementu SAML NameID

|Element źródłowy|ID (Identyfikator)|Opis|
|-----|-----|-----|
| Użytkownik | mail (poczta)|Adres e-mail|
| Użytkownik | userPrincipalName|Nazwa główna użytkownika|
| Użytkownik | nazwy pospolitej onpremisessamaccountname|Nazwa lokalnego konta sam|
| Użytkownik | employeeid|Identyfikator pracownika|
| Użytkownik | extensionattribute1 | Atrybut rozszerzenia 1 |
| Użytkownik | extensionattribute2 | Atrybut rozszerzenia 2 |
| Użytkownik | extensionattribute3 | Atrybut rozszerzenia 3 |
| Użytkownik | extensionattribute4 | Atrybut rozszerzenia 4 |
| Użytkownik | extensionattribute5 | Atrybut rozszerzenia 5 |
| Użytkownik | extensionattribute6 | Atrybut rozszerzenia 6 |
| Użytkownik | extensionattribute7 | Atrybut rozszerzenia 7 |
| Użytkownik | extensionattribute8 | Atrybut rozszerzenia 8 |
| Użytkownik | extensionattribute9 | Atrybut rozszerzenia 9 |
| Użytkownik | extensionattribute10 | Atrybut rozszerzenia 10 |
| Użytkownik | extensionattribute11 | Atrybut rozszerzenia 11 |
| Użytkownik | extensionattribute12 | Atrybut rozszerzenia 12 |
| Użytkownik | extensionattribute13 | Atrybut rozszerzenia 13 |
| Użytkownik | extensionattribute14 | Atrybut rozszerzenia 14 |
| Użytkownik | extensionattribute15 | Atrybut rozszerzenia 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: metody transformacji dozwolone dla NameID SAML

| TransformationMethod | Ograniczenia |
| ----- | ----- |
| ExtractMailPrefix | Brak |
| Join | Dołączony sufiks musi być zweryfikowaną domeną dzierżawy zasobu. |

### <a name="custom-signing-key"></a>Niestandardowy klucz podpisywania

Aby zasady mapowania oświadczeń zaczęły obowiązywać, należy przypisać niestandardowy klucz podpisywania do obiektu jednostki usługi. Zapewnia to potwierdzenie, że tokeny zostały zmodyfikowane przez twórcę zasad mapowania oświadczeń i chroni aplikacje przed zasadami mapowania oświadczeń utworzonymi przez złośliwe podmioty. Aby dodać niestandardowy klucz podpisywania, możesz użyć polecenia cmdlet Azure PowerShell, [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) Aby utworzyć poświadczenia klucza certyfikatu dla obiektu aplikacji.

Aplikacje z włączonym mapowaniem oświadczeń muszą sprawdzać poprawność swoich kluczy podpisywania tokenu przez dołączanie ich `appid={client_id}` do [żądań metadanych OpenID Connect Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Poniżej znajduje się format dokumentu metadanych OpenID Connect Connect, którego należy użyć:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scenariusze dla wielu dzierżawców

Zasady mapowania oświadczeń nie są stosowane dla użytkowników-Gości. Jeśli użytkownik-Gość próbuje uzyskać dostęp do aplikacji z zasadami mapowania oświadczeń przypisanymi do jej nazwy głównej usługi, zostanie wystawiony token domyślny (zasady nie mają żadnego skutku).

## <a name="claims-mapping-policy-assignment"></a>Przypisanie zasad mapowania oświadczeń

Zasady mapowania oświadczeń można przypisywać tylko do obiektów głównych usługi.

### <a name="example-claims-mapping-policies"></a>Przykładowe zasady mapowania oświadczeń

W usłudze Azure AD wiele scenariuszy jest możliwe, gdy można dostosować oświadczenia emitowane w tokenach dla określonych podmiotów usługi. W tej sekcji omówiono kilka typowych scenariuszy, które mogą pomóc w opanujesz, jak używać typu zasad mapowania oświadczeń.

> [!NOTE]
> Podczas tworzenia zasad mapowania oświadczeń można także wyemitować oświadczenie z atrybutu rozszerzenia schematu katalogu w tokenach. Użyj *ExtensionID* dla atrybutu rozszerzenia zamiast *identyfikatora* w `ClaimsSchema` elemencie.  Aby uzyskać więcej informacji na temat atrybutów rozszerzeń, zobacz [Używanie atrybutów rozszerzenia schematu katalogu](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach tworzysz, aktualizujesz, łączysz i usuwasz zasady dla podmiotów usługi. Jeśli dopiero zaczynasz korzystać z usługi Azure AD, zalecamy [zapoznanie się z tematem jak uzyskać dzierżawę usługi Azure AD](quickstart-create-new-tenant.md) przed wykonaniem tych przykładów.

Aby rozpocząć, wykonaj następujące czynności:

1. Pobierz najnowszą [wersję publicznej wersji zapoznawczej modułu programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie Connect, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie przy każdym uruchomieniu nowej sesji.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Aby wyświetlić wszystkie zasady, które zostały utworzone w organizacji, uruchom następujące polecenie. Zalecamy uruchomienie tego polecenia po większości operacji w następujących scenariuszach, aby sprawdzić, czy zasady są tworzone zgodnie z oczekiwaniami.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Przykład: Utwórz i przypisz zasady, aby pominąć podstawowe oświadczenia z tokenów wystawionych dla jednostki usługi

W tym przykładzie utworzysz zasady, które usuwają podstawowy zestaw roszczeń z tokenów wystawionych dla powiązanych podmiotów usługi.

1. Utwórz zasady mapowania oświadczeń. Te zasady, połączone z określonymi jednostkami usługi, usuwają podstawowy zestaw roszczeń z tokenów.
   1. Aby utworzyć zasady, uruchom następujące polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do nazwy głównej usługi. Należy również uzyskać identyfikator ObjectId nazwy głównej usługi.
   1. Aby wyświetlić wszystkie nazwy główne usługi w organizacji, można [wysłać zapytanie do interfejsu API Microsoft Graph](/graph/traverse-the-graph). Lub w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)Zaloguj się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId nazwy głównej usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Przykład: Utwórz i przypisz zasady, aby uwzględnić IDPracownika i TenantCountry jako oświadczenia w tokenach wystawionych dla jednostki usługi

W tym przykładzie utworzysz zasady, które dodają elementy IDPracownika i TenantCountry do tokenów wystawionych dla połączonych podmiotów usługi. Element IDPracownika jest emitowany jako nazwa typ wystąpienia w tokenach SAML i JWTs. TenantCountry jest emitowany jako typ roszczeń kraju/regionu w tokenach SAML i JWTs. W tym przykładzie nadal będziemy używać podstawowych oświadczeń ustawionych w tokenach.

1. Utwórz zasady mapowania oświadczeń. Te zasady połączone z określonymi jednostkami usługi dodają do tokenów oświadczenia IDPracownika i TenantCountry.
   1. Aby utworzyć zasady, uruchom następujące polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do nazwy głównej usługi. Należy również uzyskać identyfikator ObjectId nazwy głównej usługi.
   1. Aby wyświetlić wszystkie nazwy główne usługi w organizacji, można [wysłać zapytanie do interfejsu API Microsoft Graph](/graph/traverse-the-graph). Lub w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)Zaloguj się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId nazwy głównej usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Przykład: Tworzenie i przypisywanie zasad korzystających z transformacji oświadczeń w tokenach wystawionych dla jednostki usługi

W tym przykładzie utworzysz zasady, które emitują niestandardową wartość "JoinedData" do JWTs wystawionego dla połączonych jednostek usługi. To zgłoszenie zawiera wartość utworzoną przez przyłączenie danych przechowywanych w atrybucie extensionAttribute1 obiektu użytkownika z "piaskownicy". W tym przykładzie wyłączono podstawowe oświadczenia ustawione w tokenach.

1. Utwórz zasady mapowania oświadczeń. Te zasady połączone z określonymi jednostkami usługi dodają do tokenów oświadczenia IDPracownika i TenantCountry.
   1. Aby utworzyć zasady, uruchom następujące polecenie:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Aby wyświetlić nowe zasady i uzyskać identyfikator ObjectId zasad, uruchom następujące polecenie:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Przypisz zasady do nazwy głównej usługi. Należy również uzyskać identyfikator ObjectId nazwy głównej usługi.
   1. Aby wyświetlić wszystkie nazwy główne usługi w organizacji, można [wysłać zapytanie do interfejsu API Microsoft Graph](/graph/traverse-the-graph). Lub w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)Zaloguj się do konta usługi Azure AD.
   2. Jeśli masz identyfikator ObjectId nazwy głównej usługi, uruchom następujące polecenie:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Zobacz też

- Aby dowiedzieć się, jak dostosować oświadczenia wystawione w tokenie SAML za pomocą Azure Portal, zobacz [How to: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](active-directory-saml-claims-customization.md)
- Aby dowiedzieć się więcej na temat atrybutów rozszerzenia, zobacz [Używanie atrybutów rozszerzenia schematu katalogu w oświadczeniach](active-directory-schema-extensions.md).
