---
title: 'Szybki Start: Konfigurowanie aplikacji do uzyskiwania dostępu do internetowego interfejsu API | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start skonfigurujesz rejestrację aplikacji reprezentującą internetowy interfejs API na platformie tożsamości firmy Microsoft, aby umożliwić dostęp do zasobów o określonym zakresie (uprawnienia) do aplikacji klienckich.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651378"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowego interfejsu API

W tym przewodniku szybki start nadajesz aplikacji klienckiej zarejestrowanej na platformie tożsamości firmy Microsoft z zakresem dostęp oparty na uprawnieniach do własnego interfejsu API sieci Web. Możesz również zapewnić dostęp aplikacji klienckiej do Microsoft Graph.

Określając zakresy interfejsu API sieci Web w rejestracji aplikacji klienckiej, aplikacja kliencka może uzyskać token dostępu zawierający te zakresy z platformy tożsamości firmy Microsoft. W swoim kodzie internetowy interfejs API może następnie zapewnić dostęp oparty na uprawnieniach do zasobów na podstawie zakresów znalezionych w tokenie dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją — [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Kończenie [szybkiego startu: rejestrowanie aplikacji](quickstart-register-app.md)
* Kończenie [szybkiego startu: Konfigurowanie aplikacji do udostępniania internetowego interfejsu API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Dodawanie uprawnień dostępu do internetowego interfejsu API

W pierwszym scenariuszu nadajesz aplikacji klienckiej dostęp do własnego internetowego interfejsu API, co zostało zarejestrowane w ramach wymagań wstępnych. Jeśli nie masz jeszcze zarejestrowanej aplikacji klienckiej i internetowego interfejsu API, wykonaj kroki opisane w dwóch artykułach z [wymaganiami wstępnymi](#prerequisites) .

Ten diagram przedstawia, w jaki sposób dwie rejestracje aplikacji są powiązane ze sobą. W tej sekcji dodasz uprawnienia do rejestracji aplikacji klienckiej.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Diagram liniowy przedstawiający interfejs API sieci Web z uwidocznionymi zakresami po prawej stronie i aplikacji klienckiej po lewej stronie z tymi zakresami wybranymi jako uprawnienia" border="false":::

Po zarejestrowaniu zarówno aplikacji klienckiej, jak i interfejsu API sieci Web, a interfejs API został ujawniony przez Tworzenie zakresów, można skonfigurować uprawnienia klienta do interfejsu API, wykonując następujące czynności:

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę zawierający rejestrację aplikacji klienckiej.
1. Wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**, a następnie wybierz aplikację kliencką (*nie* interfejs API sieci Web).
1. Wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**  >  **Moje interfejsy API**.
1. Wybierz internetowy interfejs API, który został zarejestrowany w ramach wymagań wstępnych.

    **Delegowane uprawnienia** są domyślnie zaznaczone. Delegowane uprawnienia są odpowiednie dla aplikacji klienckich, które uzyskują dostęp do internetowego interfejsu API jako zalogowany użytkownik, a dostęp do niego powinien być ograniczony do uprawnień wybranych w następnym kroku. Pozostaw **uprawnienia delegowane** wybrane dla tego przykładu.

    **Uprawnienia aplikacji** są przeznaczone dla aplikacji typu "Service-lub demon", które muszą uzyskiwać dostęp do internetowego interfejsu API, bez interakcji z użytkownikiem w przypadku logowania lub wyrażania zgody. Jeśli nie zdefiniowano ról aplikacji dla internetowego interfejsu API, ta opcja jest wyłączona.
1. W obszarze **Wybierz uprawnienia** rozwiń zasób, którego zakresy zostały zdefiniowane dla internetowego interfejsu API, a następnie wybierz uprawnienia, które aplikacja kliencka powinna mieć w imieniu zalogowanego użytkownika.

    Jeśli użyto przykładowych nazw zakresów określonych w poprzednim przewodniku Szybki Start, zobacz **pracownicy. Read. All** i **Employees. Write. All**.
    Wybierz pozycję **pracownicy. odczyt. wszystkie** lub inne uprawnienia, które mogły zostać utworzone podczas kończenia wymagania wstępnego.
1. Wybierz pozycję **Dodaj uprawnienia** , aby zakończyć proces.

Po dodaniu uprawnień do interfejsu API w obszarze **skonfigurowane uprawnienia** powinny zostać wyświetlone wybrane uprawnienia. Na poniższej ilustracji przedstawiono przykład *Employees. Read. All* uprawnienie delegowane dodane do rejestracji aplikacji klienta.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Okienko skonfigurowane uprawnienia w Azure Portal pokazujące nowo dodane uprawnienie":::

Możesz również zauważyć uprawnienia *User. Read* dla interfejsu API Microsoft Graph. To uprawnienie jest dodawane automatycznie podczas rejestrowania aplikacji w Azure Portal.

## <a name="add-permissions-to-access-microsoft-graph"></a>Dodawanie uprawnień dostępu Microsoft Graph

Oprócz uzyskiwania dostępu do własnego internetowego interfejsu API w imieniu zalogowanego użytkownika może być również konieczne uzyskanie dostępu do danych użytkownika (lub innych) przechowywanych w Microsoft Graph. Możesz też mieć aplikację usługi lub demona, która musi uzyskać dostęp Microsoft Graph jak sama, wykonując operacje bez żadnej interakcji z użytkownikiem.

### <a name="delegated-permission-to-microsoft-graph"></a>Delegowane uprawnienia do Microsoft Graph

Skonfiguruj delegowane uprawnienie do Microsoft Graph, aby umożliwić aplikacji klienckiej wykonywanie operacji w imieniu zalogowanego użytkownika, na przykład odczytując swoją wiadomość e-mail lub modyfikując swój profil. Domyślnie użytkownicy aplikacji klienckiej są monitowani o zalogowanie się, aby wyrazić zgodę na delegowane uprawnienia, które zostały przez Ciebie skonfigurowane.

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę zawierający rejestrację aplikacji klienckiej.
1. Wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**, a następnie wybierz aplikację kliencką.
1. Wybierz **uprawnienia interfejsu API**  >  **Dodaj**  >  **Microsoft Graph** uprawnień
1. Wybierz pozycję **uprawnienia delegowane**. Microsoft Graph uwidacznia wiele uprawnień, z najczęściej używanymi w górnej części listy.
1. W obszarze **Wybieranie uprawnień** wybierz następujące uprawnienia:

    | Uprawnienie       | Opis                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Wyświetl adres e-mail użytkowników                           |
    | `offline_access` | Zapewnianie dostępu do danych, do których masz dostęp |
    | `openid`         | Loguj użytkowników                                       |
    | `profile`        | Wyświetl profil podstawowy użytkowników                           |
1. Wybierz pozycję **Dodaj uprawnienia** , aby zakończyć proces.

Za każdym razem, gdy konfigurujesz uprawnienia, użytkownicy Twojej aplikacji będą monitowani o zalogowanie się, aby umożliwić aplikacji dostęp do interfejsu API zasobów w ich imieniu.

Jako administrator możesz również udzielić zgody w imieniu *wszystkich* użytkowników, aby nie wyświetlały tego monitu. Zgoda administratora została omówiona później w sekcji [więcej informacji na temat uprawnień interfejsu API i zgody administratora](#more-on-api-permissions-and-admin-consent) w tym artykule.

### <a name="application-permission-to-microsoft-graph"></a>Uprawnienie aplikacji do Microsoft Graph

Skonfiguruj uprawnienia aplikacji dla aplikacji, która musi być uwierzytelniana jako sama bez interakcji z użytkownikiem lub zgody. Uprawnienia aplikacji są zwykle używane przez usługi w tle lub aplikacje demona, które uzyskują dostęp do interfejsu API w sposób "bezobsługowy" oraz przez interfejsy API sieci Web, które uzyskują dostęp do innego (podrzędnego) interfejsu API.

W poniższych krokach przyznasz uprawnienia do *plików Microsoft Graph. uprawnienie Read. All* jako przykład.

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę zawierający rejestrację aplikacji klienckiej.
1. Wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**, a następnie wybierz aplikację kliencką.
1. Wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**  >  **Microsoft Graph**  >  **uprawnienia aplikacji**.
1. Wszystkie uprawnienia uwidocznione przez Microsoft Graph są wyświetlane w obszarze **Wybierz uprawnienia**.
1. Wybierz uprawnienia lub uprawnienia, które chcesz udzielić aplikacji. Przykładowo może istnieć aplikacja demona, która skanuje pliki w organizacji, ostrzega o określonym typie lub nazwie pliku.

    W obszarze **Wybierz uprawnienia** rozwiń pozycję **pliki**, a następnie wybierz uprawnienie *pliki. odczyt. wszystkie* .
1. Wybierz pozycję **Dodaj uprawnienia**.

Niektóre uprawnienia, takie jak *pliki Microsoft Graph. Read. All* , wymagają zgody administratora. Możesz udzielić zgody administratora, wybierając przycisk **Udziel zgody administratora** , omówiony w dalszej części [przycisku zgody administratora](#admin-consent-button) .

### <a name="configure-client-credentials"></a>Konfigurowanie poświadczeń klienta

Aplikacje korzystające z uprawnień aplikacji są uwierzytelniane jako same przy użyciu własnych poświadczeń, bez konieczności interakcji z użytkownikiem. Zanim aplikacja (lub interfejs API) będzie mogła uzyskać dostęp do Microsoft Graph, własnego interfejsu API sieci Web lub innego interfejsu API za pomocą uprawnień aplikacji, należy skonfigurować poświadczenia aplikacji klienta.

Aby uzyskać więcej informacji na temat konfigurowania poświadczeń aplikacji, zobacz sekcję [Dodawanie poświadczeń](quickstart-register-app.md#add-credentials) w [przewodniku szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Więcej informacji na temat uprawnień interfejsu API i zgody administratora

Okienko **uprawnienia interfejsu API** rejestracji aplikacji zawiera [skonfigurowaną tabelę uprawnień](#configured-permissions) i może również zawierać [inną przyznanych uprawnień](#other-permissions-granted) tabelę. Obie tabele i [przyciski zgody administratora](#admin-consent-button) są opisane w poniższych sekcjach.

### <a name="configured-permissions"></a>Skonfigurowane uprawnienia

W tabeli **skonfigurowane uprawnienia** w okienku **uprawnienia interfejsu API** zostanie wyświetlona lista uprawnień wymaganych przez aplikację dla operacji Basic — *wymagana lista dostępu do zasobów* (RRA). Użytkownicy lub Administratorzy będą musieli wyrazić zgodę na te uprawnienia przed użyciem aplikacji. Inne uprawnienia opcjonalne można żądać później w czasie wykonywania (przy użyciu zgody dynamicznej).

Jest to minimalna lista uprawnień, które użytkownicy będą musieli wyrazić zgodę na aplikację. Może się tak zdarzyć, ale zawsze będzie to wymagane. Aby zapewnić bezpieczeństwo i pomóc użytkownikom i administratorom w większym zakresie przy użyciu aplikacji, nigdy nie podawaj nic, co nie jest potrzebne.

Uprawnienia, które pojawiają się w tej tabeli, można dodać lub usunąć, wykonując czynności opisane powyżej lub z [innych przyznanych uprawnień](#other-permissions-granted) (opisanych w następnej sekcji). Jako administrator możesz udzielić zgody administratora na pełny zestaw uprawnień interfejsu API, które znajdują się w tabeli, i odwołać zgodę na poszczególne uprawnienia.

### <a name="other-permissions-granted"></a>Inne uprawnienia przyznane

Zobaczysz również tabelę z **innymi uprawnieniami udzielonymi dla {Twoja dzierżawa}** w okienku **uprawnienia interfejsu API** . **Inne uprawnienia przyznane dla {Twoja dzierżawca}** przedstawiają uprawnienia udzielone dzierżawcy, które nie zostały jawnie skonfigurowane w obiekcie aplikacji. Te uprawnienia są dynamicznie wymagane i wyrażane zgodą na. Ta sekcja pojawia się tylko wtedy, gdy istnieje co najmniej jedno uprawnienie, które ma zastosowanie.

Można dodać pełny zestaw uprawnień interfejsu API lub poszczególnych uprawnień, które są wyświetlane w tej tabeli, do **skonfigurowanej tabeli uprawnień** . Jako administrator możesz odwołać zgodę administratora na interfejsy API lub poszczególne uprawnienia w tej sekcji.

### <a name="admin-consent-button"></a>Przycisk zgody administratora

**Przyznanie zgody administratora na przycisk {Twoja dzierżawca}** umożliwia administratorowi udzielenie zgody administratora na uprawnienia skonfigurowane dla aplikacji. Po wybraniu przycisku zostanie wyświetlone okno dialogowe z prośbą o potwierdzenie akcji zgody.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Przycisk Udziel zgody administratora wyróżniony w okienku skonfigurowane uprawnienia w Azure Portal":::

Po udzieleniu zgody uprawnienia wymagane do zgody administratora są przedstawiane jako posiadające zgodę:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Konfigurowanie tabeli uprawnień w Azure Portal pokazywania zgody administratora przydzielonej na pliki. odczyt. wszystkie uprawnienia":::

Przycisk **Udziel zgody administratora** jest *wyłączony* , jeśli nie jesteś administratorem lub nie skonfigurowano żadnych uprawnień dla aplikacji. Jeśli masz uprawnienia, które zostały przyznane, ale jeszcze nie skonfigurowano, przycisk zgody administratora poprosi o obsługę tych uprawnień. Można je dodać do skonfigurowanych uprawnień lub je usunąć.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego przewodnika Szybki Start w serii, aby dowiedzieć się, jak skonfigurować typy kont, które mogą uzyskiwać dostęp do aplikacji. Na przykład możesz chcieć ograniczyć dostęp tylko do tych użytkowników w organizacji (z jedną dzierżawą) lub zezwolić użytkownikom w innych dzierżawach usługi Azure AD (z wieloma dzierżawcami) oraz z osobistymi kontami Microsoft (MSA).

> [!div class="nextstepaction"]
> [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
