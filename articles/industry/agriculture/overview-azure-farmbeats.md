---
title: Co to jest Azure FarmBeats
description: Zawiera omówienie usługi Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87439562"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Omówienie usługi Azure FarmBeats (wersja zapoznawcza)

Azure FarmBeats to oferta biznesowa dostępna w witrynie Azure Marketplace. Umożliwia agregację zestawów danych rolnictwa między dostawcami. Usługa Azure FarmBeats umożliwia tworzenie modeli sztucznej inteligencją (AI) lub uczenia maszynowego (ML) w oparciu o wyrzucane zestawy danych. Korzystając z usługi Azure FarmBeats, firmy rolnicze mogą skupić się na podstawowych wartościach, a nie w bardzo zróżnicowanym zakresie inżynierii danych.

> [!NOTE]
> Usługa Azure FarmBeats jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usługa Azure FarmBeats jest świadczona bez umowy dotyczącej poziomu usług. Skorzystaj z [forum pomocy technicznej platformy Azure FarmBeats](https://aka.ms/farmbeatssupport) , aby uzyskać pomoc techniczną.

![Farmy projektów](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Korzystając z wersji zapoznawczej usługi Azure FarmBeats, możesz wykonać następujące działania:

- Oceniaj kondycję farmy przy użyciu indeksu roślinności i indeksu wodnego na podstawie obrazów satelitarnych.
- Zapoznaj się z zaleceniami dotyczącymi liczby czujników wilgoci gleby do użycia i miejsca, w których należy je umieścić.
- Śledź warunki farmy, wizualizowając dane naziemne zbierane przez czujniki od różnych dostawców.
- Pobierz mapę wilgotności gleby na podstawie łączenia danych satelitarnych i czujników.
- Uzyskaj szczegółowe informacje umożliwiające podjęcie odpowiednich działań, tworząc modele AI/ML na agregacji zagregowanych zestawów danych.
- Utwórz lub Uzupełnij rozwiązanie do elektronicznego rolnictwu, dostarczając doradcy kondycji farmy.

## <a name="datahub"></a>Datahub

Azure FarmBeats Datahub to warstwa interfejsu API, która umożliwia agregację, normalizację i contextualization różnych zestawów danych rolnictwa między dostawcami. Możesz użyć usługi Azure FarmBeats, aby uzyskać następujące korzyści:
- **Dane czujnika** z dwóch dostawców czujników, [instrumenty](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl instrumenty](https://metos.at/)
- **Przekazy satelitarne z** satelitarnej agencji kosmicznej z firmy The [-2](https://sentinel.esa.int/web/sentinel/home)
- **Drona** z trzech [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub zaprojektowano jako rozszerzalną platformę interfejsu API. Pracujemy nad wieloma dostawcami, którzy integrują się z usługą Azure FarmBeats, dzięki czemu masz większą możliwość wyboru podczas kompilowania rozwiązania.

## <a name="accelerator"></a>Skrót

Usługa Azure FarmBeats Accelerator to przykładowa aplikacja internetowa, która jest oparta na Datahub. Akcelerator przyśpiesza — uruchamia interfejs użytkownika i programowanie modeli. Akcelerator Azure FarmBeats używa interfejsów API usługi Azure FarmBeats. Wizualizuje dane z czujników pozyskiwane jako wykresy i wyjścia modelu jako mapy. Można na przykład użyć akceleratora, aby szybko utworzyć farmę i uzyskać mapę indeksów oddziałów lub mapę umieszczenia czujnika dla tej farmy.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Administrator może zdefiniować reguły dostępu dla usługi Azure FarmBeats przy użyciu jednej ze wstępnie zdefiniowanych ról. Role określają obszary aplikacji, do których użytkownik ma dostęp i jakie akcje mogą wykonywać. Istnieją dwa rodzaje ról w usłudze Azure FarmBeats — dla użytkowników i dla partnerów.

### <a name="user-roles"></a>Role użytkowników

[Administrator może dodawać i zarządzać użytkownikami](manage-users-in-azure-farmbeats.md) oraz definiować ich poziomy dostępu w oparciu o dwie role użytkowników: administrator i tylko do odczytu.

### <a name="partner-roles"></a>Role partnerów

Administrator może dodać wielu partnerów jako dostawców danych do usługi Azure FarmBeats. Poniżej zestawiono dostępne role partnerów w FarmBeats i ich uprawnienia:

| Typ partnera    |   Akcje  | Zakres |
| ---- | -------- | -------- |
| Partner czujnika  |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie | DeviceModel, urządzenie, SensorModel, czujnik <br/> <br/> ExtendedType |
| Partner obrazów  |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie <br/> <br/> Odczyt | Sceny, SceneFile <br/> <br/> ExtendedType <br/> <br/> Hodowli |
| Partner pogody * <br/> <br/>  (* Już wkrótce) |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie <br/> <br/> Odczyt | WeatherDataModel, WeatherDataLocation, typu zadania <br/> <br/> ExtendedType <br/> <br/> Hodowli |

## <a name="resources"></a>Zasoby

Usługa Azure FarmBeats jest oferowana bez dodatkowych opłat i płacisz tylko za zasoby platformy Azure, których używasz. Możesz użyć poniższych zasobów, aby dowiedzieć się więcej o ofercie:

- Zapoznaj się z najnowszymi informacjami na temat usługi Azure FarmBeats, odwiedzając nasz [blog usługi Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Szukaj pomocy, ogłaszając pytanie na naszym [forum pomocy technicznej Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Prześlij Opinie, publikując lub głosując, aby poznać swoją funkcję na naszym [forum opinii o usłudze Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj projekt Azure FarmBeats](install-azure-farmbeats.md)
