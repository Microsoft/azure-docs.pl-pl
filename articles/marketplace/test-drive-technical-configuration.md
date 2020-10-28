---
title: Testuj konfigurację techniczną, Microsoft Commercial Marketplace
description: Dowiedz się więcej na temat dysków testowych. Dyski testowe umożliwiają nowym klientom testowanie oferty przed zatwierdzeniem do zakupu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: keferna
ms.author: keferna
ms.openlocfilehash: 1772a61f45d88e0b63346c41d557cfddf35ab4a7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892625"
---
# <a name="test-drive-technical-configuration"></a>Wersja testowa konfiguracji technicznej

Opcja dysk testowy w komercyjnej witrynie Microsoft Marketplace umożliwia skonfigurowanie praktycznego przewodnika po kluczowych funkcjach produktu. W przypadku dysku testowego nowi klienci mogą próbować skorzystać z oferty przed zatwierdzeniem jej zakupu. Aby uzyskać więcej informacji, zobacz [co to jest dysk testowy?](what-is-test-drive.md)

Jeśli nie chcesz już podawać dysku testowego dla swojej oferty, Wróć do strony **Konfiguracja oferty** i usuń zaznaczenie pola wyboru **Włącz dysk testowy** . Nie wszystkie typy ofert mają dostępny dysk testowy.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager dysk testowy

Jest to jedyna opcja dysku testowego dla usług Virtual Machines lub Azure App, która wymaga dość szczegółowej konfiguracji. Zapoznaj się z poniższymi sekcjami dotyczącymi [szczegółów subskrypcji wdrożenia](#deployment-subscription-details) i [list na dyskach testowych](#test-drive-listings), a następnie przejdź do oddzielnego tematu [Azure Resource Manager konfiguracji dysku testowego](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Hostowany dysk testowy

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego, udostępniając i utrzymując obsługę administracyjną oraz anulowania aprowizacji. Konfiguracja dla tego typu dysku testowego jest taka sama niezależnie od tego, czy dysk testowy jest przeznaczony dla użytkowników usługi Dynamics 365 klienta lub Dynamics 365 Operations Manager.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane) — Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Każdy użytkownik współbieżny będzie korzystał z licencji Dynamics 365, gdy test jest aktywny, więc upewnij się, że masz wystarczającą liczbę licencji na obsługę maksymalnego ustawionego limitu. Zalecana wartość to 3-5.

- **Czas trwania dysku testowego** (wymagane) — wprowadź liczbę godzin aktywności dysku testowego dla każdego klienta. Po upływie tego czasu sesja zostanie zakończona i nie będzie już korzystać z jednej z Twoich licencji. Zalecamy użycie wartości 2-24 godzin w zależności od złożoności oferty. Ten czas trwania można ustawić tylko w pełnych godzinach (na przykład "2 godziny" jest prawidłowy; "1,5 godz." nie jest). Użytkownik może zażądać nowej sesji, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany) — adres URL, pod którym klient będzie rozpoczynać swój dysk testowy. Zwykle jest to adres URL wystąpienia usługi Dynamics 365 z uruchomioną aplikacją z zainstalowanymi przykładowymi danymi (na przykład `https://testdrive.crm.dynamics.com` ).

