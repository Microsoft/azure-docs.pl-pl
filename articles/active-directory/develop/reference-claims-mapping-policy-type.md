---
title: Zasady mapowania oświadczeń
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o typie zasad mapowania oświadczeń, który służy do modyfikowania oświadczeń emitowanych w tokenach wystawionych dla określonych aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 04/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 56e855bafa70360711f3e30a7c4527091af7b34c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601242"
---
# <a name="claims-mapping-policy-type"></a>Typ zasad mapowania oświadczeń

W usłudze Azure AD obiekt **Policy** reprezentuje zestaw reguł wymuszanych dla poszczególnych aplikacji lub wszystkich aplikacji w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są następnie stosowane do obiektów, do których są przypisane.

Zasady mapowania oświadczeń to typ obiektu **zasad,** który modyfikuje oświadczenia emitowane w [tokenach](active-directory-claims-mapping.md) wystawionych dla określonych aplikacji.

## <a name="claim-sets"></a>Zestawy oświadczeń

Istnieją pewne zestawy oświadczeń, które definiują sposób i czas ich korzystania z tokenów.

| Zestaw oświadczenia | Opis |
|---|---|
| Podstawowy zestaw oświadczenia | Są obecne w każdym tokenie niezależnie od zasad. Te oświadczenia są również uznawane za ograniczone i nie można ich modyfikować. |
| Podstawowy zestaw oświadczenia | Obejmuje oświadczenia, które są emitowane domyślnie dla tokenów (oprócz zestawu oświadczeń podstawowych). Można pominąć lub zmodyfikować podstawowe oświadczenia przy użyciu zasad mapowania oświadczeń. |
| Zestaw oświadczenia z ograniczeniami | Nie można zmodyfikować przy użyciu zasad. Źródła danych nie można zmienić i podczas generowania tych oświadczeń nie jest stosowane żadne przekształcenie. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: zestaw JSON Web Token (JWT) z ograniczeniami

| Typ oświadczenia (nazwa) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Aktor |
| actortoken |
| Aio |
| altsecid |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appidacr |
| Potwierdzenia |
| at_hash |
| AUD |
| auth_data |
| auth_time |
| authorization_code |
| Azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| Cnf |
| kod |
| funkcje sterowania |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
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
| Wykres |
| group_sids |
| grupy |
| grupy hasgroup |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| Iat |
| identityprovider |
| Idp |
| in_corp |
| Wystąpienie |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| Key_id |
| Key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| Nbf |
| netbios_name |
| nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| hasło |
| identyfikatory LDDS |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
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
| Scp |
| Sid |
| Podpis |
| signin_state |
| src1 |
| src2 |
| Sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| Upn |
| user_setting_sync_url |
| nazwa użytkownika |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2. Zestaw ograniczonych oświadczenia SAML

| Typ oświadczenia (URI) |
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

Aby kontrolować, jakie oświadczenia są emitowane i skąd pochodzą dane, użyj właściwości zasad mapowania oświadczeń. Jeśli zasady nie są ustawione, system wydaje tokeny, które obejmują podstawowy zestaw [](active-directory-optional-claims.md) oświadczeń, podstawowy zestaw oświadczeń i wszelkie opcjonalne oświadczenia wybrane przez aplikację do odbierania.

> [!NOTE]
> Oświadczenia w podstawowym zestawie oświadczeń są obecne w każdym tokenie, niezależnie od tego, co ta właściwość jest ustawiona.

### <a name="include-basic-claim-set"></a>Uwzględnianie podstawowego zestawu oświadczenia

**Ciąg:** IncludeBasicClaimSet

**Typ danych:** Wartość logiczna (prawda lub fałsz)

**Podsumowanie:** Ta właściwość określa, czy podstawowy zestaw uwierzytelniania jest uwzględniony w tokenach, na które te zasady mają wpływ.

- W przypadku ustawienia wartości Prawda wszystkie oświadczenia w podstawowym zestawie oświadczeń są emitowane w tokenach, których dotyczą zasady.
- W przypadku ustawienia wartości Fałsz oświadczenia w podstawowym zestawie oświadczeń nie znajdują się w tokenach, chyba że są indywidualnie dodawane we właściwości schematu oświadczeń tych samych zasad.



