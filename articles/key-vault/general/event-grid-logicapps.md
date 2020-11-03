---
title: Wyślij wiadomość e-mail, gdy Key Vault status tajnych zmian
description: Przewodnik po użyciu Logic Apps w celu reagowania na zmiany Key Vault wpisów tajnych
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284489"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Użyj Logic Apps do otrzymywania wiadomości e-mail o zmianach stanu wpisów tajnych magazynu kluczy

W tym przewodniku dowiesz się, jak odpowiedzieć na zdarzenia Azure Key Vault, które są odbierane za pośrednictwem [Azure Event Grid](../../event-grid/index.yml) przy użyciu [Azure Logic Apps](../../logic-apps/index.yml). Na koniec masz skonfigurowaną aplikację logiki Azure do wysyłania wiadomości e-mail z powiadomieniem za każdym razem, gdy wpis tajny zostanie utworzony w Azure Key Vault.

Omówienie integracji Azure Key Vault/Azure Event Grid można znaleźć w temacie [monitorowanie Key Vault z Azure Event Grid](event-grid-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwanego przez Azure Logic Apps (na przykład Office 365 Outlook). To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](/connectors).
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Magazyn kluczy w ramach subskrypcji platformy Azure. Nowy magazyn kluczy można szybko utworzyć, wykonując kroki z [zestawu i pobierając klucz tajny z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md).
- Zarejestrowano Event Grid jako dostawcę zasobów, zobacz [rejestracje dostawców zasobów](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Tworzenie aplikacji logiki za pomocą Event Grid

Najpierw Utwórz aplikację logiki za pomocą programu obsługi usługi Event Grid i Subskrybuj Azure Key Vault zdarzenia "SecretNewVersionCreated".

Aby utworzyć subskrypcję Azure Event Grid, wykonaj następujące kroki:

1. W Azure Portal przejdź do magazynu kluczy, wybierz pozycję **zdarzenia >** wprowadzenie i kliknij pozycję **Logic Apps**

    
    ![Strona Key Vault — zdarzenia](../media/eventgrid-logicapps-kvsubs.png)

1. W programie **Logic Apps Designer** Sprawdź poprawność połączenia i kliknij przycisk **Kontynuuj** . 
 
    ![Projektant aplikacji logiki — połączenie](../media/eventgrid-logicappdesigner1.png)

1. Na ekranie **po wystąpieniu zdarzenia zasobu** wykonaj następujące czynności:
    - Pozostaw nazwę **subskrypcji** i **zasobu** jako domyślną.
    - Wybierz pozycję **Microsoft. Resource magazyn. magazyny** dla **typu zasobu**.
    - W obszarze **Typ zdarzenia** wybierz pozycję **Microsoft. SecretNewVersionCreated** .

    ![Projektant aplikacji logiki — procedura obsługi zdarzeń](../media/eventgrid-logicappdesigner2.png)

1. Wybierz pozycję **+ nowy krok** spowoduje to otwarcie okna umożliwiającego wybranie akcji.
1. Wyszukaj nazwę **E-mail**. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. W tym samouczku jest stosowany **pakiet Office 365 Outlook**. Kroki dla innych dostawców poczty e-mail są podobne.
1. Wybierz akcję **Wyślij wiadomość e-mail (wersja 2)** .

   ![Projektant aplikacji logiki — Wyślij wiadomość e-mail](../media/eventgrid-logicappdesigner3.png)

1. Kompiluj szablon wiadomości e-mail:
    - **Do:** Wprowadź adres e-mail, aby otrzymywać wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, do którego masz dostęp, w celach testowych.
    - **Temat** i **Treść** : wpisz tekst wiadomości e-mail. Wybierz właściwości JSON przy użyciu narzędzia selektora, aby dodać zawartość dynamiczną na podstawie danych zdarzenia. Dane zdarzenia można pobrać przy użyciu polecenia `@{triggerBody()?['Data']}` .

    Twój szablon wiadomości e-mail może wyglądać podobnie do tego przykładu.

    ![Projektant aplikacji logiki — treść wiadomości e-mail](../media/eventgrid-logicappdesigner4.png)

8. Kliknij przycisk **Zapisz jako**.
9. Wprowadź **nazwę** nowej aplikacji logiki, a następnie kliknij przycisk **Utwórz**.
    
    ![Projektant aplikacji logiki — tworzenie](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

1.  Przejdź do magazynu kluczy na Azure Portal i wybierz pozycję **zdarzenia > subskrypcje zdarzeń**.  Sprawdź, czy utworzono nową subskrypcję
    
    ![Projektant aplikacji logiki — testowanie i weryfikowanie](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Przejdź do magazynu kluczy, wybierz pozycję wpisy **tajne** , a następnie wybierz pozycję **+ Generuj/Importuj**. Utwórz nowy wpis tajny dla celów testowych Nazwij klucz i Zachowaj pozostałe parametry w ustawieniach domyślnych.

    ![Key Vault — Utwórz klucz tajny](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na ekranie **Tworzenie wpisu tajnego** Podaj dowolną nazwę i dowolną wartość, a następnie wybierz pozycję **Utwórz**.

Po utworzeniu wpisu tajnego zostanie odebrana wiadomość e-mail ze skonfigurowanymi adresami.

## <a name="next-steps"></a>Następne kroki

- Przegląd: [monitorowanie Key Vault z Azure Event Grid](event-grid-overview.md)
- Instrukcje: [kierowanie powiadomień magazynu kluczy do Azure Automation](event-grid-tutorial.md).
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Dowiedz się więcej na temat usługi [Azure Event Grid](../../event-grid/index.yml).
- Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](../../logic-apps/index.yml).