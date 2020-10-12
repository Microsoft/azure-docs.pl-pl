---
title: 'Azure AD Connect: Rozwiązywanie problemów z błędami podczas synchronizacji | Microsoft Docs'
description: Wyjaśnia, jak rozwiązywać problemy napotkane podczas synchronizacji z Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd779c26cd523bbf33fa1be6c87f21b4415c152
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016422"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Rozwiązywanie problemów z błędami podczas synchronizacji
Błędy mogą wystąpić, gdy dane tożsamości są synchronizowane z systemu Windows Server Active Directory (AD DS) do Azure Active Directory (Azure AD). Ten artykuł zawiera omówienie różnych typów błędów synchronizacji, niektóre możliwe scenariusze, które powodują te błędy, oraz potencjalne sposoby naprawienia błędów. Ten artykuł zawiera typowe typy błędów i może nie obejmować wszystkich możliwych błędów.

 W tym artykule założono, że czytelnik zna podstawowe [koncepcje projektowe usługi Azure AD i Azure AD Connect](plan-connect-design-concepts.md).

W najnowszej wersji Azure AD Connect \( sierpnia 2016 lub nowszej \) raport o błędach synchronizacji jest dostępny w [Azure Portal](https://aka.ms/aadconnecthealth) jako część Azure AD Connect Health do synchronizacji.

Od 1 września 2016 [Azure Active Directory funkcja odporności na zduplikowane atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md) zostanie domyślnie włączona dla wszystkich *nowych* dzierżawców Azure Active Directory. Ta funkcja zostanie automatycznie włączona dla istniejących dzierżawców w nadchodzących miesiącach.

Azure AD Connect wykonuje trzy typy operacji z katalogów, które synchronizują: import, synchronizacja i eksportowanie. Błędy mogą odbywać się we wszystkich operacjach. Ten artykuł dotyczy głównie błędów podczas eksportowania do usługi Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Błędy podczas eksportowania do usługi Azure AD
W poniższej sekcji opisano różne typy błędów synchronizacji, które mogą wystąpić podczas operacji eksportowania do usługi Azure AD przy użyciu łącznika usługi Azure AD. Ten łącznik może być identyfikowany przez format nazwy "contoso". *onmicrosoft.com*".
Błędy podczas eksportowania do usługi Azure AD wskazują, że operacja \( Dodawanie, aktualizowanie, usuwanie itp. \) podjęto próbę przez \( aparat synchronizacji Azure AD Connect \) na Azure Active Directory nie powiodła się.

![Omówienie błędów eksportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Błędy niezgodności danych
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Opis
* Gdy Azure AD Connect \( aparat synchronizacji \) instruuje Azure Active Directory o dodawanie lub aktualizowanie obiektów, usługa Azure AD dopasowuje obiekt przychodzący przy użyciu atrybutu **SourceAnchor** do atrybutu **IMMUTABLEID** obiektów w usłudze Azure AD. Takie dopasowanie jest nazywane **twardym dopasowaniem**.
* Gdy usługa Azure AD nie **odnajdzie** żadnych obiektów, które pasują do atrybutu **immutableId** z atrybutem **sourceAnchor** obiektu przychodzącego, przed zainicjowaniem nowego obiektu powraca do użycia atrybutów proxyAddresses i userPrincipalName w celu znalezienia dopasowania. Ta zgodność jest nazywana **niewygładzonym dopasowaniem**. Niezrównane dopasowanie jest przeznaczone do dopasowywania obiektów już obecnych w usłudze Azure AD (które pochodzą z usługi Azure AD) z nowymi obiektami dodawanymi/aktualizowanymi podczas synchronizacji, które reprezentują tę samą jednostkę (użytkowników, grupy) lokalnie.
* Błąd **InvalidSoftMatch** występuje, gdy twarde dopasowanie nie odnajdzie pasujących obiektów **i** niewygładzone dopasowanie odnajdzie pasujący obiekt, ale ten obiekt ma inną wartość *immutableId* niż *SourceAnchor*obiektu przychodzącego, sugerując, że pasujący obiekt został zsynchronizowany z innym obiektem na podstawie Active Directory lokalnego.

Innymi słowy, aby dopasowanie niewygładzone działało, obiekt, który ma być niezgodny z elementem, nie powinien mieć żadnej wartości dla *immutableId*. Jeśli dowolnych obiektów z wartością *immutableId* ustawioną na wartość kończy się niepowodzeniem, ale spełnione są kryteria niezgodności, operacja spowodowałaby wystąpienie błędu synchronizacji InvalidSoftMatch.

Schemat Azure Active Directory nie zezwala, aby dwa lub więcej obiektów miało taką samą wartość następujących atrybutów. \(To nie jest wyczerpująca lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> Funkcja [odporności atrybutów duplikatów atrybutów usługi Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) jest również wdrażana jako domyślne zachowanie Azure Active Directory.  Pozwoli to zmniejszyć liczbę błędów synchronizacji obserwowanych przez Azure AD Connect (a także innych klientów synchronizacji), dzięki czemu usługa Azure AD jest bardziej odporna w sposób, który obsługuje zduplikowane atrybuty ProxyAddresses i UserPrincipalName obecne w lokalnych środowiskach usługi AD. Ta funkcja nie rozwiązuje błędów duplikacji. Dlatego nadal muszą zostać naprawione dane. Umożliwia jednak Inicjowanie obsługi nowych obiektów, które w przeciwnym razie zablokowano z powodu zduplikowanych wartości w usłudze Azure AD. Spowoduje to również zmniejszenie liczby błędów synchronizacji zwracanych do klienta synchronizacji.
> Jeśli ta funkcja jest włączona dla dzierżawy, nie będą wyświetlane błędy synchronizacji InvalidSoftMatch widoczne podczas aprowizacji nowych obiektów.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Przykładowe scenariusze dla InvalidSoftMatch
1. W Active Directory lokalnym istnieje co najmniej dwa obiekty o tej samej wartości atrybutu ProxyAddresses. Tylko jeden z nich jest obsługiwany w usłudze Azure AD.
2. Co najmniej dwa obiekty o tej samej wartości atrybutu userPrincipalName istnieją w Active Directory lokalnym. Tylko jeden z nich jest obsługiwany w usłudze Azure AD.
3. Obiekt został dodany w lokalnym miejscu Active Directory z taką samą wartością atrybutu ProxyAddresses jak w przypadku istniejącego obiektu w Azure Active Directory. Obiekt dodany do lokalizacji lokalnej nie jest obsługiwany w Azure Active Directory.
4. Obiekt został dodany do lokalnego Active Directory z taką samą wartością atrybutu userPrincipalName, jak w przypadku konta w Azure Active Directory. Nie zainicjowano obsługi obiektu w Azure Active Directory.
5. Zsynchronizowane konto zostało przeniesione z lasu A do lasu B. Azure AD Connect (aparat synchronizacji) używa atrybutu ObjectGUID do obliczenia SourceAnchor. Po przeniesieniu lasu wartość SourceAnchor jest różna. Nie można zsynchronizować nowego obiektu (z lasu B) z istniejącym obiektem w usłudze Azure AD.
6. Zsynchronizowany obiekt został przypadkowo usunięty z lokalnego Active Directory i nowy obiekt został utworzony w Active Directory dla tej samej jednostki (na przykład użytkownika) bez usuwania konta w Azure Active Directory. Nie można zsynchronizować nowego konta z istniejącym obiektem usługi Azure AD.
7. Azure AD Connect zostało odinstalowane i ponownie zainstalowane. Podczas ponownej instalacji został wybrany inny atrybut jako SourceAnchor. Wszystkie obiekty, które wcześniej zostały zsynchronizowane, zostały zatrzymane z błędem InvalidSoftMatch.

#### <a name="example-case"></a>Przykład przypadku:
1. **Robert Smith** to zsynchronizowany użytkownik w Azure Active Directory z lokalnego Active Directory usługi *contoso.com*
2. Element **userPrincipalName** Smith Kowalski został ustawiony jako **bobs \@ contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** to **SourceAnchor** obliczony przez Azure AD Connect przy użyciu **objectGUID** Kowalski z lokalnego Active Directory, który jest **immutableId** dla Roberta Kowalski w Azure Active Directory.
4. Robert ma również następujące wartości dla atrybutu **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Robert \@ contoso.com**
5. Nowy użytkownik, **Robert Taylor**, jest dodawany do lokalnego Active Directory.
6. Element **userPrincipalName** "Roberta Taylor" jest ustawiany jako **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** to **sourceAnchor** obliczone przez Azure AD Connect przy użyciu **objectGUID** Taylora z Active Directory lokalnych. Obiekt Roberta Taylor nie został jeszcze zsynchronizowany z Azure Active Directory.
8. Robert Taylor ma następujące wartości atrybutu proxyAddresses
   * SMTP bobt@contoso.com
   * SMTP bob.taylor@contoso.com
   * **SMTP: Robert \@ contoso.com**
9. Podczas synchronizacji Azure AD Connect rozpoznaje dodanie Roberta Taylora w lokalnej Active Directory i poproszenie usługi Azure AD o wprowadzenie tej zmiany.
10. Usługa Azure AD najpierw wykona twarde dopasowanie. Oznacza to, że będzie przeszukiwany, jeśli istnieje obiekt o immutableId równym "abcdefghijkl0123456789 = =". Twarde dopasowanie nie powiedzie się, ponieważ żaden inny obiekt w usłudze Azure AD nie będzie miał tego immutableId.
11. Usługa Azure AD podejmie następnie próbę uzyskania niezrównanej postaci Roberta Taylora. Oznacza to, że zostanie wyszukany obiekt o proxyAddresses równej trzem wartościom, w tym SMTP: bob@contoso.com
12. Usługa Azure AD odnajdzie obiekt Roberta Kowalski, aby odpowiadał kryteriom dopasowania nietrwałego. Ale ten obiekt ma wartość immutableId = "abcdefghijklmnopqrstuv = =". wskazuje, że ten obiekt został zsynchronizowany z innego obiektu z Active Directory lokalnego. W takim przypadku usługa Azure AD nie może niemniej dopasować tych obiektów i spowoduje błąd synchronizacji **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak naprawić błąd InvalidSoftMatch
Najczęstszym powodem błędu InvalidSoftMatch są dwa obiekty o różnych SourceAnchor \( immutableId \) mają taką samą wartość dla atrybutów proxyAddresses i/lub userPrincipalName, które są używane podczas procesu niezgodności w usłudze Azure AD. Aby naprawić nieprawidłowe dopasowanie miękkie

1. Zidentyfikuj zduplikowaną wartość atrybutu proxyAddresses, userPrincipalName lub inną, która powoduje błąd. Zidentyfikuj również, które co najmniej dwa \( \) obiekty są wykorzystywane w konflikcie. Raport wygenerowany przez [Azure AD Connect Health na potrzeby synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość i który obiekt nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który nie powinien mieć tej wartości. Należy wprowadzić zmianę w katalogu, z którego pochodzi obiekt. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli została wprowadzona zmiana w lokalnej usłudze AD, zezwól Azure AD Connect zsynchronizuj zmianę.

Raporty o błędach synchronizacji w ramach Azure AD Connect Health dla synchronizacji są aktualizowane co 30 minut i obejmują błędy z ostatniej próby synchronizacji.

> [!NOTE]
> ImmutableId, według definicji, nie należy zmieniać w okresie istnienia obiektu. Jeśli Azure AD Connect nie został skonfigurowany z pewnymi scenariuszami na podstawie powyższej listy, można się dokończyć w sytuacji, w której Azure AD Connect oblicza inną wartość SourceAnchor dla obiektu usługi AD, który reprezentuje tę samą jednostkę (ten sam użytkownik/Grupa/kontakt itp.), który ma istniejący obiekt usługi Azure AD, który ma być nadal używany.
>
>

#### <a name="related-articles"></a>Powiązane artykuły
* [Zduplikowane lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Opis
Gdy usługa Azure AD próbuje niezrównanie dopasować dwa obiekty, istnieje możliwość, że dwa obiekty typu "Object" (takie jak User, Group, Contact itp.) mają te same wartości atrybutów używanych do przeprowadzenia dopasowania nietrwałego. Ponieważ duplikowanie tych atrybutów nie jest dozwolone w usłudze Azure AD, operacja może spowodować błąd synchronizacji "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Przykładowe scenariusze dotyczące błędu ObjectTypeMismatch
* W Microsoft 365 zostanie utworzona grupa zabezpieczeń z włączoną obsługą poczty. Administrator dodaje nowego użytkownika lub kontakt w lokalnej usłudze AD (który jeszcze nie jest synchronizowany z usługą Azure AD) o tej samej wartości atrybutu ProxyAddresses w grupie Microsoft 365.

#### <a name="example-case"></a>Przykład wielkości liter
1. Administrator tworzy nową grupę zabezpieczeń z włączoną obsługą poczty w Microsoft 365 dla działu podatkowego i udostępnia adres e-mail jako tax@contoso.com . Do tej grupy jest przypisana ProxyAddresses wartość atrybutu **SMTP: podatek \@ contoso.com**
2. Nowy użytkownik jest przyłączany do Contoso.com i tworzone jest konto dla użytkownika lokalnego przy użyciu proxyAddress jako **SMTP: \@ contoso.com podatkowe**
3. Gdy Azure AD Connect zsynchronizuje nowe konto użytkownika, zostanie wyświetlony komunikat o błędzie "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak naprawić błąd ObjectTypeMismatch
Najczęstszym powodem błędu ObjectTypeMismatch są dwa obiekty różnego typu (użytkownik, Grupa, kontakt itp.), które mają taką samą wartość dla atrybutu ProxyAddresses. Aby naprawić ObjectTypeMismatch:

1. Zidentyfikuj zduplikowaną wartość proxyAddresses (lub inny atrybut), która powoduje błąd. Zidentyfikuj również, które co najmniej dwa \( \) obiekty są wykorzystywane w konflikcie. Raport wygenerowany przez [Azure AD Connect Health na potrzeby synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość i który obiekt nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który nie powinien mieć tej wartości. Należy pamiętać, że należy wprowadzić zmiany w katalogu, z którego pochodzą źródło obiektu. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli została wprowadzona zmiana w lokalnej usłudze AD, zezwól Azure AD Connect zsynchronizuj zmianę. Raport o błędach synchronizacji w ramach Azure AD Connect Health dla synchronizacji jest aktualizowany co 30 minut i zawiera błędy ostatniej próby synchronizacji.

## <a name="duplicate-attributes"></a>Zduplikowane atrybuty
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Opis
Schemat Azure Active Directory nie zezwala, aby dwa lub więcej obiektów miało taką samą wartość następujących atrybutów. Oznacza to, że każdy obiekt w usłudze Azure AD musi mieć unikatową wartość tych atrybutów w danym wystąpieniu.

* ProxyAddresses
* UserPrincipalName

Jeśli Azure AD Connect próbuje dodać nowy obiekt lub zaktualizować istniejący obiekt o wartości powyższych atrybutów, które są już przypisane do innego obiektu w Azure Active Directory, operacja spowoduje wystąpienie błędu synchronizacji "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Możliwe scenariusze:
1. Zduplikowana wartość jest przypisana do już zsynchronizowanego obiektu, który powoduje konflikt z innym synchronizowanym obiektem.

#### <a name="example-case"></a>Przykład przypadku:
1. **Robert Smith** to zsynchronizowany użytkownik w Azure Active Directory z lokalnego Active Directory usługi contoso.com
2. Element **userPrincipalName** firmy Roberta Smith on premises jest ustawiony jako **bobs \@ contoso.com**.
3. Robert ma również następujące wartości dla atrybutu **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Robert \@ contoso.com**
4. Nowy użytkownik, **Robert Taylor**, jest dodawany do lokalnego Active Directory.
5. Element **userPrincipalName** "Roberta Taylor" jest ustawiany jako **bobt \@ contoso.com**.
6. **Robert Taylor** ma następujące wartości atrybutu **proxyAddresses** i. SMTP: bobt@contoso.com II. SMTP bob.taylor@contoso.com
7. Obiekt Roberta Taylor został pomyślnie zsynchronizowany z usługą Azure AD.
8. Administrator zdecydował się zaktualizować atrybut **proxyAddresses** Roberta Taylora o następującej wartości: i. **SMTP: Robert \@ contoso.com**
9. Usługa Azure AD podejmie próbę zaktualizowania obiektu Roberta Taylora w usłudze Azure AD o powyższej wartości, ale ta operacja zakończy się niepowodzeniem, ponieważ ta wartość ProxyAddresses jest już przypisana do Roberta Kowalski, co spowoduje błąd "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak naprawić błąd AttributeValueMustBeUnique
Najczęstszym powodem błędu AttributeValueMustBeUnique jest dwa obiekty o różnych SourceAnchor \( immutableId mają tę \) samą wartość dla atrybutów proxyAddresses i/lub userPrincipalName. Aby naprawić błąd AttributeValueMustBeUnique

1. Zidentyfikuj zduplikowaną wartość atrybutu proxyAddresses, userPrincipalName lub inną, która powoduje błąd. Zidentyfikuj również, które co najmniej dwa \( \) obiekty są wykorzystywane w konflikcie. Raport wygenerowany przez [Azure AD Connect Health na potrzeby synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość i który obiekt nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który nie powinien mieć tej wartości. Należy pamiętać, że należy wprowadzić zmiany w katalogu, z którego pochodzą źródło obiektu. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli została wprowadzona zmiana w lokalnej usłudze AD, zezwól Azure AD Connect zsynchronizuj zmianę błędu, aby uzyskać stały.

#### <a name="related-articles"></a>Powiązane artykuły
-[Zduplikowane lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Błędy sprawdzania poprawności danych
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Opis
Azure Active Directory wymusza różne ograniczenia dotyczące danych przed zezwoleniem na zapisanie tych danych w katalogu. Te ograniczenia mają na celu zapewnienie, że użytkownicy końcowi będą mogli korzystać z aplikacji, które są zależne od tych danych.

#### <a name="scenarios"></a>Scenariusze
a. Wartość atrybutu UserPrincipalName zawiera nieprawidłowe/nieobsługiwane znaki.
b. Atrybut UserPrincipalName nie jest zgodny z wymaganym formatem.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak naprawić błąd IdentityDataValidationFailed
a. Upewnij się, że atrybut userPrincipalName ma obsługiwane znaki i wymagany format.

#### <a name="related-articles"></a>Powiązane artykuły
* [Przygotowanie do aprowizacji użytkowników przez synchronizację katalogów do Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Opis
W takim przypadku wynikiem jest błąd synchronizacji **"FederatedDomainChangeError"** , gdy sufiks elementu userPrincipalName użytkownika zostanie zmieniony z jednej domeny federacyjnej na inną domenę federacyjną.

#### <a name="scenarios"></a>Scenariusze
W przypadku synchronizowanego użytkownika sufiks UserPrincipalName został zmieniony z jednej domeny federacyjnej na inną domenę federacyjną w środowisku lokalnym. Na przykład *userPrincipalName = robert \@ contoso.com* został zmieniony na *userPrincipalName = Bob \@ fabrikam.com*.

#### <a name="example"></a>Przykład
1. Robert Kowalski, konto do Contoso.com, zostanie dodany jako nowy użytkownik w Active Directory z elementem UserPrincipalName bob@contoso.com
2. Robert przechodzi do innego działu Contoso.com o nazwie Fabrikam.com, a ich element UserPrincipalName został zmieniony na bob@fabrikam.com
3. Domeny contoso.com i fabrikam.com są domenami federacyjnymi z Azure Active Directory.
4. Element userPrincipalName Roberta nie zostanie zaktualizowany i spowoduje to błąd synchronizacji "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Jak rozwiązać problem
Jeśli sufiks UserPrincipalName użytkownika został zaktualizowany z bob@**contoso.com** do Roberta \@ **fabrikam.com**, gdzie **contoso.com** i **fabrikam.com** są **domenami federacyjnymi**, wykonaj następujące kroki, aby naprawić błąd synchronizacji

1. Zaktualizuj element UserPrincipalName użytkownika w usłudze Azure AD z bob@contoso.com programu do bob@contoso.onmicrosoft.com . Możesz użyć następującego polecenia programu PowerShell z modułem Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Zezwól na następny cykl synchronizacji w celu podjęcia próby synchronizacji. Ta synchronizacja przebiegła pomyślnie i zaktualizuje element UserPrincipalName Roberta bob@fabrikam.com zgodnie z oczekiwaniami.

#### <a name="related-articles"></a>Powiązane artykuły
* [Zmiany nie są synchronizowane przez narzędzie Azure Active Directory Sync po zmianie nazwy UPN konta użytkownika w celu użycia innej domeny federacyjnej](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Opis
Gdy atrybut przekracza dozwolony limit rozmiaru, limit długości lub limit liczby określony przez schemat Azure Active Directory, operacja synchronizacji powoduje błąd synchronizacji **dużychobject** lub **ExceededAllowedLength** . Zazwyczaj ten błąd występuje dla następujących atrybutów

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Możliwe scenariusze
1. Atrybut userCertificate Roberta zapisuje zbyt wiele certyfikatów przypisanych do Roberta. Mogą to być starsze, wygasłe certyfikaty. Sztywny limit to 15 certyfikatów. Aby uzyskać więcej informacji na temat obsługi błędów Dużychobject z atrybutem userCertificate, zapoznaj się z artykułem [Obsługa błędów w systemie LargeObject spowodowanych przez atrybut userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atrybut userSMIMECertificate Roberta zapisuje zbyt wiele certyfikatów przypisanych do Roberta. Mogą to być starsze, wygasłe certyfikaty. Sztywny limit to 15 certyfikatów.
3. Ustawienie thumbnailPhoto Roberta w Active Directory jest zbyt duże, aby można je było synchronizować w usłudze Azure AD.
4. Podczas automatycznego wypełniania atrybutu ProxyAddresses w Active Directory obiekt ma zbyt wiele przypisanych ProxyAddresses.

### <a name="how-to-fix"></a>Jak rozwiązać problem
1. Upewnij się, że atrybut powodujący błąd znajduje się w dozwolonym ograniczeniu.

## <a name="existing-admin-role-conflict"></a>Konflikt istniejącej roli administratora

### <a name="description"></a>Opis
Wystąpił **konflikt roli administratora** podczas synchronizacji, gdy ten obiekt użytkownika będzie:

- uprawnienia administracyjne i
- ten sam element UserPrincipalName jako istniejący obiekt usługi Azure AD

Azure AD Connect nie może być niedozwolony dla obiektu użytkownika z lokalnej usługi AD przy użyciu obiektu użytkownika w usłudze Azure AD z przypisaną rolą administracyjną.  Aby uzyskać więcej informacji, zobacz [populacja userPrincipalName usługi Azure AD](plan-connect-userprincipalname.md)

![Istniejący administrator](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Jak rozwiązać problem
Aby rozwiązać ten problem, należy wykonać następujące czynności:

1. Usuń konto usługi Azure AD (właściciela) ze wszystkich ról administratora. 
2. **Usuń trwale** obiekt z kwarantanny w chmurze. 
3. Następny cykl synchronizacji zajmie się niezależnym przełączaniem użytkownika lokalnego do konta w chmurze (ponieważ użytkownik chmury nie jest już globalnie dostępny). 
4. Przywróć członkostwo ról dla właściciela. 

>[!NOTE]
>Rolę administracyjną można przypisać do istniejącego obiektu użytkownika ponownie po zakończeniu niepotrzebnego dopasowania między obiektem lokalnym użytkownika a obiektem użytkownika usługi Azure AD.

## <a name="related-links"></a>Linki pokrewne
* [Lokalizowanie Active Directory obiektów w Centrum administracyjne usługi Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Jak zbadać Azure Active Directory dla obiektu za pomocą programu Azure Active Directory PowerShell](/previous-versions/azure/jj151815(v=azure.100))