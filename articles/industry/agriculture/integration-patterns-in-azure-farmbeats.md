---
title: Architektura usługi Azure FarmBeats
description: Opisuje architekturę platformy Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482376"
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
