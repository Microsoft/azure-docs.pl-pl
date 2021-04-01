---
title: Hostowanie aplikacji sieci Web platformy Azure ze zrównoważonym obciążeniem w wierzchołku strefy
description: Użyj rekordu aliasu Azure DNS, aby hostować aplikacje sieci Web z równoważeniem obciążenia w wierzchołku strefy
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 72adb2732eb0832589cbc25fb7e4288eb1899214
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954515"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hostowanie aplikacji sieci Web platformy Azure ze zrównoważonym obciążeniem w wierzchołku strefy

Protokół DNS uniemożliwia przypisanie elementów innych niż rekord A lub AAAA w wierzchołku strefy. Przykładem wierzchołka strefy jest contoso.com. To ograniczenie powoduje problem dla właścicieli aplikacji, którzy mają aplikacje o zrównoważonym obciążeniu za Traffic Manager. Nie można wskazać profilu Traffic Manager z rekordu wierzchołka strefy. W związku z tym właściciele aplikacji muszą używać obejścia. Przekierowanie w warstwie aplikacji musi przekierować ze wierzchołka strefy do innej domeny. Przykładem jest przekierowanie z contoso.com do www \. contoso.com. To rozmieszczenie przedstawia single point of failure funkcji redirect.

W przypadku rekordów aliasów ten problem już nie istnieje. Teraz właściciele aplikacji mogą wskazywać ich rekord wierzchołka strefy w profilu Traffic Manager, który ma zewnętrzne punkty końcowe. Właściciele aplikacji mogą wskazywać ten sam profil Traffic Manager, który jest używany przez dowolną inną domenę w ramach strefy DNS.

Na przykład contoso.com i www \. contoso.com mogą wskazywać na ten sam profil Traffic Manager. Jest to przypadek, o ile profil Traffic Manager ma skonfigurowane tylko zewnętrzne punkty końcowe.

W tym artykule dowiesz się, jak utworzyć rekord aliasu dla wierzchołka domeny i skonfigurować punkty końcowe Traffic Manager profilu dla aplikacji sieci Web.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale skorzystaj z własnej nazwy domeny.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której mają być przechowywane wszystkie zasoby używane w tym artykule.

## <a name="create-app-service-plans"></a>Tworzenie planów App Service

Utwórz dwa plany App Service sieci Web w grupie zasobów, korzystając z poniższej tabeli w celu uzyskania informacji o konfiguracji. Aby uzyskać więcej informacji na temat tworzenia planu App Service, zobacz [Zarządzanie planem App Service na platformie Azure](../app-service/app-service-plan-manage.md).


|Nazwa  |System operacyjny  |Lokalizacja  |Warstwa cenowa  |
|---------|---------|---------|---------|
|ASP — 01     |Windows|East US|D1-Shared tworzenia i testowania|
|ASP — 02     |Windows|Central US|D1-Shared tworzenia i testowania|

## <a name="create-app-services"></a>Utwórz App Services

Utwórz dwie aplikacje sieci Web, jeden w każdym planie App Service.

1. W lewym górnym rogu strony Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wpisz ciąg **aplikacja sieci Web** na pasku wyszukiwania i naciśnij klawisz ENTER.
3. Wybierz pozycję **aplikacja sieci Web**.
4. Wybierz przycisk **Utwórz**.
5. Zaakceptuj wartości domyślne i Skorzystaj z poniższej tabeli, aby skonfigurować dwie aplikacje sieci Web:

   |Nazwa<br>(musi być unikatowy w obrębie. azurewebsites.net)|Grupa zasobów |Stos środowiska uruchomieniowego|Region (Region)|App Service plan/lokalizacja
   |---------|---------|-|-|-------|
   |Aplikacja — 01|Użyj istniejącej<br>Wybierz grupę zasobów|.NET Core 2.2|East US|ASP — 01 (D1)|
   |App-02|Użyj istniejącej<br>Wybierz grupę zasobów|.NET Core 2.2|Central US|ASP-02 (D1)|

### <a name="gather-some-details"></a>Zbierz szczegóły

Teraz należy zanotować adres IP i nazwę hosta aplikacji sieci Web.

1. Otwórz grupę zasobów i wybierz pierwszą aplikację sieci Web (**App-01** w tym przykładzie).
2. W lewej kolumnie Wybierz pozycję **Właściwości**.
3. Zwróć uwagę na adres **URL**, a w obszarze **wychodzące adresy IP** Zanotuj pierwszy adres IP na liście. Te informacje będą używane później podczas konfigurowania punktów końcowych Traffic Manager.
4. Powtórz dla **aplikacji App-02**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil Traffic Manager w grupie zasobów. Użyj wartości domyślnych i wpisz unikatową nazwę w przestrzeni nazw trafficmanager.net.

Aby uzyskać informacje na temat tworzenia profilu Traffic Manager, zobacz [Szybki Start: Tworzenie profilu Traffic Manager dla aplikacji sieci Web o wysokiej](../traffic-manager/quickstart-create-traffic-manager-profile.md)dostępności.

### <a name="create-endpoints"></a>Tworzenie punktów końcowych

