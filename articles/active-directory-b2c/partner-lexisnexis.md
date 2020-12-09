---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu LexisNexis
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą LexisNexis, która jest funkcją profilowania i weryfikacją tożsamości i służy do weryfikowania identyfikacji użytkowników oraz zapewnienia kompleksowych ocen ryzyka opartych na urządzeniu użytkownika.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 4befcaf6b8c6b6bc4fddacb179883551609f7e19
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928651"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania LexisNexis z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure AD B2C z usługą [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis oferuje różne rozwiązania, można je znaleźć w [tym miejscu](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). W tym przykładowym samouczku omówiono rozwiązanie **ThreatMetrix** z LexisNexis. ThreatMetrix to usługa profilowania i weryfikacji tożsamości. Służy on do weryfikowania identyfikacji użytkowników i zapewnienia kompleksowych ocen ryzyka opartych na urządzeniu użytkownika.

Ta integracja służy do profilowania w oparciu o kilka informacji o użytkownikach, które są udostępniane przez użytkownika podczas przepływu rejestracji. ThreatMetrix określa, czy użytkownik powinien mieć możliwość dalszej rejestracji. Następujące atrybuty są brane pod uwagę w ThreatMetrixej analizie ryzyka:

- E-mail
- Numer telefonu
- Informacje profilowania zbierane z komputera użytkownika

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Integracja ThreatMetrix obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika, znanego również jako dostawca tożsamości

- ThreatMetrix — usługa ThreatMetrix pobiera dane wejściowe podane przez użytkownika i łączy je z informacjami profilowania zebranymi na komputerze użytkownika w celu sprawdzenia bezpieczeństwa interakcji z użytkownikiem.

- Niestandardowy interfejs API REST — ten interfejs API implementuje integrację między Azure AD B2C a usługą ThreatMetrix.

Na poniższym diagramie architektury przedstawiono implementację.

![zrzut ekranu przedstawiający diagram LexisNexis-Architecture](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Krok | Opis |
|:--------------|:-------------|
|1. | Użytkownik dotarł do strony logowania. Użytkownik wybierze opcję rejestracji, aby utworzyć nowe konto, a następnie wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API LexisNexis. Następnie program wysyła ją do LexisNexis.  
| 4. | LexisNexis zużywa informacje i przetwarza je w celu weryfikacji tożsamości użytkowników na podstawie analizy ryzyka. Następnie zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje do Azure AD B2C.
| 6. | Azure AD B2C otrzymuje informacje z z interfejsu API platformy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i udzielony.

## <a name="onboard-with-lexisnexis"></a>Dołączanie do LexisNexis

1. Aby utworzyć konto LexisNexis, skontaktuj się z [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Utwórz zasady LexisNexis, które spełniają Twoje wymagania. Skorzystaj z dokumentacji dostępnej [tutaj](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd).

>[!NOTE]
> Nazwa zasad zostanie użyta później.

Po utworzeniu konta otrzymasz informacje potrzebne do konfiguracji interfejsu API. W poniższych sekcjach opisano proces.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Konfigurowanie Azure AD B2C przy użyciu LexisNexis

### <a name="part-1---deploy-the-api"></a>Część 1 — wdrażanie interfejsu API

Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, postępując zgodnie z tymi [instrukcjami](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

### <a name="part-2---configure-the-api"></a>Część 2 — Konfigurowanie interfejsu API

Ustawienia aplikacji można [skonfigurować w usłudze App Service na platformie Azure](../app-service/configure-common.md#configure-app-settings).  Za pomocą tej metody ustawienia można bezpiecznie skonfigurować bez sprawdzania ich w repozytorium. Należy podać następujące ustawienia w interfejsie API REST:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|ThreatMetrix: adres URL | Konfiguracja konta ThreatMetrix |     |
|ThreatMetrix: OrgId | Konfiguracja konta ThreatMetrix |     |
|ThreatMetrix:ApiKey |Konfiguracja konta ThreatMetrix|  |
|ThreatMetrix: zasady | Nazwa zasad utworzonych w ThreatMetrix | |
| BasicAuth:ApiUsername |Zdefiniuj nazwę użytkownika dla interfejsu API| Nazwa użytkownika zostanie użyta w konfiguracji Azure AD B2C
| BasicAuth:ApiPassword | Definiowanie hasła dla interfejsu API | Hasło będzie używane w konfiguracji Azure AD B2C

### <a name="part-3---deploy-the-ui"></a>Część 3 — wdrażanie interfejsu użytkownika

To rozwiązanie używa niestandardowych szablonów interfejsu użytkownika, które są ładowane przez Azure AD B2C. Te szablony interfejsu użytkownika to profilowanie wysyłane bezpośrednio do usługi ThreatMetrix.

Zapoznaj się z tymi [instrukcjami](./custom-policy-ui-customization.md#custom-page-content-walkthrough) , aby wdrożyć dołączone [pliki interfejsu użytkownika](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) na koncie magazynu obiektów BLOB. Instrukcje obejmują Konfigurowanie konta magazynu obiektów blob, Konfigurowanie mechanizmu CORS i Włączanie dostępu publicznego.

Interfejs użytkownika jest oparty na [niebieskim szablonie oceanu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Wszystkie linki w interfejsie użytkownika należy zaktualizować, aby odwoływać się do wdrożonej lokalizacji. W folderze UI Znajdź i Zamień na https://yourblobstorage/blobcontainer wdrożoną lokalizację.

### <a name="part-4---create-api-policy-keys"></a>Część 4 — Tworzenie kluczy zasad interfejsu API

Zapoznaj się z tym [dokumentem](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) i Utwórz dwa klucze zasad — jeden dla nazwy użytkownika interfejsu API, a drugi dla hasła interfejsu API zdefiniowanego powyżej.

Przykładowe zasady używają tych nazw kluczy:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Część 5 — Aktualizowanie adresu URL interfejsu API

W podanych [zasadach TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)Znajdź profil techniczny `Rest-LexisNexus-SessionQuery` i zaktualizuj `ServiceUrl` element metadanych przy użyciu lokalizacji wdrożonego powyżej interfejsu API.

### <a name="part-6---update-ui-url"></a>Część 6 — aktualizowanie adresu URL interfejsu użytkownika

W podanych [zasadach TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)wykonaj Znajdź i Zamień, aby wyszukać https://yourblobstorage/blobcontainer/ lokalizację, w której są WDRAŻANE pliki interfejsu użytkownika.

>[!NOTE]
> Najlepszym rozwiązaniem jest, aby klienci mogli dodać powiadomienie o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Część 7 — Konfigurowanie zasad Azure AD B2C

Zapoznaj się z tym [dokumentem](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , aby pobrać [pakiet startowy kont lokalnych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) i skonfigurować [zasady](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) dla dzierżawy Azure AD B2C.

>[!NOTE]
>Aktualizowanie podanych zasad w celu odłożenia względem określonej dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony **przepływ użytkownika**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Wyloguj

6. Przejdź przez przepływ logowania  

7. ThreatMetrix układanki zostanie wyświetlona po wprowadzeniu **dalej**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