### <a name="claims-schema"></a>Schemat oświadczeń

**Ciąg:** ClaimsSchema

**Typ danych:** Obiekt blob JSON z co najmniej jednym wpisem schematu oświadczenia

**Podsumowanie:** Ta właściwość określa, które oświadczenia są obecne w tokenach, których dotyczą zasady, oprócz podstawowego zestawu oświadczeń i zestawu oświadczeń podstawowych.
Dla każdego wpisu schematu oświadczenia zdefiniowanego w tej właściwości wymagane są pewne informacje. Określ, skąd pochodzą dane **(wartość,** para **źródło/identyfikator** lub para **Source/ExtensionID),** a które oświadczenie jest emitowane jako (**Typ oświadczenia**).

### <a name="claim-schema-entry-elements"></a>Oświadczenia elementów wpisu schematu

**Wartość:** Wartość element definiuje wartość statyczną jako dane, które mają być emitowane w oświadczenie.

**Para źródło/identyfikator:** Elementy Source i ID definiują, skąd pochodzą dane w oświadczenie.

**Para Source/ExtensionID:** Elementy Source i ExtensionID definiują atrybut rozszerzenia schematu katalogu, z którego pochodzą dane w oświadczenie. Aby uzyskać więcej informacji, zobacz [Using directory schema extension attributes in claims (Używanie atrybutów rozszerzenia schematu katalogu w oświadczeniach).](active-directory-schema-extensions.md)

Ustaw element Source na jedną z następujących wartości:

- "użytkownik": dane w oświadczenie jest właściwością obiektu Użytkownika.
- "aplikacja": dane w oświadczenie jest właściwością jednostki usługi aplikacji (klienta).
- "zasób": dane w oświadczenie jest właściwością jednostki usługi zasobów.
- "odbiorcy": dane w oświadczenie jest właściwością jednostki usługi, która jest odbiorcą tokenu (klienta lub jednostki usługi zasobów).
- "firma": dane w oświadczenie jest właściwością obiektu Company dzierżawy zasobu.
- "transformacja": Dane w oświadczenie pochodzi z przekształcania oświadczeń (zobacz sekcję "Przekształcanie oświadczeń" w dalszej części tego artykułu).

Jeśli źródłem jest przekształcenie, **element TransformationID** musi również zostać uwzględniony w tej definicji oświadczenia.

Element ID określa, która właściwość w źródle zawiera wartość oświadczenia. W poniższej tabeli wymieniono wartości identyfikatora prawidłowe dla każdej wartości source.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3. Prawidłowe wartości identyfikatorów na źródło

| Element źródłowy | ID (Identyfikator) | Opis |
|-----|-----|-----|
| Użytkownik | surname | Nazwa rodziny |
| Użytkownik | givenname | Imię |
| Użytkownik | displayname | Nazwa wyświetlana |
| Użytkownik | Objectid | ObjectID |
| Użytkownik | mail (poczta) | Adres e-mail |
| Użytkownik | Userprincipalname | Nazwa główna użytkownika |
| Użytkownik | działu,|Dział|
| Użytkownik | onpremisessamaccountname | Nazwa lokalnego konta SAM |
| Użytkownik | netbiosname| Nazwa NetBios |
| Użytkownik | dnsdomainname (nazwa domeny dns) | Nazwa domeny DNS |
| Użytkownik | onpremisesecurityidentifier | Identyfikator zabezpieczeń w środowisku lokalnym |
| Użytkownik | Companyname| Nazwa organizacji |
| Użytkownik | Streetaddress | Ulica i numer |
| Użytkownik | Postalcode | Postal Code |
| Użytkownik | preferowany język | Preferowany język |
| Użytkownik | onpremisesuserprincipalname | Lokalna upn |
| Użytkownik | mailnickname (nazwa mailnickname) | Pseudonim poczty e-mail |
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
| Użytkownik | inna poczta | Inna poczta |
| Użytkownik | country | Kraj/region |
| Użytkownik | city | City (Miasto) |
| Użytkownik | stan | Stan |
| Użytkownik | stanowisko | Stanowisko |
| Użytkownik | employeeid | Identyfikator pracownika |
| Użytkownik | facsimiletelephonenumber | Numer telefonu facsimile |
| Użytkownik | assignedroles | lista ról aplikacji przypisanych do użytkownika|
| aplikacja, zasób, odbiorcy | displayname | Nazwa wyświetlana |
| aplikacja, zasób, odbiorcy | Objectid | ObjectID |
| aplikacja, zasób, odbiorcy | tags | Tag jednostki usługi |
| Firma | tenantcountry | Kraj/region dzierżawy |

