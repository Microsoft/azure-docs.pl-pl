---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w Azure Monitor — konfiguracje platformy Azure
description: W tym artykule opisano, jak skonfigurować platformę Azure w celu połączenia produktów/usług narzędzia ITSM z bezpiecznym eksportem w Azure Monitor w celu centralnego monitorowania i zarządzania narzędzia ITSM elementami roboczymi.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037544"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Konfigurowanie platformy Azure do łączenia narzędzi Narzędzia ITSM przy użyciu funkcji bezpiecznego eksportowania

Ten artykuł zawiera informacje o sposobie konfigurowania platformy Azure w celu używania "bezpiecznego eksportu".
Aby użyć "bezpiecznego eksportu", wykonaj następujące czynności:

1. [Zarejestruj swoją aplikację w usłudze Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Zdefiniuj jednostkę usługi.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Utwórz grupę akcji bezpiecznego elementu webhook.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Skonfiguruj środowisko partnerskie.
    Bezpieczny eksport obsługuje połączenia z następującymi narzędziami narzędzia ITSM:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [Kontroler BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Zarejestruj się w Azure Active Directory

Wykonaj następujące kroki, aby zarejestrować aplikację w usłudze Azure AD:

1. Wykonaj czynności opisane w dokumencie [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. W usłudze Azure AD wybierz opcję **Uwidocznij aplikację**.
3. Wybierz pozycję **Ustaw** dla **identyfikatora aplikacji identyfikator URI**.

   [![Zrzut ekranu przedstawiający opcję Ustawienia U R I aplikacji I D.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Wybierz pozycję **Zapisz**.

## <a name="define-service-principal"></a>Definiowanie nazwy głównej usługi

Usługa grupy akcji jest aplikacją pierwszej firmy, dlatego ma uprawnienie do uzyskiwania tokenów uwierzytelniania z aplikacji usługi AAD w celu uwierzytelniania za pomocą tej platformy.
Opcjonalnie można zdefiniować rolę aplikacji w manifeście utworzonej aplikacji, co może umożliwić dalsze ograniczanie dostępu w taki sposób, aby tylko niektóre aplikacje z daną rolą mogły wysyłać komunikaty. Ta rola musi być następnie przypisana do jednostki usługi grupy akcji (wymaga uprawnień administratora dzierżawy).

Ten krok można wykonać za pomocą tych samych [poleceń programu PowerShell](../alerts/action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Tworzenie grupy akcji bezpiecznego elementu webhook

Po zarejestrowaniu aplikacji w usłudze Azure AD można utworzyć elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure za pomocą akcji bezpiecznego elementu webhook w grupach akcji.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w Azure Portal.
Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](../alerts/action-groups.md).

Aby dodać element webhook do akcji, wykonaj następujące instrukcje dotyczące bezpiecznego elementu webhook:

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz pozycję **monitor**. Okienko **monitorowanie** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.
2. Wybierz kolejno pozycje **alerty**  >  **Zarządzanie akcje**.
3. Wybierz pozycję [Dodaj grupę akcji](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)i wypełnij pola.
4. Wprowadź nazwę w polu **Nazwa grupy akcji** , a następnie wprowadź nazwę w polu **krótka nazwa** . Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.
5. Wybierz pozycję **bezpieczny element webhook**.
6. Wybierz następujące szczegóły:
   1. Wybierz identyfikator obiektu zarejestrowanego wystąpienia Azure Active Directory.
   2. W przypadku identyfikatora URI Wklej w adresie URL elementu webhook skopiowanym ze [środowiska narzędzia Narzędzia ITSM](#configure-the-itsm-tool-environment).
   3. Ustaw opcję **Włącz wspólny schemat alertów** na **wartość tak**. 

   Na poniższej ilustracji przedstawiono konfigurację przykładowej akcji bezpiecznego elementu webhook:

   ![Zrzut ekranu pokazujący bezpieczną akcję elementu webhook.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurowanie środowiska narzędzia Narzędzia ITSM

Konfiguracja zawiera dwa kroki:

1. Pobierz identyfikator URI dla definicji bezpiecznego eksportu.
2. Definicje zgodnie z przepływem narzędzia Narzędzia ITSM.

## <a name="next-steps"></a>Następne kroki

* [Konfiguracja bezpiecznego eksportu usługi ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
* [Konfiguracja bezpiecznego eksportu składnika BMC](./itsmc-secure-webhook-connections-bmc.md)
