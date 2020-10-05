---
title: Informacje o pojęciach związanych z repozytorium modeli usługi Azure IoT | Microsoft Docs
description: Jako deweloper rozwiązania lub Specjalista IT zapoznaj się z podstawowymi pojęciami dotyczącymi repozytorium modelu usługi Azure IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715065"
---
# <a name="azure-iot-model-repository"></a>Repozytorium modeli usługi Azure IoT

Repozytorium modeli usługi Azure IoT umożliwia konstruktorom urządzeń zarządzanie i udostępnianie modeli urządzeń IoT Plug and Play. Modele urządzeń to JSON LD dokumenty zdefiniowane przy użyciu [języka Digital bliźniaczych reprezentacji Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Modele przechowywane w usłudze repozytorium modeli mogą być udostępniane deweloperom rozwiązań prywatnie poprzez kontrolę dostępu lub publicznie bez konieczności uwierzytelniania i opracowywania rozwiązania IoT Plug and Play w chmurze.

> [!NOTE]
> Konstruktory urządzeń mogą zdecydować się na wdrożenie modeli urządzeń Plug and Play IoT bezpośrednio na urządzeniu, użycie modułów lub w module IoT Edge.

Dostęp do repozytorium modelu można uzyskać przy użyciu:

- Portal [repozytorium modelu usługi Azure IoT](https://aka.ms/iotmodelrepo)
- [Interfejs API REST repozytorium modelu usługi Azure IoT](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Polecenia repozytorium modelu IoT w interfejsie Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Modele publiczne

Publiczne modele przędzy cyfrowej przechowywane w repozytorium modelu są dostępne dla wszystkich użytkowników, którzy mogą korzystać z aplikacji i integrować ją bez uwierzytelniania. Ponadto modele publiczne pozwalają na otwieranie systemu dla twórców urządzeń i deweloperów rozwiązań w celu udostępniania i ponownego użycia modeli urządzeń Plug and Play IoT.

Zapoznaj się z sekcją [Publikuj model](#publish-a-model) w obszarze **modele firmy** , aby uzyskać instrukcje dotyczące publikowania modelu w repozytorium modelu w celu udostępnienia go jako publicznego.

Aby wyświetlić model publiczny przy użyciu portalu repozytorium modelu:

1. Przejdź do [portalu repozytorium modelu IoT platformy Azure](https://aka.ms/iotmodelrepo).

1. Wybierz pozycję Włącz, aby **wyświetlić modele publiczne**.

    ![Wyświetlanie modeli publicznych](./media/concepts-model-repository/public-models.png)

Aby programowo wyświetlić model publiczny za pomocą interfejsu API REST, zobacz artykuł Pobieranie dokumentacji interfejsu API REST [modelu](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) .

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Aby wyświetlić model publiczny za pomocą interfejsu wiersza polecenia, zobacz polecenie [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) interfejsu wiersza poleceń platformy Azure.

## <a name="company-models"></a>Modele firmy

Repozytorium modelu firmy jest dzierżawcą w repozytorium modelu usługi Azure IoT dla organizacji w celu tworzenia modeli cyfrowych i zarządzania nimi, które są tworzone przez użytkowników w ramach firmy lub organizacji. Modele firmy są dostępne tylko dla uwierzytelnionych użytkowników Twojej firmy lub organizacji. Administrator dzierżawy repozytorium modelu może przypisywać uprawnienia i kontrolować dostęp innych użytkowników w firmie lub organizacji do modeli w repozytorium modelu firmy.

### <a name="set-up-your-company-model-repository"></a>Konfigurowanie repozytorium modelu firmy

Użyj *konta służbowego Azure Active Directory (Azure AD)* , aby uzyskać dostęp do repozytorium modeli. Jeśli Twoja organizacja ma już dzierżawę usługi Azure AD, możesz użyć kont użytkowników i jednostek usługi z tej dzierżawy usługi Azure AD.

Aby dowiedzieć się, jak skonfigurować dzierżawę usługi Azure AD i jak utworzyć użytkownika lub nazwę główną usługi w dzierżawie usługi Azure AD, zobacz sekcję [Informacje dodatkowe](#additional-information) .

- Jeśli jesteś pierwszym użytkownikiem z Twojej organizacji, aby uzyskać dostęp do repozytorium modelu lub zalogować się do portalu, otrzymasz rolę **Administrator dzierżawy** . Ta rola umożliwia przypisywanie ról do innych użytkowników w dzierżawie repozytorium w organizacji.

- Do innych ról można przypisywać **Administrator dzierżawy** , takich jak **Odczytywanie modeli** czy **Tworzenie modeli**.

### <a name="understand-access-management"></a>Informacje o zarządzaniu dostępem

Poniższa tabela zawiera podsumowanie obsługiwanych możliwości w repozytorium modelu firmy i skojarzonych z nimi uprawnień:

| Możliwość  | Uprawnienie| Opis|
|-------------|-----------|------------|
|Odczytaj modele|Odczytaj modele|Domyślnie wszyscy użytkownicy w dzierżawie firmy mogą wyświetlać swoje modele firmy. Ponadto użytkownik może również wyświetlać modele prywatne udostępnione innym firmom.|
|Zarządzanie dostępem|Zarządzanie dostępem|Zarządzaj przypisaniem roli użytkownika (Dodaj lub Usuń) dla innych użytkowników w organizacji.|
|Tworzenie modeli|Tworzenie modeli|Utwórz modele w repozytorium modelu firmy.|
|Publikuj modele|Publikuj modele|Publikuj modele, aby udostępnić je innym osobom do wyświetlania modelu.|

Poniższa tabela zawiera podsumowanie obsługiwanych ról i ich możliwości w repozytorium modelu, które mogą być używane do zarządzania dostępem.

|Rola|Możliwość|
|----|----------|
|TenantAdministrator|Zarządzanie dostępem, odczytywanie modeli|
|Kreator|Tworzenie modeli, Odczytaj modele|
|Publisher|Publikuj modele, Odczytaj modele|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Przekazywanie tokenu zabezpieczającego podczas uzyskiwania dostępu do modeli firmy przy użyciu interfejsu API REST

Po wywołaniu interfejsów API REST w celu zarządzania modelami firmy prywatnymi lub udostępnionymi należy podać token autoryzacji dla nazwy głównej użytkownika lub usługi w formacie JWT. Zapoznaj się z sekcją [dodatkowe informacje](#additional-information) , aby dowiedzieć się, jak uzyskać token JWT dla nazwy głównej użytkownika lub usługi.

Token JWT musi być przekazywać w nagłówku HTTP autoryzacji w interfejsie API w przypadku określania modelu firmy lub modeli udostępnionych. Token JWT nie jest wymagany w przypadku modeli publicznych.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Wyświetl firmy lub modele udostępnione

Musisz być członkiem roli *czytnika* dzierżawy repozytorium lub model musi być współużytkowany przez Ciebie, aby można było odczytać model. Zobaczysz listę nieopublikowanych modeli, które zostały Ci udostępnione, i listę opublikowanych modeli, które zostały Ci udostępnione. Domyślnie użytkownicy mogą odczytywać modele firmy, modele, które zostały im udostępnione przez inne firmy, oraz wszystkie modele publiczne.

Aby wyświetlić firmę lub model współużytkowany przy użyciu portalu:

1. Zaloguj się do [portalu repozytorium modelu usługi Azure IoT](https://aka.ms/iotmodelrepo).

1. Rozwiń węzeł **modele firmy** — Wycofaj **publikację** w okienku po lewej stronie

    ![Wyświetlanie modeli firmy](./media/concepts-model-repository/view-company-models.png)

1. Rozwiń węzeł **modele udostępnione —** Wycofaj publikację w okienku po lewej stronie

    ![Wyświetlanie modeli udostępnionych](./media/concepts-model-repository/view-shared-models.png)

Aby wyświetlić firmę lub model współużytkowany przy użyciu interfejsu API REST, zobacz dokumentację interfejsu API REST usługi [Get Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) . Zobacz [przekazywanie tokenu zabezpieczającego podczas uzyskiwania dostępu do modeli firmy przy użyciu interfejsu API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) , aby uzyskać informacje o sposobach przekazywania do nagłówka autoryzacji JWT w żądaniu HTTP.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Aby wyświetlić model firmy lub model współużytkowany przy użyciu interfejsu wiersza polecenia, zobacz polecenie [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) interfejsu wiersza poleceń platformy Azure.

### <a name="manage-roles"></a>Zarządzanie rolami

Administrator dzierżawy może przypisać role do użytkowników w dzierżawie repozytorium, aby mogli tworzyć modele prywatne dla firmy lub organizacji, publikować modele lub zarządzać rolami dla innych użytkowników.

Aby dodać użytkownika do roli dzierżawy repozytorium modelu przy użyciu portalu:

1. Zaloguj się do [portalu repozytorium modelu usługi Azure IoT](https://aka.ms/iotmodelrepo).

1. W lewym okienku wybierz pozycję **Zarządzanie dostępem** , a następnie wybierz pozycję **+ Dodaj**. W okienku **Dodaj uprawnienia** wpisz adres służbowy użytkownika, który ma zostać dodany do roli:

    ![Dodaj adres służbowy](./media/concepts-model-repository/add-user.png)

1. Wybierz rolę, do której chcesz dodać użytkownika z listy rozwijanej **rola** . Następnie wybierz pozycję **Zapisz**:

    ![Wybierz rolę](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Przekaż model

Aby przekazać model do repozytorium modelu firmy, musisz być członkiem roli **Creator** dzierżawca repozytorium.

Te modele nie są publikowane i domyślnie są dostępne tylko dla użytkowników w organizacji. Można również udostępnić co najmniej jeden nieopublikowany model z użytkownikami zewnętrznymi.

Przekazane modele są niezmienne.

Identyfikatory modeli dla tych modeli muszą być globalnie unikatowe w ramach wszystkich dzierżawców repozytorium dla wszystkich przekazanych modeli.

Aby przekazać model przy użyciu portalu:

1. Zaloguj się do [portalu repozytorium modelu usługi Azure IoT](https://aka.ms/iotmodelrepo).

1. Rozwiń węzeł **modele firmy** w okienku po lewej stronie i wybierz pozycję **Utwórz model**. Następnie wybierz pozycję **Importuj plik JSON**.

    ![Utwórz model](./media/concepts-model-repository/create-model.png)

1. Wybierz plik, który chcesz przekazać. Jeśli portal pomyślnie sprawdzi swój model, wybierz pozycję **Zapisz**.

Aby przekazać model przy użyciu interfejsu API REST, zobacz [Create a model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API. Zobacz [przekazywanie tokenu zabezpieczającego podczas uzyskiwania dostępu do modeli firmy przy użyciu interfejsu API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) , aby uzyskać informacje o sposobach przekazywania do nagłówka autoryzacji JWT w żądaniu HTTP.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

Aby przekazać model przy użyciu interfejsu wiersza polecenia, zobacz Create The [model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) CLI platformy Azure.

### <a name="publish-a-model"></a>Publikowanie modelu

Aby opublikować model, muszą być spełnione następujące wymagania:

1. Aby opublikować model, organizacja musi być członkiem programu [Microsoft Partner Network](https://docs.microsoft.com/partner-center/). Aby utworzyć konto w Centrum partnerskim, zobacz [Tworzenie konta Centrum partnerskiego](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). Po zatwierdzeniu konta można opublikować modele. Aby uzyskać więcej informacji, zapoznaj się z tematem [Centrum partnerskie — często zadawane pytania.](https://support.microsoft.com/help/4340639/partner-center-account-faqs)

2. Użytkownik musi być członkiem roli *wydawcy* dzierżawy repozytorium.

Modele, które są tworzone i publikowane przez użytkowników w organizacji, są widoczne jako *opublikowane modele*. Modele te są publiczne i mogą znajdować się w ramach **modeli publicznych**.

Aby opublikować model przy użyciu portalu:

1. Zaloguj się do [portalu repozytorium modelu usługi Azure IoT](https://aka.ms/iotmodelrepo).

2. Rozwiń węzeł **modele firmy** w okienku po lewej stronie i wybierz model, który chcesz opublikować. Następnie wybierz pozycję **Opublikuj**.

    ![Publikowanie modelu](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Jeśli otrzymasz powiadomienie z informacją, że nie masz identyfikatora partnera firmy Microsoft (MPN), postępuj zgodnie z instrukcjami rejestracji w powiadomieniu. Aby uzyskać więcej informacji, zobacz Wymagania na początku tej sekcji.

Aby opublikować model przy użyciu interfejsu API REST, zapoznaj się z dokumentacją interfejsu API REST [publikacji](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) . Podaj parametr ciągu zapytania, `update-metadata=true` Aby opublikować model przy użyciu interfejsu API REST. Zobacz [przekazywanie tokenu zabezpieczającego podczas uzyskiwania dostępu do modeli firmy przy użyciu interfejsu API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) , aby uzyskać informacje o sposobach przekazywania do nagłówka autoryzacji JWT w żądaniu HTTP.

Aby opublikować model przy użyciu interfejsu wiersza polecenia, zobacz temat polecenie [publikowania modelu](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) platformy Azure.

> [!NOTE]
> Aby można było uruchomić testy certyfikacji, modele należy opublikować w repozytorium modeli. Aby dowiedzieć się więcej, zobacz [jak certyfikowanie urządzeń Plug and Play IoT](howto-certify-device.md).

### <a name="share-a-model"></a>Udostępnianie modelu

Można udostępnić modele firmowe utworzone przez użytkownika w ramach organizacji zewnętrznych. W ten sposób można zezwolić współpracownikom na wyświetlanie i opracowywanie rozwiązań z modelami firm prywatnych.

Na przykład producent urządzenia może chcieć zachować modele prywatne dla firmy lub organizacji. Mogą oni mieć klientów, którzy wymagają, że ich możliwości urządzenia pozostają poufne.

Udostępnianie modeli między firmami lub organizacjami umożliwia bezpieczny dostęp do modeli, które nie są publiczne.

Aby udostępnić model firmy przy użyciu portalu:

- Jeśli jesteś twórcą modelu, **udział** i **udostępnione** przyciski są aktywne podczas wyświetlania modelu w sekcji **modele firmy** .

    ![Model udostępniania](./media/concepts-model-repository/share-model.png)

- Aby udostępnić model użytkownikom zewnętrznym, wybierz pozycję **Udostępnij**. W okienku **Udostępnianie modelu** wprowadź adres e-mail użytkownika zewnętrznego i wybierz pozycję **Zapisz**.

- Aby wyświetlić użytkowników, którym udostępniono model, wybierz opcję **udostępnione za pomocą**.

- Aby zatrzymać udostępnianie modelu z określonym użytkownikiem, wybierz użytkownika z listy użytkowników w okienku **udostępnione z** . Następnie wybierz pozycję **Usuń** i potwierdź wybór po wyświetleniu monitu.

    ![Zatrzymaj udostępnianie](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Dodatkowe informacje

Podczas pracy z usługą Azure AD pomocne mogą okazać się następujące tematy:

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Tworzenie nowej dzierżawy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). Większość organizacji ma już dzierżawy usługi Azure AD.

- Aby dodać użytkowników lub Gości do dzierżawy usługi Azure AD, zobacz [Dodawanie lub usuwanie użytkowników przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Aby dodać jednostkę usługi do dzierżawy usługi Azure AD, zobacz [jak używać portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Aby dowiedzieć się, jak uzyskać token JWT z usługi Azure AD do użycia podczas wywoływania interfejsów API REST, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest zapoznanie się z [architekturą Plug and Play IoT](concepts-architecture.md).
