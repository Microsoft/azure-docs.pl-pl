---
title: 'Azure AD Connect: koncepcje projektowania | Microsoft Docs'
description: W tym temacie szczegółowo opisano niektóre obszary projektowania implementacji
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279656"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: koncepcje projektowania
Celem tego dokumentu jest opisywanie obszarów, które muszą być rozważane podczas projektowania implementacji Azure AD Connect. Ten dokument to głębokie szczegółowe w niektórych obszarach. te pojęcia są również krótko opisane w innych dokumentach.

## <a name="sourceanchor"></a>sourceAnchor
Atrybut sourceAnchor jest definiowany jako *atrybut niezmienny w okresie istnienia obiektu*. Jednoznacznie identyfikuje obiekt jako ten sam obiekt w środowisku lokalnym i w usłudze Azure AD. Ten atrybut jest również nazywany **immutableId** , a dwie nazwy są używane zamiennie.

Wyraz niezmienny, czyli nie można zmienić, jest istotny dla tego dokumentu. Ponieważ wartości tego atrybutu nie można zmienić po jego ustawieniu, ważne jest, aby wybrać projekt, który obsługuje twój scenariusz.

Ten atrybut jest używany w następujących scenariuszach:

* Gdy nowy serwer aparatu synchronizacji jest skompilowany lub odbudowany po scenariuszu odzyskiwania po awarii, ten atrybut łączy istniejące obiekty w usłudze Azure AD z obiektami lokalnymi.
* Jeśli przejdziesz z tożsamości tylko do chmury do zsynchronizowanego modelu tożsamości, ten atrybut umożliwia obiektom "twardą zgodność" istniejących obiektów w usłudze Azure AD z obiektami lokalnymi.
* W przypadku korzystania z Federacji ten atrybut wraz z elementem **userPrincipalName** jest używany w ramach roszczeń do unikatowego identyfikowania użytkownika.

Ten temat dotyczy tylko sourceAnchor w odniesieniu do użytkowników. Te same reguły mają zastosowanie do wszystkich typów obiektów, ale tylko dla użytkowników ten problem jest zwykle istotny.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Wybieranie dobrego atrybutu sourceAnchor
Wartość atrybutu musi być zgodna z następującymi regułami:

* Mniej niż 60 znaków
  * Znaki, które nie są a-z, A-Z, lub 0-9 są kodowane i zliczane jako 3 znaki
* Nie zawiera znaku specjalnego: &#92;! # $% & * +/=? ^ &#96; {} | ~ < >  () '; : , [ ] " \@ _
* Musi być globalnie unikatowa
* Musi to być ciąg, liczba całkowita lub wartość binarna
* Nie powinna być oparta na nazwie użytkownika, ponieważ mogą one ulec zmianie
* Nie należy uwzględniać wielkości liter i uniknąć wartości, które mogą się różnić w zależności od wielkości liter
* Powinien być przypisywany po utworzeniu obiektu

Jeśli wybrany sourceAnchor nie jest typu String, Azure AD Connect Base64Encode wartość atrybutu, aby upewnić się, że nie są wyświetlane żadne znaki specjalne. Jeśli używasz innego serwera federacyjnego niż usług AD FS, upewnij się, że serwer może również Base64Encode atrybut.

W atrybucie sourceAnchor jest rozróżniana wielkość liter. Wartość "jankowalski" nie jest taka sama jak "jankowalski". Ale nie należy mieć dwóch różnych obiektów z tylko różnicą w przypadku.

Jeśli masz pojedynczy las w środowisku lokalnym, wówczas atrybut, którego należy użyć, to **objectGUID**. Jest to również atrybut używany podczas używania ustawień ekspresowych w Azure AD Connect, a także atrybut używany przez narzędzie DirSync.

Jeśli masz wiele lasów i nie przenosisz użytkowników między lasami i domenami, a następnie **objectGUID** jest dobrym atrybutem do użycia nawet w tym przypadku.

