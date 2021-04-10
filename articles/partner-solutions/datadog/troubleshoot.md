---
title: Rozwiązywanie problemów z rozwiązaniami partnerskimi usługi Datadog — Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z programem usługi Datadog na platformie Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563712"
---
# <a name="troubleshooting-datadog-on-azure"></a>Rozwiązywanie problemów z usługą Datadog na platformie Azure

Ten dokument zawiera informacje dotyczące rozwiązywania problemów z rozwiązaniami korzystającymi z usługi Datadog.

## <a name="purchase-errors"></a>Błędy zakupów

* Zakup nie powiódł się, ponieważ ważna karta kredytowa nie jest połączona z subskrypcją platformy Azure lub nie jest skojarzona z subskrypcją.

  Użyj innej subskrypcji platformy Azure. Można też dodać lub zaktualizować kartę kredytową lub formę płatności dla subskrypcji. Aby uzyskać więcej informacji, zobacz [Aktualizowanie kredytu i formy płatności](../../cost-management-billing/manage/change-credit-card.md).

* Subskrypcja EA nie zezwala na zakupy w portalu Marketplace.

  Użyj innej subskrypcji. Lub sprawdź, czy subskrypcja EA jest włączona dla zakupu w witrynie Marketplace. Aby uzyskać więcej informacji, zobacz [Włączanie zakupów w portalu Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Jeśli te opcje nie rozwiązują problemu, skontaktuj się z [pomocą techniczną usługi Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Nie można utworzyć zasobu usługi Datadog

Aby skonfigurować integrację z usługą Azure usługi Datadog, musisz mieć dostęp **właściciela** do subskrypcji platformy Azure. Przed rozpoczęciem instalacji upewnij się, że masz odpowiedni dostęp.

## <a name="single-sign-on-errors"></a>Błędy logowania jednokrotnego

**Nie można zapisać ustawień logowania** jednokrotnego — ten błąd występuje, gdy istnieje inna aplikacja dla przedsiębiorstw, która używa identyfikatora SAML usługi Datadog. Aby sprawdzić, która aplikacja korzysta z niej, wybierz pozycję **Edytuj** w sekcji Podstawowa konfiguracja protokołu SAML.

Aby rozwiązać ten problem, wyłącz inną aplikację lub użyj innej aplikacji jako aplikacji przedsiębiorstwa, aby skonfigurować protokół SAML SSO z usługi Datadog. Jeśli zdecydujesz się użyć innej aplikacji, upewnij się, że aplikacja ma [wymagane ustawienia](create.md#configure-single-sign-on).

**Aplikacja nie jest wyświetlana na stronie Ustawienia rejestracji** jednokrotnej — najpierw wyszukaj identyfikator aplikacji. Jeśli wynik nie jest wyświetlany, sprawdź ustawienia SAML aplikacji. Siatka pokazuje tylko aplikacje z prawidłowymi ustawieniami protokołu SAML. 

Adres URL identyfikatora musi mieć wartość `https://us3.datadoghq.com/account/saml/metadata.xml` .

Adres URL odpowiedzi musi mieć wartość `https://us3.datadoghq.com/account/saml/assertion` .

Na poniższej ilustracji przedstawiono poprawne wartości.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Sprawdź ustawienia protokołu SAML dla aplikacji usługi Datadog w usłudze AAD." border="true":::

**Użytkownicy-Goście zaproszeni do dzierżawy nie mogą uzyskać dostępu do logowania jednokrotnego** — niektórzy użytkownicy mają dwa adresy e-mail w Azure Portal. Zazwyczaj jedna wiadomość e-mail to główna nazwa użytkownika (UPN), a druga — alternatywny adres e-mail.

Podczas zapraszania użytkownika-gościa należy użyć głównej nazwy UPN dzierżawy. Przy użyciu nazwy UPN będziesz utrzymywać w synchronizacji adres e-mail w trakcie procesu logowania jednokrotnego. Nazwę UPN można znaleźć, wyszukując adres e-mail w prawym górnym rogu Azure Portal użytkownika.
  
## <a name="logs-not-being-emitted"></a>Dzienniki, które nie są emitowane

Tylko zasoby wymienione w kategorii dziennika zasobów Azure Monitor emitują dzienniki do usługi Datadog. Aby sprawdzić, czy zasób emituje dzienniki do usługi Datadog, przejdź do ustawień diagnostycznych platformy Azure dla określonego zasobu. Sprawdź, czy jest dostępne ustawienie diagnostyczne usługi Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Ustawienie diagnostyki usługi Datadog w zasobie platformy Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Metryki nie są emitowane

Do zasobu usługi Datadog przypisano rolę **czytnika monitorowania** w odpowiedniej subskrypcji platformy Azure. Ta rola umożliwia zasobowi usługi Datadog zbieranie metryk i wysyłanie tych metryk do usługi Datadog.

Aby sprawdzić, czy zasób ma poprawne przypisanie roli, Otwórz Azure Portal i wybierz subskrypcję. W lewym okienku wybierz pozycję **Access Control (IAM)**. Wyszukaj nazwę zasobu usługi Datadog. Upewnij się, że zasób usługi Datadog ma przypisanie roli **czytnika monitorowania** , jak pokazano poniżej.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Przypisanie roli usługi Datadog w ramach subskrypcji platformy Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>Niepowodzenie instalacji agenta usługi Datadog

Integracja z usługą Azure usługi Datadog umożliwia zainstalowanie agenta usługi Datadog na maszynie wirtualnej lub usłudze App Service. W przypadku konfigurowania agenta usługi Datadog jest używany klucz interfejsu API wybrany jako **klucz domyślny** na ekranie klucze interfejsu API. Jeśli nie wybrano klucza domyślnego, Instalacja agenta usługi Datadog zakończy się niepowodzeniem.

Jeśli Agent usługi Datadog został skonfigurowany z nieprawidłowym kluczem, przejdź do ekranu klucze interfejsu API i Zmień **klucz domyślny**. Musisz odinstalować agenta usługi Datadog i zainstalować go ponownie, aby skonfigurować maszynę wirtualną przy użyciu nowych kluczy interfejsu API.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzaniu wystąpieniem](manage.md) programu usługi Datadog.
