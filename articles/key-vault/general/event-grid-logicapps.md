---
title: Wiadomość e-Key Vault o zmianie stanu tajnego
description: Przewodnik dotyczący używania Logic Apps do reagowania na zmiany Key Vault wpisów tajnych
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e6ec06e3df87e0ad0e50efca24439435d655f6a1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752106"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Użyj Logic Apps, aby otrzymywać wiadomości e-mail o zmianach stanu wpisów tajnych magazynu kluczy

W tym przewodniku dowiesz się, jak reagować na zdarzenia Azure Key Vault odbierane za pośrednictwem Azure Event Grid [przy](../../event-grid/index.yml) użyciu [Azure Logic Apps](../../logic-apps/index.yml). Na koniec będziesz mieć aplikację logiki platformy Azure, która będzie wysyłać wiadomość e-mail z powiadomieniem za każdym razem, gdy klucz tajny zostanie utworzony w Azure Key Vault.

Aby uzyskać omówienie integracji Azure Key Vault/Azure Event Grid, zobacz Monitorowanie Key Vault [za pomocą Azure Event Grid.](event-grid-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwanego przez Azure Logic Apps (np. Office 365 Outlook). To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](/connectors).
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Magazyn kluczy w ramach subskrypcji platformy Azure. Nowy magazyn kluczy można szybko utworzyć, korzystając z kroków instrukcje z tematu Set and retrieve a secret from Azure Key Vault using Azure CLI (Ustawianie i pobieranie klucza tajnego z usługi [Azure Key Vault interfejsu wiersza polecenia platformy Azure).](../secrets/quick-create-cli.md)
- Zarejestrowane Event Grid jako dostawca zasobów, zobacz [Rejestracje dostawców zasobów](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Tworzenie aplikacji logiki za pośrednictwem Event Grid

Najpierw utwórz aplikację logiki z programem obsługi usługi Event Grid i zasubskrybuj Azure Key Vault zdarzeń "SecretNewVersionCreated".

Aby utworzyć Azure Event Grid subskrypcji, wykonaj następujące kroki:

1. W Azure Portal przejdź do magazynu kluczy, wybierz pozycję **Zdarzenia > Rozpocznij** i kliknij pozycję **Logic Apps**

    
    ![Key Vault — strona zdarzeń](../media/eventgrid-logicapps-kvsubs.png)

1. Na **Logic Apps Designer zweryfikuj** połączenie i kliknij przycisk **Kontynuuj** 
 
    ![Projektant aplikacji logiki — połączenie](../media/eventgrid-logicappdesigner1.png)

1. Na **ekranie Gdy wystąpi zdarzenie zasobu,** wykonaj następujące czynności:
    - Pozostaw **wartości** **domyślne Subscription (Subskrypcja) i Resource Name (Nazwa** zasobu).
    - Wybierz **pozycję Microsoft.KeyVault.vaults dla** opcji Typ **zasobu.**
    - Wybierz **pozycję Microsoft.KeyVault.SecretNewVersionCreated w pozycji** Element typu zdarzenia — **1.**

    ![Projektant aplikacji logiki — procedura obsługi zdarzeń](../media/eventgrid-logicappdesigner2.png)

1. Wybierz **pozycję + Nowy krok.** Spowoduje to otwarcie okna Wybierz akcję.
1. Wyszukaj nazwę **E-mail**. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. W tym samouczku **jest używana usługa Office 365 Outlook.** Kroki dla innych dostawców poczty e-mail są podobne.
1. Wybierz **akcję Wyślij wiadomość e-mail (wersja 2).**

   ![Projektant aplikacji logiki — wysyłanie wiadomości e-mail](../media/eventgrid-logicappdesigner3.png)

1. Skompilowanie szablonu wiadomości e-mail:
    - **Do:** Wprowadź adres e-mail, na który będą wysyłane wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, do którego masz dostęp, w celach testowych.
    - **Temat** i **Treść**: wpisz tekst wiadomości e-mail. Wybierz właściwości JSON przy użyciu narzędzia selektora, aby dodać zawartość dynamiczną na podstawie danych zdarzenia. Dane zdarzenia można pobrać przy użyciu `@{triggerBody()?['Data']}` .

    Szablon wiadomości e-mail może wyglądać podobnie do tego przykładu.

    ![Projektant aplikacji logiki — treść wiadomości e-mail](../media/eventgrid-logicappdesigner4.png)

8. Kliknij **pozycję Zapisz jako**.
9. Wprowadź nazwę **nowej aplikacji** logiki i kliknij pozycję **Utwórz.**
    
    ![Projektant aplikacji logiki — tworzenie](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

1.  Przejdź do magazynu kluczy na stronie usługi Azure Portal wybierz pozycję **Zdarzenia > subskrypcje zdarzeń.**  Sprawdź, czy utworzono nową subskrypcję
    
    ![Projektant aplikacji logiki — testowanie i weryfikowanie](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Przejdź do magazynu kluczy, wybierz pozycję **Wpisy tajne** i **wybierz pozycję + Generuj/Importuj.** Utwórz nowy klucz tajny na potrzeby testowania, nadaj kluczowi nazwę i zachowaj pozostałe parametry w ich ustawieniach domyślnych.

    ![Key Vault — tworzenie tajnego](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na **ekranie Tworzenie tajnego** pliku podaj dowolną nazwę i dowolną wartość, a następnie wybierz pozycję **Utwórz**.

Po utworzeniu tajnego adresu e-mail zostanie odebrana wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

- Omówienie: [Monitorowanie Key Vault za pomocą Azure Event Grid](event-grid-overview.md)
- How to: [Route key vault notifications to Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid zdarzeń dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Dowiedz się więcej na temat usługi [Azure Event Grid](../../event-grid/index.yml).
- Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](../../logic-apps/index.yml).