**TransformationID:** Element TransformationID należy podać tylko wtedy, gdy element Source jest ustawiony na "transformację".

- Ten element musi odpowiadać elementowi identyfikatora wpisu przekształcenia we właściwości **ClaimsTransformation,** który definiuje sposób generowania danych dla tego oświadczenia.

**Typ oświadczenia:** Elementy **JwtClaimType** i **SamlClaimType** definiują, do którego oświadczenia odnosi się ten wpis schematu oświadczenia.

- JwtClaimType musi zawierać nazwę oświadczenia, które ma być emitowane w JWTs.
- SamlClaimType musi zawierać URI oświadczenia, które mają być emitowane w tokenach SAML.

* **onPremisesUserPrincipalName, atrybut:** W przypadku używania alternatywnego identyfikatora atrybut on-premises userPrincipalName jest synchronizowany z atrybutem usługi Azure AD onPremisesUserPrincipalName. Ten atrybut jest dostępny tylko w przypadku skonfigurowania alternatywnego identyfikatora, ale jest również dostępny za pośrednictwem programu MS Graph Beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Nazwy i URI oświadczeń w zestawie oświadczeń z ograniczeniami nie mogą być używane dla elementów typu oświadczenia. Aby uzyskać więcej informacji, zobacz sekcję "Wyjątki i ograniczenia" w dalszej części tego artykułu.

### <a name="claims-transformation"></a>Przekształcanie oświadczeń

**Ciąg:** ClaimsTransformation

**Typ danych:** Obiekt blob JSON z co najmniej jednym wpisem przekształcenia

**Podsumowanie:** Ta właściwość pozwala zastosować typowe przekształcenia do danych źródłowych w celu wygenerowania danych wyjściowych dla oświadczeń określonych w schemacie oświadczeń.

**Identyfikator:** Użyj id element, aby odwołać się do tego wpisu przekształcenia we wpisie Schematu oświadczeń TransformationID. Ta wartość musi być unikatowa dla każdego wpisu przekształcenia w ramach tych zasad.

**TransformationMethod:** Element TransformationMethod określa, która operacja jest wykonywana w celu wygenerowania danych dla oświadczenia.

Na podstawie wybranej metody oczekiwany jest zestaw danych wejściowych i wyjściowych. Zdefiniuj dane wejściowe i wyjściowe przy użyciu elementów **InputClaims,** **InputParameters** i **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4. Metody przekształcania oraz oczekiwane dane wejściowe i wyjściowe

|TransformationMethod|Oczekiwane dane wejściowe|Oczekiwane dane wyjściowe|Opis|
|-----|-----|-----|-----|
|Dołączanie|string1, string2, separator|outputClaim|Sprzężenia ciągów wejściowych przy użyciu separatora między. Na przykład: string1:" foo@bar.com " , string2:"sandbox" , separator:"." powoduje wynik outputClaim:" foo@bar.com.sandbox "|
|ExtractMailPrefix|Adres e-mail lub upn|wyodrębniony ciąg|ExtensionAttributes 1–15 lub inne rozszerzenia schematu, które przechowują wartość nazwy UPN lub adresu e-mail użytkownika, johndoe@contoso.com np. . Wyodrębnia lokalną część adresu e-mail. Na przykład: mail:" foo@bar.com " powoduje outputClaim:"foo". Jeśli znak \@ nie jest obecny, oryginalny ciąg wejściowy jest zwracany w postaci, w ile jest.|