Jeśli przenosisz użytkowników między lasami i domenami, musisz znaleźć atrybut, który nie zmienia się ani nie może zostać przeniesiony do użytkowników podczas przenoszenia. Zalecanym podejściem jest wprowadzenie atrybutu syntetycznego. Atrybut, który może zawierać coś, który wygląda jak identyfikator GUID, będzie odpowiedni. Podczas tworzenia obiektu tworzony jest nowy identyfikator GUID i jest on stemplowany na użytkowniku. Niestandardowa reguła synchronizacji można utworzyć na serwerze aparatu synchronizacji, aby utworzyć tę wartość na podstawie właściwości **objectGUID** i zaktualizować wybrany atrybut w obszarze Dodawanie. Podczas przenoszenia obiektu upewnij się, że kopiujesz również zawartość tej wartości.

Innym rozwiązaniem jest wybranie istniejącego atrybutu, który nie zmienia się. Najczęściej używane atrybuty obejmują **IDPracownika**. Jeśli rozważasz atrybut, który zawiera litery, upewnij się, że przypadek (wielkie litery a małe litery) może zmienić wartość atrybutu. Złe atrybuty, które nie powinny być używane, zawierają te atrybuty z nazwą użytkownika. W przypadku małżeństwa lub rozwodu nazwa powinna ulec zmianie, co nie jest dozwolone dla tego atrybutu. Jest to również powód, dlaczego atrybuty, takie jak **userPrincipalName**, **mail**i **targetAddress** , nie są jeszcze dostępne do wyboru w Kreatorze instalacji Azure AD Connect. Te atrybuty również zawierają znak " \@ ", który jest niedozwolony w sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Zmiana atrybutu sourceAnchor
Nie można zmienić wartości atrybutu sourceAnchor po utworzeniu obiektu w usłudze Azure AD, a tożsamość jest zsynchronizowana.

Z tego powodu następujące ograniczenia mają zastosowanie do Azure AD Connect:

