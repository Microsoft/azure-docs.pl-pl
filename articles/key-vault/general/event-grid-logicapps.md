---
title: Wysyłanie wiadomości e-mail po zmianie stanu magazynu kluczy
description: Przewodnik dotyczący używania aplikacji logiki do reagowania na zmiany wpisów tajnych usługi Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 53e8586486d9a9ebf870de350d5607f58977c0f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423277"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Korzystanie z aplikacji logiki w celu otrzymywania wiadomości e-mail o zmianach stanu wpisów tajnych magazynu kluczy

W tym przewodniku dowiesz się, jak reagować na zdarzenia usługi Azure Key Vault, które są odbierane za pośrednictwem [usługi Azure Event Grid](../../event-grid/index.yml) przy użyciu usługi Azure Logic [Apps.](../../logic-apps/index.yml) Na koniec zostanie skonfigurowana aplikacja logiki platformy Azure do wysyłania wiadomości e-mail z powiadomieniem za każdym razem, gdy w usłudze Azure Key Vault zostanie utworzony klucz tajny.

Aby zapoznać się z omówieniem integracji usługi Azure Key Vault / Azure Event Grid, zobacz [Monitorowanie usługi Key Vault za pomocą usługi Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwanego przez usługę Azure Logic Apps (na przykład usługę Office 365 Outlook). To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](/connectors).
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Magazyn kluczy w subskrypcji platformy Azure. Możesz szybko utworzyć nowy magazyn kluczy, wykonując kroki opisane w [obszarze Ustaw i pobierając klucz tajny z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.](../secrets/quick-create-cli.md)

## <a name="create-a-logic-app-via-event-grid"></a>Tworzenie aplikacji logiki za pośrednictwem siatki zdarzeń

Najpierw utwórz aplikację logiki za pomocą programu obsługi siatki zdarzeń i subskrybuj zdarzenia usługi Azure Key Vault "SecretNewVersionCreated".

Aby utworzyć subskrypcję usługi Azure Event Grid, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do magazynu kluczy, wybierz pozycję **Zdarzenia > wprowadzenie** i kliknij pozycję **Aplikacje logiki**

    
    ![Przechowalnia kluczy — strona wydarzeń](../media/eventgrid-logicapps-kvsubs.png)

1. W **programie Logic Apps Designer** sprawdź poprawność połączenia i kliknij przycisk **Kontynuuj** 
 
    ![Logic App Designer - połączenie](../media/eventgrid-logicappdesigner1.png)

1. Na ekranie **Gdy wystąpi zdarzenie zasobu** wykonaj następujące czynności:
    - Pozostaw **subskrypcję** i **nazwę zasobu** jako domyślną.
    - Wybierz **pozycję Microsoft.KeyVault.vaults** dla **typu zasobu**.
    - Wybierz **pozycję Microsoft.KeyVault.SecretNewVersionTwoło** dla **elementu typu zdarzenia — 1**.

    ![Logic App Designer — program obsługi zdarzeń](../media/eventgrid-logicappdesigner2.png)

1. Wybierz **+ Nowy krok** Otworzy się okno, aby wybrać akcję.
1. Wyszukaj nazwę **E-mail**. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Ten samouczek używa **usługi Office 365 Outlook**. Kroki dla innych dostawców poczty e-mail są podobne.
1. Wybierz akcję **Wyślij wiadomość e-mail (V2).**

   ![Logic App Designer - dodawanie wiadomości e-mail](../media/eventgrid-logicappdesigner3.png)

1. Zbuduj szablon wiadomości e-mail:
    - **Do:** Wprowadź adres e-mail, aby otrzymywać wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, które nadaje się do celów testowych.
    - **Temat** i **Treść**: wpisz tekst wiadomości e-mail. Za pomocą narzędzia do wybierania wybierz właściwości JSON w celu dołączenia zawartości dynamicznej opartej na danych zdarzenia. Dane zdarzenia można pobrać za `@{triggerBody()?['Data']}`pomocą programu .

    Szablon wiadomości e-mail może wyglądać jak w tym przykładzie.

    ![Logic App Designer - dodawanie wiadomości e-mail](../media/eventgrid-logicappdesigner4.png)

8. Kliknij **pozycję Zapisz jako**.
9. Wprowadź **nazwę** nowej aplikacji logiki i kliknij przycisk **Utwórz**.
    
    ![Logic App Designer - dodawanie wiadomości e-mail](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

1.  Przejdź do magazynu kluczy w witrynie Azure portal i wybierz **pozycję Zdarzenia > subskrypcje zdarzeń**.  Sprawdzanie, czy nowa subskrypcja została utworzona
    
    ![Logic App Designer - dodawanie wiadomości e-mail](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Przejdź do magazynu kluczy, wybierz pozycję **Wpisy tajne**i wybierz **pozycję + Generuj/Importuj**. Utwórz nowy klucz tajny do celów testowania nazwij klucz i zachowaj pozostałe parametry w ustawieniach domyślnych.

    ![Magazyn kluczy — tworzenie klucza tajnego](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na ekranie **Utwórz klucz tajny** podaj dowolną nazwę, dowolną wartość i wybierz pozycję **Utwórz**.

Po utworzeniu klucza tajnego wiadomość e-mail zostanie odebrana pod skonfigurowanym adresem.

## <a name="next-steps"></a>Następne kroki

- Omówienie: [Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md)
- Jak: [Kierowanie powiadomień magazynu kluczy do usługi Azure Automation](event-grid-tutorial.md).
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)](../../event-grid/event-schema-key-vault.md)
- Dowiedz się więcej na temat usługi [Azure Event Grid](../../event-grid/index.yml).
- Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](../../logic-apps/index.yml).