**InputClaims:** Użyj InputClaims element, aby przekazać dane z wpisu schematu oświadczenia do przekształcenia. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest sprzężenia z elementem identyfikatora wpisu schematu oświadczenia, aby znaleźć odpowiednie oświadczenie wejściowe.
- **TransformationClaimType** służy do nadawać unikatową nazwę tym wejściom. Ta nazwa musi odpowiadać jednem z oczekiwanych danych wejściowych dla metody przekształcania.

**InputParameters:** Użyj elementu InputParameters, aby przekazać wartość stałą do przekształcenia. Ma dwa atrybuty: **wartość i** **identyfikator**.

- **Wartość** jest rzeczywistą wartością stałą, która ma zostać przekazana.
- **Identyfikator** służy do nadawać unikatową nazwę danych wejściowych. Nazwa musi odpowiadać jednem z oczekiwanych danych wejściowych dla metody przekształcania.

**OutputClaims:** Użyj OutputClaims element do przechowywania danych wygenerowanych przez przekształcenie i powiązać je z wpisem schematu oświadczenia. Ma dwa atrybuty: **ClaimTypeReferenceId** i **TransformationClaimType**.

- **ClaimTypeReferenceId** jest sprzężenia z identyfikatorem wpisu schematu oświadczenia, aby znaleźć odpowiednie oświadczenie wyjściowe.
- **TransformationClaimType** służy do nadawać unikatową nazwę danych wyjściowych. Nazwa musi odpowiadać jednem z oczekiwanych danych wyjściowych metody przekształcania.

### <a name="exceptions-and-restrictions"></a>Wyjątki i ograniczenia

**SamL NameID i UPN:** Atrybuty, z których pochodzą wartości NameID i UPN, oraz dozwolone przekształcenia oświadczeń, są ograniczone. Zobacz tabelę 5 i tabelę 6, aby zobaczyć dozwolone wartości.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5. Atrybuty dozwolone jako źródło danych dla atrybutu SAML NameID

|Element źródłowy|ID (Identyfikator)|Opis|
|-----|-----|-----|
| Użytkownik | mail (poczta)|Adres e-mail|
| Użytkownik | Userprincipalname|Nazwa główna użytkownika|
| Użytkownik | onpremisessamaccountname|Lokalna nazwa konta Sam|
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

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6. Metody przekształcania dozwolone dla nameID saml

| TransformationMethod | Ograniczenia |
| ----- | ----- |
| ExtractMailPrefix | Brak |
| Dołączanie | Przyłączony sufiks musi być zweryfikowaną domeną dzierżawy zasobów. |

### <a name="cross-tenant-scenarios"></a>Scenariusze między dzierżawami

Zasady mapowania oświadczeń nie mają zastosowania do użytkowników-gości. Jeśli użytkownik-gość próbuje uzyskać dostęp do aplikacji przy użyciu zasad mapowania oświadczeń przypisanych do jednostki usługi, wystawiany jest token domyślny (zasady nie mają zastosowania).


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak dostosować oświadczenia emitowane w tokenach dla określonej aplikacji w dzierżawie przy użyciu programu PowerShell, zobacz Jak dostosować oświadczenia emitowane w tokenach dla określonej aplikacji w dzierżawie [(wersja zapoznawcza)](active-directory-claims-mapping.md)
- Aby dowiedzieć się, jak dostosować oświadczenia wystawione w tokenie SAML za pośrednictwem interfejsu Azure Portal, zobacz Jak dostosować oświadczenia wystawione w [tokenie SAML](active-directory-saml-claims-customization.md) dla aplikacji dla przedsiębiorstw
- Aby dowiedzieć się więcej o atrybutach rozszerzenia, zobacz Using directory schema extension attributes in claims (Używanie atrybutów rozszerzenia [schematu katalogu w oświadczeniach).](active-directory-schema-extensions.md)
