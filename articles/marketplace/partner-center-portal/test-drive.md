---
title: Konfigurowanie dysków testowych w witrynie Microsoft Commercial Marketplace
description: Dowiedz się więcej na temat dysków testowych. Dyski testowe umożliwiają nowym klientom testowanie oferty przed zatwierdzeniem do zakupu.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786550"
---
# <a name="learn-about-test-drive-for-your-offer"></a>Dowiedz się więcej na temat dysku testowego oferty

Opcja **dysk testowy** w komercyjnej witrynie Microsoft Marketplace umożliwia skonfigurowanie praktycznego przewodnika po kluczowych funkcjach produktu. W przypadku dysku testowego nowi klienci mogą próbować skorzystać z oferty przed zatwierdzeniem jej zakupu. Aby uzyskać więcej informacji, zobacz [co to jest dysk testowy?](../cloud-partner-portal/test-drive/what-is-test-drive.md)

Jeśli nie chcesz już podawać dysku testowego dla swojej oferty, Wróć do strony **Konfiguracja oferty** i usuń zaznaczenie pola wyboru **Włącz dysk testowy**.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Dostępne są następujące typy dysków testowych, z których każda ma własne wymagania dotyczące konfiguracji technicznej.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logiki](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (konfiguracja techniczna nie jest wymagana)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Konfiguracja techniczna dla Azure Resource Managergo dysku testowego

Istnieje szablon wdrożenia zawierający wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Jest to odpowiednie rozwiązanie w przypadku produktów korzystających tylko z zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [Azure Resource Manager dysku testowego](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md).

- **Regiony** (wymagane): obecnie istnieją 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zwykle warto udostępnić dysk testowy w regionach, w których przewidujesz największą liczbę klientów, dzięki czemu można wybrać najbliższy region dla najlepszej wydajności. Musisz się upewnić, że Twoja subskrypcja może wdrażać wszystkie wymagane przez siebie regiony.

- **Wystąpienia**: Wybierz typ (gorąca lub zimna) oraz liczbę dostępnych wystąpień, które zostaną pomnożone przez liczbę regionów, w których oferta jest dostępna.

  - **Gorąca**: ten typ wystąpienia jest wdrożony i oczekuje na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do *gorącego* wystąpienia dysku testowego, a nie muszą czekać na wdrożenie. Jest to, że te wystąpienia są zawsze uruchamiane w ramach subskrypcji platformy Azure, dzięki czemu będą one miały większy koszt przestoju. Zdecydowanie zaleca się, aby miało co najmniej jedno *aktywne* wystąpienie, ponieważ większość *klientów nie chce* czekać na pełne wdrożenia, co powoduje odrzucanie w przypadku użycia klienta w przypadku braku dostępnego wystąpienia.

  - **Zimne**: ten typ wystąpienia reprezentuje łączną liczbę wystąpień, które mogą być wdrożone w poszczególnych regionach. Zimne wystąpienia wymagają, aby cały dysk testowy Menedżer zasobów szablon do wdrożenia, gdy klient zażąda dysku testowego, więc *zimne* wystąpienia są znacznie wolniejsze, aby można było ładować je od *aktywnych* wystąpień. Wadą jest to, że musisz tylko uregulować czas trwania testu, ale *nie* zawsze działa w ramach subskrypcji platformy Azure, tak jak w przypadku wystąpienia *aktywnego* .

- **Test Azure Resource Manager szablonu**: Przekaż plik zip zawierający szablon Azure Resource Manager.  Dowiedz się więcej o tworzeniu szablonu Azure Resource Manager w artykule Szybki Start [Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Po upływie tego czasu test kończy się automatycznie. Ten czas trwania może być ustawiony tylko przez całą liczbę godzin (na przykład "2" godzin, "1,5" jest nieprawidłowy).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla systemu Dynamics 365 Test Drive

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego, udostępniając i utrzymując obsługę administracyjną i wdrożenie usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców w biznesie, w ramach zaangażowania z klientami, czy z działu operacji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane): Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Każdy użytkownik współbieżny będzie korzystał z licencji Dynamics 365, gdy test jest aktywny, więc należy upewnić się, że masz wystarczającą liczbę dostępnych licencji do obsługi maksymalnego ustawionego limitu. Zalecana wartość 3-5.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego, definiując liczbę godzin. Po tylu godzinach sesja zostanie zakończona i nie będzie już korzystać z jednej z Twoich licencji. Zalecamy użycie wartości 2-24 godzin w zależności od złożoności oferty. Ten czas trwania może być ustawiony tylko przez całą liczbę godzin (na przykład "2" godzin, "1,5" jest nieprawidłowy).  Użytkownik może zażądać nowej sesji, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany): adres URL, pod którym klient zacznie testować swój dysk. Zwykle jest to adres URL wystąpienia usługi Dynamics 365 z uruchomioną aplikacją z zainstalowanymi przykładowymi danymi `https://testdrive.crm.dynamics.com`(na przykład).

