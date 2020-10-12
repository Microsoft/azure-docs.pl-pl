---
title: Instalowanie lokalnej bramy danych
description: Aby można było uzyskać dostęp do danych lokalnych z Azure Logic Apps, Pobierz i zainstaluj lokalną bramę danych.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: a36b9d20fa20df56ec53e090976ea86e689ac74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322516"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalowanie lokalnej bramy danych dla usługi Azure Logic Apps

Przed [nawiązaniem połączenia z lokalnymi źródłami danych z Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md)Pobierz i zainstaluj lokalną [bramę danych](https://aka.ms/on-premises-data-gateway-installer) na komputerze lokalnym. Brama działa jako most, który zapewnia szybki transfer i szyfrowanie danych między źródłami danych w środowisku lokalnym i aplikacjami logiki. Możesz użyć tej samej instalacji bramy z innymi usługami w chmurze, takich jak Power BI, Automatyzacja, aplikacje i Azure Analysis Services. Aby uzyskać informacje o sposobach korzystania z bramy za pomocą tych usług, zobacz następujące artykuły:

* [Microsoft energia — lokalna Brama danych](/power-automate/gateway-reference)
* [Lokalna Brama danych Power BI firmy Microsoft](/power-bi/service-gateway-onprem)
* [Lokalna Brama danych firmy Microsoft dla aplikacji zaawansowanych](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

W tym artykule pokazano, jak pobrać, zainstalować i skonfigurować lokalną bramę danych, aby można było uzyskać dostęp do lokalnych źródeł danych z Azure Logic Apps. Możesz również dowiedzieć się więcej o tym [, jak Brama danych działa](#gateway-cloud-service) w dalszej części tego tematu. Aby uzyskać więcej informacji na temat bramy, zobacz [co to jest brama lokalna](/data-integration/gateway/service-gateway-onprem)? Aby zautomatyzować zadania związane z instalacją i zarządzaniem bramą, odwiedź Galerię programu PowerShell dla [poleceń cmdlet programu PowerShell dla usługi datagateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure z subskrypcją, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Twoje konto platformy Azure musi być kontem służbowym, które wygląda następująco `username@contoso.com` . Nie można używać kont B2B (gość) platformy Azure ani osobistych kont Microsoft, takich jak @hotmail.com lub @outlook.com .

    > [!NOTE]
    > Jeśli zarejestrowano się w celu uzyskania oferty Microsoft 365 i nie podano służbowego adresu e-mail, Twój adres może wyglądać jak `username@domain.onmicrosoft.com` . Twoje konto jest przechowywane w dzierżawie usługi Azure AD. W większości przypadków główna nazwa użytkownika (UPN) konta platformy Azure jest taka sama jak w przypadku Twojego adresu e-mail.

    Aby użyć [subskrypcji programu Visual Studio w warstwie Standardowa](https://visualstudio.microsoft.com/vs/pricing/) skojarzonej z konto Microsoft, należy najpierw [utworzyć dzierżawę usługi Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) lub użyć domyślnego katalogu. Dodaj użytkownika z hasłem do katalogu, a następnie nadaj temu użytkownikowi dostęp do subskrypcji platformy Azure. Następnie możesz zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

  * Twoje konto platformy Azure musi należeć tylko do jednej [dzierżawy lub katalogu usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Aby zainstalować bramę na komputerze lokalnym i administrować nią, należy użyć tego samego konta platformy Azure.

  * Po zainstalowaniu bramy Zaloguj się przy użyciu konta platformy Azure, które łączy instalację bramy z kontem platformy Azure i tylko z tym kontem. Nie można połączyć tej samej instalacji bramy na wielu kontach platformy Azure ani dzierżawach usługi Azure AD.

  * W dalszej części Azure Portal musisz użyć tego samego konta platformy Azure, aby utworzyć zasób bramy platformy Azure, który łączy się z instalacją bramy. Do siebie można połączyć tylko jedną instalację bramy i jeden zasób bramy platformy Azure. Konto platformy Azure może jednak łączyć się z różnymi instalacjami bramy, które są skojarzone z zasobem bramy platformy Azure. Aplikacje logiki mogą następnie używać tego zasobu bramy w wyzwalaczach i akcjach, które mogą uzyskać dostęp do lokalnych źródeł danych.

* Poniżej przedstawiono wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  *  .NET Framework 4.7.2
  * 64-bitowa wersja systemu Windows 7 lub Windows Server 2008 R2 (lub nowszego)

  **Zalecane wymagania**

  * 8-rdzeniowy procesor CPU
  * 8 GB pamięci
  * 64 — bitowa wersja systemu Windows Server 2012 R2 lub nowszego
  * Magazyn dysków półprzewodnikowych (SSD) do buforowania

  > [!NOTE]
  > Brama nie obsługuje systemu Windows Server Core.

* **Powiązane zagadnienia**

  * Zainstaluj lokalną bramę danych tylko na komputerze lokalnym, a nie na kontrolerze domeny. Nie musisz instalować bramy na tym samym komputerze, na którym znajduje się źródło danych. Wymagana jest tylko jedna brama dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować opóźnienie, można zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze lokalnym, który znajduje się w sieci przewodowej, połączony z Internetem, zawsze włączone i przejdź do trybu uśpienia. W przeciwnym razie nie można uruchomić bramy, a wydajność sieci bezprzewodowej może być niedostępna.

  * Jeśli planujesz używanie uwierzytelniania systemu Windows, upewnij się, że brama jest zainstalowana na komputerze, który jest członkiem tego samego środowiska Active Directory, co źródła danych.

  * Region wybrany dla instalacji bramy jest tą samą lokalizacją, którą należy wybrać podczas późniejszego tworzenia zasobu bramy platformy Azure dla aplikacji logiki. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD, która zarządza Twoim kontem platformy Azure. Można jednak zmienić lokalizację podczas instalacji bramy.

  * Jeśli aktualizujesz instalację bramy, najpierw Odinstaluj bieżącą bramę w celu korzystania z funkcji czyszczenia.

    Najlepszym rozwiązaniem jest upewnienie się, że używasz obsługiwanej wersji. Firma Microsoft publikuje nową aktualizację lokalnej bramy danych co miesiąc, a obecnie obsługuje tylko ostatnie sześć wydań dla lokalnej bramy danych. Jeśli wystąpią problemy z używaną wersją programu, spróbuj [uaktualnić do najnowszej wersji](https://aka.ms/on-premises-data-gateway-installer) , ponieważ problem może zostać rozwiązany w najnowszej wersji.

  * Brama ma dwa tryby: tryb standardowy i tryb osobisty, który ma zastosowanie tylko do Power BI. Na tym samym komputerze nie może znajdować się więcej niż jedna brama uruchomiona w tym samym trybie.

  * Azure Logic Apps obsługuje operacje odczytu i zapisu za pomocą bramy. Jednak te operacje mają [limity rozmiaru ładunku](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz i uruchom Instalatora bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

1. Zapoznaj się z minimalnymi wymaganiami, Zachowaj domyślną ścieżkę instalacji, zaakceptuj warunki użytkowania, a następnie wybierz pozycję **Zainstaluj**.

   ![Przejrzyj wymagania i zaakceptuj warunki użytkowania](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Po pomyślnym zainstalowaniu bramy Podaj adres e-mail swojego konta platformy Azure, a następnie wybierz pozycję **Zaloguj się**, na przykład:

   ![Zaloguj się przy użyciu konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Instalacja bramy może być połączona tylko z jednym kontem platformy Azure.

1. Wybierz pozycję **zarejestruj nową bramę na tym komputerze**  >  **dalej**. Ten krok rejestruje instalację bramy w [usłudze bramy w chmurze](#gateway-cloud-service).

   ![Zarejestruj bramę na komputerze lokalnym](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Podaj te informacje na potrzeby instalacji bramy:

   * Nazwa bramy, która jest unikatowa w ramach dzierżawy usługi Azure AD
   * Klucz odzyskiwania, który musi mieć co najmniej osiem znaków, które mają być używane.
   * Potwierdzenie klucza odzyskiwania

   ![Podaj informacje dotyczące instalacji bramy](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Zapisz i Zachowaj klucz odzyskiwania w bezpiecznym miejscu. Ten klucz będzie potrzebny, jeśli kiedykolwiek chcesz zmienić lokalizację, przenieść, odzyskać lub przejmowanie instalacji bramy.

   Zwróć uwagę na to, że można **dodać do istniejącego klastra bramy**, który wybierasz podczas instalacji dodatkowych bram w [scenariuszach wysokiej dostępności](#high-availability).

1. Sprawdź region dla usługi bramy w chmurze i [wystąpienia obsługi komunikatów Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) , który jest używany przez instalację bramy. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD dla Twojego konta platformy Azure.

   ![Potwierdź region usługi bramy i usługi Service Bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Aby zaakceptować domyślny region, wybierz pozycję **Konfiguruj**. Jeśli jednak domyślny region nie jest tym, który znajduje się najbliżej Ciebie, możesz zmienić region.

   *Dlaczego należy zmienić region instalacji bramy?*

   Na przykład w celu zmniejszenia opóźnień można zmienić region bramy na ten sam region, w którym znajduje się aplikacja logiki. Możesz też wybrać region znajdujący się najbliżej lokalnego źródła danych. *Zasób bramy na platformie Azure* i aplikacja logiki mogą mieć różne lokalizacje.

   1. Obok bieżącego regionu wybierz pozycję **Zmień region**.

      ![Zmień bieżący region bramy](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na następnej stronie Otwórz listę **Wybierz region** , wybierz żądany region, a następnie wybierz pozycję **gotowe**.

      ![Wybierz inny region dla usługi bramy](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Przejrzyj informacje w oknie potwierdzenie końcowe. Ten przykład używa tego samego konta dla Logic Apps, Power BI, aplikacji zaawansowanych i automatyzacji, aby brama była dostępna dla wszystkich tych usług. Gdy wszystko będzie gotowe, wybierz pozycję **Zamknij**.

   ![Potwierdź informacje o bramie danych](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Teraz [Utwórz zasób platformy Azure na potrzeby instalacji bramy](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Sprawdzanie lub Dostosowywanie ustawień komunikacji

Lokalna Brama danych zależy od [Azure Service Bus komunikatów](../service-bus-messaging/service-bus-messaging-overview.md) na potrzeby łączności z chmurą i ustanawiania odpowiednich połączeń wychodzących do skojarzonego z nią regionu platformy Azure. Jeśli środowisko pracy wymaga, aby ruch przechodzi przez serwer proxy lub zaporę w celu uzyskania dostępu do Internetu, to ograniczenie może uniemożliwić lokalnej bramie danych łączenie się z usługą bramy w chmurze i Azure Service Bus Messaging. Brama ma kilka ustawień komunikacji, które można dostosować. Więcej informacji można znaleźć w następujących tematach:

* [Dostosowywanie ustawień komunikacji dla lokalnej bramy danych](/data-integration/gateway/service-gateway-communication)
* [Konfigurowanie ustawień serwera proxy dla lokalnej bramy danych](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Aby uniknąć pojedynczych punktów awarii w przypadku dostępu do danych lokalnych, można mieć wiele instalacji bramy (tylko tryb standardowy) z każdym innym komputerem i skonfigurować je jako klaster lub grupę. W ten sposób, jeśli Brama podstawowa jest niedostępna, żądania danych są kierowane do drugiej bramy i tak dalej. Ponieważ na komputerze można zainstalować tylko jedną bramę standardową, należy zainstalować każdą dodatkową bramę znajdującą się w klastrze na innym komputerze. Wszystkie łączniki, które współpracują z lokalną bramą danych, obsługują wysoką dostępność.

* Musisz mieć już co najmniej jedną instalację bramy z tym samym kontem platformy Azure co Brama podstawowa i klucz odzyskiwania dla tej instalacji.

* Brama podstawowa musi mieć uruchomioną aktualizację z listopada 2017 lub nowszą.

Po skonfigurowaniu bramy podstawowej po przejściu do pozycji zainstaluj inną bramę wybierz opcję **Dodaj do istniejącego klastra bramy**, wybierz bramę podstawową, która jest pierwszą zainstalowaną bramą, i Podaj klucz odzyskiwania dla tej bramy. Aby uzyskać więcej informacji, zobacz [klastrów o wysokiej dostępności dla lokalnej bramy danych](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmień lokalizację, Migruj, Przywróć lub Przejmij istniejącą bramę

Jeśli konieczna jest zmiana lokalizacji bramy, przeniesienie instalacji bramy na nowy komputer, odzyskanie uszkodzonej bramy lub przejęcie na własność istniejącej bramy wymaga klucza odzyskiwania, który został dostarczony podczas instalacji bramy.

> [!NOTE]
> Przed przystąpieniem do przywracania bramy na komputerze z oryginalną instalacją bramy należy najpierw odinstalować bramę na tym komputerze. Ta akcja rozłącza pierwotną bramę.
> Po usunięciu lub usunięciu klastra bramy dla dowolnej usługi w chmurze nie można przywrócić tego klastra.

1. Uruchom Instalatora bramy na komputerze, który ma istniejącą bramę.

1. Po otwarciu Instalatora Zaloguj się przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. Wybierz pozycję **Migruj, Przywróć lub przejęcie istniejącej bramy**  >  **dalej**, na przykład:

   ![Wybieranie opcji "Migruj, Przywróć lub przejęcie istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Wybierz spośród dostępnych klastrów i bram, a następnie wprowadź klucz odzyskiwania dla wybranej bramy, na przykład:

   ![Wybierz bramę i Podaj klucz odzyskiwania](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Aby zmienić region, wybierz pozycję **Zmień region**, a następnie wybierz nowy region.

1. Gdy wszystko będzie gotowe, wybierz pozycję **Konfiguruj** , aby można było zakończyć zadanie.

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy

Aby uzyskać wgląd we wszystkie lokalne bramy danych w dzierżawie usługi Azure AD, Administratorzy globalni w tej dzierżawie mogą zalogować się do [Centrum administracyjnego platformy na platformie zarządzania](https://powerplatform.microsoft.com) jako Administrator dzierżawy i wybrać opcję **bramy danych** . Aby uzyskać więcej informacji, zobacz [administrowanie na poziomie dzierżawy dla lokalnej bramy danych](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Ponowne uruchamianie bramy

Domyślnie instalacja bramy na komputerze lokalnym działa jako konto usługi systemu Windows o nazwie "lokalna Brama danych". Jednak instalacja bramy używa `NT SERVICE\PBIEgwService` nazwy dla poświadczeń konta "Zaloguj się jako" i ma uprawnienia "Logowanie w trybie usługi".

> [!NOTE]
> Konto usługi systemu Windows różni się od konta używanego do łączenia się z lokalnymi źródłami danych i konta platformy Azure, które jest używane podczas logowania się do usług w chmurze.

Podobnie jak w przypadku każdej innej usługi systemu Windows, można uruchomić i zatrzymać bramę na różne sposoby. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie lokalnej bramy danych](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak działa brama

Użytkownicy w organizacji mogą uzyskiwać dostęp do danych lokalnych, do których mają już dostęp autoryzowany. Jednak zanim użytkownicy będą mogli połączyć się z lokalnym źródłem danych, należy zainstalować i skonfigurować lokalną bramę danych. Zazwyczaj administrator jest osobą, która instaluje i konfiguruje bramę. Te akcje mogą wymagać uprawnień administratora serwera lub specjalnej wiedzy o serwerach lokalnych.

Brama ułatwia szybsze i bardziej bezpieczną komunikację w tle. Ta komunikacja odbywa się między użytkownikiem w chmurze, usługą bramy w chmurze i lokalnym źródłem danych. Usługa bramy w chmurze szyfruje i przechowuje poświadczenia źródła danych oraz szczegóły bramy. Usługa kieruje także zapytania i ich wyniki między użytkownikiem, bramą i lokalnym źródłem danych.

Brama współpracuje z zaporami i używa tylko połączeń wychodzących. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem [Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-overview.md). Ta usługa Service Bus tworzy kanał między bramą a usługą wywołującą, ale nie przechowuje żadnych danych. Wszystkie dane przesyłane przez bramę są szyfrowane.

![Architektura bramy danych lokalnych](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> W zależności od usługi w chmurze może być konieczne skonfigurowanie źródła danych dla bramy.

W tych krokach opisano, co się dzieje w przypadku korzystania z elementu połączonego z lokalnym źródłem danych:

1. Usługa w chmurze tworzy zapytanie wraz z zaszyfrowanymi poświadczeniami dla źródła danych. Następnie usługa wysyła zapytanie i poświadczenia do kolejki bramy w celu przetworzenia.

1. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do Azure Service Bus Messaging.

1. Azure Service Bus Messaging wysyła żądania oczekujące do bramy.

1. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie z co najmniej jednym źródłem danych przy użyciu tych poświadczeń.

1. Brama wysyła zapytanie do źródła danych w celu uruchomienia.

1. Wyniki są wysyłane ze źródła danych z powrotem do bramy, a następnie do usługi bramy w chmurze. Następnie Usługa bramy w chmurze używa wyników.

### <a name="authentication-to-on-premises-data-sources"></a>Uwierzytelnianie w lokalnych źródłach danych

Przechowywane poświadczenia są używane do nawiązywania połączenia z bramą z lokalnymi źródłami danych. Niezależnie od użytkownika, brama używa przechowywanych poświadczeń do nawiązania połączenia. Mogą wystąpić wyjątki uwierzytelniania dla określonych usług, takich jak zapytania bezpośrednie i LiveConnect Analysis Services w Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Usługi w chmurze firmy Microsoft używają usługi [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) do uwierzytelniania użytkowników. Dzierżawa usługi Azure AD zawiera nazwy użytkowników i grupy zabezpieczeń. Zwykle adres e-mail używany do logowania jest taki sam jak główna nazwa użytkownika (UPN) dla Twojego konta.

### <a name="what-is-my-upn"></a>Co to jest moja nazwa UPN?

Jeśli nie jesteś administratorem domeny, być może nie znasz nazwy UPN. Aby znaleźć nazwę UPN dla Twojego konta, uruchom `whoami /upn` polecenie z stacji roboczej. Mimo że wynik wygląda podobnie do adresu e-mail, wynik jest nazwą UPN dla lokalnego konta domeny.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronizowanie lokalnej usługi Active Directory z usługą Azure AD

Nazwa UPN lokalnych kont Active Directory i kont usługi Azure AD musi być taka sama. Upewnij się, że każde konto Active Directory lokalnego jest zgodne z kontem usługi Azure AD. Usługi w chmurze wiedzą tylko o kontach w usłudze Azure AD. W związku z tym nie musisz dodawać konta do Active Directory lokalnego. Jeśli konto nie istnieje w usłudze Azure AD, nie można użyć tego konta.

Poniżej przedstawiono sposoby dopasowywania lokalnych kont Active Directory za pomocą usługi Azure AD.

* Ręczne dodawanie kont do usługi Azure AD.

  Utwórz konto w Azure Portal lub w centrum administracyjnym Microsoft 365. Upewnij się, że nazwa konta jest zgodna z nazwą UPN dla lokalnego konta Active Directory.

* Zsynchronizuj konta lokalne z dzierżawą usługi Azure AD za pomocą narzędzia Azure Active Directory Connect.

  Narzędzie Azure AD Connect udostępnia opcje synchronizacji katalogów i konfigurowania uwierzytelniania. Te opcje obejmują funkcję synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. Jeśli nie jesteś administratorem dzierżawy lub administratorem domeny lokalnej, skontaktuj się z administratorem IT, aby uzyskać konfigurację Azure AD Connect. Azure AD Connect zapewnia, że nazwa UPN usługi Azure AD pasuje do lokalnej Active Directory nazwy UPN. Takie dopasowanie pomaga w przypadku korzystania z Analysis Services połączeń na żywo z funkcją Power BI lub logowania jednokrotnego (SSO).

  > [!NOTE]
  > Synchronizowanie kont za pomocą narzędzia Azure AD Connect tworzy nowe konta w dzierżawie usługi Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Często zadawane pytania i rozwiązywanie problemów

* [Lokalna brama danych — często zadawane pytania](/data-integration/gateway/service-gateway-onprem-faq)
* [Rozwiązywanie problemów z lokalną bramą danych](/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i optymalizowanie wydajności bramy](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z danymi lokalnymi z usługi Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
