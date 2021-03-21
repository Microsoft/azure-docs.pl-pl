---
title: Architektura usługi Azure FarmBeats
description: Opisuje architekturę platformy Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182608"
---
# <a name="integration-patterns"></a>Wzorce integracji

Azure FarmBeats to oferta biznesowa, dostępna w witrynie Azure Marketplace. FarmBeats umożliwia agregację zbiorów danych rolniczych między dostawcami oraz generowanie szczegółowych informacji z możliwością podejmowania działań przez tworzenie modeli sztucznej analizy (AI) lub Machine Learning (ML) przez odmowę zbiorów.

![Farmy projektów](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

W poniższych sekcjach opisano wzorzec integracji dla usługi Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Dlaczego warto zintegrować usługę Azure FarmBeats?

Ta sekcja koncentruje się na partnerach, którzy chcą zintegrować systemy danych (takie jak czujniki, dronomy, stacje pogodowe) z usługą Azure FarmBeats.

Azure FarmBeats to rozszerzalna oferta, dzięki której firmy rolnicze mogą dodawać różne historyczne i rolnicze zestawy danych w czasie rzeczywistym do jednej platformy. Usługa Azure FarmBeats pomaga firmie rolniczej znormalizować, contextualize i agregować swoje dane w kontekście farmy.

Zostań partnerem danych za pomocą usługi Azure FarmBeats, możesz otworzyć systemy w celu uzyskania szerszego wdrożenia i skontaktować się z innymi klientami przy użyciu ofert dotyczących danych. Usługa Azure FarmBeats zapewnia rozszerzalną warstwę interfejsu API o nazwie Datahub, która ułatwia systematyczne pozyskiwanie danych z urządzeń i w ustandaryzowanym schemacie.

Gdy dane będą dostępne w ramach wystąpienia usługi Azure FarmBeats dla klientów, klienci mogą tworzyć bogatsze analizy i narzędzia na podstawie danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji danych czujników, zobacz Integracja [danych czujników](sensor-partner-integration-in-azure-farmbeats.md) i integracja z partnerem obrazów — zobacz [integracja z partnerem obrazów](imagery-partner-integration-in-azure-farmbeats.md).
