---
title: Rozwiązywanie problemów z certyfikatami i protokołem TLS/SSL
description: Znajdź rozwiązania typowych problemów, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu TLS/SSL w Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607613"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Rozwiązywanie problemów z certyfikatami domeny i protokołu TLS/SSL w Azure App Service

W tym artykule wymieniono typowe problemy, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu TLS/SSL dla aplikacji sieci Web w programie Azure App Service. Opisano w nim również możliwe przyczyny i rozwiązania tych problemów.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemy z certyfikatami

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Nie można dodać powiązania certyfikatu TLS/SSL do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu powiązania TLS zostanie wyświetlony następujący komunikat o błędzie:

"Nie można dodać powiązania SSL. Nie można ustawić certyfikatu dla istniejącego adresu VIP, ponieważ inny wirtualny adres IP już używa tego certyfikatu ".

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli istnieje wiele powiązań SSL opartych na protokole IP dla tego samego adresu IP w wielu aplikacjach. Na przykład aplikacja A ma protokół SSL oparty na protokole IP ze starym certyfikatem. Aplikacja B ma protokół SSL oparty na protokole IP z nowym certyfikatem dla tego samego adresu IP. Aktualizacja powiązania TLS aplikacji z nowym certyfikatem kończy się niepowodzeniem z powodu tego błędu, ponieważ ten sam adres IP jest używany przez inną aplikację. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy użyć jednej z następujących metod:

- Usuń powiązanie SSL oparte na protokole IP w aplikacji, która używa starego certyfikatu. 
- Utwórz nowe powiązanie protokołu SSL opartego na protokole IP, które używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas próby usunięcia certyfikatu pojawia się następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on obecnie używany w powiązaniu TLS/SSL. Aby można było usunąć certyfikat, należy usunąć powiązanie TLS ".

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli inna aplikacja używa certyfikatu.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie TLS dla tego certyfikatu z aplikacji. Następnie spróbuj usunąć certyfikat. Jeśli nadal nie możesz usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowej i ponownie otwórz Azure Portal w nowym oknie przeglądarki. Następnie spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikatu App Service 

#### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu Azure App Service](./configure-ssl-certificate.md#import-an-app-service-certificate) z Azure Portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z następujących powodów:

- Plan App Service jest bezpłatny lub udostępniony. Te warstwy cenowe nie obsługują protokołu TLS. 

    **Rozwiązanie**: Uaktualnij plan App Service w przypadku aplikacji do warstwy Standardowa.

- Subskrypcja nie ma prawidłowej karty kredytowej.

    **Rozwiązanie**: Dodaj prawidłową kartę kredytową do subskrypcji. 

- Oferta subskrypcji nie obsługuje zakupu certyfikatu App Service, takiego jak Microsoft Student.  

    **Rozwiązanie**: Uaktualnij subskrypcję. 

- Subskrypcja osiągnęła limit zakupów dozwolonych w ramach subskrypcji.

    **Rozwiązanie**: certyfikaty App Service mają limit 10 zakupów certyfikatów dla typów subskrypcji płatność zgodnie z rzeczywistym użyciem i EA. W przypadku innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat App Service został oznaczony jako oszustwo. Został wyświetlony następujący komunikat o błędzie: "certyfikat został oflagowany dla potencjalnego oszustwa. Żądanie jest obecnie objęte przeglądem. Jeśli certyfikat nie będzie można użyć w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure.

    **Rozwiązanie**: Jeśli certyfikat jest oznaczony jako oszustwo i nie jest rozpoznawany po 24 godzinach, wykonaj następujące czynności:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Przejdź do obszaru **App Service Certificates** i wybierz certyfikat.
    3. Wybierz pozycję **Konfiguracja certyfikatu**  >  **krok 2**  >  .**weryfikacja weryfikacji domeny**. Ten krok powoduje wysłanie powiadomienia e-mail do dostawcy certyfikatów platformy Azure w celu rozwiązania problemu.

## <a name="custom-domain-problems"></a>Problemy z domeną niestandardową

### <a name="a-custom-domain-returns-a-404-error"></a>Domena niestandardowa zwraca błąd 404 

#### <a name="symptom"></a>Objaw

Gdy przejdziesz do witryny przy użyciu niestandardowej nazwy domeny, zostanie wyświetlony następujący komunikat o błędzie:

"Błąd 404 — nie znaleziono aplikacji sieci Web".

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyna 1** 

W skonfigurowanej domenie niestandardowej brakuje rekordu CNAME lub A. 

**Rozwiązanie dla przyczyny 1**

- W przypadku dodania rekordu A upewnij się, że został również dodany rekord TXT. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie musisz używać domeny głównej dla swojej aplikacji, zalecamy użycie rekordu CNAME zamiast rekordu A.
- Nie używaj rekordu CNAME i rekordu A dla tej samej domeny. Ten problem może spowodować konflikt i uniemożliwić rozpoznanie domeny. 