- **Adres URL internetowego interfejsu API wystąpienia** (wymagany) — Pobierz adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do dostosowywania **ustawień**  >  **Customization**  >  wystąpienia **zasobów deweloperskich**  >  **Web API (główny adres URL usługi)** , skopiuj adres URL znaleziony tutaj (na przykład `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nazwa roli** (wymagana) — podaj nazwę roli zabezpieczeń, która została zdefiniowana w niestandardowym dysku testowym Dynamics 365, która zostanie przypisana do użytkownika podczas testu na dysku (na przykład "rola stacji testowych").

Aby uzyskać pomoc dotyczącą sposobu konfigurowania środowiska Dynamics 365 dla dysku testowego i przyznawania uprawnień AppSource do aprowizacji i anulowania aprowizacji użytkowników w dzierżawie, postępuj zgodnie z [tymi instrukcjami](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

## <a name="logic-app-test-drive"></a>Dysk testowy aplikacji logiki

Ten typ dysku testowego nie jest hostowany przez firmę Microsoft. Służy do nawiązywania połączenia z ofertą Dynamics 365 lub innym zasobem niestandardowym, co obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź stronę [operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowanie klientów](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w serwisie GitHub.

- **Region** (wymagana, lista rozwijana z pojedynczym wyborem) — obecnie dostępne są 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma jakieś zasoby niestandardowe przechowywane w określonym regionie, upewnij się, że w tym miejscu wybrano region. Najlepszym sposobem jest w pełni wdrażanie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa ona prawidłowo przed wybraniem tej opcji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane) — Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Te stacje testowe są już wdrożone, umożliwiając klientom natychmiastowe uzyskiwanie dostępu do nich bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane) — Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Po upływie tego czasu test kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana) — wprowadź nazwę [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md#resource-groups), w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagana) — wprowadź nazwę aplikacji logiki, która przypisuje dysk testowy do użytkownika. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

- Anulowanie aprowizacji **nazwy aplikacji logiki** (wymagane) — wprowadź nazwę aplikacji logiki, która anuluje obsługę dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

## <a name="power-bi-test-drive"></a>Power BI dysk testowy

Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, mogą użyć osadzonego linku, aby udostępnić pulpit nawigacyjny, który jest wbudowanym niestandardowym jako dysk testowy, nie jest wymagana żadna dodatkowa konfiguracja techniczna. Wszystko, co musisz zrobić, Przekaż swój osadzony adres URL Power BI.

Aby uzyskać więcej informacji na temat konfigurowania Power BI aplikacji, zobacz [co to są aplikacje Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby umożliwić firmie Microsoft wdrożenie dysku testowego w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure (niewymaganą dla Power BI dysków testowych).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla Azure Resource Manager i aplikacji logiki) — wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](../cost-management-billing/manage/create-subscription.md) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagany) — wprowadź [Identyfikator dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **Właściwości** , a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu nazwy domeny w lokalizacji [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla dynamicznego 365) — wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany) — wprowadź [Identyfikator aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji** , a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Wpis tajny klienta aplikacji usługi Azure AD** (wymagane) — wprowadź [klucz tajny klienta](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji** , a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i wpisy tajne** , wybierz pozycję **Nowy wpis tajny klienta** , wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie** , a następnie wybierz pozycję **Dodaj** . Należy pamiętać o skopiowaniu wartości. Przed skopiowaniem wartości nie opuszczaj strony.

## <a name="test-drive-listings"></a>Aukcje na dysku testowym

Opcja **listy** dla wersji testowych znajduje się na karcie **dysk testowy** w centrum partnerskim przedstawia języki (i rynki), w których jest dostępny dysk testowy. obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Należy zdefiniować szczegóły stacji testowej (opis, Podręcznik użytkownika, wideo itp.) dla każdego języka/rynku.

- **Opis** (wymagane): opisz swój dysk testowy, co zostanie przedstawione, w jaki sposób użytkownik może eksperymentować z programem, funkcje do eksplorowania i wszelkie istotne informacje, które ułatwią użytkownikowi ustalenie, czy ma zostać pozyskana oferta. W tym polu można wprowadzić do 3 000 znaków tekstu.

- **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych): Wyjaśnij, co musi wiedzieć klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.

- **Podręcznik użytkownika** (wymagane): szczegółowe omówienie środowiska testowego. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.

- **Wideo: Dodaj wideo** (opcjonalnie): filmy wideo hostowane w innym miejscu można przywoływać w tym miejscu za pomocą linku i obrazu miniatury (533 x 324 pikseli), aby klient mógł wyświetlić szczegółowe informacje ułatwiające im lepsze zrozumienie dysku testowego, w tym sposób pomyślnego użycia funkcji oferty i poznanie scenariuszy, które wyróżnią swoje korzyści.
  - **Nazwa** (wymagana)
  - **Adres URL** (tylko w serwisie YouTube lub Vimeo; wymagane)
  - **Miniatura** (533 x 324 pikseli) — obraz musi być w formacie PNG.

Jeśli obecnie tworzysz dysk testowy w centrum partnerskim, wybierz opcję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-steps"></a>Następne kroki

- [Najlepsze rozwiązania dotyczące wersji testowej](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przegląd](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF; upewnij się, że blokowanie wyskakujących okienek jest wyłączone)
- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](partner-center-portal/update-existing-offer.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

## <a name="feedback"></a>Opinia

Prześlij i Wyświetl opinię dla [tego produktu](https://feedback.azure.com/forums/216369-azure-marketplace)
