---
title: Zarządzanie usługą współpracy w chmurze — rozwiązania partnerskie platformy Azure
description: W tym artykule opisano zarządzanie chmurą dodaną na Azure Portal. Jak skonfigurować Logowanie jednokrotne, usunąć organizację i uzyskać pomoc techniczną.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989106"
---
# <a name="manage-the-confluent-cloud-resource"></a>Zarządzanie zasobem chmury

W tym artykule opisano sposób zarządzania wystąpieniem Apache Kafka w przypadku korzystania z chmury na platformie Azure. Pokazano, jak skonfigurować Logowanie jednokrotne (SSO), usunąć organizację i utworzyć żądanie pomocy technicznej.

## <a name="single-sign-on"></a>Logowanie jednokrotne

Aby zaimplementować Logowanie jednokrotne w organizacji, Administrator dzierżawy może zaimportować aplikację galerii. Ta czynność jest opcjonalna. Aby uzyskać informacje na temat importowania aplikacji, zobacz [Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Gdy administrator dzierżawy zaimportuje aplikację, użytkownicy nie muszą jawnie wyrazić zgody na dostęp do portalu w chmurze.

Aby włączyć logowanie jednokrotne, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **omówienia** wystąpienia zasobu z chmurą.
1. Wybierz łącze, które ma być **zarządzane w chmurze z** systemem.

   :::image type="content" source="media/sso-link.png" alt-text="Logowanie jednokrotne w portalu.":::

1. Jeśli administrator dzierżawy nie zaimportował aplikacji galerii do zgody na logowanie jednokrotne, Udziel uprawnień i zgody. Ten krok jest wymagany tylko przy pierwszym dostępie do linku do **zarządzania w chmurze**.
1. Wybierz konto usługi Azure AD na potrzeby logowania jednokrotnego do portalu z obsługą chmury.
1. Po udzieleniu zgody nastąpi przekierowanie do portalu z chmurą.

## <a name="set-up-cluster"></a>Konfigurowanie klastra

Aby uzyskać informacje na temat konfigurowania klastra, zobacz [Tworzenie klastra w dokumentacji z obsługą chmury](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Usuń organizację z organizacją

Gdy zasób w chmurze nie jest już potrzebny, usuń go na platformie Azure i w chmurze.

Aby usunąć zasoby na platformie Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **wszystkie zasoby** i **Filtruj według nazwy** zasobu w chmurze lub **typu zasobu** _._ Lub w Azure Portal, wyszukaj nazwę zasobu.
1. W obszarze **Przegląd** zasobu wybierz pozycję **Usuń**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ikona usunięcia zasobu.":::

1. Aby potwierdzić usunięcie, wpisz nazwę zasobu i wybierz pozycję **Usuń**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Monituj o potwierdzenie usunięcia zasobu.":::

Aby usunąć zasób z usługi w chmurze, zapoznaj się z dokumentacją dotyczącą [środowisk w chmurze](https://docs.confluent.io/current/cloud/using/environments.html) , która jest oparta na dokumentacji i w oparciu o [Informacje podstawowe w chmurze — z dokumentacją](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Klaster i wszystkie dane w klastrze są trwale usuwane. Jeśli kontrakt zawiera klauzulę przechowywania danych, usługa nofluent utrzymuje dane przez okres określony w dokumentacji dotyczącej [usługi](https://www.confluent.io/confluent-cloud-tos).

Opłaty są naliczane proporcjonalnie do czasu usunięcia klastra. Po ostatecznym usunięciu klastra zostanie wysłane potwierdzenie wiadomości e-mail.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby przesłać żądanie pomocy technicznej, skontaktuj się z [pomocą techniczną](https://support.confluent.io) lub Przekaż żądanie za pomocą portalu, jak pokazano poniżej.

> [!NOTE]
> Po raz pierwszy użytkownicy zresetuj hasło przed zalogowaniem się do portalu pomocy technicznej. Jeśli nie masz konta z chmurą, Wyślij wiadomość e-mail na adres, aby `cloud-support@confluent.io` uzyskać dalszą pomoc.

W portalu możesz przesłać żądanie za pośrednictwem pomocy i pomocy technicznej platformy Azure lub bezpośrednio z wystąpienia Apache Kafka w celu uzyskania chmury na platformie Azure.

Aby przesłać żądanie za pomocą pomocy i pomocy technicznej platformy Azure:

1. Wybierz pozycję **Pomoc i obsługa techniczna**.
1. Wybierz pozycję **Utwórz żądanie obsługi**.
1. W formularzu wybierz pozycję **techniczne** dla opcji **typ problemu**. Wybierz subskrypcję. Na liście usług wybierz pozycję **na platformie Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Utwórz żądanie pomocy technicznej, aby uzyskać pomoc.":::

Aby przesłać żądanie z zasobu, wykonaj następujące kroki:

1. W Azure Portal wybierz organizację, w której znajduje się Twoja organizacja.
1. Z menu po lewej stronie ekranu wybierz pozycję **nowe żądanie obsługi**.
1. Aby utworzyć żądanie pomocy technicznej, wybierz link do **portalu** z systemem.

    :::image type="content" source="media/support-request.png" alt-text="Utwórz żądanie obsługi z wystąpienia.":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, zobacz [Apache Kafka rozwiązywania problemów z rozwiązaniami w chmurze](troubleshoot.md).