- **Adres URL internetowego interfejsu API wystąpienia** (wymagane): Pobierz adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do **ustawień** \&gt; **Dostosowywanie** \&gt; **Zasoby** \&dla deweloperów gt; **Interfejs API sieci Web wystąpienia (główny adres URL usługi)**, skopiuj adres URL znaleziony w tym `https://testdrive.crm.dynamics.com/api/data/v9.0`miejscu (na przykład).

- **Nazwa roli** (wymagana): Podaj nazwę roli zabezpieczeń zdefiniowaną w niestandardowym dysku testowym Dynamics 365. Ta wartość zostanie przypisana do użytkownika w trakcie jego dysku testowego (na przykład roli "Test-Drive-role").

### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla dysku testowego aplikacji logiki

Wszystkie produkty niestandardowe powinny korzystać z tego typu szablonu wdrożenia dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź stronę [operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowanie klientów](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w serwisie GitHub.

- **Region** (wymagana, lista rozwijana z pojedynczym wyborem): obecnie dostępne są 26 regionów obsługiwanych przez platformę Azure. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma jakieś zasoby niestandardowe przechowywane w określonym regionie, upewnij się, że w tym miejscu wybrano region. Najlepszym sposobem jest w pełni wdrażanie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa ona prawidłowo przed wybraniem tej opcji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane): Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Te stacje testowe są już wdrożone, umożliwiając klientom natychmiastowe uzyskiwanie dostępu do nich bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Po upływie tego czasu test kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana): Wprowadź nazwę [grupy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) , w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagana): Wprowadź nazwę aplikacji logiki, która przypisuje dysk testowy do użytkownika. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

- Anulowanie aprowizacji **nazwy aplikacji logiki** (wymagane): Wprowadź nazwę aplikacji logiki, która powoduje anulowanie obsługi dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna dla Power BI dysków testowych nie jest wymagana

Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, mogą użyć osadzonego linku, aby udostępnić pulpit nawigacyjny, który jest wbudowanym niestandardowym jako dysk testowy, nie jest wymagana żadna dodatkowa konfiguracja techniczna. Dowiedz się więcej o konfigurowaniu aplikacji [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) Template.

## <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć dysk testowy w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure. (Niewymagane w przypadku Power BI dysków testowych).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla Azure Resource Manager i aplikacji logiki): Wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład `a83645ac-1234-5ab6-6789-1h234g764ghty`).

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź [Identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **Właściwości** , a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład `50c464d3-4930-494c-963c-1e951d15360e`). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla dynamicznego 365): wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagane): wprowadź [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład `50c464d3-4930-494c-963c-1e951d15360e`).

- **Wpis tajny klienta aplikacji usługi Azure AD** (wymagane): wprowadź [wpis tajny klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i wpisy tajne**, wybierz pozycję **Nowy wpis tajny klienta**, wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**. Należy pamiętać o skopiowaniu wartości. (Nie opuszczaj strony przed wykonaniem tej czynności lub nie będziesz mieć dostępu do wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

## <a name="test-drive-listings-optional"></a>Aukcje na dysku testowym (opcjonalnie)

Opcja **aukcje na dysku testowym** znajduje się na karcie **dysk testowy** zawiera języki (i rynki), w których jest dostępny dysk testowy. obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Należy zdefiniować szczegóły stacji testowej (opis, Podręcznik użytkownika, wideo itp.) dla każdego języka/rynku.

- **Opis** (wymagane): opisz swój dysk testowy, co zostanie przedstawione, w jaki sposób użytkownik może eksperymentować z programem, funkcje do eksplorowania i wszelkie istotne informacje, które ułatwią użytkownikowi ustalenie, czy ma zostać pozyskana oferta. W tym polu można wprowadzić do 3 000 znaków tekstu.

- **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych): Wyjaśnij, co musi wiedzieć klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.

- **Podręcznik użytkownika** (wymagane): szczegółowy przewodnik dotyczący środowiska testowego. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.

- **Wideo: Dodaj wideo** (opcjonalnie): klipy wideo można przekazać do usługi YouTube lub Vimeo i przywoływane w tym miejscu za pomocą linku i miniatury (533 x 324 pikseli), aby klient mógł wyświetlić szczegółowe informacje ułatwiające lepsze zrozumienie dysku testowego, w tym jak pomyślnie korzystać z funkcji oferty i zrozumieć scenariusze, które wyróżnią swoje korzyści.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko w usłudze YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324 pikseli)** — plik obrazu musi być w formacie PNG.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

## <a name="next-steps"></a>Następne kroki

[Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
