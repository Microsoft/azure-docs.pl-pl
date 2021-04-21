---
title: Rozwiązywanie problemów z certyfikatami domeny i protokołu TLS/SSL
description: Znajdź rozwiązania typowych problemów, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu TLS/SSL w Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: c2c09e1a30c9cef4d65b2d5443481c84ab779af8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833836"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Rozwiązywanie problemów z domeną i certyfikatem TLS/SSL w Azure App Service

W tym artykule wymieniono typowe problemy, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu TLS/SSL dla aplikacji internetowych w Azure App Service. Opisano w nim również możliwe przyczyny i rozwiązania tych problemów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie w tym artykule, możesz skontaktować się z ekspertami platformy Azure w witrynie [MSDN Stack Overflow forach.](https://azure.microsoft.com/support/forums/) Alternatywnie możesz złożyć zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomoc techniczna platformy Azure i](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj **pomoc techniczną.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemy z certyfikatami

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Nie można dodać powiązania certyfikatu protokołu TLS/SSL do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu powiązania TLS zostanie wyświetlony następujący komunikat o błędzie:

"Nie można dodać powiązania SSL. Nie można ustawić certyfikatu dla istniejącego adresu VIP, ponieważ inny adres VIP już używa tego certyfikatu".

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązań SSL opartych na adresach IP dla tego samego adresu IP w wielu aplikacjach. Na przykład aplikacja A ma oparty na adresie IP protokół SSL ze starym certyfikatem. Aplikacja B ma oparty na adresie IP protokół SSL z nowym certyfikatem dla tego samego adresu IP. Zaktualizowanie powiązania protokołu TLS aplikacji przy użyciu nowego certyfikatu kończy się niepowodzeniem z powodu tego błędu, ponieważ ten sam adres IP jest używany dla innej aplikacji. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, użyj jednej z następujących metod:

- Usuń powiązanie SSL oparte na adresie IP w aplikacji, która używa starego certyfikatu. 
- Utwórz nowe powiązanie SSL oparte na adresie IP, które używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas próby usunięcia certyfikatu jest wyświetlany następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on obecnie używany w powiązaniu protokołu TLS/SSL. Zanim będzie można usunąć certyfikat, należy usunąć powiązanie TLS."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli inna aplikacja używa certyfikatu.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie TLS dla tego certyfikatu z aplikacji. Następnie spróbuj usunąć certyfikat. Jeśli nadal nie możesz usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowej i otwórz ponownie Azure Portal w nowym oknie przeglądarki. Następnie spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikatu App Service klienta 

#### <a name="symptom"></a>Objaw
Nie można kupić certyfikatu [Azure App Service z](./configure-ssl-certificate.md#import-an-app-service-certificate) Azure Portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z dowolnej z następujących przyczyn:

- Plan App Service jest bezpłatny lub udostępniony. Te warstwy cenowe nie obsługują obsługi TLS. 

    **Rozwiązanie:** Uaktualnij plan App Service aplikacji do wersji Standardowa.

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie:** Dodaj prawidłową kartę kredytową do subskrypcji. 

- Oferta subskrypcji nie obsługuje zakupu certyfikatu usługi App Service takiego jak Microsoft Student.  

    **Rozwiązanie:** Uaktualnij subskrypcję. 

- Subskrypcja osiągnęła limit zakupów, które są dozwolone w ramach subskrypcji.

    **Rozwiązanie:** App Service certyfikatów mają limit 10 zakupów certyfikatów dla typów subskrypcji z płatnością zgodnie z ty i umowy EA. W przypadku innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z pomocą [techniczną platformy Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Certyfikat App Service został oznaczony jako oszustwo. Został wyświetlony następujący komunikat o błędzie: "Twój certyfikat został oflagowany w związku z możliwym oszustwem. Żądanie jest obecnie w trakcie przeglądu. Jeśli certyfikat nie stanie się użyteczny w ciągu 24 godzin, skontaktuj się z pomoc techniczna platformy Azure".

    **Rozwiązanie:** Jeśli certyfikat jest oznaczony jako oszustwo i nie zostanie rozwiązany po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Przejdź do **App Service certyfikatów** i wybierz certyfikat.
    3. Wybierz **pozycję Konfiguracja certyfikatu** Krok  >  **2. Weryfikacja**  >  **weryfikacji domeny.** Ten krok powoduje wysłanie powiadomienia e-mail do dostawcy certyfikatów platformy Azure w celu rozwiązania problemu.

## <a name="custom-domain-problems"></a>Problemy z domeną niestandardową

### <a name="a-custom-domain-returns-a-404-error"></a>Domena niestandardowa zwraca błąd 404 

#### <a name="symptom"></a>Objaw

Podczas przeglądania witryny przy użyciu niestandardowej nazwy domeny pojawia się następujący komunikat o błędzie:

"Błąd 404 — Nie znaleziono aplikacji internetowej".

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyna 1** 

Skonfigurowana domena niestandardowa nie ma rekordu CNAME lub A. 

**Rozwiązanie dla przyczyny 1**

- Jeśli dodano rekord A, upewnij się, że dodano również rekord TXT. Aby uzyskać więcej informacji, [zobacz Create the A record (Tworzenie rekordu A).](./app-service-web-tutorial-custom-domain.md#create-the-a-record)
- Jeśli nie musisz używać domeny głównej dla swojej aplikacji, zalecamy użycie rekordu CNAME zamiast rekordu A.
- Nie używaj zarówno rekordu CNAME, jak i rekordu A dla tej samej domeny. Ten problem może spowodować konflikt i uniemożliwić rozwiązanie domeny. 

**Przyczyna 2** 

Przeglądarka internetowa może nadal buforowanie starego adresu IP dla domeny. 

**Rozwiązanie dotyczące przyczyny 2**

Wyczyść przeglądarkę. W przypadku urządzeń z systemem Windows możesz uruchomić polecenie `ipconfig /flushdns` . Użyj [WhatsmyDNS.net,](https://www.whatsmydns.net/) aby sprawdzić, czy domena wskazuje adres IP aplikacji.

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie można dodać nowej nazwy hosta do aplikacji w celu przypisania poddomeny.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwy hosta do aplikacji.
- Jeśli potrzebujesz większej liczby domen podrzędnych, zalecamy zmianę hostingu domeny na usługę Azure Domain Name Service (DNS). Za pomocą Azure DNS można dodać do aplikacji 500 nazw hostów. Aby uzyskać więcej informacji, zobacz [Dodawanie poddomeny](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>Nie można rozpoznać systemu DNS

#### <a name="symptom"></a>Objaw

Został wyświetlony następujący komunikat o błędzie:

"Nie można zlokalizować rekordu DNS".

#### <a name="cause"></a>Przyczyna
Ten problem występuje z jednej z następujących przyczyn:

- Okres wygaśnięcia (TTL) nie upłynął. Sprawdź konfigurację DNS domeny, aby określić wartość czasu wygaśnięcia, a następnie poczekaj na wygaśnięcie okresu.
- Konfiguracja dns jest niepoprawna.

#### <a name="solution"></a>Rozwiązanie
- Poczekaj 48 godzin na rozwiązanie tego problemu.
- Jeśli możesz zmienić ustawienie czasu wygaśnięcia w konfiguracji DNS, zmień wartość na 5 minut, aby sprawdzić, czy to rozwiąże problem.
- Użyj [WhatsmyDNS.net,](https://www.whatsmydns.net/) aby sprawdzić, czy domena wskazuje adres IP aplikacji. Jeśli tak nie jest, skonfiguruj rekord A na poprawny adres IP aplikacji.

### <a name="you-need-to-restore-a-deleted-domain"></a>Musisz przywrócić usuniętą domenę 

#### <a name="symptom"></a>Objaw
Domena nie jest już widoczna w Azure Portal.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji mógł przypadkowo usunąć domenę.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż siedem dni temu, proces usuwania domeny nie został jeszcze rozpoczęty. W takim przypadku możesz ponownie kupić tę samą domenę na Azure Portal w ramach tej samej subskrypcji. (Pamiętaj, aby wpisać dokładną nazwę domeny w polu wyszukiwania). Nie zostaną naliczone ponownie opłaty za tę domenę. Jeśli domena została usunięta ponad siedem dni temu, skontaktuj się z pomocą techniczną platformy [Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby uzyskać pomoc w przywracaniu domeny.

## <a name="domain-problems"></a>Problemy z domeną

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Zakupiono certyfikat TLS/SSL dla niewłaściwej domeny

#### <a name="symptom"></a>Objaw

Zakupiono certyfikat App Service dla niewłaściwej domeny. Nie można zaktualizować certyfikatu, aby używać poprawnej domeny.

#### <a name="solution"></a>Rozwiązanie

Usuń ten certyfikat, a następnie kup nowy certyfikat.

Jeśli bieżący certyfikat, który używa niewłaściwej domeny, jest w stanie "Wystawiony", będą również naliczane faktury za ten certyfikat. App Service certyfikaty nie są zwracane, ale możesz skontaktować się z pomocą techniczną platformy [Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby sprawdzić, czy istnieją inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Certyfikat App Service został odnowiony, ale aplikacja wyświetla stary certyfikat 

#### <a name="symptom"></a>Objaw

Certyfikat App Service został odnowiony, ale aplikacja korzystająca z certyfikatu App Service nadal używa starego certyfikatu. Ponadto otrzymano ostrzeżenie, że wymagany jest protokół HTTPS.

#### <a name="cause"></a>Przyczyna 
App Service automatycznie synchronizuje certyfikat w ciągu 48 godzin. Podczas rotacji lub aktualizowania certyfikatu czasami aplikacja nadal pobieranie starego certyfikatu, a nie nowo zaktualizowanego certyfikatu. Przyczyną jest to, że zadanie synchronizacji zasobu certyfikatu nie zostało jeszcze uruchomione. Kliknij pozycję Synchronizuj. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania żadnych przestojów w działaniu aplikacji.

#### <a name="solution"></a>Rozwiązanie

Możesz wymusić synchronizację certyfikatu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **App Service certyfikaty,** a następnie wybierz certyfikat.
2. Wybierz **pozycję Wyklucz ponownie i zsynchronizuj,** a następnie wybierz pozycję **Synchronizuj.** Synchronizacja zajmuje trochę czasu. 
3. Po zakończeniu synchronizacji zostanie wyświetlony następujący komunikat: "Pomyślnie zaktualizowano wszystkie zasoby przy użyciu najnowszego certyfikatu".

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa 

#### <a name="symptom"></a>Objaw 
Certyfikat App Service wymaga weryfikacji domeny, zanim certyfikat będzie gotowy do użycia. Wybranie opcji **Weryfikuj** kończy się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie
Ręcznie zweryfikuj domenę, dodając rekord TXT:

1. Przejdź do dostawcy systemu Domain Name Service (DNS), który hostuje nazwę domeny.
1. Dodaj rekord TXT dla domeny korzystającej z wartości tokenu domeny pokazanego w witrynie Azure Portal. 

Poczekaj kilka minut na uruchomienie propagacji dns, a następnie wybierz przycisk **Odśwież,** aby wyzwolić weryfikację. 

Alternatywnie możesz użyć metody strony internetowej HTML, aby ręcznie zweryfikować domenę. Ta metoda umożliwia urządowi certyfikacji potwierdzenie własności domeny, dla których wystawiono certyfikat.

1. Utwórz plik HTML o nazwie {token weryfikacji domeny}.html. Zawartość tego pliku powinna być wartością tokenu weryfikacji domeny.
1. Przekaż ten plik w katalogu głównym serwera internetowego, który hostuje Twoją domenę.
1. Wybierz **pozycję Odśwież,** aby sprawdzić stan certyfikatu. Weryfikacja może potrwać kilka minut.

Jeśli na przykład kupujesz standardowy certyfikat dla usługi azure.com z tokenem weryfikacji domeny 1234abcd, żądanie internetowe, które ma zwrócić kod https://azure.com/1234abcd.html 1234abcd. 

> [!IMPORTANT]
> Zamówienie certyfikatu ma tylko 15 dni na ukończenie operacji weryfikacji domeny. Po upływie 15 dni urząd certyfikacji odmówi certyfikatu i nie zostaną naliczone opłaty za certyfikat. W takiej sytuacji usuń ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domeny

#### <a name="symptom"></a>Objaw
Nie można kupić domeny App Service domenie w Azure Portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje z jednej z następujących przyczyn:

- W subskrypcji platformy Azure nie ma karty kredytowej lub karta kredytowa jest nieprawidłowa.

    **Rozwiązanie:** Dodaj prawidłową kartę kredytową do subskrypcji.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień do zakupu domeny.

    **Rozwiązanie:** [Przypisz rolę właściciela](../role-based-access-control/role-assignments-portal.md) do swojego konta. Możesz też skontaktować się z administratorem subskrypcji, aby uzyskać uprawnienia do zakupu domeny.
- Osiągnięto limit zakupów domen w ramach subskrypcji. Bieżący limit wynosi 20.

    **Rozwiązanie:** Aby zażądać zwiększenia limitu, skontaktuj się z pomocą [techniczną platformy Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Typ subskrypcji platformy Azure nie obsługuje zakupu domeny usługi App Service.

    **Rozwiązanie:** Uaktualnij subskrypcję platformy Azure do innego typu subskrypcji, na przykład subskrypcji z płatnością zgodnie z potrzebami.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nie można dodać nazwy hosta do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta proces nie może zweryfikować i zweryfikować domeny.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje z jednej z następujących przyczyn:

- Nie masz uprawnień do dodawania nazwy hosta.

    **Rozwiązanie:** Poproś administratora subskrypcji o nadanie Uprawnień do dodawania nazwy hosta.
- Nie można zweryfikować własności domeny.

    **Rozwiązanie:** Sprawdź, czy rekord CNAME lub A jest poprawnie skonfigurowany. Aby zamapować domenę niestandardową na aplikację, utwórz rekord CNAME lub rekord A. Jeśli chcesz użyć domeny głównej, musisz użyć rekordów A i TXT:

    |Typ rekordu|Host|Wskaż|
    |------|------|-----|
    |A|@|Adres IP aplikacji|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Często zadawane pytania

**Czy muszę skonfigurować domenę niestandardową dla mojej witryny internetowej po zakupie?**

W przypadku zakupu domeny w Azure Portal aplikacja App Service zostanie automatycznie skonfigurowana do używania tej domeny niestandardowej. Nie musisz wykonać żadnych dodatkowych czynności. Aby uzyskać więcej informacji, [obejrzyj Azure App Service Self Help: Add a Custom Domain Name (Samodzielna pomoc:](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) dodawanie nazwy Custom Domain na platformie Channel9).

**Czy mogę użyć domeny zakupionej w usłudze Azure Portal, aby zamiast tego wskazać maszynę wirtualną platformy Azure?**

Tak, możesz wskazać domenę na maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

**Czy moja domena jest hostowana przez firmę GoDaddy, czy Azure DNS?**

App Service domeny używają usługi GoDaddy do rejestracji domeny Azure DNS do hostowania domen. 

**Mam włączone automatyczne odnawianie, ale nadal otrzymano powiadomienie o odnowieniu mojej domeny za pośrednictwem poczty e-mail. Co mam zrobić?**

Jeśli automatyczne odnawianie jest włączone, nie trzeba nic robić. Wiadomość e-mail z powiadomieniem informuje o tym, że domena jest bliski wygaśnięcia, i o ręcznym odnowieniu, jeśli automatyczne odnawianie nie jest włączone.

**Czy zostanie naliczona opłata za Azure DNS hostingu mojej domeny?**

Początkowy koszt zakupu domeny dotyczy tylko rejestracji domeny. Oprócz kosztu rejestracji opłaty za rejestrację są naliczane za Azure DNS na podstawie użycia. Aby uzyskać więcej informacji, [zobacz Azure DNS cennika,](https://azure.microsoft.com/pricing/details/dns/) aby uzyskać więcej informacji.

**Moja domena została zakupiona wcześniej od Azure Portal i chcę przejść z hostingu GoDaddy do Azure DNS hostingu. Jak to zrobić?**

Migracja do hostowania Azure DNS nie jest obowiązkowa. Jeśli chcesz przeprowadzić migrację do usługi Azure DNS, środowisko zarządzania domenami w Azure Portal zawiera informacje na temat kroków niezbędnych do przeniesienia do Azure DNS. Jeśli domena została zakupiona za pośrednictwem App Service, migracja z hostingu GoDaddy Azure DNS jest stosunkowo bezproblemowa.

**Chcę kupić domenę w domenie App Service, ale czy mogę hostować moją domenę na goDaddy zamiast Azure DNS?**

Od 24 lipca 2017 r. domeny App Service zakupione w portalu są hostowane w Azure DNS. Jeśli wolisz używać innego dostawcy hostingu, musisz przejść do jego witryny internetowej, aby uzyskać rozwiązanie do hostingu domeny.

**Czy muszę płacić za ochronę prywatności w mojej domenie?**

W przypadku zakupu domeny za pośrednictwem Azure Portal można dodać prywatność bez dodatkowych kosztów. Jest to jedna z zalet zakupu domeny za pośrednictwem Azure App Service.

**Jeśli zdecydujem, że nie chcę już korzystać z mojej domeny, czy mogę odzyskać pieniądze?**

W przypadku zakupu domeny nie są naliczane opłaty za okres pięciu dni, w którym można zdecydować, że domena nie ma być naliczana. Jeśli zdecydujesz, że nie chcesz, aby domena nie była w ciągu tego pięciodniowego okresu, nie są naliczane opłaty. (Wyjątkiem są domeny .uk. W przypadku zakupu domeny .uk opłata jest naliczana natychmiast i nie można uzyskać zwrotu kosztów).

**Czy mogę używać domeny w innej Azure App Service aplikacji w mojej subskrypcji?**

Tak. Gdy uzyskujesz dostęp do bloku Domeny niestandardowe i TLS w Azure Portal, zobaczysz zakupione domeny. Możesz skonfigurować aplikację do używania dowolnej z tych domen.

**Czy mogę przenieść domenę z jednej subskrypcji do innej?**

Domenę można przenieść do innej subskrypcji/grupy zasobów za pomocą polecenia cmdlet [Move-AzResource](/powershell/module/az.Resources/Move-azResource) programu PowerShell.

**Jak mogę zarządzać domeną niestandardową, jeśli nie mam jeszcze Azure App Service aplikacji?**

Możesz zarządzać domeną, nawet jeśli nie masz App Service Web App. Domena może być używana dla usług platformy Azure, takich jak Maszyna wirtualna, Magazyn itp. Jeśli zamierzasz używać domeny dla usługi App Service Web Apps, musisz dołączyć aplikację internetową, która nie znajduje się w planie usługi Free App Service, aby powiązać domenę z aplikacją internetową.

**Czy mogę przenieść aplikację internetową z domeną niestandardową do innej subskrypcji lub z wersji App Service Environment 1 do wersji 2?**

Tak, możesz przenieść aplikację internetową między subskrypcjami. Postępuj zgodnie ze wskazówkami [z tematu Jak przenieść zasoby na platformie Azure.](../azure-resource-manager/management/move-resource-group-and-subscription.md) Przenoszenie aplikacji internetowej ma kilka ograniczeń. Aby uzyskać więcej informacji, zobacz [Ograniczenia dotyczące przenoszenia App Service zasobów.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Po przeniesieniu aplikacji internetowej powiązania nazw hostów domen w ramach ustawienia domen niestandardowych powinny pozostać takie same. Do skonfigurowania powiązań nazw hostów nie są wymagane żadne dodatkowe kroki.
