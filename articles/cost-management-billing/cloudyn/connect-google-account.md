---
title: Łączenie konta Google Cloud Platform z usługą Cloudyn na platformie Azure
description: Połącz konto Google Cloud Platform, aby wyświetlać dane dotyczące kosztów i użycia w raportach usługi Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 8d549a8fd7c8b03f98fe2b11c94531ed323e0e3a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92526461"
---
# <a name="connect-a-google-cloud-platform-account"></a>Nawiązywanie połączenia z kontem Google Cloud Platform

Istniejące konto Google Cloud Platform można połączyć z usługą Cloudyn. Po połączeniu konta z usługą Cloudyn dane dotyczące kosztów i użycia będą dostępne w raportach usługi Cloudyn. Ten artykuł pomaga w konfigurowaniu i łączeniu konta Google z usługą Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="collect-project-information"></a>Zbieranie informacji o projekcie

Zacznij od zebrania informacji o projekcie.

1. Zaloguj się do konsoli Google Cloud Platform w witrynie [https://console.cloud.google.com](https://console.cloud.google.com).
2. Przejrzyj informacje o projekcie, które mają zostać dołączone do usługi Cloudyn, i zanotuj **Nazwę projektu** i **Identyfikator projektu** . Zachowaj te informacje w celu użycia w kolejnych krokach.  
    ![Nazwa projektu i identyfikator projektu wyświetlane w konsoli Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Jeśli rozliczenia nie są włączone i połączone z Twoim projektem, utwórz konto rozliczeniowe. Aby uzyskać więcej informacji, zobacz temat [Tworzenie nowego konta rozliczeniowego](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Włączanie eksportowania rozliczeń z zasobnika magazynu

Usługa Cloudyn pobiera dane rozliczeniowe firmy Google z zasobnika magazynu. Zachowaj **Nazwę zasobnika** i **Prefiks raportu** do późniejszego użycia podczas rejestracji usługi Cloudyn.

Użycie usługi Google Cloud Storage do przechowywania raportów użycia minimalizuje opłaty. Aby uzyskać więcej informacji, zobacz [Cloud Storage Pricing (Cennik usługi Cloud Storage)](https://cloud.google.com/storage/pricing).

1. Jeśli nie włączono eksportowania rozliczeń do pliku, postępuj zgodnie z instrukcjami w artykule [How to enable billing export to a file (Jak włączyć eksport rozliczeń do pliku)](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Do eksportowania rozliczeń możesz użyć formatu JSON lub CSV.
2. W przeciwnym razie w konsoli Google Cloud Platform przejdź do obszaru **Płatności** > **Eksport płatności** . Zanotuj **Nazwę zasobnika** i **Prefiks raportu** .  
    ![Informacje o eksporcie rozliczeń widoczne na stronie eksportowania rozliczeń](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Włączanie interfejsów API Google Cloud Platform

Aby móc zbierać informacje dotyczące użycia i zasobów, usługa Cloudyn wymaga włączenia następujących interfejsów API Google Cloud Platform:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Włączanie lub weryfikowanie interfejsów API

1. W konsoli Google Cloud Platform wybierz projekt, który chcesz zarejestrować w usłudze Cloudyn.
2. Przejdź do obszaru **Interfejsy API i usługi** > **Biblioteka** .
3. Użyj funkcji wyszukiwania, aby znaleźć poszczególne wymienione wcześniej interfejsy API.
4. Dla każdego interfejsu API sprawdź, czy jest wyświetlany komunikat **Włączono interfejs API** . W przeciwnym razie kliknij przycisk **WŁĄCZ** .

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Dodawanie konta usługi Google Cloud do usługi Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę [https://azure.cloudyn.com](https://azure.cloudyn.com/) i zaloguj się.
2. Kliknij pozycję **Ustawienia** (symbol koła zębatego), a następnie wybierz pozycję **Cloud Accounts** (Konta w chmurze).
3. W obszarze **Accounts Management** (Zarządzanie kontami) wybierz kartę **Google Accounts** (Konta Google), a następnie kliknij przycisk **Add new +** (Dodaj nowe +).
4. W obszarze **Google Account Name** (Nazwa konta Google) wprowadź adres e-mail dla konta rozliczeniowego i kliknij przycisk **Dalej** .
5. W oknie dialogowym uwierzytelniania Google wybierz lub wprowadź nazwę konta Google, a następnie w polu **ALLOW** (Zezwól) pozwól na dostęp z witryny cloudyn.com do Twojego konta.
6. Dodaj zanotowane wcześniej informacje o projekcie żądania. Są to informacje, takie jak **identyfikator projektu** , **nazwa projektu** , **nazwa zasobnika rozliczeń** i **prefiks pliku raportu rozliczeniowego** , a następnie kliknij przycisk **Save** (Zapisz).  
    ![Dodawanie projektu Google do konta usługi Cloudyn](./media/connect-google-account/add-project.png)

Twoje konto Google zostanie wyświetlone na liście kont i powinno być oznaczone jako **Authenticated** (Uwierzytelnione). Pod nazwą konta powinny być wyświetlone nazwa i identyfikator projektu Google z zielonym znacznikiem wyboru. Stan konta powinien być wyświetlany jako **Completed** (Ukończony).

W ciągu kilku godzin w raportach usługi Cloudyn zostaną wyświetlone informacje o kosztach i użyciu konta Google.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat usługi Cloudyn, przejdź do samouczka usługi Cloudyn [Przeglądanie użycia i kosztów](tutorial-review-usage.md).
