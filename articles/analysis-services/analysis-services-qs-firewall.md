---
title: Szybki start — konfigurowanie Azure Analysis Services zapory serwera | Microsoft Docs
description: Ten przewodnik Szybki start pomaga skonfigurować zaporę dla serwera Azure Analysis Services przy użyciu Azure Portal.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/12/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: dd8324607667c6d1f5e397a3266026cf7f2dcc93
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536716"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Szybki start: konfigurowanie zapory serwera — Portal

Ten przewodnik Szybki start ułatwia konfigurowanie zapory na potrzeby serwera usług Azure Analysis Services. Włączanie zapory i konfiguracja zakresów adresów IP tylko na potrzeby komputerów uzyskujących dostęp do serwera użytkownika są ważnym elementem zabezpieczenia serwera i danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Serwer usług Analysis Services w ramach subskrypcji. Więcej informacji znajduje się w tematach [Quickstart: Create a server - Portal](analysis-services-create-server.md) (Szybki start: tworzenie serwera — Portal) oraz [Quickstart: Create a server - PowerShell](analysis-services-create-powershell.md) (Szybki start: tworzenie serwera — program PowerShell)
- Co najmniej jeden zakres adresów IP na potrzeby komputerów klienckich (w razie potrzeby).

> [!NOTE]
> Importowanie (odświeżanie) danych i połączenia raportów podzielonych na strony z usługi Power BI Premium w usłudze Microsoft Cloud w Niemczech nie są obecnie obsługiwane, gdy zapora jest włączona, nawet jeśli ustawienie Zezwalaj na dostęp z sieci Power BI jest włączone.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal 

[Zaloguj się do portalu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurowanie zapory

1. Kliknij serwer, aby otworzyć stronę Omówienie. 
2. W   >  **ustawieniach**  >  **Zapora Włącz zaporę** wybierz pozycję **Wł.**
3. Aby włączyć połączenia z Power BI i Power BI Premium, w opcji **Zezwalaj** na dostęp z Power BI wybierz pozycję **Wł.**.  
4. (Opcjonalnie) Podaj co najmniej jeden zakres adresów IP. Wprowadź nazwę oraz początkowy i końcowy adres IP dla każdego zakresu. Nazwa reguły zapory powinna być ograniczona do 128 znaków i może zawierać tylko wielkie litery, małe litery, cyfry, podkreślenie i łącznik. Puste spacje i inne znaki specjalne nie są dozwolone.
5. Kliknij pozycję **Zapisz**.

     ![Ustawienia zapory](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zakresy adresów IP nie będą już potrzebne, usuń je lub wyłącz zaporę.

## <a name="next-steps"></a>Następne kroki
W tym przewodniu Szybki start opisano, jak skonfigurować zaporę dla serwera. Teraz gdy serwer został już utworzony i zabezpieczony przy użyciu zapory, możesz do niego dodać podstawowy przykładowy model danych z portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Tutorial: Add a sample model to your server](analysis-services-create-sample-model.md) (Samouczek: dodawanie przykładowego modelu do serwera)
