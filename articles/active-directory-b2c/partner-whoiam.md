---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu whoIam
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C za pomocą whoIam na potrzeby weryfikacji użytkownika
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817567"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania WhoIAM z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące konfigurowania systemu [WhoIAMego](https://www.whoiam.ai/brims/) zarządzania tożsamościami (BRIMS) w środowisku i integrowania go z Azure AD B2C.

WhoIAM BRIMS to zestaw aplikacji i usług, które są wdrażane w środowisku programu. Zawiera ona informacje o głosowaniu, wiadomościach SMS i weryfikacji na podstawie bazy użytkowników. BRIMS działa w połączeniu z istniejącym rozwiązaniem do zarządzania tożsamościami i dostępem oraz jest platformą niezależny od.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , która jest połączona z subskrypcją platformy Azure.

- [Konto wersji próbnej](https://www.whoiam.ai/contact-us/) WhoIAM

## <a name="scenario-description"></a>Opis scenariusza

Integracja WhoIAM obejmuje następujące składniki:

- Dzierżawa Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika w oparciu o zasady niestandardowe zdefiniowane w dzierżawie. Jest on również znany jako dostawca tożsamości.

- Portal administracyjny służący do zarządzania klientami i ich konfiguracjami

- Usługa interfejsu API, która udostępnia różne funkcje za poorednictwem punktów końcowych  

- Azure Cosmos DB używany jako zaplecze zarówno dla portalu administracji BRIMS, jak i usługi interfejsu API

Na poniższym diagramie architektury przedstawiono implementację.

![zrzut ekranu przedstawiający diagram whoiam-Architecture](media/partner-whoiam/whoiam-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownik uruchamia żądanie rejestracji lub logowania do aplikacji, która używa Azure AD B2C jako dostawcy tożsamości.
| 2. | W ramach uwierzytelniania użytkownik żąda potwierdzenia własności poczty e-mail lub telefonu lub użycia głosu jako współczynnika weryfikacji biometrycznej.  
| 3. | Azure AD B2C wykonuje wywołanie usługi API BRIMS na adres e-mail użytkownika, numer telefonu i nagranie głosu
| 4. | BRIMS używa wstępnie zdefiniowanych konfiguracji, takich jak w pełni dostosowywalne szablony poczty e-mail i programu SMS, aby współdziałać z użytkownikiem w odpowiednim języku, który jest zgodny z wyglądem i działaniem aplikacji.
| 5. | Po zakończeniu weryfikacji tożsamości użytkownika BRIMS zwraca token, aby Azure AD B2C wskazujący wynik weryfikacji. Azure AD B2C następnie udzieli użytkownikowi dostępu do aplikacji lub próba uwierzytelnienia nie powiedzie się.  

## <a name="onboard-with-whoiam"></a>Dołączanie do WhoIAM

1. Skontaktuj się z [WhoIAM](https://www.whoiam.ai/contact-us/) i Utwórz konto BRIMS.

2. Po utworzeniu konta należy użyć dostępnych wytycznych dotyczących dołączania i skonfigurować następujące usługi platformy Azure:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) — służy do bezpiecznego przechowywania haseł, takich jak hasła usługi poczty.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) — używany do HOSTOWANIA interfejsu API BRIMS i usług portalu administracyjnego

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) — służy do uwierzytelniania użytkowników administracyjnych w portalu administracyjnym.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) — służy do przechowywania i pobierania ustawień

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (opcjonalnie) — służy do logowania się do zarówno interfejsu API, jak i portalu administracyjnego

3. Wdróż interfejs API BRIMS i Portal administracyjny BRIMS w środowisku platformy Azure.

4. Azure AD B2C niestandardowe przykłady zasad są dostępne w dokumentacji dołączania do BRIMS. Postępuj zgodnie z dokumentem, aby skonfigurować aplikację i korzystać z platformy BRIMS na potrzeby weryfikacji tożsamości użytkowników.  

Aby uzyskać więcej informacji na temat BRIMS WhoIAM, zobacz [dokumentację produktu](https://www.whoiam.ai/brims/) .

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Usługa BRIMS zostanie wywołana w przepływie, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