Teraz można utworzyć punkty końcowe dla dwóch aplikacji sieci Web.

1. Otwórz grupę zasobów i wybierz swój profil Traffic Manager.
2. W lewej kolumnie Wybierz pozycję **punkty końcowe**.
3. Wybierz pozycję **Dodaj**.
4. Skorzystaj z poniższej tabeli, aby skonfigurować punkty końcowe:

   |Typ  |Nazwa  |Cel  |Lokalizacja  |Niestandardowe ustawienia nagłówka|
   |---------|---------|---------|---------|---------|
   |Zewnętrzny punkt końcowy     |Koniec-01|Adres IP zarejestrowany dla aplikacji App-01|East US|Host:\<the URL you recorded for App-01\><br>Przykład: **host: App-01.azurewebsites.NET**|
   |Zewnętrzny punkt końcowy     |Koniec 02|Adres IP zarejestrowany dla aplikacji App-02|Central US|Host:\<the URL you recorded for App-02\><br>Przykład: **host: App-02.azurewebsites.NET**

## <a name="create-dns-zone"></a>Utwórz strefę DNS

Możesz użyć istniejącej strefy DNS do testowania lub można utworzyć nową strefę. Aby utworzyć i delegować nową strefę DNS na platformie Azure, zobacz [Samouczek: Hostowanie domeny w Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Dodawanie rekordu TXT na potrzeby weryfikacji domeny niestandardowej

Po dodaniu niestandardowej nazwy hosta do aplikacji sieci Web szuka określonego rekordu TXT w celu zweryfikowania domeny.

1. Otwórz grupę zasobów i wybierz strefę DNS.
2. Wybierz pozycję **Zestaw rekordów**.
3. Dodaj zestaw rekordów, korzystając z poniższej tabeli. Dla wartości Użyj rzeczywistego adresu URL aplikacji sieci Web, który został wcześniej zarejestrowany:

   |Nazwa  |Typ  |Wartość|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej

Dodaj domenę niestandardową dla obu aplikacji sieci Web.

1. Otwórz grupę zasobów i wybierz swoją pierwszą aplikację sieci Web.
2. W lewej kolumnie Wybierz pozycję **domeny niestandardowe**.
3. W obszarze **domeny niestandardowe** wybierz pozycję **Dodaj domenę niestandardową**.
4. W obszarze **domena niestandardowa** wpisz swoją niestandardową nazwę domeny. Na przykład contoso.com.
5. Wybierz przycisk **Weryfikuj**.

   Twoja domena powinna przekazywać walidację i wyświetlać zielone znaczniki wyboru obok pozycji **Nazwa hosta** i **własność domeny**.
5. Wybierz pozycję **Dodaj domenę niestandardową**.
6. Aby wyświetlić nową nazwę hosta w obszarze **nazwy hostów przypisanych do lokacji**, Odśwież przeglądarkę. Odświeżenie na stronie nie zawsze pokazuje zmiany od razu.
7. Powtórz tę procedurę dla drugiej aplikacji sieci Web.

## <a name="add-the-alias-record-set"></a>Dodaj zestaw rekordów aliasu

Teraz Dodaj rekord aliasu dla wierzchołka strefy.

1. Otwórz grupę zasobów i wybierz strefę DNS.
2. Wybierz pozycję **Zestaw rekordów**.
3. Dodaj zestaw rekordów, korzystając z następującej tabeli:

   |Nazwa  |Typ  |Zestaw rekordów aliasu  |Typ aliasu  |Zasób platformy Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Tak|Zasób platformy Azure|Traffic Manager — Twój profil|


## <a name="test-your-web-apps"></a>Testowanie aplikacji sieci Web

Teraz możesz przeprowadzić test, aby upewnić się, że można nawiązać połączenie z aplikacją sieci Web i że jest ona równoważna.

1. Otwórz przeglądarkę internetową i przejdź do swojej domeny. Na przykład contoso.com. Powinna zostać wyświetlona domyślna strona aplikacji sieci Web.
2. Zatrzymaj swoją pierwszą aplikację internetową.
3. Zamknij przeglądarkę internetową i odczekaj kilka minut.
4. Uruchom przeglądarkę internetową i przejdź do swojej domeny. Nadal powinna zostać wyświetlona domyślna strona aplikacji sieci Web.
5. Zatrzymywanie drugiej aplikacji sieci Web.
6. Zamknij przeglądarkę internetową i odczekaj kilka minut.
7. Uruchom przeglądarkę internetową i przejdź do swojej domeny. Powinien pojawić się błąd 403 wskazujący, że aplikacja sieci Web jest zatrzymana.
8. Uruchom drugą aplikację sieci Web.
9. Zamknij przeglądarkę internetową i odczekaj kilka minut.
10. Uruchom przeglądarkę internetową i przejdź do swojej domeny. Powinna zostać wyświetlona ponownie domyślna strona aplikacji sieci Web.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rekordów aliasów, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md)
- [Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](./dns-faq.md#alias-records)

Aby dowiedzieć się, jak przeprowadzić migrację aktywnej nazwy DNS, zobacz [Migrowanie aktywnej nazwy DNS do Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).