---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu Onfido
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą Onfido w celu weryfikacji identyfikatorów dokumentów i biometrii
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928634"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Onfido z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure AD B2C z usługą [Onfido](https://onfido.com/). Onfido to identyfikator dokumentu i aplikacja weryfikująca twarz biometryczną. Dzięki temu firmy mogą zaspokoić wymagania *klienta* i tożsamości w czasie rzeczywistym. Onfido używa zaawansowanej weryfikacji tożsamości opartej na formacie AI, która najpierw weryfikuje identyfikator zdjęcia, a następnie dopasowuje go do biometrii twarzy. To rozwiązanie wiąże się z tożsamością cyfrową dla swojej rzeczywistej osoby i zapewnia bezpieczne środowisko dołączania podczas zmniejszania oszustw.

W tym przykładzie nawiązujemy połączenie usługi Onfido w procesie rejestracji lub logowania w celu weryfikacji tożsamości. Świadome decyzje dotyczące produktu i usługi, do których użytkownik może uzyskać dostęp, odbywa się w oparciu o wyniki Onfido.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

- [Konto wersji próbnej](https://onfido.com/signup/)Onfido.

## <a name="scenario-description"></a>Opis scenariusza

Integracja Onfido obejmuje następujące składniki:

- Dzierżawa Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika w oparciu o zasady niestandardowe zdefiniowane w dzierżawie. Jest on również znany jako dostawca tożsamości. Hostuje aplikację kliencką Onfido, która gromadzi dokumenty użytkowników i przesyła je do usługi interfejsu API Onfido.

- Klient Onfido — konfigurowalne narzędzie do zbierania dokumentów klienta JavaScript wdrożone w innych stronach sieci Web. Gromadzi dokumenty i sprawdza wstępne kontrole, takie jak rozmiar i jakość dokumentu.

- Pośredni interfejs API REST — zawiera punkty końcowe dla dzierżawy Azure AD B2C do komunikacji z usługą interfejsu API Onfido, obsługę przetwarzania danych i przestrzeganie wymagań dotyczących zabezpieczeń obu.

- Usługa interfejsu API Onfido — usługa zaplecza świadczona przez Onfido, która zapisuje i weryfikuje dokumenty dostarczone przez użytkownika.

Na poniższym diagramie architektury przedstawiono implementację.

![zrzut ekranu przedstawiający diagram onfido-Architecture](media/partner-onfido/onfido-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownik tworzy nowe konto i wprowadza informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika. Aplikacja kliencka Onfido hostowana w Azure AD B2C przeprowadza wstępne sprawdzanie informacji o użytkowniku.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API Onfido. Następnie program wysyła ją do Onfido.
| 4. | Onfido zużywa informacje i przetwarza je w celu zweryfikowania tożsamości użytkowników. Następnie zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje w prawidłowym formacie JSON do Azure AD B2C.
| 6. | Azure AD B2C otrzymuje informacje z z interfejsu API platformy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="onboard-with-onfido"></a>Dołączanie do Onfido

1. Aby utworzyć konto Onfido, skontaktuj się z [Onfido](https://onfido.com/signup/).

2. Po utworzeniu konta Utwórz [klucz interfejsu API](https://documentation.onfido.com/). Klucze na żywo są rozliczane, jednak można użyć [kluczy piaskownicy do testowania](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) rozwiązania. Klucze piaskownicy generują tę samą strukturę wyników co klucze na żywo, jednak wyniki są zawsze wstępnie określone. Dokumenty nie są przetwarzane ani zapisywane.

>[!NOTE]
> Ten klucz będzie potrzebny później.

Aby uzyskać więcej informacji na temat Onfido, zobacz [dokumentację interfejsu API Onfido](https://documentation.onfido.com) i [Centrum deweloperów Onfido](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Konfigurowanie Azure AD B2C przy użyciu Onfido

### <a name="part-1---deploy-the-api"></a>Część 1 — wdrażanie interfejsu API

- Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, postępując zgodnie z tymi [instrukcjami](/visualstudio/deployment/quickstart-deploy-to-azure).
- Konfiguracja mechanizmu CORS, Dodaj **dozwolone Źródło** jako https://{your_tenant_name}. b2clogin. com

>[!NOTE]
>Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

#### <a name="adding-sensitive-configuration-settings"></a>Dodawanie poufnych ustawień konfiguracji

Ustawienia aplikacji można skonfigurować w [usłudze App Service na platformie Azure](../app-service/configure-common.md#configure-app-settings). Usługa App Service umożliwia bezpieczne skonfigurowanie ustawień bez sprawdzania ich w repozytorium. Interfejs API REST wymaga następujących ustawień:

| Nazwa ustawienia aplikacji | Element źródłowy | Uwagi |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Konto Onfido |

### <a name="part-2---deploy-the-ui"></a>Część 2 — wdrażanie interfejsu użytkownika

#### <a name="configure-your-storage-location"></a>Konfigurowanie lokalizacji magazynu

1. Konfigurowanie [kontenera magazynu obiektów BLOB na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Zapisz pliki interfejsu użytkownika z [folderu UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) do kontenera obiektów BLOB.

3. Zezwól na dostęp mechanizmu CORS do kontenera magazynu utworzonego przez następujące instrukcje:

   a. Przejdź do pozycji **Ustawienia**  > **dozwolone Źródło**, wprowadź `https://{your_tenant_name}.b2clogin.com` . Zastąp swoją nazwę dzierżawy nazwą swojej dzierżawy Azure AD B2C. Na przykład https://fabrikam.b2clogin.com . W przypadku wprowadzania nazwy dzierżawy używaj wszystkich małych liter.

   b. W przypadku **dozwolonych metod** wybierz pozycję `GET` i `PUT` .

   c. Wybierz pozycję **Zapisz**.

#### <a name="update-ui-files"></a>Aktualizowanie plików interfejsu użytkownika

1. W plikach interfejsu użytkownika przejdź do folderu [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Otwórz każdy plik HTML.

3. Znajdź i Zamień {interfejs użytkownika-obiekt BLOB-Container-URL} adresem URL, w którym znajdują się foldery interfejsu użytkownika **ocean_blue**, **rozkłu** i **zasobów**

4. Znajdź i Zamień ciąg {pośredni-API-URL} adresem URL pośredniczącej usługi aplikacji interfejsu API.

#### <a name="upload-your-files"></a>Przekaż pliki

1. Zapisz pliki interfejsu użytkownika z folderu UI do kontenera obiektów BLOB.

2. Użyj [Eksplorator usługi Azure Storage](../virtual-machines/disks-use-storage-explorer-managed-disks.md) do zarządzania plikami i uprawnieniami dostępu.

### <a name="part-3---configure-azure-ad-b2c"></a>Część 3 — Konfigurowanie Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Zastąp wartości konfiguracyjne

W podanych [zasadach niestandardowych](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)Znajdź następujące symbole zastępcze i zamień je na odpowiednie wartości z danego wystąpienia.

| Symbol zastępczy | Zamień na wartość | Przykład  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Krótka nazwa dzierżawy |  "yourtenant" z yourtenant.onmicrosoft.com |
| {your_tenantID} | TenantID dzierżawy Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF           |
| {your_tenant_IdentityExperienceFramework_appid}        | Identyfikator aplikacji IdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Identyfikator aplikacji ProxyIdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | Identyfikator aplikacji magazynu Twojej dzierżawy                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | Identyfikator obiektu aplikacji magazynu dzierżawy                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_app_insights_instrumentation_key} | Klucz Instrumentacji wystąpienia usługi App Insights *| 01234567-89ab-cdef-0123-456789ABCDEF|
|{your_ui_file_base_url}| Adres URL lokalizacji, w której znajdują się foldery **ocean_blue**, **ROZKŁ** i **Assets** interfejsu użytkownika | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | Adres URL skonfigurowanej usługi App Service                                             | `https://yourapp.azurewebsites.net`          |

* Szczegółowe informacje o aplikacji mogą znajdować się w innej dzierżawie. Ta czynność jest opcjonalna. Usuń odpowiednie TechnicalProfiles i OrchestrationSteps, jeśli nie jest to trzeba.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Część 4 — Konfigurowanie zasad Azure AD B2C

Zapoznaj się z tym [dokumentem](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , aby uzyskać instrukcje dotyczące sposobu konfigurowania dzierżawy usługi Azure AD B2C i konfigurowania zasad.

>[!NOTE]
> Najlepszym rozwiązaniem jest, aby klienci mogli dodać powiadomienie o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Usługa Onfido zostanie wywołana w przepływie, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
