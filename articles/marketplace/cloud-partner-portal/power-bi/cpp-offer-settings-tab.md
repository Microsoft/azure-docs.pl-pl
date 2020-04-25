---
title: Ustawienia oferty dla oferty aplikacji Power BI | Portal Azure Marketplace
description: Skonfiguruj ustawienia oferty dla oferty aplikacji Power BI dla witryny Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143035"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji Power BI

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami Power BI aplikacji do Centrum partnerskiego. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Power BI — omówienie tworzenia aplikacji](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) , aby zarządzać migrowanymi ofertami.

Po otwarciu nowej strony **oferty** dla aplikacji usługi, najpierw zobaczysz kartę **Ustawienia oferty** . Na tej karcie Podaj identyfikatory podstawowe i nazwę oferty. Gwiazdka (*) wskazuje pole wymagane.

![Karta ustawień oferty](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pola ustawień oferty 

Na karcie **Ustawienia oferty** musisz wprowadzić informacje w następujących wymaganych polach. Wymagane pola są indicted przez gwiazdkę (*).

|  Pole        |  Opis                                                               |
|---------------|----------------------------------------------------------------------------|
| **Identyfikator oferty\***  | Unikatowy identyfikator (w ramach profilu wydawcy) oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach Azure Resource Manager i raportach dotyczących rozliczeń. Maksymalna długość to 50 znaków. Może zawierać tylko małe znaki alfanumeryczne i łączniki (-). Nie może kończyć się kreską. Nie można zmienić tego identyfikatora po przejściu oferty na żywo. Jeśli firma Contoso opublikuje ofertę o IDENTYFIKATORze `sample-SvcApp`oferty, do oferty zostanie przypisany adres URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource.      |
| **Dawc\*** | Unikatowy identyfikator organizacji w [AppSource](https://appsource.microsoft.com). Wszystkie oferty powinny być skojarzone z IDENTYFIKATORem wydawcy. Tej wartości nie można zmienić po zapisaniu oferty.                         |
| **Nazwa\***      | Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w AppSource i na portal Cloud Partner. Maksymalna długość to 50 znaków. Użyj nazwy marki, która jest rozpoznawana jako produkt. Nie dodawaj tutaj nazwy organizacji, chyba że aplikacja zostanie wprowadzona na rynek. Jeśli udostępniasz tę ofertę w innych witrynach sieci Web i publikacjach, Użyj tej samej nazwy we wszystkich publikacjach.    <br/>Jeśli zwolnisz ofertę w okresie zapoznawczym dla aplikacji Power BI, Dodaj ciąg `(Preview)` na końcu nazwy aplikacji, na przykład:. `Sample Scv App (Preview)` |
|     |     |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie określisz [Informacje techniczne](./cpp-technical-info-tab.md) dla swojej oferty.