* Atrybut sourceAnchor można ustawić tylko podczas początkowej instalacji. Jeśli ponownie uruchomisz kreatora instalacji, ta opcja jest tylko do odczytu. Jeśli trzeba zmienić to ustawienie, należy odinstalować i ponownie zainstalować program.
* Jeśli zostanie zainstalowany inny serwer Azure AD Connect, należy wybrać ten sam atrybut sourceAnchor, jak poprzednio używany. Jeśli wcześniej korzystano z narzędzia DirSync i przejdziesz do Azure AD Connect, należy użyć **objectGUID** , ponieważ jest atrybutem używanym przez narzędzie DirSync.
* Jeśli wartość sourceAnchor jest zmieniana po wyeksportowaniu obiektu do usługi Azure AD, Azure AD Connect Synchronize zgłasza błąd i nie będzie zezwalać na więcej zmian w tym obiekcie przed naprawieniem problemu i sourceAnchor zostanie zmieniony z powrotem w katalogu źródłowym.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Korzystanie z MS-DS-ConsistencyGuid jako sourceAnchor
Domyślnie Azure AD Connect (wersja 1.1.486.0 i starsze) używa objectGUID jako atrybutu sourceAnchor. ObjectGUID jest generowany przez system. Nie można określić jej wartości podczas tworzenia lokalnych obiektów usługi AD. Jak wyjaśniono w sekcji [sourceAnchor](#sourceanchor), istnieją scenariusze, w których należy określić wartość sourceAnchor. Jeśli masz odpowiednie scenariusze, musisz użyć konfigurowalnego atrybutu AD (na przykład MS-DS-ConsistencyGuid) jako atrybutu sourceAnchor.

Azure AD Connect (wersja 1.1.524.0 i późniejsza) ułatwia teraz korzystanie z usługi MS-DS-ConsistencyGuid jako atrybutu sourceAnchor. W przypadku korzystania z tej funkcji Azure AD Connect automatycznie konfiguruje reguły synchronizacji w następujący sposób:

1. Użyj MS-DS-ConsistencyGuid jako atrybutu sourceAnchor dla obiektów użytkownika. ObjectGUID jest używany dla innych typów obiektów.

2. Dla każdego z danych lokalnego obiektu użytkownika usługi AD, którego atrybut MS-DS-ConsistencyGuid nie jest wypełniony, Azure AD Connect zapisuje jego wartość objectGUID z powrotem do atrybutu MS-DS-ConsistencyGuid w lokalnym Active Directory. Po wyAzure AD Connect pełnieniu atrybutu MS-DS-ConsistencyGuid wyeksportuj obiekt do usługi Azure AD.

>[!NOTE]
> Gdy lokalny obiekt usługi AD zostanie zaimportowany do Azure AD Connect (to znaczy zaimportowany do obszaru łącznika usługi AD i jest rzutowany na obiekt Metaverse), nie można już zmienić jego wartości sourceAnchor. Aby określić wartość sourceAnchor dla danego lokalnego obiektu usługi AD, należy skonfigurować jego atrybut MS-DS-ConsistencyGuid przed jego zaimportowaniem do Azure AD Connect.

### <a name="permission-required"></a>Wymagane uprawnienie
Aby ta funkcja działała, konto AD DS używane do synchronizacji z lokalnym Active Directory musi mieć przyznane uprawnienie do zapisu dla atrybutu MS-DS-ConsistencyGuid w Active Directory lokalnym.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak włączyć funkcję ConsistencyGuid — Nowa instalacja
W trakcie nowej instalacji można włączyć funkcję ConsistencyGuid jako sourceAnchor. W tej sekcji opisano instalację ekspresowa i niestandardową.

  >[!NOTE]
  > Tylko nowsze wersje Azure AD Connect (1.1.524.0 i After) obsługują użycie ConsistencyGuid jako sourceAnchor podczas nowej instalacji.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak włączyć funkcję ConsistencyGuid

#### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalowania Azure AD Connect z trybem Express Kreator Azure AD Connect automatycznie określa najbardziej odpowiedni atrybut usługi AD, który będzie używany jako atrybut sourceAnchor, przy użyciu następującej logiki:

* Najpierw Kreator Azure AD Connect wysyła zapytanie do dzierżawy usługi Azure AD, aby pobrać atrybut usługi AD używany jako atrybut sourceAnchor w poprzedniej instalacji Azure AD Connect (jeśli istnieje). Jeśli te informacje są dostępne, Azure AD Connect używa tego samego atrybutu usługi AD.

  >[!NOTE]
  > Tylko nowsze wersje Azure AD Connect (1.1.524.0 i After) przechowują informacje w dzierżawie usługi Azure AD o atrybucie sourceAnchor używanym podczas instalacji. Starsze wersje Azure AD Connect nie.

* Jeśli informacje o używanym atrybucie sourceAnchor nie są dostępne, Kreator sprawdza stan atrybutu MS-DS-ConsistencyGuid w lokalnym Active Directory. Jeśli atrybut nie jest skonfigurowany dla żadnego obiektu w katalogu, Kreator użyje właściwości MS-DS-ConsistencyGuid jako atrybutu sourceAnchor. Jeśli atrybut jest skonfigurowany w co najmniej jednym obiekcie w katalogu, Kreator końcowy ten atrybut jest używany przez inne aplikacje i nie jest odpowiedni jako atrybut sourceAnchor...

* W takim przypadku Kreator powraca do użycia objectGUID jako atrybut sourceAnchor.

* Po ustaleniu atrybutu sourceAnchor kreator zapisuje informacje w dzierżawie usługi Azure AD. Informacje będą używane podczas przyszłej instalacji Azure AD Connect.

Po zakończeniu instalacji ekspresowej Kreator informuje, który atrybut został wybrany jako atrybut zakotwiczenia źródła.

![Kreator informuje o pobraniu atrybutu AD dla sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalacja niestandardowa
Podczas instalowania Azure AD Connect z trybem niestandardowym Kreator Azure AD Connect udostępnia dwie opcje podczas konfigurowania atrybutu sourceAnchor:

![Instalacja niestandardowa — konfiguracja sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. W przypadku wybrania tej opcji Kreator Azure AD Connect ma zastosowanie tej samej [logiki wyboru atrybutów sourceAnchor używanej podczas instalacji ekspresowej](#express-installation). Podobnie jak w przypadku instalacji ekspresowej, Kreator informuje, który atrybut został pobrany jako atrybut zakotwiczenia źródła po zakończeniu instalacji niestandardowej. |
| Określony atrybut | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut usługi AD jako atrybut sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak włączyć funkcję ConsistencyGuid — istniejące wdrożenie
Jeśli masz istniejące wdrożenie Azure AD Connect, które korzysta z objectGUID jako atrybutu kotwicy źródłowej, możesz zamiast tego przełączyć go do użycia ConsistencyGuid.

>[!NOTE]
> Tylko nowsze wersje Azure AD Connect (1.1.552.0 i After) obsługują przełączanie z elementu ObjectGuid do ConsistencyGuid jako atrybut kotwicy źródłowej.

Aby zmienić parametr objectGUID na ConsistencyGuid jako źródłowy atrybut zakotwiczenia:

1. Uruchom Kreatora Azure AD Connect i kliknij przycisk **Konfiguruj** , aby przejść do ekranu zadania.

2. Wybierz opcję **Konfiguruj źródłową kotwicę** zadania i kliknij przycisk **dalej**.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Wprowadź swoje poświadczenia administratora usługi Azure AD, a następnie kliknij przycisk **dalej**.

4. Kreator Azure AD Connect analizuje stan atrybutu MS-DS-ConsistencyGuid w Active Directory lokalnym. Jeśli atrybut nie jest skonfigurowany na żadnym obiekcie w katalogu, Azure AD Connect stwierdza, że żadna inna aplikacja nie korzysta obecnie z atrybutu i jest bezpieczna do użycia jako atrybut zakotwiczenia źródła. Kliknij przycisk **Dalej** , aby kontynuować.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na ekranie **gotowy do skonfigurowania** kliknij pozycję **Konfiguruj** , aby wprowadzić zmiany w konfiguracji.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po zakończeniu konfiguracji Kreator wskazuje, że usługa MS-DS-ConsistencyGuid jest teraz używana jako atrybut zakotwiczenia źródła.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Podczas analizy (krok 4), jeśli atrybut jest skonfigurowany w co najmniej jednym obiekcie w katalogu, Kreator końcowy ten atrybut jest używany przez inną aplikację i zwraca błąd, jak pokazano na poniższym diagramie. Ten błąd może również wystąpić, jeśli wcześniej włączono funkcję ConsistencyGuid na serwerze podstawowym Azure AD Connect i podjęto próbę wykonania tego samego na serwerze tymczasowym.

![Włącz ConsistencyGuid dla istniejącego wdrożenia — błąd](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Jeśli masz pewność, że atrybut nie jest używany przez inne istniejące aplikacje, możesz pominąć ten błąd przez ponowne uruchomienie Kreatora Azure AD Connect z określonym przełącznikiem **/SkipLdapSearch** . Aby to zrobić, uruchom następujące polecenie w wierszu polecenia:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Wpływ na konfigurację Federacji AD FS lub innej firmy
Jeśli używasz Azure AD Connect do zarządzania wdrożeniem lokalnym AD FS, Azure AD Connect automatycznie aktualizuje reguły dotyczące roszczeń, aby używały tego samego atrybutu AD co sourceAnchor. Daje to pewność, że ImmutableID wygenerowane przez usługi AD FS będzie spójna z wartościami sourceAnchor wyeksportowanymi w usłudze Azure AD.

Jeśli zarządzasz AD FS poza Azure AD Connect lub używasz serwerów federacyjnych innych firm do uwierzytelniania, musisz ręcznie zaktualizować reguły dotyczące roszczeń ImmutableID, aby były zgodne z wartościami sourceAnchor wyeksportowanymi do usługi Azure AD, zgodnie z opisem w sekcji artykułu [Modyfikowanie reguł roszczeń AD FS](./how-to-connect-fed-management.md#modclaims). Po zakończeniu instalacji Kreator zwróci następujące ostrzeżenie:

![Konfiguracja Federacji innej firmy](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Dodawanie nowych katalogów do istniejącego wdrożenia
Załóżmy, że wdrożono Azure AD Connect z włączoną funkcją ConsistencyGuid, a teraz chcesz dodać inny katalog do wdrożenia. Podczas próby dodania katalogu Kreator Azure AD Connect sprawdza stan atrybutu MS-DS-ConsistencyGuid w katalogu. Jeśli atrybut jest skonfigurowany w co najmniej jednym obiekcie w katalogu, Kreator końcowy ten atrybut jest używany przez inne aplikacje i zwraca błąd, jak pokazano na poniższym diagramie. Jeśli masz pewność, że atrybut nie jest używany przez istniejące aplikacje, możesz pominąć ten błąd przez ponowne uruchomienie Kreatora Azure AD Connect z przełącznikiem **/SkipLdapSearch** określonym powyżej lub należy skontaktować się z pomocą techniczną, aby uzyskać więcej informacji.

![Dodawanie nowych katalogów do istniejącego wdrożenia](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Logowanie za pomocą usługi Azure AD
Podczas integrowania katalogu lokalnego z usługą Azure AD ważne jest, aby zrozumieć, w jaki sposób ustawienia synchronizacji mogą mieć wpływ na sposób uwierzytelniania użytkownika. Usługa Azure AD używa do uwierzytelniania użytkownika userPrincipalName (UPN). Jednak podczas synchronizowania użytkowników należy wybrać atrybut, który będzie używany w przypadku wartości userPrincipalName uważnie.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Wybieranie atrybutu dla elementu userPrincipalName
Podczas wybierania atrybutu w celu podania wartości nazwy UPN używanej na platformie Azure należy zapewnić

* Wartości atrybutów są zgodne z składnią UPN (RFC 822), która powinna mieć format nazwa_użytkownika \@ domena
* Sufiks w wartości jest zgodny z jedną z zweryfikowanych domen niestandardowych w usłudze Azure AD

W ustawieniach ekspresowych założono wybór dla atrybutu jest userPrincipalName. Jeśli atrybut userPrincipalName nie zawiera wartości, na które użytkownicy mają logować się na platformie Azure, należy wybrać opcję **Instalacja niestandardowa**.

### <a name="custom-domain-state-and-upn"></a>Niestandardowy stan domeny i nazwa UPN
Ważne jest, aby upewnić się, że istnieje zweryfikowana domena dla sufiksu nazwy UPN.

Jan jest użytkownikiem w contoso.com. Chcesz, aby Jan używał lokalnej nazwy UPN Jan \@ contoso.com do logowania się do platformy Azure po zsynchronizowaniu użytkowników z katalogiem usługi Azure AD contoso.onmicrosoft.com. Aby to zrobić, należy dodać i zweryfikować contoso.com jako domenę niestandardową w usłudze Azure AD przed rozpoczęciem synchronizowania użytkowników. Jeśli sufiks nazwy UPN Jan, na przykład contoso.com, nie pasuje do zweryfikowanej domeny w usłudze Azure AD, usługa Azure AD zamieni sufiks nazwy UPN na contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domeny lokalne bez routingu i nazwa UPN dla usługi Azure AD
Niektóre organizacje mają domeny bez obsługi routingu, takie jak contoso. Local, lub proste domeny z pojedynczą etykietą, takie jak contoso. Nie można zweryfikować domeny bez obsługi routingu w usłudze Azure AD. Azure AD Connect można synchronizować tylko z zweryfikowaną domeną w usłudze Azure AD. Podczas tworzenia katalogu usługi Azure AD tworzy domenę routingu, która jest domeną domyślną dla usługi Azure AD na przykład contoso.onmicrosoft.com. W związku z tym, konieczna jest weryfikacja wszelkich innych domen routingu w taki scenariusz, jeśli nie chcesz synchronizować się z domyślną domeną onmicrosoft.com.

Aby uzyskać więcej informacji na temat dodawania i weryfikowania domen, przeczytaj artykuł [Dodawanie niestandardowej nazwy domeny do Azure Active Directory](../fundamentals/add-custom-domain.md) .

Azure AD Connect wykrywa, czy program jest uruchomiony w środowisku domeny bez obsługi routingu, a następnie ostrzega użytkownika o przejściu z ustawień ekspresowych. Jeśli pracujesz w domenie bez obsługi routingu, prawdopodobnie nazwy UPN użytkowników mają również sufiksy nieobsługujące routingu. Na przykład jeśli korzystasz z usługi contoso. Local, Azure AD Connect sugeruje użycie ustawień niestandardowych zamiast korzystania z ustawień ekspresowych. Za pomocą ustawień niestandardowych można określić atrybut, który ma być używany jako nazwa UPN do logowania się do platformy Azure po zsynchronizowaniu użytkowników z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).