---
title: Przykładowa aplikacja do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace
description: Korzystaj z przykładowej aplikacji, aby utworzyć własną komercyjną aplikację analityczną w portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584078"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Przykładowa aplikacja do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace

Przykładowe aplikacje są tworzone w językach C# i JAVA i są dostępne w serwisie [GitHub](https://github.com/partneranalytics).

- [Przykładowa aplikacja w języku C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Przykładowa aplikacja JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Możesz skorzystać z przykładowej aplikacji i utworzyć własną aplikację w dowolnym języku.

Przykładowa aplikacja realizuje następujące cele:

- Generuje token Azure Active Directory (Azure AD).
- Pobiera dostępne zestawy danych.
- Tworzy zapytania zdefiniowane przez użytkownika.
- Pobiera kwerendy systemowe i zdefiniowane przez użytkownika.
- Planuje raport.

Przykładowa aplikacja nie obejmuje metody wywoływania interfejsów API dla innych funkcji. Jednak proces wywoływania innych interfejsów API pozostaje taki sam, jak opisano powyżej.

## <a name="how-to-run-the-application"></a>Jak uruchomić aplikację

1. Sklonuj repozytorium do systemu lokalnego przy użyciu tego polecenia:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Aby uzyskać więcej instrukcji, zapoznaj się z `ProgrammaticExportSampleAppISV/README.md` plikiem w [repozytorium](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)GitHub.

1. Aby szybko uruchomić aplikację, zaktualizuj identyfikator klienta i klucz tajny klienta w **appsettings.Development.jsna**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Ilustruje fragment appsettings.Development.jspliku.":::

Uruchomienie aplikacji spowoduje uruchomienie lokalnego serwera sieci Web, a strona zostanie otwarta ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Ilustruje zaplanowaną stronę raportu.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Ta strona spowoduje wywołanie interfejsu API na serwerze sieci Web działającym na komputerze lokalnym, co z kolei spowoduje wykonanie rzeczywistych wywołań interfejsu API dostępu programowego.

## <a name="code-snippets"></a>Fragmenty kodu

Podstawowa struktura kodu C# do wykonywania wywołań interfejsu API dostępu programowego jest następująca:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Zrzut ekranu wywołań interfejsu API.":::

## <a name="next-steps"></a>Następne kroki

- [Interfejsy API do uzyskiwania dostępu do komercyjnych danych analitycznych portalu Marketplace](analytics-available-apis.md)
