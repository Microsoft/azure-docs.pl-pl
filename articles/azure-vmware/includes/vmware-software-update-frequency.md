---
title: Częstotliwość aktualizacji oprogramowania VMware
description: Obsługiwana częstotliwość aktualizacji oprogramowania VMware dla rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869867"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Firma Microsoft jest odpowiedzialna za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NXS) w chmurze prywatnej rozwiązania Azure VMware.

Oprogramowanie chmury prywatnej jest uaktualniane zgodnie z harmonogramem, który śledzi wydanie pakietu oprogramowania z programu VMware. Chmura prywatna nie wymaga przestojów w przypadku uaktualnień.

Uaktualnienie pakietu oprogramowania w chmurze prywatnej zachowuje oprogramowanie w ramach jednej wersji najnowszego pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN).

Aktualizacje oprogramowania obejmują:

- **Poprawki** — poprawki zabezpieczeń lub poprawki błędów wydane przez program VMware
- **Aktualizacje** — zmiana wersji pomocniczej składnika stosu VMware
- **Uaktualnienia** — główna zmiana wersji składnika stosu VMware

Firma Microsoft testuje krytyczną poprawkę zabezpieczeń, gdy tylko staną się dostępne z programu VMware.

Udokumentowane obejścia programu VMware są implementowane zamiast instalowania odpowiedniej poprawki do momentu wdrożenia kolejnych zaplanowanych aktualizacji. 