**Przyczyna 2** 

Przeglądarka internetowa nadal może buforować stary adres IP dla Twojej domeny. 

**Rozwiązanie dla przyczyny 2**

Wyczyść przeglądarkę. W przypadku urządzeń z systemem Windows można uruchomić polecenie `ipconfig /flushdns` . Użyj [WhatsmyDNS.NET](https://www.whatsmydns.net/) , aby sprawdzić, czy domena wskazuje adres IP aplikacji.

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie możesz dodać nowej nazwy hosta do aplikacji, aby przypisać poddomenę.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwy hosta do aplikacji.
- Jeśli potrzebujesz więcej domen poddomen, zalecamy zmianę hostingu domeny w usłudze Azure Domain Name Service (DNS). Za pomocą Azure DNS można dodać do aplikacji nazwy hostów 500. Aby uzyskać więcej informacji, zobacz [Dodawanie poddomeny](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>Nie można rozpoznać systemu DNS

#### <a name="symptom"></a>Objaw

Został wyświetlony następujący komunikat o błędzie:

"Nie można zlokalizować rekordu DNS".

#### <a name="cause"></a>Przyczyna
Ten problem występuje z jednego z następujących powodów:

- Okres czasu wygaśnięcia (TTL) nie wygasł. Sprawdź konfigurację DNS domeny, aby określić wartość czasu wygaśnięcia, a następnie poczekaj na wygaśnięcie okresu.
- Konfiguracja DNS jest niepoprawna.

#### <a name="solution"></a>Rozwiązanie
- Aby rozwiązać ten problem, poczekaj przez 48 godzin.
- Jeśli można zmienić ustawienie czasu wygaśnięcia w konfiguracji DNS, należy zmienić wartość na 5 minut, aby sprawdzić, czy problem został rozwiązany.
- Użyj [WhatsmyDNS.NET](https://www.whatsmydns.net/) , aby sprawdzić, czy domena wskazuje adres IP aplikacji. Jeśli tak nie jest, skonfiguruj rekord A na prawidłowy adres IP aplikacji.

### <a name="you-need-to-restore-a-deleted-domain"></a>Należy przywrócić usuniętą domenę. 

#### <a name="symptom"></a>Objaw
Twoja domena nie jest już widoczna w Azure Portal.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji może przypadkowo usunąć domenę.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż siedem dni temu, w domenie nie uruchomiono jeszcze procesu usuwania. W takim przypadku można ponownie kupić tę samą domenę w Azure Portal w ramach tej samej subskrypcji. (Pamiętaj, aby w polu wyszukiwania wpisać dokładną nazwę domeny). Nie zostanie naliczona opłata ponownie dla tej domeny. Jeśli domena została usunięta ponad siedem dni temu, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby uzyskać pomoc dotyczącą przywracania domeny.

## <a name="domain-problems"></a>Problemy z domeną

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Zakupiłeś certyfikat TLS/SSL dla niewłaściwej domeny

#### <a name="symptom"></a>Objaw

Zakupiłeś certyfikat App Service dla niewłaściwej domeny. Nie można zaktualizować certyfikatu w celu używania prawidłowej domeny.

#### <a name="solution"></a>Rozwiązanie

Usuń ten certyfikat, a następnie Kup nowy certyfikat.

Jeśli bieżący certyfikat, który używa niewłaściwej domeny, jest w stanie "wystawiony", opłaty będą naliczane również za ten certyfikat. App Service certyfikaty nie podlegają zwrotowi, ale możesz skontaktować się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby sprawdzić, czy są dostępne inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Certyfikat App Service został odnowiony, ale aplikacja pokazuje stary certyfikat 

#### <a name="symptom"></a>Objaw

Certyfikat App Service został odnowiony, ale Aplikacja korzystająca z certyfikatu App Service nadal używa starego certyfikatu. Otrzymano również ostrzeżenie, że protokół HTTPS jest wymagany.

#### <a name="cause"></a>Przyczyna 
App Service automatycznie synchronizuje certyfikat w ciągu 48 godzin. Gdy użytkownik obraca lub aktualizuje certyfikat, czasami aplikacja wciąż pobiera stary certyfikat, a nie nowo zaktualizowany certyfikat. Przyczyną jest to, że zadanie synchronizacji zasobu certyfikatu nie zostało jeszcze uruchomione. Kliknij pozycję Synchronizuj. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania przestojów aplikacji.

#### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizację certyfikatu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **App Service certyfikaty**, a następnie wybierz certyfikat.
2. Wybierz pozycję Wymień **i zsynchronizuj**, a następnie wybierz pozycję **Synchronizuj**. Synchronizacja zajmuje trochę czasu. 
3. Po zakończeniu synchronizacji zostanie wyświetlone następujące powiadomienie: "pomyślnie zaktualizowano wszystkie zasoby przy użyciu najnowszego certyfikatu".

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa 

#### <a name="symptom"></a>Objaw 
Certyfikat App Service wymaga weryfikacji domeny, zanim certyfikat będzie gotowy do użycia. Po wybraniu opcji **Weryfikuj** proces kończy się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie
Ręcznie Zweryfikuj domenę przez dodanie rekordu TXT:

1. Przejdź do dostawcy systemu Domain Name Service (DNS), który hostuje nazwę domeny.
1. Dodaj rekord TXT dla domeny korzystającej z wartości tokenu domeny pokazanego w witrynie Azure Portal. 

Poczekaj kilka minut, aż Propagacja DNS zostanie uruchomiona, a następnie wybierz przycisk **Odśwież** , aby wyzwolić weryfikację. 

Alternatywnie możesz użyć metody sieci Web HTML do ręcznego zweryfikowania domeny. Ta metoda umożliwia urzędowi certyfikacji potwierdzenie własności domeny, dla której certyfikat został wystawiony.

1. Utwórz plik HTML o nazwie {Domain Verification token}. html. Zawartość tego pliku powinna być wartością tokenu weryfikacji domeny.
1. Przekaż ten plik w katalogu głównym serwera sieci Web, który hostuje Twoją domenę.
1. Wybierz pozycję **Odśwież** , aby sprawdzić stan certyfikatu. Zweryfikowanie może potrwać kilka minut.

Na przykład jeśli kupujesz standardowy certyfikat dla usługi azure.com z tokenem weryfikacji domeny 1234abcd, żądanie sieci Web, które miało https://azure.com/1234abcd.html zwrócić 1234abcd. 

> [!IMPORTANT]
> Kolejność certyfikatów ma tylko 15 dni, aby ukończyć operację weryfikacji domeny. Po upływie 15 dni urząd certyfikacji odrzuca certyfikat i nie jest naliczana opłata za certyfikat. W tej sytuacji usuń ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domeny

#### <a name="symptom"></a>Objaw
Nie można kupić domeny App Service w Azure Portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje z jednego z następujących powodów:

- W subskrypcji platformy Azure nie ma karty kredytowej lub karta kredytowa jest nieprawidłowa.

    **Rozwiązanie**: Dodaj prawidłową kartę kredytową do subskrypcji.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień do zakupu domeny.

    **Rozwiązanie**: [Przypisz rolę właściciela](../role-based-access-control/role-assignments-portal.md) do Twojego konta. Lub skontaktuj się z administratorem subskrypcji, aby uzyskać uprawnienia do zakupu domeny.
- Osiągnięto limit zakupów domen w ramach subskrypcji. Bieżący limit wynosi 20.

    **Rozwiązanie**: Aby zażądać zwiększenia limitu, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ subskrypcji platformy Azure nie obsługuje zakupu domeny usługi App Service.

    **Rozwiązanie**: Uaktualnij subskrypcję platformy Azure do innego typu subskrypcji, takiego jak subskrypcja z płatność zgodnie z rzeczywistym użyciem.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nie można dodać nazwy hosta do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta proces nie może zweryfikować i zweryfikować domeny.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje z jednego z następujących powodów:

- Nie masz uprawnień do dodania nazwy hosta.

    **Rozwiązanie**: poproszenie administratora subskrypcji o przyznanie uprawnień do dodawania nazwy hosta.
- Nie można zweryfikować własności domeny.

    **Rozwiązanie**: Sprawdź, czy rekord CNAME lub A został skonfigurowany poprawnie. Aby zmapować domenę niestandardową na aplikację, Utwórz rekord CNAME lub rekord A. Jeśli chcesz użyć domeny głównej, musisz użyć rekordów a i TXT:

    |Typ rekordu|Host|Wskaż|
    |------|------|-----|
    |A|@|Adres IP aplikacji|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Często zadawane pytania

**Czy muszę skonfigurować moją domenę niestandardową dla mojej witryny internetowej po jej zakupie?**

W przypadku zakupienia domeny z Azure Portal aplikacja App Service jest automatycznie konfigurowana do korzystania z tej domeny niestandardowej. Nie trzeba podejmować żadnych dodatkowych kroków. Aby uzyskać więcej informacji, zobacz [Azure App Service samodzielna pomoc: Dodaj niestandardową nazwę domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) w channel9.

**Czy mogę użyć domeny zakupionej w Azure Portal, aby wskazać zamiast niej maszynę wirtualną platformy Azure?**

Tak, możesz wskazać domenę na maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

**Czy moja domena jest hostowana przez GoDaddy czy Azure DNS?**

Domeny App Service używają GoDaddy do rejestracji domeny i Azure DNS do hostowania domen. 

**Mam włączoną funkcję autoodnawiania, ale nadal otrzymujesz powiadomienie o odnowieniu dla mojej domeny za pośrednictwem poczty e-mail. Co mam zrobić?**

Jeśli włączono funkcję autoodnawiania, nie musisz podejmować żadnych działań. Powiadomienie e-mail zostanie przekazane, aby poinformować użytkownika o tym, że domena zbliża się do wygaśnięcia, i przenowić ręcznie, jeśli nie jest włączona funkcja autoodnawiania.

**Czy zostanie naliczona opłata za Azure DNS Hostowanie mojej domeny?**

Początkowy koszt zakupu domeny dotyczy tylko rejestracji domeny. Oprócz kosztu rejestracji naliczane są opłaty za Azure DNS w zależności od użycia. Aby uzyskać więcej informacji, zobacz [Azure DNS cennika](https://azure.microsoft.com/pricing/details/dns/) , aby uzyskać więcej szczegółów.

**Mam moją domenę wcześniej z Azure Portal i chcesz przenieść z hostingu GoDaddy do usługi Azure DNS hosting. Jak można to zrobić?**

Migracja do Azure DNS hostingu nie jest obowiązkowa. Jeśli chcesz przeprowadzić migrację do Azure DNS, środowisko zarządzania domeną w Azure Portal zawiera informacje na temat kroków niezbędnych do przejścia do Azure DNS. Jeśli domena została zakupiona za pośrednictwem App Service, migracja z hostingu GoDaddy do Azure DNS jest relatywnie bezproblemową procedurą.

**Chcę kupić moją domenę z domeny App Service, ale mogę hostować moją domenę na GoDaddy zamiast Azure DNS?**

Od 24 lipca 2017 App Service domen zakupionych w portalu są hostowane na Azure DNS. Jeśli wolisz używać innego dostawcy hostingu, musisz przejść do swojej witryny sieci Web, aby uzyskać rozwiązanie hostingu domeny.

**Czy muszę uregulować ochronę prywatności dla mojej domeny?**

W przypadku zakupu domeny za pomocą Azure Portal można wybrać opcję dodania prywatności bez dodatkowych kosztów. Jest to jedna z zalet kupowania domeny za pomocą Azure App Service.

**Czy nie chcę już korzystać z mojej domeny?**

W przypadku zakupienia domeny nie jest naliczana opłata przez okres pięciu dni, podczas której można zdecydować, że nie chcesz, aby dana domena została wybrana. Jeśli zdecydujesz, że nie chcesz, aby domena była w tym pięciu dni, opłaty nie są naliczone. (domeny. uk to wyjątek dla tego elementu. W przypadku zakupienia domeny z. uk opłata zostanie naliczona natychmiast i nie będzie można jej ponownie obfundować.

**Czy mogę użyć domeny w innej aplikacji Azure App Service w mojej subskrypcji?**

Tak. Gdy uzyskujesz dostęp do niestandardowych domen i protokołu TLS w Azure Portal, zobaczysz zakupione domeny. Aplikację można skonfigurować tak, aby korzystała z dowolnej z tych domen.

**Czy mogę przenieść domenę z jednej subskrypcji do innej?**

Domenę można przenieść do innej subskrypcji/grupy zasobów przy użyciu polecenia cmdlet [Move-AzResource](/powershell/module/az.Resources/Move-azResource) programu PowerShell.

**Jak mogę zarządzać moją domeną niestandardową, jeśli nie mam obecnie aplikacji Azure App Service?**

Możesz zarządzać domeną, nawet jeśli nie masz aplikacji sieci Web App Service. Domeny można używać dla usług platformy Azure, takich jak maszyna wirtualna, magazyn itp. Jeśli zamierzasz używać domeny do App Service Web Apps, musisz dołączyć aplikację sieci Web, która nie znajduje się w planie App Service w warstwie Bezpłatna, aby powiązać domenę z aplikacją sieci Web.

**Czy mogę przenieść aplikację sieci Web z domeną niestandardową do innej subskrypcji lub z App Service Environment V1 do wersji v2?**

Tak, możesz przenieść swoją aplikację sieci Web między subskrypcjami. Postępuj zgodnie ze wskazówkami dotyczącymi [przenoszenia zasobów na platformie Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Podczas przechodzenia do aplikacji internetowej istnieje kilka ograniczeń. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące przeniesienia App Service zasobów](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Po przeniesieniu aplikacji sieci Web powiązania nazw hostów domen w ramach ustawienia domen niestandardowych powinny pozostać takie same. Nie są wymagane żadne dodatkowe kroki, aby skonfigurować powiązania nazw hostów.